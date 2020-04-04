---
title: Vereisten voor Azure HPC-cache
description: Vereisten voor het gebruik van Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 6da35cb60dc5f22be01ae25393bd62327db64867
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655647"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Vereisten voor Azure HPC-cache

Voordat u de Azure-portal gebruikt om een nieuwe Azure HPC-cache te maken, controleert u of uw omgeving aan deze vereisten voldoet.

## <a name="azure-subscription"></a>Azure-abonnement

Een betaald abonnement wordt aanbevolen.

> [!NOTE]
> Tijdens de eerste maanden van de GA-release moet het Azure HPC-cacheteam uw abonnement toevoegen aan de toegangslijst voordat het kan worden gebruikt om een cache-exemplaar te maken. Deze procedure helpt ervoor te zorgen dat elke klant een hoge kwaliteit responsiviteit krijgt uit zijn caches. Vul [dit formulier](https://aka.ms/onboard-hpc-cache) in om toegang aan te vragen.

## <a name="network-infrastructure"></a>Netwerkinfrastructuur

Er moeten twee vereisten voor het netwerk worden ingesteld voordat u uw cache gebruiken:

* Een speciaal subnet voor het exemplaar Azure HPC-cache
* DNS-ondersteuning zodat de cache toegang heeft tot opslag en andere bronnen

### <a name="cache-subnet"></a>Subnet cache

De Azure HPC-cache heeft een speciaal subnet nodig met deze kwaliteiten:

* Het subnet moet ten minste 64 IP-adressen beschikbaar hebben.
* Het subnet kan geen andere VM's hosten, zelfs niet voor gerelateerde services zoals clientmachines.
* Als u meerdere Azure HPC-cache-exemplaren gebruikt, heeft elk dit-nodig subnet nodig.

De beste praktijk is het maken van een nieuw subnet voor elke cache. U een nieuw virtueel netwerk en subnet maken als onderdeel van het maken van de cache.

### <a name="dns-access"></a>DNS-toegang

De cache heeft DNS nodig om toegang te krijgen tot bronnen buiten het virtuele netwerk. Afhankelijk van de resources die u gebruikt, moet u mogelijk een aangepaste DNS-server instellen en doorsturen tussen die server en Azure DNS-servers configureren:

* Als u toegang wilt krijgen tot azure blob-opslageindpunten en andere interne bronnen, hebt u de dns-server op basis van Azure nodig.
* Als u toegang wilt krijgen tot on-premises opslag, moet u een aangepaste DNS-server configureren die uw hostnamen voor opslagservers kan oplossen.

Als u alleen toegang tot Blob-opslag nodig hebt, u de standaard DNS-server van Azure gebruiken voor uw cache. Als u echter toegang nodig hebt tot andere bronnen, moet u een aangepaste DNS-server maken en deze configureren om aanvragen voor Azure-specifieke resolutie door te sturen naar de Azure DNS-server.

Een eenvoudige DNS-server kan ook worden gebruikt om balansclientverbindingen te laden tussen alle beschikbare cachemountpunten.

Meer informatie over virtuele Azure-netwerken en DNS-serverconfiguraties in [Naamomzetting voor resources in virtuele Azure-netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Machtigingen

Controleer deze vereisten met betrekking tot machtigingen voordat u begint met het maken van uw cache.

* De cache-instantie moet virtuele netwerkinterfaces (NIC's) kunnen maken. De gebruiker die de cache maakt, moet voldoende bevoegdheden in het abonnement hebben om NIC's te maken.

* Als azure HPC-cache blob-opslag gebruikt, heeft het autorisatie nodig om toegang te krijgen tot uw opslagaccount. Gebruik rbac (Role-based access control) om de cache toegang te geven tot uw Blob-opslag. Er zijn twee rollen vereist: opslagaccountinzender en opslagblobgegevensbijdrager.

  Volg de instructies in [Opslagdoelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de rollen toe te voegen.

## <a name="storage-infrastructure"></a>Opslaginfrastructuur

De cache ondersteunt Azure Blob-containers of NFS-hardwareopslagexport. Voeg opslagdoelen toe nadat u de cache hebt gemaakt.

Elk opslagtype heeft specifieke vereisten.

### <a name="blob-storage-requirements"></a>Vereisten voor Blob-opslag

Als u Azure Blob-opslag met uw cache wilt gebruiken, hebt u een compatibel opslagaccount nodig en een lege Blob-container of een container die is gevuld met azure HPC-cache-opgemaakte gegevens zoals beschreven in [Move-gegevens naar Azure Blob-opslag](hpc-cache-ingest.md).

Maak het account voordat u een opslagdoel probeert toe te voegen. U een nieuwe container maken wanneer u het doel toevoegt.

Als u een compatibel opslagaccount wilt maken, gebruikt u de volgende instellingen:

* Prestaties: **Standaard**
* Accountsoort: **StorageV2 (algemeen doel v2)**
* Replicatie: **Lokaal redundante opslag (LRS)**
* Toegangslaag (standaard): **Hot**

Het is een goede gewoonte om een opslagaccount te gebruiken op dezelfde locatie als uw cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

U moet de cachetoepassing ook toegang geven tot uw Azure-opslagaccount zoals hierboven vermeld in [Machtigingen.](#permissions) Volg de procedure in [Opslagdoelen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) om de cache de vereiste toegangsrollen te geven. Als u niet de eigenaar van het opslagaccount bent, laat de eigenaar deze stap doen.

### <a name="nfs-storage-requirements"></a>NFS-opslagvereisten

Als u een NFS-opslagsysteem gebruikt (bijvoorbeeld een on-premises hardware-NAS-systeem), controleert u of het aan deze vereisten voldoet. Mogelijk moet u samenwerken met de netwerkbeheerders of firewallbeheerders voor uw opslagsysteem (of datacenter) om deze instellingen te verifiëren.

> [!NOTE]
> Het maken van opslagdoelmislukt als de cache onvoldoende toegang heeft tot het NFS-opslagsysteem.

Meer informatie is opgenomen in [Problemen met NAS-configuratie en NFS-opslagdoelproblemen](troubleshoot-nas.md)oplossen.

* **Netwerkconnectiviteit:** De Azure HPC-cache heeft netwerktoegang met hoge bandbreedte nodig tussen het subnet cache en het datacenter van het NFS-systeem. [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) of soortgelijke toegang wordt aanbevolen. Als u een VPN gebruikt, moet u deze mogelijk configureren om TCP MSS op 1350 te klemmen om ervoor te zorgen dat grote pakketten niet worden geblokkeerd. Lees [beperkingen voor vpn-pakketgrootte](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) voor extra hulp bij het oplossen van VPN-instellingen.

* **Poorttoegang:** De cache heeft toegang nodig tot specifieke TCP/UDP-poorten op uw opslagsysteem. Verschillende soorten opslag hebben verschillende poortvereisten.

  Volg deze procedure om de instellingen van uw opslagsysteem te controleren.

  * Geef `rpcinfo` een opdracht uit aan uw opslagsysteem om de benodigde poorten te controleren. De opdracht hieronder geeft de poorten en nota ties de relevante resultaten in een tabel. (Gebruik het IP-adres van uw systeem in plaats van de *<storage_IP>* term.)

    U deze opdracht uitvoeren vanaf elke Linux-client waarop de NFS-infrastructuur is geïnstalleerd. Als u een client in het clustersubnet gebruikt, kan deze ook helpen bij het verifiëren van de verbinding tussen het subnet en het opslagsysteem.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Zorg ervoor dat alle poorten ``rpcinfo`` die door de query zijn geretourneerd, onbeperkt verkeer toestaan van het subnet van de Azure HPC-cache.

  * Als u de `rpcinfo` opdracht niet gebruiken, moet u ervoor zorgen dat deze veelgebruikte poorten binnenkomend en uitgaand verkeer toestaan:

    | Protocol | Poort  | Service  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | gemonteerd   |
    | TCP/UDP  | 4047  | status   |

    Sommige systemen gebruiken verschillende poortnummers voor deze services - raadpleeg de documentatie van uw opslagsysteem om zeker te zijn.

  * Controleer firewall-instellingen om er zeker van te zijn dat ze verkeer op al deze vereiste poorten toestaan. Controleer firewalls die worden gebruikt in Azure en on-premises firewalls in uw datacenter.

* **Directorytoegang:** Schakel `showmount` de opdracht in op het opslagsysteem. Azure HPC Cache gebruikt deze opdracht om te controleren of uw opslagdoelconfiguratie wijst op een geldige export, en ook om ervoor te zorgen dat meerdere mounts geen toegang hebben tot dezelfde submappen (een risico voor bestandsbotsing).

  > [!NOTE]
  > Als uw NFS-opslagsysteem het ONTAP 9.2-besturingssysteem van NetApp gebruikt, **schakelt u niet in. `showmount` ** [Neem contact op met Microsoft Service en Ondersteuning](hpc-cache-support-ticket.md) voor hulp.

  Meer informatie over toegang tot directoryvermeldingen vindt u in het artikel over [het oplossen van problemen met het probleemie van NFS-opslagtarget](troubleshoot-nas.md#enable-export-listing).

* **Root-toegang** (lezen/schrijven): de cache maakt verbinding met het back-endsysteem als gebruikers-ID 0. Controleer de volgende instellingen op uw opslagsysteem:
  
  * Inschakelen `no_root_squash`. Deze optie zorgt ervoor dat de externe hoofdgebruiker toegang heeft tot bestanden die eigendom zijn van root.

  * Controleer het exportbeleid om ervoor te zorgen dat er geen beperkingen op worden opgenomen voor roottoegang via het subnet van de cache.

  * Als uw opslag exporten heeft die subdirectories zijn van een andere export, controleert u of de cache roottoegang heeft tot het laagste segment van het pad. Lees [Root-toegang op directorypaden](troubleshoot-nas.md#allow-root-access-on-directory-paths) in het artikel over het oplossen van problemen met nfs-opslagdoel voor meer informatie.

* NFS back-end opslag moet een compatibel hardware/softwareplatform zijn. Neem contact op met het Azure HPC-cacheteam voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure HPC-cache-exemplaar maken](hpc-cache-create.md) vanuit de Azure-portal
