---
title: Problemen oplossen azure HPC Cache NFS-opslagdoelen
description: Tips om configuratiefouten en andere problemen te voorkomen en op te lossen die kunnen leiden tot fouten bij het maken van een NFS-opslagdoel
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: rohogue
ms.openlocfilehash: c88ffb9e87bc0688cc87b816efaa8e101e23407c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77652085"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Problemen met NAS-configuratie en NFS-opslagdoel oplossen

In dit artikel vindt u oplossingen voor een aantal veelvoorkomende configuratiefouten en andere problemen die kunnen voorkomen dat Azure HPC-cache een NFS-opslagsysteem toevoegt als opslagdoel.

In dit artikel vindt u informatie over het controleren van poorten en hoe u roottoegang tot een NAS-systeem inschakelen. Het bevat ook gedetailleerde informatie over minder voorkomende problemen die ertoe kunnen leiden dat nfs-opslagdoelcreatie mislukt.

> [!TIP]
> Lees voordat u deze handleiding [gebruikt, vereisten voor NFS-opslagdoelen.](hpc-cache-prereqs.md#nfs-storage-requirements)

Als de oplossing voor uw probleem hier niet is opgenomen, opent u [een ondersteuningsticket](hpc-cache-support-ticket.md) zodat Microsoft Service en Support met u kunnen samenwerken om het probleem te onderzoeken en op te lossen.

## <a name="check-port-settings"></a>Poortinstellingen controleren

Azure HPC-cache heeft lees-/schrijftoegang nodig tot verschillende UDP/TCP-poorten op het back-end NAS-opslagsysteem. Zorg ervoor dat deze poorten toegankelijk zijn op het NAS-systeem en ook dat verkeer is toegestaan naar deze poorten via firewalls tussen het opslagsysteem en het subnet cache. Mogelijk moet u samenwerken met firewall- en netwerkbeheerders voor uw datacenter om deze configuratie te verifiëren.

De poorten zijn verschillend voor opslagsystemen van verschillende leveranciers, dus controleer de vereisten van uw systeem bij het instellen van een opslagdoel.

Over het algemeen heeft de cache toegang nodig tot deze poorten:

| Protocol | Poort  | Service  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | gemonteerd   |
| TCP/UDP  | 4047  | status   |

Als u wilt weten welke specifieke poorten ``rpcinfo`` nodig zijn voor uw systeem, gebruikt u de volgende opdracht. Deze opdracht hieronder geeft een overzicht van de poorten en maakt de relevante resultaten op in een tabel. (Gebruik het IP-adres van uw systeem in plaats van de *<storage_IP>* term.)

U deze opdracht uitvoeren vanaf elke Linux-client waarop de NFS-infrastructuur is geïnstalleerd. Als u een client in het clustersubnet gebruikt, kan deze ook helpen bij het verifiëren van de verbinding tussen het subnet en het opslagsysteem.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Zorg ervoor dat alle poorten ``rpcinfo`` die door de query zijn geretourneerd, onbeperkt verkeer toestaan van het subnet van de Azure HPC-cache.

Controleer deze instellingen zowel op de NAS zelf als op alle firewalls tussen het opslagsysteem en het cachesubnet.

## <a name="check-root-access"></a>Hoofdtoegang controleren

Azure HPC-cache heeft toegang nodig tot de export van uw opslagsysteem om het opslagdoel te maken. In het bijzonder, het mounts de export als gebruiker ID 0.

Verschillende opslagsystemen gebruiken verschillende methoden om deze toegang mogelijk te maken:

* Linux-servers voegen ``no_root_squash`` over het algemeen ``/etc/exports``toe aan het geëxporteerde pad in .
* NetApp- en EMC-systemen beheren doorgaans de toegang met exportregels die zijn gekoppeld aan specifieke IP-adressen of netwerken.

Als u exportregels gebruikt, moet u er rekening mee houden dat de cache meerdere verschillende IP-adressen uit het subnet cache kan gebruiken. Toegang toestaan vanuit het volledige scala aan mogelijke subnet IP-adressen.

Werk samen met uw NAS-opslagleverancier om het juiste toegangsniveau voor de cache mogelijk te maken.

### <a name="allow-root-access-on-directory-paths"></a>Roottoegang toestaan op mappaden
<!-- linked in prereqs article -->

Voor NAS-systemen die hiërarchische mappen exporteren, heeft Azure HPC-cache roottoegang nodig tot elk exportniveau.

Een systeem kan bijvoorbeeld drie uitvoer als deze weergeven:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

De ``/ifs/accounting/payroll`` export is ``/ifs/accounting``een ``/ifs/accounting`` kind van , ``/ifs``en is zelf een kind van .

Als u ``payroll`` de export toevoegt als een opslagdoel voor ``/ifs/`` de HPC-cache, wordt de cache vanaf daar daadwerkelijk gemonteerd en toegang tot de salarismap. Azure HPC-cache heeft ``/ifs`` dus roottoegang ``/ifs/accounting/payroll`` nodig om toegang te krijgen tot de export.

Deze vereiste is gerelateerd aan de manier waarop de cache bestanden indexeert en bestandsbotsingen vermijdt, met behulp van bestandsgrepen die het opslagsysteem biedt.

Een NAS-systeem met hiërarchische export kan verschillende bestandsgrepen voor hetzelfde bestand geven als het bestand wordt opgehaald uit verschillende exporten. Een client kan bijvoorbeeld ``/ifs/accounting`` het bestand ``payroll/2011.txt``monteren en openen. Een andere ``/ifs/accounting/payroll`` client monteert ``2011.txt``en opent het bestand. Afhankelijk van hoe het opslagsysteem bestandsgrepen toewijst, kunnen deze twee clients hetzelfde bestand ontvangen met verschillende bestandsgrepen (één voor ``<mount2>/payroll/2011.txt`` en één voor ``<mount3>/2011.txt``).

Het back-endopslagsysteem bewaart interne aliassen voor bestandsgrepen, maar Azure HPC Cache kan niet zien welke bestandsgrepen in de index hetzelfde item verwijzen. Het is dus mogelijk dat de cache verschillende schrijft in de cache kan hebben voor hetzelfde bestand en de wijzigingen onjuist kan toepassen omdat het niet weet dat ze hetzelfde bestand zijn.

Om deze mogelijke bestandsbotsing voor bestanden in meerdere exporten te voorkomen, monteert Azure``/ifs`` HPC Cache automatisch de meest ondieper beschikbare export in het pad (in het voorbeeld) en gebruikt het de bestandsgreep die uit die export wordt gegeven. Als meerdere exporten hetzelfde basispad gebruiken, heeft Azure HPC-cache roottoegang tot dat pad nodig.

## <a name="enable-export-listing"></a>Exportaanbieding inschakelen
<!-- link in prereqs article -->

De NAS moet de export vermelden wanneer de Azure HPC-cache deze opvraagt.

Op de meeste NFS-opslagsystemen u dit testen door de volgende query van een Linux-client te verzenden:``showmount -e <storage IP address>``

Gebruik indien mogelijk een Linux-client uit hetzelfde virtuele netwerk als uw cache.

Als met die opdracht de export niet wordt vermeld, heeft de cache problemen met het maken van verbinding met uw opslagsysteem. Werk samen met uw NAS-leverancier om exportvermelding in te schakelen.

## <a name="adjust-vpn-packet-size-restrictions"></a>Beperkingen voor vpn-pakketgrootte aanpassen
<!-- link in prereqs article -->

Als u een VPN hebt tussen de cache en uw NAS-apparaat, kan de VPN 1500-byte Ethernet-pakketten op ware grootte blokkeren. U dit probleem hebben als grote uitwisselingen tussen de NAS en de instantie Azure HPC-cache niet worden voltooid, maar kleinere updates werken zoals verwacht.

Er is geen eenvoudige manier om te bepalen of uw systeem dit probleem heeft, tenzij u de details van uw VPN-configuratie kent. Hier zijn een paar methoden die u kunnen helpen controleren op dit probleem.

* Gebruik packet sniffers aan beide zijden van de VPN om te detecteren welke pakketten succesvol worden overgedragen.
* Als uw VPN ping-opdrachten toestaat, u het verzenden van een pakket op ware grootte testen.

  Voer met deze opties een ping-opdracht uit via de VPN naar de NAS. (Gebruik het IP-adres van uw opslagsysteem in plaats van de *<storage_IP>* waarde.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Dit zijn de opties in de opdracht:

  * ``-M do``- Niet fragmenteren
  * ``-c 1``- Stuur slechts één pakket
  * ``-s 1472``- Stel de grootte van het laadvermogen in op 1472 bytes. Dit is de maximale grootte payload voor een 1500-byte pakket na de boekhouding voor de Ethernet overhead.

  Een geslaagd antwoord ziet er zo uit:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Als de ping mislukt met 1472 bytes, moet u mogelijk MSS-klemmen op de VPN configureren om het externe systeem de maximale framegrootte goed te laten detecteren. Lees de [documentatie over de parameters van VPN Gateway IPsec/IKE](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) voor meer informatie.

## <a name="check-for-acl-security-style"></a>Controleren op ACL-beveiligingsstijl

Sommige NAS-systemen gebruiken een hybride beveiligingsstijl die toegangscontrolelijsten (ACL's) combineert met traditionele POSIX- of UNIX-beveiliging.

Als uw systeem zijn beveiligingsstijl als UNIX of POSIX rapporteert zonder het acroniem "ACL" op te nemen, heeft dit probleem geen invloed op u.

Voor systemen die ACL's gebruiken, moet Azure HPC-cache aanvullende gebruikersspecifieke waarden bijhouden om de toegang tot bestanden te beheren. Dit wordt gedaan door een toegangscache in te schakelen. Er is geen besturingselement dat door de gebruiker is gericht om de toegangscache in te schakelen, maar u wel een ondersteuningsticket openen om te vragen dat het wordt ingeschakeld voor de getroffen opslagdoelen op uw cachesysteem.

## <a name="next-steps"></a>Volgende stappen

Als je een probleem hebt dat niet is aangepakt in dit artikel, [open je een ondersteuningsticket](hpc-cache-support-ticket.md) om deskundige hulp te krijgen.
