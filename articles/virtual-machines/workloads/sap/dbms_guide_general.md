---
title: Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload | Microsoft Documenten
description: Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101369"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Deze handleiding maakt deel uit van de documentatie over het implementeren en implementeren van SAP-software op Microsoft Azure. Voordat u deze handleiding leest, leest u de [plannings- en implementatiehandleiding.][planning-guide] Dit document behandelt de algemene implementatieaspecten van SAP-gerelateerde DBMS-systemen op virtuele Microsoft Azure-machines (VM's) door de Azure-infrastructuur als service (IaaS)-mogelijkheden te gebruiken.

Het document vormt een aanvulling op de SAP-installatiedocumentatie en SAP Notes, die de belangrijkste bronnen vertegenwoordigen voor installaties en implementaties van SAP-software op bepaalde platforms.

In dit document worden overwegingen van het uitvoeren van SAP-gerelateerde DBMS-systemen in Azure VM's geïntroduceerd. Er zijn weinig verwijzingen naar specifieke DBMS-systemen in dit hoofdstuk. In plaats daarvan worden de specifieke DBMS-systemen na dit document in dit document behandeld.

## <a name="definitions"></a>Definities
In het hele document worden deze termen gebruikt:

* **IaaS**: Infrastructure as a service.
* **PaaS**: Platform as a service.
* **SaaS**: Software as a service.
* **SAP-component**: Een afzonderlijke SAP-toepassing zoals ERP Central Component (ECC), Business Warehouse (BW), Solution Manager of Enterprise Portal (EP). SAP-componenten kunnen worden gebaseerd op traditionele ABAP- of Java-technologieën of op een niet-NetWeaver-toepassing, zoals Bedrijfsobjecten.
* **SAP-omgeving**: Een of meer SAP-componenten zijn logisch gegroepeerd om een bedrijfsfunctie uit te voeren, zoals ontwikkeling, kwaliteitsborging, training, disaster recovery of productie.
* **SAP-landschap**: Deze term verwijst naar de volledige SAP-assets in het IT-landschap van een klant. Het SAP-landschap omvat alle productie- en niet-productieomgevingen.
* **SAP-systeem**: De combinatie van een DBMS-laag en een toepassingslaag van bijvoorbeeld een SAP ERP-ontwikkelingssysteem, een SAP Business Warehouse-testsysteem of een SAP CRM-productiesysteem. In Azure-implementaties wordt deze twee lagen niet ondersteund tussen on-premises en Azure. Als gevolg hiervan wordt een SAP-systeem on-premises geïmplementeerd of wordt het geïmplementeerd in Azure. U de verschillende systemen van een SAP-landschap implementeren in Azure of on-premises. U bijvoorbeeld de SAP CRM-ontwikkelings- en testsystemen implementeren in Azure, maar het SAP CRM-productiesysteem on-premises implementeren.
* **Cross-premises**: beschrijft een scenario waarbij VM's worden geïmplementeerd op een Azure-abonnement met site-to-site-, multisite- of Azure ExpressRoute-connectiviteit tussen de on-premises datacenters en Azure. In veelvoorkomende Azure-documentatie worden dit soort implementaties ook beschreven als cross-premises scenario's. 

    De reden voor de verbinding is om on-premises domeinen, on-premises Active Directory en on-premises DNS uit te breiden naar Azure. Het on-premises landschap wordt uitgebreid naar de Azure-elementen van het abonnement. Met deze extensie kunnen de VM's deel uitmaken van het on-premises domein. Domeingebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op die VM's, zoals DBMS-services. Communicatie en naamoplossing tussen VM's die on-premises zijn geïmplementeerd en VM's die in Azure zijn geïmplementeerd, is mogelijk. Dit scenario is het meest voorkomende scenario dat wordt gebruikt om SAP-assets op Azure te implementeren. Zie [Plannen en ontwerpen voor VPN-gateway voor](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)meer informatie.

