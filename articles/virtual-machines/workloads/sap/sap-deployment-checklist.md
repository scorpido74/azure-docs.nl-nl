---
title: Checklist voor de planning en implementatie van SAP-workloads | Microsoft Documenten
description: Checklist voor het plannen van SAP-workloadimplementaties naar Azure en het implementeren van de workloads
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
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060071"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>SAP-workloads op Azure: checklist voor planning en implementatie

Deze checklist is ontworpen voor klanten die SAP NetWeaver-, S/4HANA- en Hybris-toepassingen naar Azure-infrastructuur als service verplaatsen. Gedurende de duur van het project moet een klant en/of SAP-partner de checklist bekijken. Het is belangrijk op te merken dat veel van de controles worden voltooid aan het begin van het project en tijdens de planningsfase. Nadat de implementatie is uitgevoerd, kunnen eenvoudige wijzigingen op geïmplementeerde Azure-infrastructuur of SAP-softwarereleases complex worden.

Bekijk de checklist bij belangrijke mijlpalen tijdens uw project. Hierdoor u kleine problemen detecteren voordat ze grote problemen worden. Je hebt ook genoeg tijd om de noodzakelijke wijzigingen opnieuw te ontwerpen en te testen. Beschouw deze checklist niet als voltooid. Afhankelijk van uw situatie moet u mogelijk veel meer controles uitvoeren.

De checklist bevat geen taken die onafhankelijk zijn van Azure. Sap-toepassingsinterfaces worden bijvoorbeeld gewijzigd tijdens een overstap naar het Azure-platform of naar een hostingprovider.

Deze checklist kan ook worden gebruikt voor systemen die al zijn geïmplementeerd. Nieuwe functies, zoals Schrijfaccelerator- en beschikbaarheidszones en nieuwe VM-typen, zijn mogelijk toegevoegd sinds u hebt geïmplementeerd. Het is dus handig om de checklist regelmatig te bekijken om ervoor te zorgen dat u op de hoogte bent van nieuwe functies in het Azure-platform.

## <a name="project-preparation-and-planning-phase"></a>Projectvoorbereiding en planningsfase
Tijdens deze fase plant u de migratie van uw SAP-workload naar het Azure-platform. In deze fase moet u ten minste de volgende documenten maken en de volgende elementen van de migratie definiëren en bespreken:

