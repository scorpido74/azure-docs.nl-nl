---
title: "SAP op Azure: ondersteunde scenario's met Azure VM's"
description: Azure Virtual Machines ondersteunde scenario's met SAP-workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137625"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-werkbelasting op door Azure ondersteunde scenario's voor virtuele machines
Het ontwerpen van SAP NetWeaver-, Business one- `Hybris` of S/4HANA-systeemarchitectuur in Azure biedt veel verschillende mogelijkheden voor verschillende architecturen en hulpprogramma's om te gebruiken om tot een schaalbare, efficiënte en zeer beschikbare implementatie te komen. Hoewel afhankelijk van het gebruikte besturingssysteem of DBMS, zijn er beperkingen. Ook worden niet alle on-premises scenario's op dezelfde manier ondersteund in Azure. Dit document leidt door de ondersteunde configuraties met niet-hoge beschikbaarheid en configuraties en architecturen met hoge beschikbaarheid die uitsluitend Azure VM's gebruiken. Voor scenario's die worden ondersteund met [HANA Large Instances,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)raadpleegt u het artikel [Ondersteunde scenario's voor HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario). 


## <a name="2-tier-configuration"></a>2-laags-configuratie
Een SAP 2-tier configuratie wordt beschouwd als opgebouwd uit een gecombineerde laag van de SAP DBMS en toepassingslaag die op dezelfde server of VM-eenheid worden uitgevoerd. De tweede laag wordt beschouwd als de gebruikersinterfacelaag. In het geval van een 2-tier configuratie delen de DBMS- en SAP-toepassingslaag de bronnen van de Azure VM. Als gevolg hiervan moet u de verschillende componenten zo configureren dat deze niet concurreren om resources. U moet ook oppassen dat u de bronnen van de VM niet te veel inschrijft. Een dergelijke configuratie biedt geen hoge beschikbaarheid, buiten de [Azure Service Level-overeenkomsten](https://azure.microsoft.com/support/legal/sla/) van de verschillende Azure-componenten die betrokken zijn.

Een grafische weergave van een dergelijke configuratie kan eruit zien als:

![Eenvoudige 2-laags-configuratie](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Dergelijke configuraties worden ondersteund met Windows, Red Hat, SUSE en Oracle Linux voor de DBMS-systemen van SQL Server, Oracle, Db2, maxDB en SAP ASE voor productie- en niet-productiecases. Voor SAP HANA als DBMS wordt een dergelijk type configuraties alleen ondersteund voor niet-productiegevallen. Dit geldt ook voor de implementatiecase van [Azure HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
Voor alle OS/DBMS-combinaties die op Azure worden ondersteund, wordt dit type configuratie ondersteund. Het is echter verplicht dat u de configuratie van de DBMS- en SAP-componenten zo instelt dat DBMS- en SAP-componenten niet concurreren om geheugen- en CPU-bronnen en daarmee de fysieke beschikbare bronnen overschrijden. Dit moet worden gedaan door het beperken van het geheugen van de DBMS is toegestaan toe te wijzen. U moet ook het SAP Extended Memory beperken voor toepassingsinstanties. U moet ook het CPU-verbruik van de VM in het algemeen controleren om ervoor te zorgen dat de componenten de CPU-resources niet maximaliseren. 

> [!NOTE]
> Voor productie SAP-systemen raden we extra configuraties met hoge beschikbaarheid en eventuele disaster recovery aan, zoals later in dit document wordt beschreven


## <a name="3-tier-configuration"></a>3-laags-configuratie
In dergelijke configuraties scheidt u de SAP-toepassingslaag en de DBMS-laag in verschillende VM's. Meestal doe je dat voor grotere systemen en uit redenen om flexibeler te zijn op de resources van de SAP-toepassingslaag. In de meest eenvoudige installatie is er geen hoge beschikbaarheid buiten de [Azure Service Level-overeenkomsten](https://azure.microsoft.com/support/legal/sla/) van de verschillende Azure-componenten betrokken. 

De grafische weergave ziet eruit als:

![Eenvoudige 2-laags-configuratie](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Dit type configuratie wordt ondersteund op Windows, Red Hat, SUSE en Oracle Linux voor de DBMS-systemen van SQL Server, Oracle, Db2, SAP HANA, maxDB en SAP ASE voor productie- en niet-productiecases. Dit is de standaardimplementatieconfiguratie voor [Azure HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Voor vereenvoudiging hebben we geen onderscheid gemaakt tussen SAP Central Services en SAP-dialoogvensters in de SAP-toepassingslaag. In deze eenvoudige 3-tier configuratie zou er geen bescherming voor hoge beschikbaarheid zijn voor SAP Central Services.

> [!NOTE]
> Voor productie SAP-systemen raden we extra configuraties met hoge beschikbaarheid en eventuele disaster recovery aan, zoals later in dit document wordt beschreven


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Meerdere DBMS-exemplaren per VM- of HANA-eenheid voor grote instanties
In dit configuratietype host u meerdere DBMS-exemplaren per Azure VM- of HANA-eenheid voor grote instanties. De motivatie kan zijn om minder besturingssystemen te onderhouden en daarmee lagere kosten. Andere motivaties kunnen zijn om meer flexibiliteit en meer efficiëntie te hebben door het delen van bronnen van een grotere VM of HANA Large Instance-eenheid tussen meerdere DBMS-exemplaren. Tot nu toe werden deze configuraties weergegeven meestal voor niet-productiesystemen.

Zo'n configuratie kan er uitzien als:

![Meerdere DBMS-exemplaren in één eenheid](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Dit type DBMS-implementatie wordt ondersteund voor:

- SQL Server op Windows
- IBM Db2. Zoek details in het artikel [Meerdere instanties (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Voor Oracle. Zie [SAP-ondersteuningsnotitie #1778431](https://launchpad.support.sap.com/#/notes/1778431) en gerelateerde SAP-notities voor meer informatie
- Voor SAP HANA worden meerdere exemplaren op één VM ondersteund, waarbij SAP deze implementatiemethode MCOS aanroept. Zie voor meer informatie het SAP-artikel [Multiple SAP HANA Systems on One Host (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Als u meerdere database-exemplaren op één host uitvoert, moet u ervoor zorgen dat de verschillende instanties niet concurreren om resources en daarmee de fysieke resourcelimieten van de VM overschrijden. Dit geldt met name voor geheugen waar u het geheugen moet afsluiten dat iedereen van de instanties die de VM deelt, kan toewijzen. Dat geldt ook voor de CPU-bronnen die de verschillende database-exemplaren kunnen gebruiken. Alle genoemde DBMS hebben configuraties die het mogelijk maken de geheugentoewijzing en CPU-resources op instantieniveau te beperken.
Om ondersteuning te hebben voor een dergelijke configuratie voor Azure VM's, wordt verwacht dat de schijven of volumes die worden gebruikt voor de gegevens en logbestanden van de databases die door de verschillende instanties worden beheerd, gescheiden zijn. Of met andere woorden gegevens of log/redo logbestanden van databases die worden beheerd door verschillende DBMS-instantie worden niet verondersteld om dezelfde schijven of volumes te delen. 

De schijfconfiguratie voor HANA Large Instances wordt geconfigureerd geleverd en wordt beschreven in [ondersteunde scenario's voor HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos). 

> [!NOTE]
> Voor productie-SAP-systemen raden we extra configuraties met hoge beschikbaarheid en eventuele disaster recovery aan, zoals later in dit document wordt beschreven. VM's met meerdere DBMS-exemplaren worden niet ondersteund met de configuraties met hoge beschikbaarheid die later in dit document worden beschreven.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Meerdere SAP-dialoogvensters in één VM
In veel gevallen werden meerdere dialogen geïmplementeerd op bare metal servers of zelfs in VM's die in privéclouds worden uitgevoerd. Reden voor dergelijke configuraties was om bepaalde SAP-dialoogvensters af te stemmen op bepaalde werkbelasting, bedrijfsfunctionaliteit of werkbelastingtypen. Reden om deze exemplaren niet te isoleren in afzonderlijke VM's was de inspanning van onderhoud en exploitatie van het besturingssysteem. Of in tal van gevallen de kosten in het geval de hoster of exploitant van de VM vraagt om een maandelijkse vergoeding per VM geëxploiteerd en beheerd. In Azure, een scenario van het hosten van meerdere SAP-dialoogvensters binnen één VM die wordt ondersteund voor productie- en niet-productiedoeleinden op de besturingssystemen van Windows, Red Hat, SUSE en Oracle Linux. De PARAMETER SAP-kernel PHYS_MEMSIZE, beschikbaar op Windows en moderne Linux-kernels, moet worden ingesteld als meerdere SAP Application Server-exemplaren op één VM worden uitgevoerd. Het wordt ook aangeraden om de uitbreiding van SAP Extended Memory op besturingssystemen te beperken, zoals Windows waar automatische groei van het SAP extended Memory wordt geïmplementeerd. Dit kan met de SAP-profielparameter. `em/max_size_MB`

Bij 3-tier configuratie waarbij meerdere SAP-dialoogvensters worden uitgevoerd binnen Azure VM's eruit kunnen zien:

![Meerdere DBMS-exemplaren in één eenheid](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Voor vereenvoudiging hebben we geen onderscheid gemaakt tussen SAP Central Services en SAP-dialoogvensters in de SAP-toepassingslaag. In deze eenvoudige 3-tier configuratie zou er geen bescherming voor hoge beschikbaarheid zijn voor SAP Central Services. Voor productiesystemen wordt het afgeraden om SAP Central Services onbeschermd te laten. Zie latere secties van dit document voor specifieke details over zogenaamde multi-SID-configuraties rond SAP Central Instances en de hoge beschikbaarheid van dergelijke multi-SID-configuraties.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Bescherming met hoge beschikbaarheid voor de SAP DBMS-laag
Als u SAP-productiesystemen wilt implementeren, moet u rekening houden met het type hot-stand-by configuraties met hoge beschikbaarheid. Vooral met SAP HANA, waar gegevens in het geheugen moeten worden geladen voordat ze de volledige prestaties en schaalbaarheid terug kunnen krijgen, is Azure-servicehealing geen ideale maatregel voor hoge beschikbaarheid. 

In het algemeen ondersteunt Microsoft alleen configuraties met hoge beschikbaarheid en softwarepakketten die worden beschreven onder de sectie SAP-werkbelasting in docs.microsoft.com. U dezelfde verklaring lezen in SAP note [#1928533.](https://launchpad.support.sap.com/#/notes/1928533) Microsoft biedt geen ondersteuning voor andere softwareframeworks van derden met hoge beschikbaarheid die niet door Microsoft zijn gedocumenteerd in combinatie met SAP-workload. In dergelijke gevallen is de externe leverancier van het high availability framework de ondersteunende partij voor de configuratie met hoge beschikbaarheid die door u als klant moet worden betrokken bij het ondersteuningsproces. Uitzonderingen worden genoemd in dit artikel. 

Over het algemeen ondersteunt Microsoft een beperkte set configuraties met hoge beschikbaarheid op Azure VM's of HANA Large Instances-eenheden. Lees voor de ondersteunde scenario's van HANA Large Instances het document [Ondersteunde scenario's voor HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

Voor Azure VM's worden de volgende configuraties met hoge beschikbaarheid ondersteund op DBMS-niveau:

- SAP HANA System Replication gebaseerd op Linux Pacemaker op SUSE en Red Hat. Zie de gedetailleerde artikelen:
    - [Hoge beschikbaarheid van SAP HANA op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Hoge beschikbaarheid van SAP HANA op Azure VM's op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- SAP HANA scale-out n+m configuraties met [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) on SUSE en Red Hat. Details zijn opgenomen in deze artikelen:
    - [Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op SUSE Linux Enterprise Server}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- SQL Server Failover-cluster op basis van Windows Scale-Out File Services. Hoewel aanbeveling voor productiesystemen is om SQL Server Always On te gebruiken in plaats van clustering. SQL Server Always On biedt een betere beschikbaarheid met behulp van afzonderlijke opslag. Details worden beschreven in dit artikel: 
    - [Een SQL Server-failoverclusterinstantie configureren op virtuele Azure-machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- SQL Server Always On wordt ondersteund met het Windows-besturingssysteem voor SQL Server op Azure. Dit is de standaardaanbeveling voor het produceren van SQL Server-exemplaren op Azure. Details worden beschreven in deze artikelen:
    - [Maak kennis met SQL Server Always On-beschikbaarheidsgroepen op virtuele Azure-machines.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
    - [Configureer een groep Always On beschikbaarheid op virtuele Azure-machines in verschillende regio's](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
    - [Een load balancer configureren voor een groep Always On beschikbaarheid in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).
- Oracle Data Guard voor Windows en Oracle Linux. Details voor Oracle Linux vindt u in dit artikel:
    - [Oracle Data Guard implementeren op een virtuele Azure Linux-machine](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR op SUSE en RHEL Gedetailleerde documentatie voor SUSE en RHEL met pacemaker vindt u hier:
    - [Hoge beschikbaarheid van IBM Db2 LUW op Azure VM's op SUSE Linux Enterprise Server met Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Hoge beschikbaarheid van IBM Db2 LUW in Azure-VM's in Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- SAP ASE- en SAP maxDB-configuratie zoals beschreven in deze documenten:
    - [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [SAP MaxDB-, liveCache- en ContentServer-implementatie op Azure VM's](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- HANA Large Instances met een hoge beschikbaarheid worden beschreven in:
    - [Ondersteunde scenario's voor HANA Large Instances- HSR met STONITH voor hoge beschikbaarheid](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Ondersteunde scenario's voor HANA Large Instances - Host auto failover (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> Voor geen van de hierboven beschreven scenario's ondersteunen we configuraties van meerdere DBMS-exemplaren in één VM. Betekent dat in elk van de gevallen slechts één database-exemplaar per VM kan worden geïmplementeerd en kan worden beschermd met de beschreven methoden voor hoge beschikbaarheid. Het beveiligen van meerdere DBMS-exemplaren onder hetzelfde Windows- of Pacemaker-failovercluster wordt op dit moment **NIET** ondersteund. Ook Oracle Data Guard wordt alleen ondersteund voor afzonderlijke exemplaars per VM-implementatiecases. 

Verschillende databasesystemen maken het mogelijk om meerdere databases onder één DBMS-exemplaar te hosten. Net als in het geval van SAP HANA kunnen meerdere databases worden gehost in meerdere databasecontainers (MDC). Voor gevallen waarin deze multi-databaseconfiguraties binnen één failoverclusterbron werken, worden deze configuraties ondersteund. Configuraties die niet worden ondersteund, zijn gevallen waarin meerdere clusterbronnen nodig zijn. Wat betreft configuraties waarbij u meerdere SQL Server-beschikbaarheidsgroepen definieert onder één SQL Server-instantie.


![DBMS HA-configuratie](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Afhankelijk van de DBMS an/of besturingssystemen zijn onderdelen zoals Azure load balancer mogelijk wel of niet vereist als onderdeel van de oplossingsarchitectuur. 

Specifiek voor maxDB moet de opslagconfiguratie anders zijn. In maxDB moeten de gegevens en logbestanden worden gelokaliseerd op gedeelde opslag voor configuraties met hoge beschikbaarheid. Alleen in het geval van maxDB wordt gedeelde opslag ondersteund voor hoge beschikbaarheid. Voor alle andere DBMS afzonderlijke opslagstacks per knooppunt zijn de enige ondersteunde schijfconfiguraties.

Andere frameworks met hoge beschikbaarheid staan bekend om te bestaan en staan erom bekend dat ze ook op Microsoft Azure worden uitgevoerd. Microsoft heeft deze frameworks echter niet getest. Als u uw configuratie met hoge beschikbaarheid met deze frameworks wilt bouwen, moet u samenwerken met de leverancier van die software om:

- Een implementatiearchitectuur ontwikkelen
- Implementatie van de architectuur
- Ondersteuning van de architectuur

> [!IMPORTANT]
> Microsoft Azure Marketplace biedt een verscheidenheid aan zachte apparaten die opslagoplossingen bieden bovenop azure native storage. Deze zachte apparaten kunnen ook worden gebruikt om NFS-shares te maken die theoretisch kunnen worden gebruikt in de SAP HANA scale-out implementaties waar een stand-by node vereist is. Om verschillende redenen wordt geen van deze zachte opslagapparaten ondersteund voor een van de DBMS-implementaties van Microsoft en SAP op Azure. Implementaties van DBMS op SMB-aandelen worden op dit moment helemaal niet ondersteund. Implementaties van DBMS op NFS-shares is beperkt tot NFS 4.1-shares op [Azure NetApp-bestanden.](https://azure.microsoft.com/services/netapp/)


## <a name="high-availability-for-sap-central-service"></a>Hoge beschikbaarheid voor SAP Central Service
SAP Central Services is een tweede single point of failure van uw SAP-configuratie. Als gevolg hiervan zou u deze Central Services-processen ook moeten beschermen. Het aanbod ondersteund en gedocumenteerd voor SAP werkbelasting leest als:

- Windows Failoverclusterserver met Windows Scale-out File Services voor sapmnt en wereldwijde transportdirectory. Details worden beschreven in het artikel:
    - [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een bestandsshare in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Azure-infrastructuur voorbereiden op hoge beschikbaarheid van SAP met behulp van een Windows-failovercluster en bestandsshare voor SAP ASCS/SCS-exemplaren](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Windows Failoverclusterserver met SMB-share op basis van [Azure NetApp-bestanden](https://azure.microsoft.com/services/netapp/) voor sapmnt en wereldwijde transportmap. Details zijn opgenomen in het artikel:
    - [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op Windows met Azure NetApp Files(SMB) voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Windows Failoverclusterserver op `Datakeeper`basis van SIOS . Hoewel gedocumenteerd door Microsoft, hebt u een ondersteuningsrelatie met SIOS nodig, zodat u communiceren met SIOS-ondersteuning wanneer u deze oplossing gebruikt. Details worden beschreven in het artikel:
    - [Een SAP ASCS/SCS-exemplaar clusteren op een Windows-failovercluster met behulp van een gedeelde clusterschijf in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [De Azure-infrastructuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en gedeelde schijf voor SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Pacemaker op SUSE-besturingssysteem met het maken van een zeer `drdb` beschikbare NFS-share met behulp van twee SUSE VM's en voor bestandsreplicatie. Details zijn gedocumenteerd in het artikel
    - [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Hoge beschikbaarheid voor NFS op Azure VM's op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Pacemaker SUSE-besturingssysteem met gebruikmaking van NFS-aandelen die worden geleverd door [Azure NetApp-bestanden.](https://azure.microsoft.com/services/netapp/) Details worden gedocumenteerd in
    - [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Pacemaker op Red Hat-besturingssysteem met `glusterfs` NFS-share gehost op een cluster. Details zijn te vinden in de artikelen
    - [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`op Azure VM's op Red Hat Enterprise Linux voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Pacemaker op Red Hat-besturingssysteem met NFS-share gehost op [Azure NetApp-bestanden](https://azure.microsoft.com/services/netapp/). Details worden beschreven in het artikel
    - [Azure Virtual Machines hoge beschikbaarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp-bestanden voor SAP-toepassingen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Van de genoemde oplossingen hebt u een ondersteuningsrelatie `Datakeeper` met SIOS nodig om het product te ondersteunen en rechtstreeks met SIOS in contact te komen in geval van problemen. Afhankelijk van de manier waarop u een licentie hebt gegeven voor windows, Red Hat en/of SUSE OS, u ook worden verplicht om een ondersteuningscontract met uw besturingssysteemprovider te hebben om volledige ondersteuning te hebben van de vermelde configuraties met hoge beschikbaarheid.

De configuratie kan ook worden weergegeven als:

![DBMS- en ASCS HA-configuratie](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Aan de rechterkant van de graphics wordt de zeer beschikbare SAP Central Services getoond. Naast het hebben van de SAP Central-services die worden beschermd met een failoverclusterframework dat kan mislukken in geval van een probleem, is er een noodzaak voor een zeer beschikbaar NFS- of SMB-aandeel, of een gedeelde Windows-schijf om ervoor te zorgen dat de sapmnt- en wereldwijde transportmap beschikbaar onafhankelijk van het bestaan van één VM. Voor een aantal oplossingen, zoals Windows Failover Cluster Server en Pacemaker, is een Azure load balancer nodig om verkeer naar een gezond knooppunt te leiden of om te leiden.

In de getoonde lijst wordt geen melding gemaakt van het Oracle Linux-besturingssysteem. Oracle Linux ondersteunt Pacemaker niet als clusterframework. Als u uw SAP-systeem wilt implementeren op Oracle Linux en u een framework met hoge beschikbaarheid voor Oracle Linux nodig hebt, moet u samenwerken met externe leveranciers. Een van de leveranciers is SIOS met hun Protection Suite voor Linux die wordt ondersteund door SAP op Azure. Voor meer informatie lees SAP nota [#1662610 - Ondersteuningsdetails voor SIOS Protection Suite voor Linux](https://launchpad.support.sap.com/#/notes/1662610) voor meer informatie.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Ondersteunde opslag met de hierboven genoemde SAP Central Services-scenario's
Aangezien slechts een subset van Azure-opslagtypen zeer beschikbare NFS- of SMB-shares levert die kwaliteit voor het gebruik in onze SAP Central Services-clusterscenario's, een lijst met ondersteunde opslagtypen

- Windows Failoverclusterserver met Windows Scale-out Bestandsserver kan worden geïmplementeerd op alle native Azure-opslagtypen, behalve Azure NetApp-bestanden. Aanbeveling is echter om Premium Storage te benutten vanwege superieure servicelevelovereenkomsten in doorvoer en IOPS.
- Windows Failoverclusterserver met SMB op Azure NetApp-bestanden wordt ondersteund op Azure NetApp-bestanden. SMB-shares op Azure **File-services** worden op dit moment NIET ondersteund.
- Windows Failoverclusterserver met gedeelde Windows-schijf op basis van SIOS `Datakeeper` kan worden geïmplementeerd op alle native Azure-opslagtypen, behalve Azure NetApp-bestanden. Aanbeveling is echter om Premium Storage te benutten vanwege superieure servicelevelovereenkomsten in doorvoer en IOPS.
- SUSE of Red Hat Pacemaker met NFS-shares op Azure NetApp-bestanden wordt ondersteund op Azure NetApp-bestanden. 
- SUSE Pacemaker `drdb` met behulp van een configuratie tussen twee VM's wordt ondersteund met behulp van native Azure-opslagtypen, met uitzondering van Azure NetApp-bestanden. Aanbeveling is echter om Premium Storage te benutten vanwege superieure servicelevelovereenkomsten in doorvoer en IOPS.
- Het gebruik `glusterfs` van Red Hat Pacemaker voor het leveren van NFS-share wordt ondersteund met native Azure-opslagtypen, met uitzondering van Azure NetApp-bestanden. Aanbeveling is echter om Premium Storage te benutten vanwege superieure servicelevelovereenkomsten in doorvoer en IOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace biedt een verscheidenheid aan zachte apparaten die opslagoplossingen bieden bovenop azure native storage. Deze zachte apparaten kunnen worden gebruikt om NFS- of SMB-shares te maken die theoretisch ook kunnen worden gebruikt in de failover geclusterde SAP Central Services. Deze oplossingen worden niet rechtstreeks ondersteund voor SAP-workload s. Als u besluit een dergelijke oplossing te gebruiken om uw NFS- of SMB-aandeel te maken, moet ondersteuning voor de SAP Central Service-configuratie worden geboden door de derde partij die eigenaar is van de software in het zachte opslagapparaat.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Multi-SID SAP Central Services failover clusters
Om het aantal VM's dat nodig is in grote SAP-landschappen te verminderen, maakt SAP het mogelijk om SAP Central Services-exemplaren van meerdere verschillende SAP-systemen uit te voeren in failoverclusterconfiguratie. Stel je gevallen voor waarin je 30 of meer NetWeaver- of S/4HANA-productiesystemen hebt. Zonder multi-SID-clustering vereisen deze configuraties 60 of meer VM's in 30 of meer Windows- of Pacemaker-failoverclusterconfiguraties. Naast de DBMS failover clusters noodzakelijk. Het implementeren van meerdere CENTRALE SAP-services in twee knooppunten in een failoverclusterconfiguratie kan het aantal VM's aanzienlijk verminderen. Het implementeren van meerdere SAP Central-services-exemplaren op één clusterconfiguratie met twee knooppunten heeft echter ook enkele nadelen. Problemen rond één VM in de clusterconfiguratie zijn van toepassing op meerdere SAP-systemen. Onderhoud aan het gastbesturingssysteem dat in de clusterconfiguratie wordt uitgevoerd, vereist meer coördinatie omdat meerdere productie-SAP-systemen worden beïnvloed. Tools zoals SAP LaMa ondersteunen geen multi-SID clustering in hun systeem klonen proces.

Op Azure wordt een multi-SID-clusterconfiguratie ondersteund voor het Windows-besturingssysteem met ENSA1 en ENSA2. Aanbeveling is niet om de oudere Enqueue Replication Service-architectuur (ENSA1) te combineren met de nieuwe architectuur (ENSA2) op één multi-SID-cluster. Details over een dergelijke architectuur zijn gedocumenteerd in de artikelen

- [SAP ASCS/SCS-instantie multi-SID hoge beschikbaarheid met Windows Server Failover Clustering en gedeelde schijf op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [SAP ASCS/SCS-instantie multi-SID hoge beschikbaarheid met Windows Server Failover Clustering en bestandsshare op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

Voor SUSE wordt ook een multi-SID cluster op basis van Pacemaker ondersteund. Tot nu toe wordt de configuratie ondersteund voor:

- Maximaal vijf SAP ASCS/SCS-exemplaren
- De oude replicatieserverijsarchitectuur (ENSA1)
- Twee node Pacemaker-clusterconfiguraties

De configuratie is gedocumenteerd in [Hoge beschikbaarheid voor SAP NetWeaver op Azure VM's op SUSE Linux Enterprise Server voor SAP-toepassingen multi-SID-handleiding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Een multi-SID-cluster met Enqueue Replication-server ziet er schematisch uit als

![DBMS- en ASCS HA-configuratie](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA scale-out scenario's
SAP HANA-scale-outscenario's worden ondersteund voor een subset van de HANA-gecertificeerde Azure VM's zoals vermeld in de [SAP HANA-hardwaremap.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Alle VM's met 'Ja' in de kolom 'Clustering' kunnen worden gebruikt voor olap of S/4HANA scale-out. Configuraties zonder stand-by worden ondersteund met de Azure Storage-typen: 

- Azure Premium Storage, inclusief Azure Write-versneller voor het /hana/logboekvolume
- [Ultraschijven](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA scale-out configuraties voor OLAP of S/4HANA met stand-by node(s) worden exclusief ondersteund met NFS shared gehost op Azure NetApp Files.

Voor meer informatie over exacte opslagconfiguraties met of zonder stand-by node, raadpleegt u de artikelen:

- [Configuraties van SAP HANA in virtuele Azure-machineopslag](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [SAP-ondersteuningsnotitie #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Voor meer informatie over HANA Large Instances ondersteunde HANA scale-out configuraties, de volgende documentatie is van toepassing:

- [Ondersteunde scenario's voor HANA Large Instances scale-out met stand-by](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Ondersteunde scenario's voor HANA Large Instances scale-out zonder stand-by](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenario voor noodherstel
Er zijn verschillende scenario's voor noodherstel die worden ondersteund. We definiëren Disaster-architecturen als architecturen die moeten compenseren voor een volledige Azure-regio's die van het net verdwijnen. Dit betekent dat we het doel voor noodherstel nodig hebben om een andere Azure-regio te zijn als doel om uw SAP-landschap uit te voeren. We scheiden methoden en configuraties in DBMS-laag en niet-DBMS-laag. 

### <a name="dbms-layer"></a>DBMS-laag
Voor de DBMS-laag worden configuraties met de dbms-native replicatiemechanismen, zoals Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On of HANA System Replication, ondersteund. Het is verplicht dat de replicatiestream in dergelijke gevallen asynchroon is, in plaats van synchroon zoals in typische scenario's met hoge beschikbaarheid die binnen één Azure-gebied worden geïmplementeerd. Een typisch voorbeeld van een dergelijke ondersteunde DBMS-noodherstelconfiguratie wordt beschreven in het artikel [SAP HANA-beschikbaarheid in Azure-regio's.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions) De tweede afbeelding in die sectie beschrijft een scenario met HANA als voorbeeld. De belangrijkste databases die worden ondersteund voor SAP-toepassingen kunnen allemaal in een dergelijk scenario worden geïmplementeerd.

Het wordt ondersteund om een kleinere VM te gebruiken als doelinstantie in het rampherstelgebied, omdat die VM niet het volledige workloadverkeer ervaart. Als u dit doet, moet u rekening houden met de volgende overwegingen:

- Kleinere VM-typen staan niet toe dat veel schijven zijn aangesloten dan kleinere VM's
- Kleinere VM's hebben minder netwerk- en opslagdoorvoer
- Het opnieuw aanpassen van de grootte van VM-families kan een probleem zijn wanneer de verschillende VM's worden verzameld in één Azure Availability Set of wanneer de grootte moet plaatsvinden tussen de M-serie-familie en Mv2-familie van VM's
- CPU- en geheugenverbruik voor de databaseinstantie die de stroom van wijzigingen met minimale vertraging en voldoende CPU- en geheugenbronnen kan ontvangen om deze wijzigingen met minimale vertraging op de gegevens toe te passen  

Meer informatie over beperkingen van verschillende VM-formaten vindt u [hier](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Een andere ondersteunde methode voor het implementeren van een DR-doel is om een tweede DBMS-exemplaar te installeren op een VM die een niet-productie DBMS-exemplaar van een niet-productie-SAP-exemplaar uitvoert. Dit kan een beetje uitdagender zijn, omdat u moet uitzoeken wat op geheugen, CPU-bronnen, netwerkbandbreedte en opslagbandbreedte nodig is voor de specifieke doelinstanties die moeten functioneren als hoofdinstantie in het DR-scenario. Vooral in HANA wordt het ten zeerste aanbevolen dat u de instantie configureert die als DR-doel functie op een gedeelde host fungeert, zodat de gegevens niet vooraf in de DR-doelinstantie worden geladen.

Controleer voor HANA Large Instance DR-scenario's deze documenten:

- [Eén knooppunt met DR met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Eén knooppunt met DR (multifunctioneel) met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Eén knooppunt met DR (multifunctioneel) met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Hoge beschikbaarheid met HSR en DR met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Uitschalen met DR met opslagreplicatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Eén knooppunt met DR met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Single node HSR naar DR (kosten geoptimaliseerd)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Hoge beschikbaarheid en disaster recovery met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Hoge beschikbaarheid en disaster recovery met HSR (kosten geoptimaliseerd)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Scale-out met DR met HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> Het gebruik van [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) is niet getest op DBMS-implementaties onder SAP-workload. Als gevolg hiervan wordt het op dit moment niet ondersteund voor de DBMS-laag van SAP-systemen. Andere replicatiemethoden van Microsoft en SAP die niet worden vermeld, worden niet ondersteund. Het gebruik van software van derden voor het repliceren van de DBMS-laag van SAP-systemen tussen verschillende Azure-regio's, moet worden ondersteund door de leverancier van de software en wordt niet ondersteund via Microsoft- en SAP-ondersteuningskanalen. 
 
## <a name="non-dbms-layer"></a>Niet-DBMS-laag
Voor de SAP-toepassingslaag en eventuele aandelen of opslaglocaties die nodig zijn, worden de twee belangrijkste scenario's door klanten gebruikt:

- De doelen voor noodherstel in de tweede Azure-regio worden niet gebruikt voor productie- of niet-productiedoeleinden. In dit scenario worden de VM's die fungeren als noodhersteldoel idealiter niet geïmplementeerd en worden de afbeelding en wijzigingen in de afbeeldingen van de productie-SAP-toepassingslaag gerepliceerd naar het rampherstelgebied. Een functionaliteit die een dergelijke taak kan uitvoeren, is [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview). Azure Site Recovery ondersteunt een Azure-naar-Azure-replicatiescenario's als deze. 
- De disaster recovery targets zijn VM's die daadwerkelijk worden gebruikt door niet-productiesystemen. Het hele SAP-landschap is verspreid over twee verschillende Azure-regio's met productiesystemen meestal in één regio en niet-productiesystemen in een andere regio. In veel klantimplementaties heeft de klant een niet-productiesysteem dat gelijkwaardig is aan een productiesysteem. De klant heeft productietoepassingsexemplaren vooraf geïnstalleerd op de niet-productiesystemen van de toepassingslaag. In het geval van een failover zouden de niet-productie-exemplaren worden afgesloten, worden de virtuele namen van de productie-VM's verplaatst naar de niet-productie-VM's (na het toewijzen van nieuwe IP-adressen in DNS) en worden de vooraf geïnstalleerde productie-exemplaren gestart

### <a name="sap-central-services-clusters"></a>SAP Central Services clusters
SAP Central Services-clusters die gedeelde schijven (Windows), SMB-shares (Windows) of NFS-shares gebruiken, zijn een beetje moeilijker te repliceren. Aan de Windows-kant is Windows Storage Replication een mogelijke oplossing. Op Linux rsync is een haalbare oplossing.



## <a name="non-supported-scenario"></a>Niet-ondersteund scenario
Er is een lijst met scenario's die niet worden ondersteund voor SAP-workload op Azure-architecturen. **Niet ondersteund** betekent dat SAP en Microsoft deze configuraties niet kunnen ondersteunen en moeten uitstellen tot een uiteindelijk betrokken derde partij die software heeft geleverd om dergelijke architecturen te maken. Twee van de categorieën zijn:

- Opslag soft appliances: Er is een aantal opslag soft appliances aangeboden in Azure marketplace. Sommige leveranciers bieden eigen documentatie over het gebruik van deze opslagsoft-apparaten op Azure met betrekking tot SAP-software. Ondersteuning van configuraties of implementaties met dergelijke opslag soft appliances moet worden verstrekt door de leverancier van deze opslag zachte apparaten. Dit feit komt ook tot uiting in [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Frameworks met hoge beschikbaarheid: Alleen Pacemaker- en Windows Server Failover-cluster worden ondersteund met frameworks met hoge beschikbaarheid voor SAP-workload s. Zoals eerder vermeld, wordt de `Datakeeper` oplossing van SIOS beschreven en gedocumenteerd door Microsoft. Niettemin moeten de componenten `Datakeeper` van SIOS worden ondersteund via SIOS als de leverancier die deze componenten levert. SAP heeft ook andere gecertificeerde high availability frameworks in verschillende SAP notes vermeld. Sommige van hen zijn ook gecertificeerd door de externe leverancier voor Azure. Niettemin moet ondersteuning voor configuraties die deze producten gebruiken, worden geleverd door de productleverancier. Verschillende leveranciers hebben verschillende integratie in de SAP-ondersteuningsprocessen. U moet verduidelijken welk ondersteuningsproces het beste werkt voor de specifieke leverancier voordat u besluit het product te gebruiken in SAP-configuraties die op Azure zijn geïmplementeerd.
- Gedeelde schijfclusters waar databasebestanden zich op de gedeelde schijven bevinden, worden niet ondersteund met uitzondering van maxDB. Voor alle andere database is de ondersteunde oplossing om afzonderlijke opslaglocaties te hebben in plaats van een SMB- of NFS-share of gedeelde schijf om scenario's met hoge beschikbaarheid te configureren

Andere scenario's, die niet worden ondersteund, zijn scenario's zoals:

- Implementatiescenario's die een grotere netwerklatentie introduceren tussen de SAP-toepassingslaag en de SAP DBMS-laag in de gemeenschappelijke architectuur van SAP, zoals weergegeven in NetWeaver, S/4HANA en bijvoorbeeld . `Hybris` Dit omvat:
    - Een van de lagen on-premise implementeren terwijl de andere laag wordt geïmplementeerd in Azure
    - De SAP-toepassingslaag van een systeem implementeren in een andere Azure-regio dan de DBMS-laag
    - Een laag implementeren in datacenters die zich naast Azure en de andere laag in Azure bevinden, behalve wanneer een dergelijke architectuurpatronen worden geleverd door een Azure-native service
    - Virtuele netwerkapparaten implementeren tussen de SAP-toepassingslaag en de DBMS-laag
    - Gebruikmaken van opslag die wordt gehost in datacenters die zich naast Azure-datacenter bevinden voor de SAP DBMS-laag of de algemene transportmap van SAP
    - Implementeer de twee lagen met twee verschillende cloudleveranciers. Bijvoorbeeld het implementeren van de DBMS-laag in Oracle Cloud Infrastructure en de toepassingslaag in Azure
- HanA Pacemaker-clusterconfiguraties met meerdere instanties
- Windows Cluster-configuraties met gedeelde schijven via SOFS of SMB op ANF voor SAP-databases die op Windows worden ondersteund. In plaats daarvan raden we het gebruik van native high availability replicatie van de specifieke databases en gebruik maken van aparte opslag stacks
- Implementatie van SAP-databases ondersteund op Linux met databasebestanden in NFS-shares bovenop ANF, met uitzondering van SAP HANA
- Implementatie van Oracle DBMS op een ander besturingssysteem dan Windows en Oracle Linux. Zie ook [SAP support note #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scenario(s) dat we niet hebben getest en dus geen ervaring hebben met lijst als:

- Azure Site Recovery repliceert DBMS-laag VM's. Daarom raden we u aan gebruik te maken van de databasenative asynchrone replicatiefunctionaliteit voor een mogelijke configuratie van noodherstel
 

## <a name="next-steps"></a>Volgende stappen
Lees de volgende stappen in de [planning en implementatie van Azure Virtual Machines voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