> [!NOTE]
> Cross-premises implementaties van SAP-systemen zijn de plaats waar Azure virtuele machines die SAP-systemen uitvoeren lid zijn van een on-premises domein en worden ondersteund voor productie SAP-systemen. Cross-premises configuraties worden ondersteund voor het implementeren van onderdelen of het voltooien van SAP-landschappen in Azure. Voor het uitvoeren van het volledige SAP-landschap in Azure moeten deze VM's deel uitmaken van een on-premises domein en Active Directory/LDAP. 
>
> In eerdere versies van de documentatie werden hybride-IT-scenario's genoemd. De term *hybride* is geworteld in het feit dat er een cross-premises connectiviteit is tussen on-premises en Azure. In dit geval betekent hybride ook dat de VM's in Azure deel uitmaken van de on-premises Active Directory.
>
>

Sommige Microsoft-documentatie beschrijft cross-premises scenario's een beetje anders, vooral voor DBMS hoge beschikbaarheid configuraties. In het geval van de SAP-gerelateerde documenten komt het cross-premises scenario neer op site-to-site of private [ExpressRoute-connectiviteit](https://azure.microsoft.com/services/expressroute/) en een SAP-landschap dat is verdeeld tussen on-premises en Azure.

## <a name="resources"></a>Resources
Er zijn andere artikelen beschikbaar over SAP-werkbelasting op Azure. Begin met [SAP-werkbelasting op Azure: ga aan de slag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) en kies vervolgens uw interessegebied.

De volgende SAP-notities zijn gerelateerd aan SAP op Azure met betrekking tot het gebied dat in dit document wordt behandeld.

| Notitienummer | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Probleemoplossing voor verbeterde Azure-bewaking voor SAP |
| [2178632] |Belangrijkste monitoringstatistieken voor SAP op Microsoft Azure |
| [1409604] |Virtualisatie op Windows: verbeterde bewaking |
| [2191498] |SAP op Linux met Azure: verbeterde monitoring |
| [2039619] |SAP-toepassingen op Microsoft Azure met behulp van de Oracle-database: ondersteunde producten en versies |
| [2233094] |DB6: SAP-toepassingen op Azure met IBM DB2 voor Linux, UNIX en Windows: aanvullende informatie |
| [2243692] |Linux op Microsoft Azure (IaaS) VM: SAP-licentieproblemen |
| [1984787] |SUSE LINUX Enterprise Server 12: Installatienotities |
| [2002167] |Red Hat Enterprise Linux 7.x: Installatie en upgrade |
| [2069760] |Oracle Linux 7.x SAP installatie en upgrade |
| [1597355] |Swap-space aanbeveling voor Linux |
| [2171857] |Oracle Database 12c: Ondersteuning voor bestandssystemen op Linux |
| [1114181] |Oracle Database 11g: Ondersteuning voor bestandssystemen op Linux |


