---
title: Problemen met de NFS-opslag doelen van Azure HPC cache oplossen
description: Tips voor het voor komen en oplossen van configuratie fouten en andere problemen die kunnen leiden tot fouten bij het maken van een NFS-opslag doel
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652085"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Problemen met de NAS-configuratie en NFS-opslag doelen oplossen

Dit artikel bevat oplossingen voor enkele veelvoorkomende configuratie fouten en andere problemen die kunnen voor komen dat Azure HPC-cache een NFS-opslag systeem kan toevoegen als opslag doel.

Dit artikel bevat informatie over het controleren van poorten en het inschakelen van toegang tot het hoofd niveau van een NAS-systeem. Het bevat ook gedetailleerde informatie over minder veelvoorkomende problemen die ertoe kunnen leiden dat het maken van NFS-opslag doel mislukt.

> [!TIP]
> Lees de [vereisten voor NFS-opslag doelen](hpc-cache-prereqs.md#nfs-storage-requirements)voordat u deze hand leiding gebruikt.

Als de oplossing voor uw probleem hier niet is opgenomen, [opent u een ondersteunings ticket](hpc-cache-support-ticket.md) zodat micro soft-service en-ondersteuning met u kunnen samen werken om het probleem te onderzoeken en op te lossen.

## <a name="check-port-settings"></a>Poort instellingen controleren

Azure HPC-cache vereist lees-/schrijftoegang tot verschillende UDP/TCP-poorten op het back-end NAS-opslag systeem. Zorg ervoor dat deze poorten toegankelijk zijn op het NAS-systeem en dat verkeer wordt toegestaan aan deze poorten via alle firewalls tussen het opslag systeem en het cache-subnet. U moet mogelijk met de firewall en netwerk beheerder voor uw Data Center werken om deze configuratie te controleren.

De poorten zijn verschillend voor opslag systemen van verschillende leveranciers. Controleer daarom de vereisten van uw systeem bij het instellen van een opslag doel.

In het algemeen moet de cache toegang hebben tot deze poorten:

| Protocol | Poort  | Service  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | gekoppeld   |
| TCP/UDP  | 4047  | status   |

Gebruik de volgende ``rpcinfo`` opdracht voor meer informatie over de specifieke poorten die nodig zijn voor uw systeem. Met deze opdracht hieronder worden de poorten weer gegeven en worden de relevante resultaten in een tabel opgemaakt. (Gebruik het IP-adres van uw systeem in plaats van de *< storage_IP >* term.)

U kunt deze opdracht geven vanuit elke Linux-client waarop de NFS-infra structuur is geïnstalleerd. Als u een-client in het cluster-subnet gebruikt, kunt u hiermee ook de connectiviteit tussen het subnet en het opslag systeem controleren.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Zorg ervoor dat alle poorten die door de ``rpcinfo`` query worden geretourneerd, onbeperkt verkeer van het subnet van de Azure HPC-cache toestaan.

Controleer deze instellingen zowel op de NAS zelf als op alle firewalls tussen het opslag systeem en het cache-subnet.

## <a name="check-root-access"></a>Toegang tot hoofdmap controleren

De Azure HPC-cache moet toegang hebben tot de export van uw opslag systeem om het opslag doel te maken. Met name de exports worden gekoppeld als gebruiker-ID 0.

Verschillende opslag systemen gebruiken verschillende methoden om deze toegang in te scha kelen:

* Linux-servers voegen doorgaans ``no_root_squash`` toe aan het geëxporteerde pad in ``/etc/exports``.
* NetApp-en EMC-systemen bepalen doorgaans de toegang tot de export regels die zijn gekoppeld aan specifieke IP-adressen of netwerken.

Als u regels voor exporteren gebruikt, moet u er rekening mee houden dat de cache meerdere verschillende IP-adressen van het cache-subnet kan gebruiken. Toegang vanaf het volledige bereik van mogelijke subnet-IP-adressen toestaan.

Werk samen met de leverancier van uw NAS-opslag om het juiste toegangs niveau voor de cache in te scha kelen.

### <a name="allow-root-access-on-directory-paths"></a>Toegang tot hoofdmap toestaan voor Directory paden
<!-- linked in prereqs article -->

Voor NAS-systemen die hiërarchische directory's exporteren, moet Azure HPC-cache toegang hebben tot de hoofdmap voor elk export niveau.

Een systeem kan bijvoorbeeld drie exports als volgt weer geven:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

De export ``/ifs/accounting/payroll`` is een onderliggend item van ``/ifs/accounting``en ``/ifs/accounting`` is zelf een onderliggend element van ``/ifs``.

Als u de ``payroll`` exporteren als een HPC-cache-opslag doel, wordt de cache echt gekoppeld ``/ifs/`` en wordt de map payroll van daaruit geopend. Azure HPC-cache heeft ``/ifs`` alleen toegang tot de hoofdmap nodig om de ``/ifs/accounting/payroll`` te kunnen exporteren.

Deze vereiste houdt verband met de manier waarop de cache bestanden indexeert en conflicten met bestanden voor komt, met behulp van bestands ingangen die het opslag systeem biedt.

Een NAS-systeem met hiërarchische uitvoer kan verschillende bestands ingangen geven voor hetzelfde bestand als het bestand wordt opgehaald uit verschillende exports. Een client kan bijvoorbeeld ``/ifs/accounting`` koppelen en toegang krijgen tot het bestand ``payroll/2011.txt``. Een andere client koppelt ``/ifs/accounting/payroll`` en opent het bestand ``2011.txt``. Afhankelijk van hoe het opslag systeem bestands ingangen toewijst, kunnen deze twee clients hetzelfde bestand met verschillende bestands ingangen ontvangen (één voor ``<mount2>/payroll/2011.txt`` en één voor ``<mount3>/2011.txt``).

Het back-end-opslag systeem houdt interne aliassen voor bestands ingangen bij, maar de Azure HPC-cache kan niet bepalen welke bestands ingangen in de index naar hetzelfde item verwijzen. Het is dus mogelijk dat de cache verschillende schrijf bewerkingen voor hetzelfde bestand kan hebben en dat de wijzigingen onjuist worden toegepast omdat deze niet overeenkomen met het bestand.

Als u wilt voor komen dat er conflicten ontstaan met bestanden in meerdere uitvoer bewerkingen, koppelt Azure HPC cache automatisch de meest recente uitvoer in het pad (``/ifs`` in het voor beeld) en gebruikt de bestands ingang die van die export is opgegeven. Als meerdere exports hetzelfde basispad gebruiken, moet Azure HPC-cache toegang hebben tot het pad naar de hoofdmap.

## <a name="enable-export-listing"></a>Export vermelding inschakelen
<!-- link in prereqs article -->

De NAS moet de exports vermelden wanneer de Azure HPC-cache deze opvraagt.

Op de meeste NFS-opslag systemen kunt u dit testen door de volgende query te verzenden vanaf een Linux-client: ``showmount -e <storage IP address>``

Gebruik, indien mogelijk, een Linux-client uit hetzelfde virtuele netwerk als uw cache.

Als met deze opdracht geen uitvoer wordt vermeld, kan de cache problemen met het maken van een verbinding met uw opslag systeem krijgen. Werk samen met de leverancier van uw NAS om de export vermelding in te scha kelen.

## <a name="adjust-vpn-packet-size-restrictions"></a>Beperkingen voor VPN-pakket grootte aanpassen
<!-- link in prereqs article -->

Als u een VPN tussen de cache en uw NAS-apparaat hebt, kan het VPN de volledige 1500-bytes Ethernet-pakketten blok keren. Dit probleem kan optreden als grote uitwisselingen tussen de NAS en het Azure HPC-cache-exemplaar niet zijn voltooid, maar kleinere updates werken zoals verwacht.

Er is geen eenvoudige manier om te bepalen of uw systeem dit probleem heeft tenzij u de details van uw VPN-configuratie kent. Hier volgen enkele methoden die u kunnen helpen bij het controleren op dit probleem.

* Gebruik pakket-sniffs aan beide zijden van de VPN om te detecteren welke pakketten zijn overgedragen.
* Als uw VPN ping-opdrachten toestaat, kunt u de verzen ding van een pakket op volledige grootte testen.

  Voer een ping-opdracht via de VPN-verbinding met de NAS uit met deze opties. (Gebruik het IP-adres van uw opslag systeem in plaats van de *< storage_IP >* waarde.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Dit zijn de opties in de opdracht:

  * ``-M do``-niet fragmenteren
  * ``-c 1``-slechts één pakket verzenden
  * ``-s 1472``: Stel de grootte van de payload in op 1472 bytes. Dit is de maximale payload-waarde voor een 1500-byte-pakket na de accounting voor de Ethernet-overhead.

  Een geslaagd antwoord ziet er zo uit:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Als de ping mislukt met 1472 bytes, moet u de MSS-verbinding op het VPN configureren om ervoor te zorgen dat het externe systeem de maximale frame grootte correct detecteert. Lees de [documentatie van VPN gateway IPSec/IKE-para meters](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) voor meer informatie.

## <a name="check-for-acl-security-style"></a>Controleren op ACL-beveiligings stijl

Sommige NAS-systemen gebruiken een hybride beveiligings stijl waarmee Acl's (Access Control Lists) worden gecombineerd met de traditionele POSIX-of UNIX-beveiliging.

Als uw systeem de beveiligings stijl rapporteert als UNIX of POSIX zonder het acroniem ' ACL'S ' op te nemen, heeft dit probleem geen invloed op u.

Voor systemen die gebruikmaken van Acl's, moet Azure HPC cache aanvullende gebruikersspecifieke waarden bijhouden om de toegang tot het bestand te beheren. Dit doet u door een toegangs cache in te scha kelen. Er is geen besturings element aan de gebruiker gericht om de toegangs cache in te scha kelen, maar u kunt een ondersteunings ticket openen om aan te vragen dat het wordt ingeschakeld voor de betrokken opslag doelen op uw cache systeem.

## <a name="next-steps"></a>Volgende stappen

Als u een probleem ondervindt dat niet in dit artikel is opgelost, [opent u een ondersteunings ticket](hpc-cache-support-ticket.md) om hulp te krijgen bij de deskundige.
