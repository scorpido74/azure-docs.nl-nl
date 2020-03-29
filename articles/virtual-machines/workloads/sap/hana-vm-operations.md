---
title: SAP HANA-infrastructuurconfiguraties en -bewerkingen op Azure | Microsoft Documenten
description: Operations guide voor SAP HANA-systemen die worden geïmplementeerd op virtuele Azure-machines.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 07c8f84f2e37abd87953d8e4cb20b37258b25fda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920468"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configuraties en bewerkingen van SAP HANA-infrastructuur in Azure
Dit document biedt richtlijnen voor het configureren van Azure-infrastructuur en het bedienen van SAP HANA-systemen die zijn geïmplementeerd op Virtuele Azure-servers (VM's). Het document bevat ook configuratie-informatie voor SAP HANA scale-out voor de M128s VM SKU. Dit document is niet bedoeld ter vervanging van de standaard SAP-documentatie, die de volgende inhoud bevat:

- [SAP-beheergids](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP-installatiehandleidingen](https://service.sap.com/instguides)
- [SAP-notities](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Vereisten
Als u deze handleiding wilt gebruiken, hebt u basiskennis van de volgende Azure-componenten nodig:

- [Virtuele machines van Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure-netwerken en virtuele netwerken](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Zie het gedeelte [SAP op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) van de [Azure-documentatie](https://docs.microsoft.com/azure/)voor meer informatie over SAP NetWeaver en andere SAP-componenten op Azure.

## <a name="basic-setup-considerations"></a>Basisoverwegingen voor het instellen
In de volgende secties worden basisoverwegingen beschreven voor het implementeren van SAP HANA-systemen in Azure VM's.

### <a name="connect-into-azure-virtual-machines"></a>Verbinding maken met virtuele Azure-machines
Zoals gedocumenteerd in de [planningshandleiding voor virtuele machines van Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)zijn er twee basismethoden voor verbinding maken met Azure VM's:

- Maak verbinding via het internet en openbare eindpunten op een Jump VM of op de VM waarop SAP HANA wordt uitgevoerd.
- Verbinding maken via een [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) of Azure [ExpressRoute.](https://azure.microsoft.com/services/expressroute/)

Site-to-site connectiviteit via VPN of ExpressRoute is noodzakelijk voor productiescenario's. Dit type verbinding is ook nodig voor niet-productiescenario's die worden uitgevoerd in productiescenario's waarin SAP-software wordt gebruikt. De volgende afbeelding toont een voorbeeld van cross-site connectiviteit:

![Cross-site connectiviteit](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM-typen kiezen
De Azure VM-typen die kunnen worden gebruikt voor productiescenario's worden vermeld in de [SAP-documentatie voor IAAS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) Voor niet-productiescenario's is een breder scala aan native Azure VM-typen beschikbaar.

>[!NOTE]
> Gebruik voor niet-productiescenario's de VM-typen die worden vermeld in de [SAP-notitie #1928533](https://launchpad.support.sap.com/#/notes/1928533). Controleer voor het gebruik van Azure VM's voor productiescenario's op SAP HANA-gecertificeerde VM's in de lijst met door SAP gepubliceerde [gecertificeerde IaaS-platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Implementeer de VM's in Azure met behulp van:

- De Azure-portal.
- Azure PowerShell-cmdlets.
- De Azure CLI.

U ook een volledig geïnstalleerd SAP HANA-platform implementeren op de Azure VM-services via het [SAP Cloud-platform.](https://cal.sap.com/) Het installatieproces wordt beschreven in [Deploy SAP S/4HANA of BW/4HANA op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) of met de [hier](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)vrijgegeven automatisering.

>[!IMPORTANT]
> Als u M208xx_v2 VM's wilt gebruiken, moet u voorzichtig zijn met het selecteren van uw Linux-afbeelding in de Azure VM-afbeeldingsgalerie. Om de details te lezen, lees het artikel [Geheugen geoptimaliseerdvirtuele machine maten](../../mv2-series.md).
> 


### <a name="storage-configuration-for-sap-hana"></a>Opslagconfiguratie voor SAP HANA
Lees het document [SAP HANA Azure-opslagconfiguraties](./hana-vm-operations-storage.md) voor opslagconfiguraties en opslagtypen die met SAP HANA in Azure moeten worden gebruikt


### <a name="set-up-azure-virtual-networks"></a>Virtuele Azure-netwerken instellen
Wanneer u site-to-site-connectiviteit in Azure hebt via VPN of ExpressRoute, moet u ten minste één virtueel Azure-netwerk hebben dat via een virtuele gateway is verbonden met het VPN- of ExpressRoute-circuit. In eenvoudige implementaties kan de Virtuele Gateway worden geïmplementeerd in een subnet van het Virtuele Azure-netwerk (VNet) dat ook de SAP HANA-exemplaren host. Als u SAP HANA wilt installeren, maakt u twee extra subnetten binnen het virtuele Azure-netwerk. In één subnet worden de VM's weergeven om de SAP HANA-exemplaren uit te voeren. Het andere subnet draait Jumpbox of Management VM's om SAP HANA Studio, andere beheersoftware of uw applicatiesoftware te hosten.

> [!IMPORTANT]
> Uit functionaliteit, maar belangrijker uit prestatieredenen, wordt het niet ondersteund om [Virtuele Azure Network-apparaten](https://azure.microsoft.com/solutions/network-appliances/) te configureren in het communicatiepad tussen de SAP-toepassing en de DBMS-laag van een SAP NetWeaver- of S/4HANA-gebaseerd SAP-systeem. De communicatie tussen de SAP-toepassingslaag en de DBMS-laag moet direct zijn. De beperking omvat geen [Azure ASG- en NSG-regels](https://docs.microsoft.com/azure/virtual-network/security-overview) zolang deze ASG- en NSG-regels een directe communicatie toestaan. Andere scenario's waarin NVA's niet worden ondersteund, bevinden zich in communicatiepaden tussen Azure VM's die Linux Pacemaker-clusterknooppunten en SBD-apparaten vertegenwoordigen, zoals beschreven in [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) Of in communicatiepaden tussen Azure VM's en Windows Server SOFS die zijn ingesteld zoals beschreven in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een bestandsshare in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). NVA's in communicatiepaden kunnen de netwerklatentie tussen twee communicatiepartners eenvoudig verdubbelen, kan de doorvoer beperken in kritieke paden tussen de SAP-toepassingslaag en de DBMS-laag. In sommige scenario's die bij klanten worden waargenomen, kunnen NA's ervoor zorgen dat Pacemaker Linux-clusters mislukken in gevallen waarin communicatie tussen de Linux Pacemaker-clusterknooppunten via een NVA met hun SBD-apparaat moet communiceren.  
> 

> [!IMPORTANT]
> Een ander ontwerp dat **niet** wordt ondersteund, is de scheiding van de SAP-toepassingslaag en de DBMS-laag in verschillende virtuele Azure-netwerken die niet met elkaar worden [gekeken.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Het wordt aanbevolen om de SAP-toepassingslaag en de DBMS-laag te scheiden met subnetten binnen een virtueel Azure-netwerk in plaats van verschillende virtuele Azure-netwerken te gebruiken. Als u besluit de aanbeveling niet te volgen en in plaats daarvan de twee lagen in verschillende virtuele netwerken te scheiden, moeten de twee virtuele netwerken worden [peered.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Houd er rekening mee dat netwerkverkeer tussen twee [gepeerde](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure-virtuele netwerken onderhevig is aan overdrachtskosten. Met het enorme gegevensvolume in veel Terabytes die tussen de SAP-toepassingslaag en de DBMS-laag worden uitgewisseld, kunnen aanzienlijke kosten worden verzameld als de SAP-toepassingslaag en dbms-laag worden gescheiden tussen twee peered Azure virtuele netwerken. 

Wanneer u de VM's installeert om SAP HANA uit te voeren, hebben de VM's het:

- Twee virtuele NIC's geïnstalleerd: een NIC om verbinding te maken met het beheersubnet en één NIC om verbinding te maken vanuit het on-premises netwerk of andere netwerken, met de SAP HANA-instantie in de Azure VM.
- Statische privé-IP-adressen die zijn geïmplementeerd voor beide virtuele NIC's.

> [!NOTE]
> U moet statische IP-adressen via Azure-middelen toewijzen aan afzonderlijke vNIC's. U mag geen statische IP-adressen binnen het gastbesturingssysteem toewijzen aan een vNIC. Sommige Azure-services zoals Azure Backup Service zijn afhankelijk van het feit dat ten minste de primaire vNIC is ingesteld op DHCP en niet op statische IP-adressen. Zie ook het document [Problemen met azure virtual machine backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Als u meerdere statische IP-adressen aan een vm moet toewijzen, moet u meerdere vNIC's aan een vm toewijzen.
>
>

Voor implementaties die duurzaam zijn, moet u echter een virtuele datacenternetwerkarchitectuur maken in Azure. Deze architectuur beveelt de scheiding aan van de Azure VNet-gateway die verbinding maakt met on-premises in een afzonderlijk Azure VNet. Deze afzonderlijke VNet moet hosten al het verkeer dat overlaat aan on-premises of naar het internet. Met deze aanpak u software implementeren voor het controleren en registreren van verkeer dat het virtuele datacenter in Azure binnenkomt in deze afzonderlijke hub VNet. U hebt dus één VNet dat alle software en configuraties host die betrekking hebben op in- en uitgaande verkeer van uw Azure-implementatie.

De artikelen [Azure Virtual Datacenter: A Network Perspective](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) en Azure Virtual Datacenter en het Enterprise Control [Plane](https://docs.microsoft.com/azure/architecture/vdc/) geven meer informatie over de aanpak van virtuele datacenters en het bijbehorende Azure VNet-ontwerp.


>[!NOTE]
>Verkeer dat tussen een hub VNet en spoke VNet met [Azure VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) stroomt, is onderhevig aan extra [kosten](https://azure.microsoft.com/pricing/details/virtual-network/). Op basis van deze kosten moet u mogelijk overwegen compromissen te sluiten tussen het uitvoeren van een strikt hub- en spaaknetwerkontwerp en het uitvoeren van meerdere [Azure ExpressRoute-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) die u met 'spaken' verbindt om VNet-peering te omzeilen. Azure ExpressRoute Gateways brengen echter ook extra [kosten](https://azure.microsoft.com/pricing/details/vpn-gateway/) met zich mee. U ook extra kosten ondervinden voor software van derden die u gebruikt voor het registreren, controleren en controleren van netwerkverkeer. Afhankelijk van de kosten voor gegevensuitwisseling via VNet-peering aan de ene kant en de kosten die worden gemaakt door extra Azure ExpressRoute-gateways en aanvullende softwarelicenties, u kiezen voor microsegmentatie binnen één VNet door subnetten als isolatie-eenheid te gebruiken in plaats van VNets.


Zie [IP-adrestypen en toewijzingsmethoden in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)voor een overzicht van de verschillende methoden voor het toewijzen van IP-adressen. 

Voor VM's met SAP HANA moet u werken met statische IP-adressen toegewezen. Reden is dat sommige configuratiekenmerken voor HANA-referentie-IP-adressen.

[Azure Network Security Groups (NSG's)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) worden gebruikt om verkeer te leiden dat wordt doorgestuurd naar het SAP HANA-exemplaar of de jumpbox. De NSG's en uiteindelijk [Application Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) zijn gekoppeld aan het SAP HANA subnet en het Management subnet.

De volgende afbeelding toont een overzicht van een ruw implementatieschema voor SAP HANA volgens een hub en spaak VNet-architectuur:

![Ruw implementatieschema voor SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Als u SAP HANA in Azure wilt implementeren zonder een site-to-site-verbinding, wilt u het SAP HANA-exemplaar nog steeds van het openbare internet beschermen en verbergen achter een forward proxy. In dit basisscenario is de implementatie afhankelijk van inAzure ingebouwde DNS-services om hostnamen op te lossen. In een complexere implementatie waar openbare IP-adressen worden gebruikt, zijn Azure ingebouwde DNS-services bijzonder belangrijk. Gebruik Azure NSGs en [Azure NVA's](https://azure.microsoft.com/solutions/network-appliances/) om de routering van het internet naar uw Azure VNet-architectuur in Azure te beheren. De volgende afbeelding toont een ruw schema voor het implementeren van SAP HANA zonder een site-to-site-verbinding in een hub en spoke VNet-architectuur:
  
![Ruw implementatieschema voor SAP HANA zonder site-to-site-verbinding](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Een andere beschrijving over het gebruik van Azure NVA's om toegang vanaf internet te beheren en te controleren zonder de hub- en spaak-VNet-architectuur is te vinden in het artikel [Deploy highly available network virtual appliances](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Azure-infrastructuur configureren voor SAP HANA-scale-out
Als u de Azure VM-typen wilt achterhalen die zijn gecertificeerd voor olap-scale-out of S/4HANA-scale-out, raadpleegt u de [SAP HANA-hardwaremap.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Een vinkje in de kolom 'Clustering' geeft ondersteuning voor scale-out aan. Toepassingstype geeft aan of OLAP scale-out of S/4HANA scale-out wordt ondersteund. Voor meer informatie over knooppunten die zijn gecertificeerd in scale-out voor elk van de VM's, raadpleegt u de details van de vermeldingen in de specifieke VM SKU die wordt vermeld in de SAP HANA-hardwaremap.

De minimale OS-releases voor het implementeren van scale-outconfiguraties in Azure VM's, controleer de details van de vermeldingen in de specifieke VM SKU die wordt vermeld in de SAP HANA-hardwaremap. Van een n-node OLAP scale-out configuratie, een knooppunt functioneert als master node. De andere knooppunten tot aan de limiet van de certificering fungeren als werkknooppunt. Extra stand-byknooppunten tellen niet mee in het aantal gecertificeerde knooppunten

>[!NOTE]
> Azure VM scale-out implementaties van SAP HANA met stand-by node zijn alleen mogelijk met behulp van de [Azure NetApp](https://azure.microsoft.com/services/netapp/) Files-opslag. Geen enkele andere SAP HANA-gecertificeerde Azure-opslag maakt de configuratie van SAP HANA-stand-byknooppunten mogelijk
>

Voor /hana/shared raden we ook het gebruik van [Azure NetApp-bestanden aan.](https://azure.microsoft.com/services/netapp/) 

Een typisch basisontwerp voor één knooppunt in een schaal-outconfiguratie gaat eruit zien als:

![Scale-out basisprincipes van één knooppunt](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

De basisconfiguratie van een VM-knooppunt voor SAP HANA scale-out ziet eruit als volgt:

- Voor **/hana/shared**maakt u gebruik van de native NFS-service die wordt geleverd via Azure NetApp Files. 
- Alle andere schijfvolumes worden niet gedeeld tussen de verschillende knooppunten en zijn niet gebaseerd op NFS. Installatieconfiguraties en -stappen voor scale-out HANA-installaties met niet-gedeelde **/hana/gegevens** en **/hana/log** worden later in dit document verder weergegeven. Voor HANA-gecertificeerde opslag die kan worden gebruikt, raadpleegt u de configuratie van de configuratie van de [sap HANA Azure-opslagvoor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).


Als u de volumes of schijven wijzigt, moet u de vereisten voor SAP [HANA TDI-opslag](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)controleren, voor de vereiste grootte, afhankelijk van het aantal werknemersknooppunten. In het document wordt een formule vrijgegeven die u moet toepassen om de vereiste capaciteit van het volume te krijgen

De andere ontwerpcriteria die worden weergegeven in de afbeeldingen van de configuratie met één knooppunt voor een scale-out SAP HANA VM is de VNet, of beter de subnetconfiguratie. SAP beveelt ten zeerste een scheiding aan van de client/applicatie die wordt geconfronteerd met verkeer van de communicatie tussen de HANA-knooppunten. Zoals in de graphics wordt weergegeven, wordt dit doel bereikt door twee verschillende vNIC's aan de VM te koppelen. Beide vNIC's zijn in verschillende subnetten, hebben twee verschillende IP-adressen. Vervolgens controleert u de verkeersstroom met routeringsregels met behulp van NSGs of door de gebruiker gedefinieerde routes.

Met name in Azure zijn er geen middelen en methoden om de kwaliteit van de service en quota af te dwingen op specifieke vNIC's. Als gevolg hiervan biedt de scheiding van client/applicatie en intra-node communicatie geen mogelijkheden om de ene verkeersstroom boven de andere prioriteit te geven. In plaats daarvan blijft de scheiding een zekere mate van beveiliging bij het afschermen van de intra-node communicatie van de scale-out configuraties.  

>[!NOTE]
>SAP raadt aan om netwerkverkeer te scheiden naar de client/applicatiekant en het intra-nodeverkeer zoals beschreven in dit document. Daarom wordt het aan te raden om een architectuur op zijn plaats te zetten, zoals in de laatste afbeeldingen wordt weergegeven. Raadpleeg ook uw beveiligings- en complianceteam voor vereisten die afwijken van de aanbeveling 
>

Vanuit netwerkoogpunt zou de minimaal vereiste netwerkarchitectuur eruit zien als:

![Scale-out basisprincipes van één knooppunt](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>SAP HANA scale-out n Azure installeren
Als u een scale-out SAP-configuratie installeert, moet u ruwe stappen uitvoeren van:

- Nieuwe implementeren of een bestaande Azure VNet-infrastructuur aanpassen
- De nieuwe VM's implementeren met Azure Managed Premium Storage, Ultra-schijfvolumes en/of NFS-volumes op basis van ANF
- - Pas de netwerkroutering aan om ervoor te zorgen dat bijvoorbeeld de intra-nodecommunicatie tussen VM's niet via een [NVA](https://azure.microsoft.com/solutions/network-appliances/)wordt geleid. 
- Installeer het SAP HANA master node.
- Configuratieparameters van het SAP HANA-hoofdknooppunt aanpassen
- Doorgaan met de installatie van de SAP HANA-werkknooppunten

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installatie van SAP HANA in scale-out configuratie
Aangezien uw Azure VM-infrastructuur is geïmplementeerd en alle andere voorbereidingen zijn uitgevoerd, moet u de SAP HANA-scale-outconfiguraties in deze stappen installeren:

- Installeer het SAP HANA master node volgens de documentatie van SAP
- In het geval van het gebruik van Azure Premium Storage of Ultra schijfopslag met niet-gedeelde schijven van /hana/data en /hana/log, moet u het global.ini-bestand wijzigen en de parameter 'basepath_shared = nee' toevoegen aan het global.ini-bestand. Met deze parameter kan SAP HANA in scale-out worden uitgevoerd zonder 'gedeelde' **/hana/data** en **/hana/log** volumes tussen de knooppunten. Details zijn gedocumenteerd in [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991). Als u NFS-volumes gebruikt op basis van ANF voor /hana/data en /hana/log, hoeft u deze wijziging niet aan te brengen
- Na de uiteindelijke wijziging in de parameter global.ini start u het SAP HANA-exemplaar opnieuw
- Voeg extra werkknooppunten toe. Zie <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>ook . Geef het interne netwerk voor SAP HANA internode communicatie tijdens de installatie of daarna met behulp van, bijvoorbeeld, de lokale hdblcm. Zie ook [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363)voor meer gedetailleerde documentatie. 

Details voor het instellen van een SAP HANA-scale-outsysteem met stand-by node op SUSE Linux worden in detail beschreven in [Het implementeren van een SAP HANA-scale-outsysteem met stand-by node op Azure VM's met behulp van Azure NetApp-bestanden op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse). Gelijkwaardige documentatie voor Red Hat is te vinden in het artikel [Implementeer een SAP HANA scale-out systeem met stand-by node op Azure VM's met behulp van Azure NetApp Files op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 voor virtuele Azure-machines

Naast de SAP HANA-certificeringen op Azure M-serie VM's wordt SAP HANA Dynamic Tiering 2.0 ook ondersteund op Microsoft Azure (zie SAP HANA Dynamic Tiering-documentatiekoppelingen verder naar beneden). Hoewel er geen verschil is in het installeren of bedienen van het product, bijvoorbeeld via SAP HANA Cockpit in een Azure Virtual Machine, zijn er een paar belangrijke items, die verplicht zijn voor officiële ondersteuning op Azure. Deze belangrijke punten worden hieronder beschreven. In het hele artikel wordt de afkorting "DT 2.0" gebruikt in plaats van de volledige naam Dynamic Tiering 2.0.

SAP HANA Dynamic Tiering 2.0 wordt niet ondersteund door SAP BW of S4HANA. Belangrijkste use cases op dit moment zijn native HANA-toepassingen.


### <a name="overview"></a>Overzicht

De onderstaande afbeelding geeft een overzicht met betrekking tot DT 2.0-ondersteuning op Microsoft Azure. Er is een reeks verplichte eisen, die moeten worden gevolgd om te voldoen aan de officiële certificering:

- DT 2.0 moet worden geïnstalleerd op een speciale Azure VM. Het kan niet draaien op dezelfde VM waar SAP HANA draait
- SAP HANA- en DT 2.0 VM's moeten worden geïmplementeerd binnen hetzelfde Azure Vnet
- De SAP HANA- en DT 2.0 VM's moeten worden geïmplementeerd met Azure-versnelde netwerknetwerken
- Opslagtype voor de DT 2.0 VM's moet Azure Premium Storage zijn
- Meerdere Azure-schijven moeten zijn gekoppeld aan de DT 2.0 VM
- Het is vereist om een software raid / gestreept volume te maken (via lvm of mdadm) met behulp van striping over de Azure-schijven

Meer details zullen worden uitgelegd in de volgende secties.

![SAP HANA DT 2.0 ArchitectuurOverzicht](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>Dedicated Azure VM voor SAP HANA DT 2.0

Op Azure IaaS wordt DT 2.0 alleen ondersteund op een speciale VM. Het is niet toegestaan om DT 2.0 uit te voeren op dezelfde Azure VM waar het HANA-exemplaar wordt uitgevoerd. In eerste instantie kunnen twee VM-typen worden gebruikt om SAP HANA DT 2.0 uit te voeren:

- M64-32ms 
- E32sv3 

Zie VM-typebeschrijving [hier](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Gezien het basisidee van DT 2.0, dat gaat over het ontladen van "warme" gegevens om kosten te besparen is het zinvol om overeenkomstige VM-formaten te gebruiken. Er is echter geen strikte regel met betrekking tot de mogelijke combinaties. Het hangt af van de specifieke klantworkload.

Aanbevolen configuraties zou zijn:

| SAP HANA VM-type | Vm-type DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128's | M64-32ms |
| M64ms | E32sv3 |
| M64's | E32sv3 |


Alle combinaties van SAP HANA-gecertificeerde M-series VM's met ondersteunde DT 2.0 VM's (M64-32ms en E32sv3) zijn mogelijk.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure-netwerken en SAP HANA DT 2.0

Voor het installeren van DT 2.0 op een speciale VM is een netwerkdoorvoer nodig tussen de DT 2.0 VM en de SAP HANA VM van minimaal 10 Gb. Daarom is het verplicht om alle VM's binnen hetzelfde Azure Vnet te plaatsen en Azure versnelde netwerken in te schakelen.

Bekijk [hier](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) meer informatie over azure-versnelde netwerken

### <a name="vm-storage-for-sap-hana-dt-20"></a>VM-opslag voor SAP HANA DT 2.0

Volgens dt 2.0 best practice richtlijnen, de schijf IO doorvoer moet minimaal 50 MB / sec per fysieke kern. Kijkend naar de specificaties voor de twee Azure VM-typen, die worden ondersteund voor DT 2.0, ziet de maximale schijf-IO-doorvoerlimiet voor de VM eruit:

- E32sv3 : 768 MB/sec (niet in de cache opgeslagen) wat een verhouding van 48 MB/sec per fysieke kern betekent
- M64-32ms : 1000 MB/sec (uncached) wat een verhouding van 62,5 MB/sec per fysieke kern betekent

Het is vereist om meerdere Azure-schijven aan de DT 2.0 VM te koppelen en een softwareraid (striping) op OS-niveau te maken om de maximale limiet van schijfdoorvoer per VM te bereiken. Eén Azure-schijf kan in dit opzicht niet de doorvoer bieden om de maximale VM-limiet te bereiken. Azure Premium-opslag is verplicht om DT 2.0 uit te voeren. 

- Details over beschikbare Azure-schijftypen vindt u [hier](../../windows/disks-types.md)
- Details over het maken van software raid via mdadm vindt u [hier](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Details over het configureren van LVM om een gestreept volume te maken voor maximale doorvoer vindt u [hier](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

Afhankelijk van de groottevereisten zijn er verschillende opties om de maximale doorvoer van een VM te bereiken. Hier zijn mogelijke gegevensvolumeschijfconfiguraties voor elk DT 2.0 VM-type om de bovenste VM-doorvoerlimiet te bereiken. De E32sv3 VM moet worden beschouwd als een instapniveau voor kleinere workloads. In het geval dat het moet blijken dat het niet snel genoeg is, kan het nodig zijn om het formaat van de VM te wijzigen naar M64-32ms.
Aangezien de M64-32ms VM veel geheugen heeft, kan de IO-belasting de limiet niet bereiken, vooral voor leesintensieve workloads. Daarom kunnen minder schijven in de streepset voldoende zijn, afhankelijk van de specifieke werkbelasting van de klant. Maar om aan de veilige kant van de schijf configuraties hieronder werden gekozen om de maximale doorvoer te garanderen:


| VM-SKU | Schijfconfig 1 | Schijfconfig 2 | Schijfconfig 3 | Schijfconfig 4 | Schijfconfig 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Vooral in het geval dat de werkbelasting is read-intense het io prestaties kan stimuleren om Azure host cache in te schakelen "read-only" zoals aanbevolen voor de gegevensvolumes van database software. Terwijl voor het transactielogboek Azure host disk cache moet worden "geen". 

Met betrekking tot de grootte van het logvolume is een aanbevolen uitgangspunt een heuristisch van 15% van de gegevensgrootte. Het maken van het logboekvolume kan worden uitgevoerd door verschillende Azure-schijftypen te gebruiken, afhankelijk van de kosten en doorvoervereisten. Voor het logvolume is een hoge I/O-doorvoer vereist.  In het geval van het gebruik van het VM-type M64-32ms is het verplicht om [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)in te schakelen. Azure Write Accelerator biedt optimale vertraging voor het schrijven van schijven voor het transactielogboek (alleen beschikbaar voor M-reeksen). Er zijn een aantal items te overwegen maar als het maximum aantal schijven per VM-type. Details over Write Accelerator vindt u [hier](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)


Hier volgen een paar voorbeelden over het aanpassen van het logboekvolume:

| grootte van het gegevensvolume en schijftype | logvolume en schijftype config 1 | logvolume en schijftype config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Net als bij SAP HANA scale-out moet de /hana/shared directory worden gedeeld tussen de SAP HANA VM en de DT 2.0 VM. Dezelfde architectuur als voor SAP HANA scale-out met behulp van speciale VM's, die fungeren als een zeer beschikbare NFS-server wordt aanbevolen. Om een gedeeld back-upvolume te bieden, kan het identieke ontwerp worden gebruikt. Maar het is aan de klant als HA nodig zou zijn of als het voldoende is om gewoon gebruik maken van een speciale VM met voldoende opslagcapaciteit om op te treden als een back-up server.



### <a name="links-to-dt-20-documentation"></a>Links naar DT 2.0-documentatie 

- [SAP HANA Dynamic Tiering installatie- en updatehandleiding](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Zelfstudies en bronnen voor SAP HANA Dynamic Tiering](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamic Tiering PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamische tiering verbeteringen](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Bewerkingen voor het implementeren van SAP HANA op Azure VM's
In de volgende secties worden enkele bewerkingen beschreven die betrekking hebben op het implementeren van SAP HANA-systemen op Azure VM's.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Back-ups maken en bewerkingen herstellen op Azure VM's
In de volgende documenten wordt beschreven hoe u een back-up maken en uw SAP HANA-implementatie herstellen:

- [Overzicht van back-ups van SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA back-up op bestandsniveau](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA storage snapshot benchmark](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>VM's met SAP HANA starten en opnieuw starten
Een opvallend kenmerk van de Azure public cloud is dat u alleen wordt opgeladen voor uw computerminuten. Wanneer u bijvoorbeeld een vm afsluit waarop SAP HANA wordt uitgevoerd, worden de opslagkosten gedurende die periode alleen gefactureerd. Een andere functie is beschikbaar wanneer u statische IP-adressen voor uw VM's opgeeft in uw eerste implementatie. Wanneer u een VM met SAP HANA opnieuw start, wordt de VM opnieuw opgestart met de eerdere IP-adressen. 


### <a name="use-saprouter-for-sap-remote-support"></a>SAProuter gebruiken voor ondersteuning op afstand van SAP
Als u een site-to-site-verbinding hebt tussen uw on-premises locaties en Azure en als u SAP-componenten gebruikt, hebt u waarschijnlijk al een SAProuter. Voer in dit geval de volgende items in voor externe ondersteuning:

- Behoud het privé- en statische IP-adres van de VM waarmee SAP HANA wordt host in de Configuratie van de SAProuter.
- Configureer de NSG van het subnet dat de HANA VM host om verkeer via TCP/IP-poort 3299 toe te staan.

Als u verbinding maakt met Azure via internet en u hebt geen SAP-router voor de VM met SAP HANA, moet u het onderdeel installeren. Installeer SAProuter in een aparte VM in het subnet Beheer. De volgende afbeelding toont een ruw schema voor het implementeren van SAP HANA zonder site-to-site-verbinding en met SAProuter:

![Ruw implementatieschema voor SAP HANA zonder site-to-site-verbinding en SAP-router](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Zorg ervoor dat u SAProuter installeert in een aparte VM en niet in uw Jumpbox VM. De afzonderlijke vm moet een statisch IP-adres hebben. Neem contact op met SAP voor een IP-adres om uw SAProuter aan te sluiten op de SAProuter die wordt gehost door SAP. (De SAProuter die wordt gehost door SAP is de tegenhanger van de SAProuter-instantie die u op uw VM installeert.) Gebruik het IP-adres van SAP om uw SAProuter-exemplaar te configureren. In de configuratie-instellingen is de enige benodigde poort TCP-poort 3299.

Zie de [SAP-documentatie](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)voor meer informatie over het instellen en onderhouden van externe ondersteuningsverbindingen via SAProuter.

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Hoge beschikbaarheid met SAP HANA op Azure native VM's
Als u SUSE Linux Enterprise Server of Red Hat gebruikt, u een Pacemaker-cluster instellen met STONITH-apparaten. U de apparaten gebruiken om een SAP HANA-configuratie in te stellen die synchrone replicatie gebruikt met HANA-systeemreplicatie en automatische failover. Voor meer informatie in de sectie 'volgende stappen'.

## <a name="next-steps"></a>Volgende stappen
Vertrouwd raken met de genoemde artikelen
- [Configuraties van SAP HANA in virtuele Azure-machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Hoge beschikbaarheid van SAP HANA op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Hoge beschikbaarheid van SAP HANA op Azure VM's op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