Voor informatie over alle SAP Notes voor Linux, zie de [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

U hebt een werkende kennis van de Microsoft Azure-architectuur nodig en hoe virtuele Microsoft Azure-machines worden geïmplementeerd en beheerd. Zie [Azure-documentatie](https://docs.microsoft.com/azure/)voor meer informatie.

In het algemeen, de Windows, Linux, en DBMS installatie en configuratie zijn in wezen hetzelfde als elke virtuele machine of bare metal machine die u installeert on-premises. Er zijn enkele implementatiebeslissingen over architectuur en systeembeheer die anders zijn wanneer u Azure IaaS gebruikt. In dit document worden de specifieke verschillen in architectuur en systeembeheer uitgelegd waarop u moet worden voorbereid wanneer u Azure IaaS gebruikt.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Opslagstructuur van een VM voor RDBMS-implementaties
Lees en begrijp de informatie in [dit hoofdstuk][deployment-guide-3] van de [implementatiehandleiding][deployment-guide]om dit hoofdstuk te volgen. U moet de verschillende VM-series en de verschillen tussen standaard- en premiumopslag begrijpen en kennen voordat u dit hoofdstuk leest. 

Zie voor meer informatie over Azure Storage voor Azure VM's:

- [Inleiding tot beheerde schijven voor Azure Windows VM's](../../windows/managed-disks-overview.md).
- [Inleiding tot beheerde schijven voor Azure Linux VM's](../../linux/managed-disks-overview.md).

In een basisconfiguratie raden we meestal een implementatiestructuur aan waarbij het besturingssysteem, DBMS en eventuele SAP-binaire bestanden gescheiden zijn van de databasebestanden. We raden SAP-systemen die in Virtuele Azure-machines worden uitgevoerd, aan om de basis VHD of schijf te installeren met het besturingssysteem, uitvoerbare gegevens en SAP-uitvoerbare bestanden. 

De DBMS-gegevens en logbestanden worden opgeslagen in standaardopslag of premium opslag. Ze worden opgeslagen in afzonderlijke schijven en als logische schijven gekoppeld aan de oorspronkelijke Azure-besturingssysteemafbeelding VM. Voor Linux-implementaties worden verschillende aanbevelingen gedocumenteerd, vooral voor SAP HANA.

Wanneer u uw schijfindeling plant, zoekt u de beste balans tussen deze items:

* Het aantal gegevensbestanden.
* Het aantal schijven dat de bestanden bevat.
* De IOPS-quota van één schijf.
* De gegevensdoorvoer per schijf.
* Het aantal extra gegevensschijven mogelijk per VM-grootte.
* De totale opslagdoorvoer die een VM kan bieden.
* De latentie die verschillende Azure Storage-typen kunnen bieden.
* VM SLA's.

Azure dwingt een IOPS-quotum per gegevensschijf af. Deze quota zijn verschillend voor schijven die worden gehost op standaardopslag en premium opslag. I/O latentie is ook verschillend tussen de twee opslagtypen. Premium opslag zorgt voor een betere I/O-latency. 

Elk van de verschillende VM-typen heeft een beperkt aantal gegevensschijven die u koppelen. Een andere beperking is dat alleen bepaalde VM-typen premium opslag kunnen gebruiken. Meestal besluit u een bepaald VM-type te gebruiken op basis van CPU- en geheugenvereisten. U ook rekening houden met de IOPS-, latentie- en schijfdoorvoervereisten die meestal worden geschaald met het aantal schijven of het type premium-opslagschijven. Het aantal IOPS en de doorvoer die door elke schijf moet worden bereikt, kan de schijfgrootte dicteren, vooral met premium opslag.

> [!NOTE]
> Voor DBMS-implementaties raden we het gebruik van premium opslag aan voor gegevens, transactielogboeken of opnieuw gebruiken bestanden. Het maakt niet uit of u productie- of niet-productiesystemen wilt implementeren.

> [!NOTE]
> Om te kunnen profiteren van de unieke [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)van Azure, moeten alle schijven die zijn aangesloten het premium opslagtype zijn, dat de basis VHD bevat.

> [!NOTE]
> Het hosten van hoofddatabasebestanden, zoals gegevens- en logboekbestanden, van SAP-databases op opslaghardware die zich bevindt in co-gevestigde datacenters van derden naast Azure-datacenters, wordt niet ondersteund. Voor SAP-workloads wordt alleen opslag die wordt weergegeven als native Azure-service ondersteund voor de gegevens- en transactielogboekbestanden van SAP-databases.

De plaatsing van de databasebestanden en de logboek- en redobestanden en het type Azure Storage dat u gebruikt, wordt gedefinieerd door IOPS, latentie en doorvoervereisten. Om voldoende IOPS te hebben, wordt u mogelijk gedwongen om meerdere schijven te gebruiken of een grotere premium opslagschijf te gebruiken. Als u meerdere schijven gebruikt, bouwt u een softwarestreep over de schijven die de gegevensbestanden of de logboek- en redo-bestanden bevatten. In dergelijke gevallen zijn de IOPS en de schijfdoorvoerSLA's van de onderliggende premium-opslagschijven of de maximaal haalbare IOPS van standaardopslagschijven cumulatief voor de resulterende streepset.

Zoals reeds vermeld, als uw IOPS-vereiste groter is dan wat een enkele VHD kan bieden, balanceert u het aantal IOPS dat nodig is voor de databasebestanden in een aantal VHD's. De eenvoudigste manier om de IOPS-belasting over schijven te verdelen, is door een softwarestreep over de verschillende schijven te bouwen. Plaats vervolgens een aantal gegevensbestanden van de SAP DBMS op de LUN's die uit de softwarestreep zijn gesneden. Het aantal schijven in de streep wordt aangestuurd door IPP-eisen, eisen voor schijfdoorvoer en volumevereisten.


---
> ![Windows][Logo_Windows] Windows
>
> We raden u aan Windows-opslagruimten te gebruiken om streepsets te maken voor meerdere Azure VHD's. Gebruik ten minste Windows Server 2012 R2 of Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Alleen MDADM en Logical Volume Manager (LVM) worden ondersteund om een software RAID op Linux te bouwen. Zie voor meer informatie:
>
> - [Software RAID configureren op Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) met MDADM
> - [LVM configureren op een Linux-vm in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) met LVM
>
>

---

> [!NOTE]
> Omdat Azure Storage drie afbeeldingen van de VHD's bewaart, heeft het geen zin om een redundantie te configureren wanneer u streept. U hoeft alleen striping te configureren, zodat de I/O's over de verschillende VHD's worden verdeeld.
>

### <a name="managed-or-nonmanaged-disks"></a>Beheerde of niet-beheerde schijven
Een Azure-opslagaccount is een administratieve constructie en ook een onderwerp van beperkingen. Beperkingen verschillen tussen standaard opslagaccounts en premium opslagaccounts. Zie schaalbaarheid en [prestatiedoelen voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)voor informatie over mogelijkheden en beperkingen.

Voor standaardopslag moet u er rekening mee houden dat er een limiet is voor het IOPS-account per opslagaccount. Bekijk de rij met **total request-snelheid** in de [schaalbaarheid en prestatiedoelen van Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)in het artikel . Er is ook een eerste limiet voor het aantal opslagaccounts per Azure-abonnement. Balanceer VHD's voor het grotere SAP-landschap over verschillende opslagaccounts om te voorkomen dat de grenzen van deze opslagaccounts worden bereikt. Dit is vervelend werk als je het hebt over een paar honderd virtuele machines met meer dan duizend VHD's.

Omdat het gebruik van standaardopslag voor DBMS-implementaties in combinatie met een SAP-workload niet wordt aanbevolen, zijn verwijzingen en aanbevelingen naar standaardopslag beperkt tot dit korte [artikel](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Om te voorkomen dat vhd's worden gepland en geïmplementeerd in verschillende Azure-opslagaccounts, heeft Microsoft in 2017 [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) geïntroduceerd. Beheerde schijven zijn beschikbaar voor standaardopslag en premium opslag. De belangrijkste voordelen van beheerde schijven in vergelijking met niet-beheerde schijven zijn:

- Voor beheerde schijven distribueert Azure de verschillende VHD's automatisch over verschillende opslagaccounts tijdens de implementatie. Op deze manier worden opslagaccountlimieten voor gegevensvolume, I/O-doorvoer en IOPS niet bereikt.
- Azure Storage gebruikt beheerde schijven en eert de concepten van Azure-beschikbaarheidssets. Als de VM deel uitmaakt van een Azure-beschikbaarheidsset, worden de basisVHD en de gekoppelde schijf van een VM geïmplementeerd in verschillende fout- en updatedomeinen.


> [!IMPORTANT]
> Gezien de voordelen van Azure Managed Disks raden we u aan Azure Managed Disks te gebruiken voor uw DBMS-implementaties en SAP-implementaties in het algemeen.
>

Zie als u wilt converteren van niet-beheerde naar beheerde schijven:

- [Converteer een virtuele Windows-machine van onbeheerde schijven naar beheerde schijven.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Converteer een Virtuele Linux-machine van onbeheerde schijven naar beheerde schijven.](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching voor VM's en gegevensschijven
Wanneer u schijven aan VM's monteert, u kiezen of het I/O-verkeer tussen de VM en de schijven in Azure-opslag in de cache is opgeslagen. Standaard- en premiumopslag gebruiken twee verschillende technologieën voor dit type cache.

De volgende aanbevelingen gaan uit van deze I/O-kenmerken voor standaard DBMS:

- Het is meestal een leeswerkbelasting ten opzichte van gegevensbestanden van een database. Deze reads zijn prestatiekritisch voor het DBMS-systeem.
- Het schrijven tegen de gegevensbestanden vindt plaats in bursts op basis van checkpoints of een constante stroom. Gemiddeld over een dag, zijn er minder schrijft dan leest. Tegenover leest uit gegevensbestanden, deze schrijft zijn asynchroon en houden geen transacties van gebruikers.
- Er zijn nauwelijks reads uit de transactie log of redo bestanden. Uitzonderingen zijn grote I/O's wanneer u back-ups van transactielogboeken uitvoert.
- De belangrijkste belasting tegen transactie- of redo-logboekbestanden wordt geschreven. Afhankelijk van de aard van de werklast, u I / O's zo klein als 4 KB of, in andere gevallen, I / O maten van 1 MB of meer.
- Alle schrijft moet worden volgehouden op schijf in een betrouwbare manier.

Voor standaardopslag zijn de mogelijke cachetypen:

* Geen
* Lezen
* Lezen/Schrijven

Als u consistente en deterministische prestaties wilt leveren, stelt u de caching op standaardopslag in voor alle schijven die DBMS-gerelateerde gegevensbestanden bevatten, logboek- en redobestanden en tabelruimte op **GEEN**. De caching van de basis VHD kan blijven met de standaard.

Voor premium opslag zijn de volgende cachingopties:

* Geen
* Lezen
* Lezen/schrijven
* None + Write Accelerator, which is only for Azure M-Series VMs
* Lezen + Schrijfversneller, wat alleen voor Azure M-serie VM's is

Voor premium opslag raden we u aan **Read caching te** gebruiken voor gegevensbestanden van de SAP-database en Geen caching te kiezen voor de schijven van **logboekbestanden.**

Voor implementaties uit de M-serie raden we u aan Azure Write Accelerator te gebruiken voor uw DBMS-implementatie. Zie [Schrijfaccelerator inschakelen](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)voor meer informatie, beperkingen en implementatie van Azure Write Accelerator.


### <a name="azure-nonpersistent-disks"></a>Niet-persistente schijven voor Azure
Azure VM's bieden niet-persistente schijven nadat een VM is geïmplementeerd. In het geval van een VM reboot, alle inhoud op deze stations wordt weggevaagd. Het is een gegeven dat gegevensbestanden en log- en redobestanden van databases in geen geval op die niet-volgehouden stations mogen worden geplaatst. Er kunnen uitzonderingen zijn voor sommige databases, waar deze niet-volgehouden schijven geschikt kunnen zijn voor tempdb- en tijdelijke tabelruimten. Vermijd het gebruik van deze schijven voor VM's uit de A-serie, omdat deze niet-volhoudende schijven beperkt zijn in doorvoer met die VM-familie. 

Zie [Het tijdelijke station op Windows VM's in Azure begrijpen](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)voor meer informatie.

---
> ![Windows][Logo_Windows] Windows
>
> Station D in een Azure VM is een niet-volgehouden schijf, die wordt ondersteund door enkele lokale schijven op het Azure-compute-knooppunt. Omdat deze niet is volgehouden, gaan wijzigingen in de inhoud op station D verloren wanneer de VM opnieuw wordt opgestart. Wijzigingen omvatten bestanden die zijn opgeslagen, mappen die zijn gemaakt en toepassingen die zijn geïnstalleerd.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM's monteren automatisch een station op /mnt/resource dat een niet-volgehouden schijf is die wordt ondersteund door lokale schijven op het Azure-compute-knooppunt. Omdat deze niet is volgehouden, gaan wijzigingen in inhoud in /mnt/resource verloren wanneer de VM opnieuw wordt opgestart. Wijzigingen omvatten bestanden die zijn opgeslagen, mappen die zijn gemaakt en toepassingen die zijn geïnstalleerd.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Tolerantie voor Microsoft Azure-opslag
Microsoft Azure Storage slaat de basis VHD, met OS en aangesloten schijven of blobs, op ten minste drie afzonderlijke opslagknooppunten. Dit type opslag wordt lokaal redundante opslag (LRS) genoemd. LRS is de standaardinstelling voor alle typen opslag in Azure.

Er zijn andere redundantiemethoden. Zie [Azure Storage-replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie .

> [!NOTE]
>Premium-opslag is het aanbevolen type opslag voor DBMS VM's en schijven die database- en log- en redo-bestanden opslaan. De enige beschikbare redundantiemethode voor premium opslag is LRS. Als gevolg hiervan moet u databasemethoden configureren om databasegegevensreplicatie in te schakelen in een andere Azure-regio of beschikbaarheidszone. Databasemethoden zijn SQL Server Always On, Oracle Data Guard en HANA System Replication.


> [!NOTE]
> Voor DBMS-implementaties wordt het gebruik van georedundante opslag (GRS) niet aanbevolen voor standaardopslag. GRS heeft ernstige invloed op de prestaties en eert de schrijfvolgorde niet voor verschillende VHD's die aan een VM zijn gekoppeld. Het niet honoreren van de schrijfvolgorde voor verschillende VHD's leidt mogelijk tot inconsistente databases aan de doelzijde van replicatie. Deze situatie treedt op als database- en log- en redobestanden zijn verspreid over meerdere VHD's, zoals in het algemeen het geval is, aan de bron-VM-kant.



## <a name="vm-node-resiliency"></a>VM-knooppunttolerantie
Azure biedt verschillende SLA's voor VM's. Zie voor meer informatie de meest recente release van [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Omdat de DBMS-laag meestal essentieel is voor beschikbaarheid in een SAP-systeem, moet u inzicht krijgen in beschikbaarheidssets, beschikbaarheidszones en onderhoudsgebeurtenissen. Zie De beschikbaarheid van [virtuele Windows-machines in Azure beheren en](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) De beschikbaarheid van virtuele [Linux-machines in Azure beheren](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)voor meer informatie over deze concepten.

De minimale aanbeveling voor productie DBMS-scenario's met een SAP-werkbelasting is:

- Implementeer twee VM's in een afzonderlijke beschikbaarheiddie is ingesteld in dezelfde Azure-regio.
- Voer deze twee VM's uit in hetzelfde Virtuele Azure-netwerk en heb NIC's die zijn aangesloten op dezelfde subnetten.
- Gebruik databasemethoden om een hot stand-by te houden met de tweede VM. Methoden kunnen SQL Server Always On, Oracle Data Guard of HANA-systeemreplicatie zijn.

U ook een derde vm implementeren in een andere Azure-regio en dezelfde databasemethoden gebruiken om een asynchrone replica te leveren in een andere Azure-regio.

Zie [deze zelfstudie](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)voor informatie over het instellen van Azure-beschikbaarheidssets.



## <a name="azure-network-considerations"></a>Overwegingen voor Azure-netwerken
Gebruik bij grootschalige SAP-implementaties de blauwdruk van [Azure Virtual Datacenter.](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) Gebruik het voor uw virtuele netwerkconfiguratie en machtigingen en roltoewijzingen naar verschillende delen van uw organisatie.

Deze best practices zijn het resultaat van honderden implementaties van klanten:

- De virtuele netwerken waar de SAP-toepassing in wordt geïmplementeerd, hebben geen toegang tot het internet.
- De database VM's worden uitgevoerd in hetzelfde virtuele netwerk als de toepassingslaag.
- De VM's binnen het virtuele netwerk hebben een statische toewijzing van het privé-IP-adres. Zie [IP-adrestypen en toewijzingsmethoden in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)voor meer informatie.
- Routeringsbeperkingen van en naar de DBMS VM's zijn *niet* ingesteld met firewalls die zijn geïnstalleerd op de lokale DBMS VM's. In plaats daarvan wordt de verkeersroutering gedefinieerd met [netwerkbeveiligingsgroepen (NSG's).](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Als u het verkeer naar de DBMS-VM wilt scheiden en isoleren, wijst u verschillende NIC's toe aan de VM. Elke NIC krijgt een ander IP-adres en elke NIC wordt toegewezen aan een ander virtueel netwerksubnet. Elk subnet heeft andere NSG-regels. De isolatie of scheiding van netwerkverkeer is een maatregel voor routering. Het wordt niet gebruikt om quota in te stellen voor netwerkdoorvoer.

> [!NOTE]
> Het toewijzen van statische IP-adressen via Azure betekent dat ze worden toegewezen aan afzonderlijke virtuele NIC's. Wijs geen statische IP-adressen binnen het gastbesturingssysteem toe aan een virtuele NIC. Sommige Azure-services zoals Azure Backup vertrouwen op het feit dat ten minste de primaire virtuele NIC is ingesteld op DHCP en niet op statische IP-adressen. Zie [Problemen met de back-up van azure virtuele machines oplossen](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)voor meer informatie. Als u meerdere statische IP-adressen aan een vm wilt toewijzen, wijst u meerdere virtuele NIC's toe aan een vm.
>


> [!IMPORTANT]
> Het configureren van [virtuele netwerkapparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatiepad tussen de SAP-toepassing en de DBMS-laag van een SAP NetWeaver-, Hybris- of S/4HANA-gebaseerd SAP-systeem wordt niet ondersteund. Deze beperking is om functionaliteit en prestatieredenen. Het communicatiepad tussen de SAP-toepassingslaag en de DBMS-laag moet direct zijn. De beperking omvat geen [application security group (ASG) en NSG-regels](https://docs.microsoft.com/azure/virtual-network/security-overview) als deze ASG- en NSG-regels een direct communicatiepad toestaan. 
>
> Andere scenario's waarin virtuele netwerkapparaten niet worden ondersteund, bevinden zich in:
>
> * Communicatiepaden tussen Azure VM's die clusterknooppunten voor Linux Pacemaker en SBD-apparaten vertegenwoordigen, zoals beschreven in [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
> * Communicatiepaden tussen Azure VM's en Windows Server Scale-Out File Server (SOFS) zijn ingesteld zoals beschreven in [cluster een SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een bestandsshare in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Netwerkvirtuele apparaten in communicatiepaden kunnen de netwerklatentie tussen twee communicatiepartners eenvoudig verdubbelen. Ze kunnen ook de doorvoer beperken in kritieke paden tussen de SAP-toepassingslaag en de DBMS-laag. In sommige klantscenario's kunnen virtuele apparaten van het netwerk ervoor zorgen dat Pacemaker Linux-clusters mislukken. Dit zijn gevallen waarin communicatie tussen de Linux Pacemaker-clusterknooppunten via een virtueel netwerktoestel met hun SBD-apparaat communiceert.
>

> [!IMPORTANT]
> Een ander ontwerp dat *niet* wordt ondersteund, is de scheiding van de SAP-toepassingslaag en de DBMS-laag in verschillende virtuele Azure-netwerken die niet met elkaar worden [gekeken.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) We raden u aan de SAP-toepassingslaag en de DBMS-laag te scheiden door subnetten binnen een virtueel Azure-netwerk te gebruiken in plaats van verschillende virtuele Azure-netwerken te gebruiken. 
>
> Als u besluit de aanbeveling niet te volgen en in plaats daarvan de twee lagen in verschillende virtuele netwerken te scheiden, moeten de twee virtuele netwerken worden [peered.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 
>
> Houd er rekening mee dat netwerkverkeer tussen twee [gepeerde](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure-virtuele netwerken onderhevig is aan overdrachtskosten. Een enorm gegevensvolume dat uit veel terabytes bestaat, wordt uitgewisseld tussen de SAP-toepassingslaag en de DBMS-laag. U aanzienlijke kosten maken als de SAP-toepassingslaag en de DBMS-laag zijn gescheiden tussen twee gepeerde Virtuele Azure-netwerken.

Gebruik twee VM's voor uw productie-DBMS-implementatie binnen een Azure-beschikbaarheidsset. Gebruik ook aparte routering voor de SAP-toepassingslaag en het beheer- en bewerkingenverkeer naar de twee DBMS VM's. Zie de volgende afbeelding:

![Diagram van twee VM's in twee subnetten](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure Load Balancer gebruiken om verkeer om te leiden
Het gebruik van virtuele privé-IP-adressen die worden gebruikt in functionaliteiten zoals SQL Server Always On of HANA-systeemreplicatie vereist de configuratie van een Azure-load balancer. De load balancer gebruikt sondepoortenpoorten om het actieve DBMS-knooppunt te bepalen en het verkeer uitsluitend naar dat actieve databaseknooppunt te leiden. 

Als er een failover van het databaseknooppunt is, hoeft de SAP-toepassing niet opnieuw te configureren. In plaats daarvan maken de meest voorkomende SAP-toepassingsarchitecturen opnieuw verbinding met het privé virtuele IP-adres. Ondertussen reageert de load balancer op de failover van het knooppunt door het verkeer om te leiden naar het privé virtuele IP-adres naar het tweede knooppunt.

Azure biedt twee verschillende [SKU's:](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)een basis SKU en een standaard SKU. Tenzij u wilt implementeren in Azure-beschikbaarheidszones, doet de basisloadbalancer SKU het prima.

Wordt het verkeer tussen de DBMS VM's en de SAP-toepassingslaag altijd door de load balancer gerouteerd? Het antwoord is afhankelijk van hoe u de load balancer configureert. 

Op dit moment wordt het binnenkomende verkeer naar de DBMS VM altijd doorgestuurd via de load balancer. De uitgaande verkeersroute van de DBMS VM naar de toepassingslaag VM is afhankelijk van de configuratie van de load balancer. 

De load balancer biedt een optie van DirectServerReturn. Als deze optie is geconfigureerd, wordt het verkeer dat van de DBMS-VM naar de SAP-toepassingslaag wordt *geleid, niet* door de load balancer geleid. In plaats daarvan gaat het rechtstreeks naar de toepassingslaag. Als DirectServerReturn niet is geconfigureerd, wordt het retourverkeer naar de SAP-toepassingslaag doorgestuurd via de load balancer.

We raden u aan DirectServerReturn te configureren in combinatie met load balancers die zich tussen de SAP-toepassingslaag en de DBMS-laag bevinden. Deze configuratie vermindert de netwerklatentie tussen de twee lagen.

Zie [Een ILB-listener configureren voor Beschikbaarheidsgroepen altijd in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)voor een voorbeeld van het instellen van deze configuratie met SQL Server Always On.

Als u gepubliceerde GitHub JSON-sjablonen gebruikt als referentie voor uw SAP-infrastructuurimplementaties in Azure, bestudeer je deze [sjabloon voor een SAP 3-Tier-systeem.](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md) In deze sjabloon ziet u ook de juiste instellingen voor de load balancer.

### <a name="azure-accelerated-networking"></a>Azure-versnelde netwerken
Om de netwerklatentie tussen Azure VM's verder te verminderen, raden we u aan azure [accelerated networking te](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)kiezen. Gebruik het wanneer u Azure VM's implementeert voor een SAP-werkbelasting, met name voor de SAP-toepassingslaag en de SAP DBMS-laag.

> [!NOTE]
> Niet alle VM-typen ondersteunen Accelerated Networking. In het vorige artikel worden de VM-typen weergegeven die versnelde netwerken ondersteunen.
>

---
> ![Windows][Logo_Windows] Windows
>
> Zie [Een virtuele windows-machine maken met Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)voor meer informatie over het implementeren van VM's met versnelde netwerken voor Windows.
>
> ![Linux][Logo_Linux] Linux
>
> Zie [Een Virtuele Linux-machine maken met Accelerated Networking](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)voor meer informatie over Linux-distributie.
>
>

---

> [!NOTE]
> In het geval van SUSE, Red Hat en Oracle Linux wordt Accelerated Networking ondersteund met recente releases. Oudere releases zoals SLES 12 SP2 of RHEL 7.2 ondersteunen geen Azure Accelerated Networking.
>


## <a name="deployment-of-host-monitoring"></a>Implementatie van hostmonitoring
Voor het productiegebruik van SAP-toepassingen in virtuele Azure-machines vereist SAP de mogelijkheid om hostbewakingsgegevens te krijgen van de fysieke hosts die de virtuele Azure-machines uitvoeren. Er is een specifiek SAP Host Agent-patchniveau vereist dat deze mogelijkheid in SAPOSCOL en SAP Host Agent mogelijk maakt. Het exacte patchniveau is gedocumenteerd in SAP Note [1409604].

Zie de [implementatiehandleiding][deployment-guide]voor meer informatie over de implementatie van componenten die hostgegevens leveren aan SAPOSCOL en SAP Host Agent en het levenscyclusbeheer van die componenten.


## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over een bepaalde DBMS:

- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SQL Server](dbms_guide_sqlserver.md)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle](dbms_guide_oracle.md)
- [IBM DB2 Azure Virtual Machines DBMS-implementatie voor SAP-workload](dbms_guide_ibm.md)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](dbms_guide_sapase.md)
- [SAP maxDB-, Live-cache- en ContentServer-implementatie op Azure](dbms_guide_maxdb.md)
- [Bedieningsgids voor SAP HANA op Azure](hana-vm-operations.md)
- [SAP HANA hoge beschikbaarheid voor Virtuele Azure-machines](sap-hana-availability-overview.md)
- [Back-uphandleiding voor SAP HANA op virtuele Azure-machines](sap-hana-backup-guide.md)