1. Ontwerpdocument op hoog niveau. Dit document moet het daarop staan:
    - De huidige voorraad sap-componenten en -toepassingen en een doeltoepassingsvoorraad voor Azure.
    - Een responsibility assignment matrix (RACI) die de verantwoordelijkheden en opdrachten van de betrokken partijen definieert. Begin op een hoog niveau en werk naar meer gedetailleerde niveaus tijdens de planning en de eerste implementaties.
    - Een oplossingsarchitectuur op hoog niveau.
    - Een beslissing over welke Azure-regio's moeten worden geïmplementeerd. Zie de [lijst met Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/). Zie [Producten die beschikbaar](https://azure.microsoft.com/global-infrastructure/services/)zijn per regio voor meer informatie over welke services beschikbaar zijn in elke regio.
    - Een netwerkarchitectuur om verbinding te maken van on-premises naar Azure. Begin vertrouwd te raken met de [Virtual Datacenter-blauwdruk voor Azure.](https://docs.microsoft.com/azure/architecture/vdc/)
    - Beveiligingsprincipes voor het uitvoeren van bedrijfsgegevens met een hoge impact in Azure. Ga voor meer informatie over gegevensbeveiliging aan de slag met de [Azure-beveiligingsdocumentatie](https://docs.microsoft.com/azure/security/).
2.  Technisch ontwerpdocument. Dit document moet het daarop staan:
    - Een blokdiagram voor de oplossing.
    - De grootte van reken-, opslag- en netwerkcomponenten in Azure. Zie [SAP-ondersteuningsnotitie #1928533 voor](https://launchpad.support.sap.com/#/notes/1928533)SAP-formaat van Azure VM's.
    - Business continuity en disaster recovery architectuur.
    - Gedetailleerde informatie over versies van os-, DB-, kernel- en SAP-ondersteuningspakketten. Het is niet noodzakelijkerwijs waar dat elke OS-release die wordt ondersteund door SAP NetWeaver of S/4HANA wordt ondersteund op Azure VM's. Hetzelfde geldt voor DBMS releases. Controleer de volgende bronnen om SAP-releases, DBMS-releases en OS-releases uit te lijnen en zo nodig te upgraden om SAP- en Azure-ondersteuning te garanderen. U moet releasecombinaties laten ondersteunen door SAP en Azure om volledige ondersteuning te krijgen van SAP en Microsoft. Indien nodig moet u plannen voor het upgraden van sommige softwarecomponenten. Meer details over ondersteunde SAP-, OS- en DBMS-software worden hier gedocumenteerd:
        - [SAP-ondersteuningsnotitie #1928533](https://launchpad.support.sap.com/#/notes/1928533). In deze notitie worden de minimale OS-releases gedefinieerd die worden ondersteund op Azure VM's. Het definieert ook de minimale database releases die nodig zijn voor de meeste niet-HANA databases. Ten slotte biedt het de SAP-dimensionering voor SAP-ondersteunde Azure VM-typen.
        - [SAP-ondersteuningsnotitie #2015553](https://launchpad.support.sap.com/#/notes/2015553). In deze notitie wordt het ondersteuningsbeleid rond Azure-opslag- en ondersteuningsrelatie gedefinieerd dat nodig is met Microsoft.
        - [SAP-ondersteuningsnotitie #2039619](https://launchpad.support.sap.com/#/notes/2039619). In deze notitie wordt de Oracle-ondersteuningsmatrix voor Azure gedefinieerd. Oracle ondersteunt alleen Windows en Oracle Linux als gastbesturingssystemen op Azure voor SAP-workloads. Deze ondersteuningsinstructie is ook van toepassing op de SAP-toepassingslaag waarop SAP-exemplaren worden uitgevoerd. Oracle ondersteunt echter geen hoge beschikbaarheid voor SAP Central Services in Oracle Linux via Pacemaker. Als u een hoge beschikbaarheid voor ASCS op Oracle Linux nodig hebt, moet u SIOS Protection Suite voor Linux gebruiken. Zie SAP-ondersteuningsnotitie [#1662610 - Ondersteuningsgegevens voor SIOS Protection Suite voor Linux voor](https://launchpad.support.sap.com/#/notes/1662610)gedetailleerde SAP-certificeringsgegevens. Voor Windows wordt de door SAP ondersteunde Windows Server Failover Clustering-oplossing voor SAP Central Services ondersteund in combinatie met Oracle als de DBMS-laag.
        - [SAP-ondersteuningsnotitie #2235581](https://launchpad.support.sap.com/#/notes/2235581). Deze notitie biedt de ondersteuningsmatrix voor SAP HANA op verschillende OS-releases.
        - Sap HANA-ondersteunde Azure VM's en [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) worden vermeld op de [SAP-website.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        - [SAP-productbeschikbaarheidsmatrix](https://support.sap.com/en/).
        - [SAP support note #2555629 - SAP HANA 2.0 Dynamic Tiering – Hypervisor en Cloud Support](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP support note #1662610 - Ondersteuningsgegevens voor SIOS Protection Suite voor Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP notes voor andere SAP-specifieke producten.     
    - We raden strikte drielaagse ontwerpen aan voor SAP-productiesystemen. We raden niet aan om ASCS en/of DBMS- en/of app-servers op één VM te combineren. Het gebruik van multi-SID-clusterconfiguraties voor SAP Central Services wordt ondersteund op Windows-gastbesturingssystemen op Azure. Deze configuratie wordt echter niet ondersteund voor SAP Central Services op Linux-besturingssystemen op Azure. In deze artikelen vindt u documentatie voor het Windows-gast-besturingssysteemscenario:
        - [SAP ASCS/SCS-instantie multi-SID hoge beschikbaarheid met Windows Server Failover Clustering en gedeelde schijf op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [SAP ASCS/SCS-instantie multi-SID hoge beschikbaarheid met Windows Server Failover Clustering en bestandsshare op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Hoge beschikbaarheid en disaster recovery architectuur.
        - Bepaal op basis van RTO en RPO hoe de architectuur met hoge beschikbaarheid en disaster recovery eruit moet zien.
        - Controleer voor een hoge beschikbaarheid binnen een zone wat de gewenste DBMS te bieden heeft in Azure. De meeste DBMS-pakketten bieden synchrone methoden van een synchrone hot stand-by, die we aanbevelen voor productiesystemen. Controleer ook de SAP-gerelateerde documentatie voor verschillende databases, te beginnen met [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) en gerelateerde documenten.
           Het gebruik van Windows Server Failover clustering met een gedeelde schijfconfiguratie voor de DBMS-laag, zoals bijvoorbeeld [beschreven voor SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), wordt niet ondersteund. Gebruik in plaats daarvan oplossingen zoals:
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA-systeemreplicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Bekijk de oplossingen die worden aangeboden door verschillende DBMS-leveranciers voor herstel bij noodgevallen in Azure-regio's. De meeste van hen ondersteunen asynchrone replicatie of logboekverzending.
        - Bepaal voor de SAP-toepassingslaag of u uw bedrijfsregressietestsystemen uitvoert, die idealiter replica's zijn van uw productie-implementaties, in dezelfde Azure-regio of in uw DR-regio. In het tweede geval u dat bedrijfsregressiesysteem targeten als het DR-doel voor uw productie-implementaties.
        - Als u besluit de niet-productiesystemen niet in de DR-site te plaatsen, kijkt u naar Azure Site Recovery als een methode voor het repliceren van de SAP-toepassingslaag in het Azure DR-gebied. Zie voor meer informatie een [herstel voor rampspoed instellen voor een implementatie van een SAP NetWeaver-app met meerdere lagen](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap).
        - Als u besluit een gecombineerde HADR-configuratie te gebruiken met [azure-beschikbaarheidszones,](https://docs.microsoft.com/azure/availability-zones/az-overview)maakt u kennis met de Azure-regio's waar beschikbaarheidszones beschikbaar zijn. Houd ook rekening met beperkingen die kunnen worden ingevoerd door verhoogde netwerklatencies tussen twee beschikbaarheidszones.  
3.  Een inventarisatie van alle SAP interfaces (SAP en niet-SAP).
4.  Ontwerp van funderingsdiensten. Dit ontwerp moet de volgende punten bevatten:
    - Active Directory- en DNS-ontwerp.
    - Netwerktopologie binnen Azure en toewijzing van verschillende SAP-systemen.
    - [Op rollen gebaseerde toegangsstructuur](https://docs.microsoft.com/azure/role-based-access-control/overview) voor teams die infrastructuur en SAP-toepassingen in Azure beheren.
    - Topologie van resourcegroepen.
    - [Tagging strategie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Naamgevingsconventies voor VM's en andere infrastructuurcomponenten en/of logische namen.
5.  Microsoft Premier Support-contract. Identificeer uw Microsoft Technical Account Manager (TAM). Zie [SAP-ondersteuningsnotitie #2015553](https://launchpad.support.sap.com/#/notes/2015553)voor SAP-ondersteuningsvereisten.
6.  Het aantal Azure-abonnementen en het kernquotum voor de abonnementen. [Open ondersteuningsaanvragen om de quota van Azure-abonnementen te verhogen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) als dat nodig is.
7.  Datareductie- en gegevensmigratieplan voor het migreren van SAP-gegevens naar Azure. Voor SAP NetWeaver-systemen heeft SAP richtlijnen voor het beperken van het volume van grote hoeveelheden gegevens. Bekijk [deze SAP-gids](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) over datamanagement in SAP ERP-systemen. Een deel van de inhoud is ook van toepassing op NetWeaver- en S/4HANA-systemen in het algemeen.
8.  Een geautomatiseerde implementatieaanpak. Het doel van de automatisering van infrastructuurimplementaties op Azure is om op een deterministische manier te implementeren en deterministische resultaten te krijgen. Veel klanten gebruiken PowerShell- of CLI-gebaseerde scripts. Maar er zijn verschillende open-source technologieën die u gebruiken om Azure-infrastructuur voor SAP te implementeren en zelfs SAP-software te installeren. Voorbeelden vindt u op GitHub:
    - [Geautomatiseerde SAP-implementaties in Azure Cloud](https://github.com/Azure/sap-hana)
    - [SAP HANA-installatie](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definieer een regelmatige ontwerp- en implementatierevisiecadans tussen u als klant, de systeemintegrator, Microsoft en andere betrokken partijen.

 
## <a name="pilot-phase-strongly-recommended"></a>Proeffase (sterk aanbevolen)
 
U een pilot uitvoeren voor of tijdens de projectplanning en -voorbereiding. U de pilotfase ook gebruiken om benaderingen en ontwerpen te testen die tijdens de plannings- en voorbereidingsfase zijn gemaakt. En je de pilotfase uitbreiden om er een echte proof of concept van te maken.

We raden u aan een volledige HADR-oplossing en beveiligingsontwerp in te stellen en te valideren tijdens een pilot-implementatie. Sommige klanten voeren schaalbaarheidstests uit tijdens deze fase. Andere klanten gebruiken implementaties van SAP sandbox-systemen als pilotfase. We gaan ervan uit dat u al een systeem hebt geïdentificeerd dat u wilt migreren naar Azure voor de pilot.

1. Gegevensoverdracht naar Azure optimaliseren. De optimale keuze is sterk afhankelijk van het specifieke scenario. Overdracht van on-premises via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) is het snelst als het ExpressRoute-circuit voldoende bandbreedte heeft. In andere scenario's is het overbrengen via het internet sneller.
2. Voor een heterogene SAP-platformmigratie waarbij gegevens worden geëxporteerd en geïmporteerd, worden de export- en importfasen getest en geoptimaliseerd. Voor grote migraties waarbij SQL Server het doelplatform is, vindt u [aanbevelingen.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070) U migratiemonitor/SWPM gebruiken als u geen gecombineerde release-upgrade nodig hebt. U het [SAP DMO-proces](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) gebruiken wanneer u de migratie combineert met een SAP-release-upgrade. Om dit te doen, moet u voldoen aan bepaalde vereisten voor de bron- en doelcombinatie van dbms-platforms. Dit proces is gedocumenteerd in [Database Migration Option (DMO) van SOM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exporteer naar bron, exporteer het uploaden van bestanden naar Azure en importeer prestaties. Maximaliseer de overlap tussen exporteren en importeren.
   2.  Evalueer het volume van de database op de doel- en bestemmingsplatforms ten behoeve van infrastructuurgrootte.
   3.  Timing valideren en optimaliseren.
1. Technische validatie.
   1. VM-typen.
        - Bekijk de bronnen in SAP-ondersteuningsnotities, in de SAP HANA-hardwaremap en in het SAP PAM opnieuw. Zorg ervoor dat er geen wijzigingen zijn in ondersteunde VM's voor Azure, ondersteunde OS-releases voor die VM-typen en ondersteunde SAP- en DBMS-releases.
        - Valideer opnieuw de grootte van uw toepassing en de infrastructuur die u implementeert op Azure. Als u bestaande toepassingen verplaatst, u vaak de benodigde SAPS ontlenen aan de infrastructuur die u gebruikt en de [SAP-benchmarkwebpagina](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) en deze vergelijken met de SAPS-nummers die worden vermeld in [SAP-ondersteuningsnotitie #1928533](https://launchpad.support.sap.com/#/notes/1928533). Houd [ook dit artikel over SAPS-classificaties](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) in gedachten.
        - Evalueer en test de grootte van uw Azure VM's met betrekking tot maximale opslagdoorvoer en netwerkdoorvoer van de VM-typen die u tijdens de planningsfase hebt gekozen. De gegevens vindt u hier:
           -  [Groottes voor virtuele Windows-machines in Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Het is belangrijk om rekening te houden met de *maximale schijfdoorvoer zonder cache* voor het dimensioneren.
           -  [Formaten voor virtuele Linux-machines in Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Het is belangrijk om rekening te houden met de *maximale schijfdoorvoer zonder cache* voor het dimensioneren.
   2. Opslag.
        - Gebruik minimaal [Azure Standard SSD-opslag](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) voor VM's die SAP-toepassingslagen vertegenwoordigen en voor implementatie van DMS's die niet prestatiegevoelig zijn.
        - Over het algemeen raden we het gebruik van [Azure Standard HDD-schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)niet aan.
        - Gebruik [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) voor dbms-vm's die op afstand prestatiegevoelig zijn.
        - Gebruik [door Azure beheerde schijven](https://azure.microsoft.com/services/managed-disks/).
        - Gebruik Azure Write Accelerator voor DBMS-logboekstations met M-serie. Wees je bewust van Write Accelerator grenzen en het gebruik, zoals gedocumenteerd in [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - Controleer voor de verschillende DBMS-typen de [generieke SAP-gerelateerde DBMS-documentatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) en de DBMS-specifieke documentatie waarnaar het generieke document verwijst.
        - Zie [SAP HANA-infrastructuurconfiguraties en -bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)voor meer informatie over SAP HANA.
        - Monteer azure-gegevensschijven nooit op een Azure Linux-vm met behulp van de apparaat-id. Gebruik in plaats daarvan de universeel unieke id (UUID). Wees voorzichtig wanneer u bijvoorbeeld grafische hulpprogramma's gebruikt om Azure-gegevensschijven te monteren. Controleer de vermeldingen in /etc/fstab om er zeker van te zijn dat de UUID wordt gebruikt om de schijven te monteren. Meer details vindt u in [dit artikel.](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Netwerken.
        - Test en evalueer uw virtuele netwerkinfrastructuur en de distributie van uw SAP-toepassingen over of binnen de verschillende virtuele Azure-netwerken.
        -  Evalueer de aanpak van virtuele netwerkarchitectuur met hub en spoke of de microsegmentatiebenadering binnen één virtueel Azure-netwerk. Baseer deze evaluatie op:
               1. Kosten voor gegevensuitwisseling tussen [gepeerde Azure virtuele netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Zie Prijzen van virtuele netwerken voor informatie over de [kosten.](https://azure.microsoft.com/pricing/details/virtual-network/)
               2. Voordelen van een snelle ontkoppeling van het peering tussen Azure virtuele netwerken in tegenstelling tot het wijzigen van de netwerkbeveiligingsgroep om een subnet binnen een virtueel netwerk te isoleren. Deze evaluatie is voor gevallen waarin toepassingen of VM's gehost in een subnet van het virtuele netwerk werd een beveiligingsrisico.
                3. Centrale logboekregistratie en controle van netwerkverkeer tussen on-premises, de buitenwereld en het virtuele datacenter dat u in Azure hebt ingebouwd.
        - Evalueer en test het gegevenspad tussen de SAP-toepassingslaag en de SAP DBMS-laag.
            -  Plaatsing van [virtuele Azure-netwerkapparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatiepad tussen de SAP-toepassing en de DBMS-laag van SAP-systemen op basis van SAP NetWeaver, Hybris of S/4HANA wordt niet ondersteund.
            -  Plaatsing van de SAP-toepassingslaag en SAP DBMS in verschillende virtuele Azure-netwerken die niet worden peered, wordt niet ondersteund.
            -  U [toepassingsbeveiligingsgroep- en netwerkbeveiligingsgroepregels](https://docs.microsoft.com/azure/virtual-network/security-overview) gebruiken om routes te definiëren tussen de SAP-toepassingslaag en de SAP DBMS-laag.
        - Controleer of [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de VM's die worden gebruikt in de SAP-toepassingslaag en de SAP DBMS-laag. Houd er rekening mee dat er verschillende OS-niveaus nodig zijn om versnelde netwerken in Azure te ondersteunen:
            - Windows Server 2012 R2 of nieuwer.
            - SUSE Linux 12 SP3 of hoger.
            - RHEL 7.4 of hoger.
            - Oracle Linux 7.5. Als u de RHCKL-kernel gebruikt, is het vereist om 3.10.0-862.13.1.el7 vrij te geven. Als u de Oracle UEK-kernel gebruikt, is versie 5 vereist.
        - Test en evalueer de netwerklatentie tussen de SAP-toepassingslaag VM's en DBMS's volgens SAP-ondersteuningsnotities [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Evalueer de resultaten aan de hand van de netwerklatentierichtlijnen in [SAP-ondersteuningsnotitie #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerklatentie moet zich in het matige of goede bereik bevinden. Uitzonderingen zijn van toepassing op verkeer tussen VM's en HANA Large Instance-eenheden, zoals gedocumenteerd in [dit artikel.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
        - Controleer of ILB-implementaties zijn ingesteld om Direct Server Return te gebruiken. Met deze instelling wordt de latentie verminderd wanneer Azure ID's worden gebruikt voor configuraties met hoge beschikbaarheid op de DBMS-laag.
        - Als u Azure Load Balancer samen met Linux-gastbesturingssystemen gebruikt, controleert u of de Linux-netwerkparameter **net.ipv4.tcp_timestamps** is ingesteld op **0**. Deze aanbeveling is in strijd met aanbevelingen in oudere versies van [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421). De SAP-notitie is nu bijgewerkt om aan te geven dat deze parameter moet worden ingesteld op **0** om te werken met Azure-loadbalancers.
        - Overweeg [Azure-nabijheidsplaatsingsgroepen](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) te gebruiken om optimale netwerklatentie te krijgen. Zie [Azure proximity placementgroepen voor optimale netwerklatentie met SAP-toepassingen voor](sap-proximity-placement-scenarios.md)meer informatie.
   4. Implementaties met hoge beschikbaarheid en noodherstel.
        - Als u de SAP-toepassingslaag implementeert zonder een specifieke Azure-beschikbaarheidszone te definiëren, moet u ervoor zorgen dat alle VM's met SAP-dialoogvensters of middleware-exemplaren van één SAP-systeem worden geïmplementeerd in een [beschikbaarheidsset.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)
        - Als u geen hoge beschikbaarheid nodig hebt voor SAP Central Services en de DBMS, u deze VM's implementeren in dezelfde beschikbaarheidsset als de SAP-toepassingslaag.
        - Als u SAP Central Services en de DBMS-laag beschermt voor hoge beschikbaarheid met passieve replicatie, plaatst u de twee knooppunten voor SAP Central Services in een afzonderlijke beschikbaarheidsset en de twee DBMS-knooppunten in een andere beschikbaarheidsset.
        - Als u implementeert in Azure Availability Zones, u geen beschikbaarheidssets gebruiken. Maar u moet er wel voor zorgen dat u de actieve en passieve Central Services-knooppunten implementeert in twee verschillende beschikbaarheidszones. Gebruik beschikbaarheidszones met de laagste latentie tussen deze zones.
          Houd er rekening mee dat u [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) moet gebruiken voor de use case van het instellen van Windows- of Pacemaker-failoverclusters voor de laag DBMS en SAP Central Services in beschikbaarheidszones. U [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) niet gebruiken voor zonale implementaties.
   5. Time-outinstellingen.
        - Controleer de SAP NetWeaver-ontwikkelaarssporen van de SAP-exemplaren om te controleren of er geen verbindingsonderbrekingen zijn tussen de wachtrijserver en de SAP-werkprocessen. U deze verbindingsonderbrekingen voorkomen door deze twee registerparameters in te stellen:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Zie [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))voor meer informatie.
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Zie [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))voor meer informatie.
        - Als u wilt voorkomen dat GUI-time-outs tussen on-premises SAP GUI-interfaces en SAP-toepassingslagen die in Azure zijn geïmplementeerd, wordt gecontroleerd of deze parameters zijn ingesteld in de standaard.pfl of het instantieprofiel:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Om onderbreking van gevestigde verbindingen tussen het SAP-wachtrijproces en de SAP-werkprocessen te voorkomen, moet u de parameter **enque/encni/set_so_keepalive** op **true**instellen. Zie ook [SAP nota #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Als u een Windows-failoverclusterconfiguratie gebruikt, moet u ervoor zorgen dat de tijd om te reageren op niet-responsieve knooppunten correct is ingesteld voor Azure. In het artikel [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) worden parameters weergegeven en hoe deze van invloed zijn op failovergevoeligheden. Ervan uitgaande dat de clusterknooppunten zich in hetzelfde subnet bevinden, moet u de volgende parameters wijzigen:
            - SamesubnetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. BE-instellingen of patches
        - Voor het uitvoeren van HANA op SAP, lees deze notities en documentatie:
            -   [SAP-ondersteuningsnotitie #2814271 - SAP HANA Backup mislukt op Azure met Checksum-fout](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP-ondersteuningsnotitie #2753418 - Mogelijke prestatiedegradatie door timerterugval](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP-ondersteuningsnotitie #2791572 - Prestatiedegradatie vanwege ontbrekende VDSO-ondersteuning voor Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP-ondersteuningsnotitie #2382421 - De netwerkconfiguratie optimaliseren op HANA- en OS-niveau](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP support note #2694118 - Red Hat Enterprise Linux HA Add-On op Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP support note #1984787 - SUSE LINUX Enterprise Server 12: Installatienotities](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installatie en upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP support note #2292690 - SAP HANA DB: Aanbevolen OS-instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP-ondersteuningsnotitie #2772999 - Red Hat Enterprise Linux 8.x: Installatie en configuratie](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP support note #2777782 - SAP HANA DB: Aanbevolen OS-instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP-ondersteuningsnotitie #2578899 - SUSE Linux Enterprise Server 15: Installatienotitie](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP support note #2455582 - Linux: Running SAP applicaties compiled with GCC 6.x SAP support note - Linux: Running SAP applications compiled with GCC 6.x SAP support note - Linux: Running SAP applications compiled with GCC 6.x SAP support note -](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP-ondersteuningsnotitie #2729475 - HWCCT mislukt met fout "Hypervisor wordt niet ondersteund" op Azure VM's die zijn gecertificeerd voor SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Test uw procedures voor hoge beschikbaarheid en noodherstel.
   1. Simuleer failoversituaties door VM's (Windows-gastbesturingssystemen) af te sluiten of besturingssystemen in paniekmodus te zetten (Linux-gastbesturingssystemen). Met deze stap u achterhalen of uw failoverconfiguraties werken zoals ontworpen.
   1. Meet hoe lang het duurt om een failover uit te voeren. Als de tijden te lang zijn, overweeg dan:
        - Gebruik voor SUSE Linux SBD-apparaten in plaats van de Azure Fence-agent om failover te versnellen.
        - Voor SAP HANA, als het herladen van gegevens te lang duurt, overwegen om meer opslagbandbreedte in te richten.
   3. Test uw back-up / herstel volgorde en timing en correcties aanbrengen als je nodig hebt. Zorg ervoor dat de back-uptijden voldoende zijn. U moet ook de herstel- en tijdherstelactiviteiten testen. Zorg ervoor dat hersteltijden binnen uw RTO-SLA's liggen, waar uw RTO ook vertrouwt op een database- of VM-herstelproces.
   4. Test de DR-functionaliteit en architectuur in verschillende regio's.
1. Veiligheidscontroles.
   1. Test de geldigheid van uw RBAC-architectuur (Azure role-based access control). Het doel is om de toegang en machtigingen van verschillende teams te scheiden en te beperken. SAP Basis-teamleden moeten bijvoorbeeld VM's kunnen implementeren en schijven uit Azure-opslag kunnen toewijzen aan een bepaald virtueel Azure-netwerk. Maar het SAP Basis-team mag niet in staat zijn om zijn eigen virtuele netwerken te maken of de instellingen van bestaande virtuele netwerken te wijzigen. Leden van het netwerkteam mogen vm's niet kunnen implementeren in virtuele netwerken waarin SAP-toepassingen en DBMS-VM's worden uitgevoerd. Ook mogen leden van dit team geen kenmerken van VM's kunnen wijzigen of zelfs VM's of schijven kunnen verwijderen.  
   1.  Controleer of [de netwerkbeveiligingsgroep en ASC-regels](https://docs.microsoft.com/azure/virtual-network/security-overview) naar verwachting werken en bescherm de beveiligde bronnen.
   1.  Zorg ervoor dat alle bronnen die moeten worden versleuteld, zijn versleuteld. Definieer en implementeer processen om een back-up van certificaten te maken, deze certificaten op te slaan en te openen en de versleutelde entiteiten te herstellen.
   1.  Gebruik [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) voor OS-schijven waar mogelijk vanuit het oogpunt van OS-ondersteuning.
   1.  Zorg ervoor dat u niet te veel versleutelingslagen gebruikt. In sommige gevallen is het zinvol om Azure Disk Encryption te gebruiken samen met een van de DBMS Transparent Data Encryption-methoden om verschillende schijven of componenten op dezelfde server te beschermen.  Op een SAP DBMS-server kan bijvoorbeeld de Azure Disk Encryption (ADE) worden ingeschakeld op de opstartschijf van het besturingssysteem (als het besturingssysteem ADE ondersteunt) en die gegevensschijf(s) die niet worden gebruikt door de DBMS-gegevenspersistentiebestanden.  Een voorbeeld is het gebruik van ADE op de schijf met de DBMS TDE-coderingssleutels.
1. Prestatietesten. In SAP, gebaseerd op SAP tracing en metingen, maak je deze vergelijkingen:
   - Vergelijk waar van toepassing de top 10 online rapporten met uw huidige implementatie.
   - Vergelijk waar van toepassing de top 10 batchtaken met uw huidige implementatie.
   - Vergelijk gegevensoverdrachtvia interfaces in het SAP-systeem. Focus op interfaces waarvan u weet dat de overdracht tussen verschillende locaties verloopt, zoals van on-premises naar Azure.


## <a name="non-production-phase"></a>Niet-productiefase 
In deze fase gaan we ervan uit dat u na een succesvolle pilot of proof of concept (POC) beginnen met het implementeren van niet-productie SAP-systemen in Azure. Neem alles wat je hebt geleerd en ervaren tijdens de POC op in deze implementatie. Alle criteria en stappen die voor POC's worden vermeld, zijn ook van toepassing op deze implementatie.

Tijdens deze fase implementeert u meestal ontwikkelingssystemen, testsystemen voor eenheden en testsystemen voor bedrijfsregressies in Azure. We raden aan dat ten minste één niet-productiesysteem in één SAP-toepassingslijn de volledige configuratie met hoge beschikbaarheid heeft die het toekomstige productiesysteem zal hebben. Hier volgen enkele aanvullende stappen die u in deze fase moet voltooien:  

1.  Voordat u systemen van het oude platform naar Azure verplaatst, verzamelt u bronverbruiksgegevens, zoals CPU-gebruik, opslagdoorvoer en IOPS-gegevens. Verzamel deze gegevens vooral uit de DBMS-laageenheden, maar verzamel ze ook uit de toepassingslaageenheden. Meet ook de netwerk- en opslaglatentie.
2.  Neem de gebruikstijdpatronen van uw systemen op. Het doel is om erachter te komen of niet-productiesystemen de hele dag beschikbaar moeten zijn, elke dag, of dat er niet-productiesystemen zijn die in bepaalde fasen van een week of maand kunnen worden uitgeschakeld.
3.  Test en bepaal of u uw eigen BE-afbeeldingen voor uw VM's in Azure wilt maken of dat u een afbeelding wilt gebruiken in de Azure Shared Image Gallery. Als u een afbeelding uit de gedeelde afbeeldingsgalerie gebruikt, moet u een afbeelding gebruiken die het ondersteuningscontract met uw leverancier van het besturingssysteem weerspiegelt. Voor sommige leveranciers van besturingssysteem u met Shared Image Gallery uw eigen licentieafbeeldingen meenemen. Voor andere OS-afbeeldingen is ondersteuning opgenomen in de prijs die door Azure wordt geciteerd. Als u besluit uw eigen besturingssysteemafbeeldingen te maken, u documentatie vinden in deze artikelen:
    -   [Een algemene afbeelding van een Windows-VM maken die in Azure is geïmplementeerd](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Een algemene afbeelding van een Linux-VM maken die in Azure is geïmplementeerd](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Als u SUSE- en Red Hat Linux-afbeeldingen uit de Shared Image Gallery gebruikt, moet u de afbeeldingen voor SAP gebruiken die door de Linux-leveranciers worden geleverd in de Shared Image Gallery.
4.  Zorg ervoor dat u voldoet aan de VEREISTEN voor SAP-ondersteuning voor Ondersteuningsovereenkomsten van Microsoft. Zie [SAP-ondersteuningsnotitie #2015553](https://launchpad.support.sap.com/#/notes/2015553). Zie [Onboarding-vereisten voor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)HANA Large Instances .
4.  Zorg ervoor dat de juiste mensen [geplande onderhoudsmeldingen](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) ontvangen, zodat u de beste downtimes kiezen.
5.  Controleer regelmatig op Azure-presentaties op kanalen zoals [Kanaal 9](https://channel9.msdn.com/) op nieuwe functionaliteit die mogelijk van toepassing is op uw implementaties.
6.  Controleer SAP-notities met betrekking tot Azure, zoals [ondersteuningsnotitie #1928533](https://launchpad.support.sap.com/#/notes/1928533), voor nieuwe VM SKU's en nieuw ondersteunde OS- en DBMS-releases. Vergelijk de prijzen van nieuwe VM-typen met die van oudere VM-typen, zodat u VM's implementeren met de beste prijs-prestatieverhouding.
7.  Controleer de SAP-ondersteuningsnotities, de SAP HANA-hardwaremap en de SAP PAM opnieuw. Zorg ervoor dat er geen wijzigingen zijn aangebracht in ondersteunde VM's voor Azure, ondersteunde OS-releases op die VM's en ondersteunde SAP- en DBMS-releases.
8.  Kijk op [de SAP-website](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor nieuwe HANA-gecertificeerde SKU's in Azure. Vergelijk de prijzen van nieuwe SKU's met de prijzen die u van plan was te gebruiken. Breng uiteindelijk noodzakelijke wijzigingen aan om degenen te gebruiken die de beste prijs-prestatieverhouding hebben.
9.  Pas uw implementatiescripts aan om nieuwe VM-typen te gebruiken en nieuwe Azure-functies op te nemen die u wilt gebruiken.
10. Test en evalueer na implementatie van de infrastructuur de netwerklatentie tussen SAP-toepassingslaag VM's en DBMS VM's, volgens SAP-ondersteuningsnotities [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Evalueer de resultaten aan de hand van de netwerklatentierichtlijnen in [SAP-ondersteuningsnotitie #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerklatentie moet zich in het matige of goede bereik bevinden. Uitzonderingen zijn van toepassing op verkeer tussen VM's en HANA Large Instance-eenheden, zoals gedocumenteerd in [dit artikel.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) Zorg ervoor dat geen van de beperkingen die worden genoemd in [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) en [SAP HANA-infrastructuurconfiguraties en -bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) van toepassing zijn op uw implementatie.
11. Zorg ervoor dat uw VM's zijn geïmplementeerd in de juiste [Azure-plaatsingsgroep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)voor nabijheid, zoals beschreven in [Azure-nabijheidsplaatsingsgroepen voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md)
11. Voer alle andere controles uit die worden vermeld voor de proof of conceptfase voordat de werkbelasting wordt toegepast.
12. Terwijl de werkbelasting van toepassing is, registreert u het resourceverbruik van de systemen in Azure. Vergelijk dit verbruik met records van uw oude platform. Pas vm-grootte van toekomstige implementaties aan als u ziet dat u grote verschillen hebt. Houd er rekening mee dat wanneer u de grootte, opslag en netwerkbandbreedtes van VM's inkrimpen, ook wordt verminderd.
    - [Grootten voor Windows Virtual Machines in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Grootten voor Linux Virtual Machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimenteer met systeemkopieerfunctionaliteit en -processen. Het doel is om het u gemakkelijk te maken om een ontwikkelingssysteem of een testsysteem te kopiëren, zodat projectteams snel nieuwe systemen kunnen krijgen. Overweeg [sap LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) te gebruiken voor deze taken.
14. Optimaliseer en verbeter de Azure-functiegebaseerde toegang, machtigingen en processen van uw team om ervoor te zorgen dat u takenscheiding hebt. Controleer er tegelijkertijd voor dat alle teams hun taken kunnen uitvoeren in de Azure-infrastructuur.
15. Oefen, test en documenteer procedures met hoge beschikbaarheid en noodherstel om uw personeel in staat te stellen deze taken uit te voeren. Vergeen tekortkomingen en pas nieuwe Azure-functionaliteit aan die u integreert in uw implementaties.

 
## <a name="production-preparation-phase"></a>Productievoorbereidingsfase 
Verzamel in deze fase wat u hebt ervaren en geleerd tijdens uw niet-productie-implementaties en pas deze toe op toekomstige productie-implementaties. U moet ook het werk van de gegevensoverdracht tussen uw huidige hostinglocatie en Azure voorbereiden.

1.  Voltooi de benodigde SAP-release-upgrades van uw productiesystemen voordat u overstapt naar Azure.
1.  Spreek het met de ondernemers over functionele en zakelijke tests die moeten worden uitgevoerd na de migratie van het productiesysteem.
1.  Zorg ervoor dat deze tests zijn voltooid met de bronsystemen op de huidige hostinglocatie. Vermijd het uitvoeren van tests voor de eerste keer nadat het systeem is verplaatst naar Azure.
1.  Test het proces van het migreren van productiesystemen naar Azure. Als u niet alle productiesystemen in hetzelfde tijdsbestek naar Azure verplaatst, bouwt u groepen productiesystemen die zich op dezelfde hostinglocatie moeten bevinden. Gegevensmigratie testen. Hier zijn enkele veelvoorkomende methoden:
    - Gebruik DBMS-methoden zoals back-up/herstel in combinatie met SQL Server Always On, HANA-systeemreplicatie of Logboekverzending om database-inhoud in Azure te zaaien en te synchroniseren.
    - Gebruik back-up/herstel voor kleinere databases.
    - Gebruik SAP Migration Monitor, die is geïntegreerd in SAP SWPM, om heterogene migraties uit te voeren.
    - Gebruik het [SAP DMO-proces](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) als u uw migratie wilt combineren met een SAP-release-upgrade. Houd er rekening mee dat niet alle combinaties van bron DBMS en doel DBMS worden ondersteund. Meer informatie vindt u in de specifieke SAP support notes voor de verschillende releases van DMO. Bijvoorbeeld, [Database Migratie Optie (DMO) van SOM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Test of de doorvoer van gegevensoverdracht beter is via het internet of via ExpressRoute, voor het geval u back-ups of SAP-exportbestanden moet verplaatsen. Als u gegevens via internet verplaatst, moet u mogelijk een aantal regels voor de netwerkbeveiligingsgroep/toepassingsbeveiligingsgroep wijzigen die u moet hebben voor toekomstige productiesystemen.
1.  Voordat u systemen van uw oude platform naar Azure verplaatst, verzamelt u gegevens over het verbruik van resources. Nuttige gegevens zijn CPU-gebruik, opslagdoorvoer en IOPS-gegevens. Verzamel deze gegevens vooral uit de DBMS-laageenheden, maar verzamel ze ook uit de toepassingslaageenheden. Meet ook de netwerk- en opslaglatentie.
1.  Controleer de SAP-ondersteuningsnotities en de vereiste OS-instellingen, de SAP HANA-hardwaremap en de SAP PAM opnieuw. Zorg ervoor dat er geen wijzigingen zijn aangebracht in ondersteunde VM's voor Azure, ondersteunde OS-releases in die VM's en ondersteunde SAP- en DBMS-releases.
1.  Werk implementatiescripts bij om rekening te houden met de nieuwste beslissingen die u hebt genomen over VM-typen en Azure-functionaliteit.
1.  Na het implementeren van infrastructuur en toepassingen, bevestigt u dat:
    - De juiste VM-typen zijn geïmplementeerd, met de juiste kenmerken en opslagformaten.
    - De VM's zijn op de juiste en gewenste OS releases en patches en zijn uniform.
    - VM's worden gehard zoals vereist en op een uniforme manier.
    - De juiste applicatie releases en patches werden geïnstalleerd en geïmplementeerd.
    - De VM's zijn volgens plan geïmplementeerd in Azure-beschikbaarheidssets.
    - Azure Premium Storage wordt gebruikt voor latentiegevoelige schijven of waar de [single-VM SLA van 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) vereist is.
    - Azure Write Accelerator is correct geïmplementeerd.
        - Zorg ervoor dat binnen de VM's opslagruimten of streepsets correct zijn gebouwd op schijven die Write Accelerator nodig hebben.
        - Controleer de [configuratie van software RAID op Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Controleer de [configuratie van LVM op Linux VM's in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    - [Azure beheerde schijven](https://azure.microsoft.com/services/managed-disks/) worden uitsluitend gebruikt.
    - VM's zijn geïmplementeerd in de juiste beschikbaarheidssets en beschikbaarheidszones.
    - [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de VM's die worden gebruikt in de SAP-toepassingslaag en de SAP DBMS-laag.
    - Er bevinden zich geen [virtuele Azure-netwerkapparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatiepad tussen de SAP-toepassing en de DBMS-laag van SAP-systemen op basis van SAP NetWeaver, Hybris of S/4HANA.
    - Toepassingsbeveiligingsgroep en netwerkbeveiligingsgroepregels maken communicatie mogelijk zoals gewenst en gepland en blokkeren waar nodig communicatie.
    - Time-outinstellingen zijn correct ingesteld, zoals eerder beschreven.
    - VM's worden geïmplementeerd in de juiste [Azure-plaatsingsgroep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)voor nabijheidsplaatsing, zoals beschreven in [Azure-nabijheidsplaatsingsgroepen voor optimale netwerklatentie met SAP-toepassingen](sap-proximity-placement-scenarios.md).
    - Netwerklatentie tussen SAP-toepassingslaag VM's en DBMS VM's wordt getest en gevalideerd zoals beschreven in SAP-ondersteuningsnotities [#500235](https://launchpad.support.sap.com/#/notes/500235) en [#1100926.](https://launchpad.support.sap.com/#/notes/1100926/E) Evalueer de resultaten aan de hand van de netwerklatentierichtlijnen in [SAP-ondersteuningsnotitie #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerklatentie moet zich in het matige of goede bereik bevinden. Uitzonderingen zijn van toepassing op verkeer tussen VM's en HANA Large Instance-eenheden, zoals gedocumenteerd in [dit artikel.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    - Encryptie werd waar nodig en met de juiste versleutelingsmethode geïmplementeerd.
    - Interfaces en andere toepassingen kunnen de nieuw geïmplementeerde infrastructuur met elkaar verbinden.
1.  Maak een draaiboek om te reageren op gepland Azure-onderhoud. Bepaal de volgorde waarin systemen en VM's opnieuw moeten worden opgestart voor gepland onderhoud.
    

## <a name="go-live-phase"></a>Go-live fase
Tijdens de go-live fase, zorg ervoor dat u de playbooks die u hebt ontwikkeld tijdens eerdere fasen te volgen. Voer de stappen uit die u hebt getest en geoefend. Accepteer geen last-minute wijzigingen in configuraties en processen. Voer ook de volgende stappen uit:

1. Controleer of Azure-portalbewaking en andere bewakingshulpprogramma's werken. Wij raden Windows Performance Monitor (perfmon) voor Windows en SAR voor Linux.
    - CPU tellers.
        - Gemiddelde CPU-tijd, totaal (alle CPU's)
        - Gemiddelde CPU-tijd, elke afzonderlijke processor (128 processors op M128 VM's)
        - CPU kernel tijd, elke individuele processor
        - CPU-gebruikerstijd, elke afzonderlijke processor
    - Geheugen.
        - Vrij geheugen
        - Geheugenpagina in/seconde
        - Geheugenpagina uit/seconde
    - Schijf.
        - Schijf lezen in KBps, per afzonderlijke schijf
        - Schijf leest/seconde, per afzonderlijke schijf
        - Schijf gelezen in microseconden/lezen, per afzonderlijke schijf
        - Schijf schrijven in KBps, per individuele schijf
        - Schijfschrijven/seconde, per afzonderlijke schijf
        - Schijf schrijven in microseconden/lezen, per individuele schijf
    - Netwerk.
        - Netwerkpakketten in/seconde
        - Netwerkpakketten uit/seconde
        - Netwerk KB in/seconde
        - Netwerk KB uit/seconde
1.  Voer na de gegevensmigratie alle validatietests uit die u met de bedrijfseigenaren hebt afgesproken. Accepteer alleen validatietestresultaten als u resultaten hebt voor de oorspronkelijke bronsystemen.
1.  Controleer of interfaces werken en of andere toepassingen kunnen communiceren met de nieuw geïmplementeerde productiesystemen.
1.  Controleer het transport- en correctiesysteem via SAP-transactie STMS.
1.  Databaseback-ups uitvoeren nadat het systeem is vrijgegeven voor productie.
1.  Vm-back-ups uitvoeren voor de VM's van de SAP-toepassingslaag nadat het systeem is vrijgegeven voor productie.
1.  Voor SAP-systemen die geen deel uitmaakten van de huidige go-live-fase, maar die communiceren met de SAP-systemen die u tijdens deze go-live-fase naar Azure hebt verplaatst, moet u de hostnaambuffer in SM51 opnieuw instellen. Als u dit doet, worden de oude IP-adressen in de cache verwijderd die zijn gekoppeld aan de namen van de toepassingsinstanties die u naar Azure hebt verplaatst.  


## <a name="post-production"></a>Postproductie
Deze fase gaat over het monitoren, bedienen en beheren van het systeem. Vanuit SAP-oogpunt zijn de gebruikelijke taken die u moest uitvoeren op uw oude hostinglocatie van toepassing. Voltooi deze Azure-specifieke taken ook:

1. Bekijk Azure-facturen voor systemen met hoge kosten.
2. Optimaliseer de prijs-prestatie-efficiëntie aan de VM-kant en de opslagzijde.
3. Optimaliseer de tijden waarop u systemen afsluiten.  


## <a name="next-steps"></a>Volgende stappen
Zie deze artikelen:

- [Azure Virtual Machines planning en implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

