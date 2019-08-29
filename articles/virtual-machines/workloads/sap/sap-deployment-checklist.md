---
title: Controle lijst voor SAP-werk belasting plannen en implementeren | Microsoft Docs
description: controle lijst voor het plannen en implementeren van SAP-workloads op Azure
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d6170dead4e8577cea6883ffea25b90ebe39b88
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078997"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>SAP-werk belasting op de Azure-planning en implementatie Controlelijst 

Deze controle lijst is ontworpen voor klanten die hun SAP NetWeaver-, S/4HANA-en Hybris-toepassingen verplaatsen naar een Azure-infra structuur als een service.  Deze controle lijst moet worden gecontroleerd door een klant en/of SAP-partner tijdens de duur van het project. Het is belang rijk dat veel van de controles aan het begin van het project en in de plannings fase worden uitgevoerd. Zodra de implementatie is voltooid, kunnen elementaire wijzigingen in de geïmplementeerde Azure-infra structuur of SAP-software versies ingewikkeld worden. Bekijk deze controle lijst op belang rijke mijl palen in een project.  Er kunnen kleine problemen worden gedetecteerd voordat ze grote problemen worden en er voldoende tijd is om de nood zakelijke wijzigingen te hergebruiken en te testen. De controle lijst op geen enkele manier om claims te volt ooien. Afhankelijk van uw situatie is het mogelijk dat er veel meer controles moeten worden uitgevoerd. 

De verzamelde controle lijst bevat geen taken die onafhankelijk zijn van Azure.  Voorbeeld: SAP-toepassings interfaces worden gewijzigd tijdens een overstap naar een open bare Azure-Cloud of een hosting provider.    

Deze controle lijst kan ook worden gebruikt voor reeds geïmplementeerde systemen. Nieuwe functies, zoals Write Accelerator, Beschikbaarheidszones en nieuwe VM-typen, zijn mogelijk toegevoegd sinds u hebt geïmplementeerd.  Daarom is het handig om de controle lijst regel matig te bekijken, zodat u op de hoogte bent van de nieuwe functies van het Azure-platform. 

## <a name="project-preparation-and-planning-phase"></a>Project voorbereiding en plannings fase
In deze fase wordt een migratie van SAP-werk belasting naar een open bare Azure-Cloud gepland. Het minimum aantal entiteiten en gediscussiete onderdelen en de gedefinieerde lijst, zoals:

1. Ontwerp document op hoog niveau: dit document moet het volgende bevatten:
    1. De huidige inventaris van SAP-onderdelen en-toepassingen en de inventaris van doel toepassingen op Azure
    2. Een RACI (verantwoordelijkheids toewijzings matrix) maken en gebruiken waarmee de verantwoordelijkheden en toewijzingen van de betrokken partijen worden gedefinieerd. Begin op het hoge niveau en werk met meer nauw keurige meer gedetailleerde niveaus door Voer de planning en eerste implementaties
    2. Een oplossings architectuur op hoog niveau
    3. Beslissing over Azure-regio's om in te implementeren. Controleer de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)voor een lijst met Azure-regio's. Voor services die beschikbaar zijn in elk van de Azure-regio's, controleert u de artikel [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/)
    4. Netwerk architectuur om vanuit on-premises verbinding te maken met Azure. Begin met het maken [van de blauw druk van Virtual Data Center voor Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Beveiligings principes voor het uitvoeren van belang rijke Bedrijfs gegevens in Azure. Begin met [Azure Security-documentatie](https://docs.microsoft.com/azure/security/) voor lees materiaal
2.  Technisch ontwerp document – dat bevat:
    1.  Een diagram van de oplossings blok 
    2.  Grootte van compute-, opslag-en netwerk onderdelen in Azure. Voor SAP-grootte van virtuele Azure-machines raadpleegt u SAP-ondersteunings opmerking [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Architectuur voor bedrijfs continuïteit en herstel na nood gevallen
    4.  Gedetailleerde versies van het besturings systeem, DB, kernel en SAP-ondersteunings pakket. Het is niet toegestaan dat elke besturingssysteem versie die wordt ondersteund door SAP NetWeaver of S/4HANA wordt ondersteund in azure Vm's. Hetzelfde geldt voor DBMS-releases. Het is verplicht om de volgende bronnen te laten uitlijnen en zo nodig upgrade SAP-releases, DBMS-releases of versies van het besturings systeem in een ondersteund SAP-en Azure-venster te maken. Het is verplicht dat u binnen SAP-en Azure-ondersteunde release combinaties bent om volledige ondersteuning door SAP en micro soft te krijgen. Indien nodig moet u een upgrade van sommige software onderdelen plannen. Meer informatie over ondersteunde SAP-, OS-en DBMS-software wordt beschreven op de volgende locaties:
        1.  SAP-ondersteunings opmerking [#1928533](https://launchpad.support.sap.com/#/notes/1928533). In deze opmerking worden de minimale OS-Releases gedefinieerd die worden ondersteund in azure Vm's. Ook worden de minimale database releases gedefinieerd die vereist zijn voor de meeste niet-HANA-data base. De notitie bevat ook de SAP-grootte van de verschillende door SAP ondersteunde Azure VM-typen.
        2.  SAP-ondersteunings opmerking [#2039619](https://launchpad.support.sap.com/#/notes/2039619). De notitie definieert de Oracle-ondersteunings matrix op Azure. Beseft dat Oracle alleen ondersteuning biedt voor Windows en Oracle Linux als gast besturingssysteem in azure voor SAP-workload. Deze ondersteunings verklaring is ook van toepassing op SAP-toepassings lagen met SAP-exemplaren. Oracle biedt echter geen ondersteuning voor hoge Beschik baarheid voor SAP Central-Services in Oracle Linux via pacemaker. Als u maximale Beschik baarheid voor ASCS op Oracle Linux nodig hebt, moet u gebruikmaken van de SIOS Protection Suite voor Linux. Voor gedetailleerde SAP-certificerings gegevens raadpleegt u SAP-ondersteuning opmerking [#1662610: ondersteunings Details voor de SIOS-beveiligings Suite voor Linux](https://launchpad.support.sap.com/#/notes/1662610). Voor Windows wordt de door SAP ondersteunde Windows failover cluster-failover-oplossing voor SAP Central-Services ondersteund in combi natie met Oracle als DBMS-laag. 
        3.  SAP-ondersteunings notitie [#2235581](https://launchpad.support.sap.com/#/notes/2235581) om de ondersteunings matrix voor SAP Hana te krijgen in de verschillende versies van het besturings systeem
        4.  SAP HANA ondersteunde Azure-Vm's en [Hana grote instanties](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) worden [hier](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) weer gegeven
        5.  [SAP-product beschikbaarheids matrix](https://support.sap.com/en/)
        6.  Andere SAP-opmerkingen voor andere SAP-specifieke producten  
    5.  We raden u aan om op te bieden van SAP-productie systemen strikte 3-tier ontwerpen. Het combi neren van ASCS +-APP-servers op dezelfde VM wordt niet aanbevolen.  Het gebruik van multi-SID-cluster configuraties voor SAP Central-Services wordt ondersteund door Windows als gast besturingssysteem in Azure. Terwijl multi-SID-cluster configuraties van SAP Central Services niet worden ondersteund met Linux-besturings systemen in Azure. Documentatie voor de Windows-gast besturingssysteem Case vindt u in:
        1.  [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en gedeelde schijf op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid met Windows Server Failover Clustering en file share op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Architectuur met hoge Beschik baarheid en herstel na nood gevallen
        1.  Definieer op basis van RTO en RPO waarvan de architectuur voor hoge Beschik baarheid en herstel na nood gevallen eruitziet
        2.  Voor hoge Beschik baarheid binnen dezelfde zone, controleert u wat de gewenste DBMS in azure moet aanbieden. De meeste DBMS bieden synchrone methoden van een synchrone hot standby-stand. deze worden aanbevolen voor productie systemen. Raadpleeg ook de SAP-gerelateerde documentatie voor de verschillende data bases, te beginnen met [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) en gerelateerde documenten
            1.  Het gebruik van de Windows failover cluster-service met de configuratie van de gedeelde schijf voor de DBMS-laag als bijvoorbeeld hier wordt beschreven voor SQL Server wordt [hier](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) **niet** ondersteund. In plaats daarvan worden oplossingen zoals:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA-systeem replicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Voor herstel na nood gevallen in verschillende Azure-regio's, controleert u welke mogelijkheden er worden geboden door de verschillende DBMS-leveranciers. De meeste hiervan ondersteunen asynchrone replicatie of logboek-verzen ding
        4.  Voor de SAP-toepassingslaag definieert u of u uw zakelijke regressie test systemen wilt uitvoeren, die in het ideale geval replica's zijn van uw productie-implementaties, in dezelfde Azure-regio of in uw DR-regio. In het laatste geval kunt u het regressie systeem van dat bedrijf richten als een DR-doel voor uw productie
        5.  Als u besluit de niet-productie systemen te plaatsen op de DR-site, kijkt u naar Azure Site Recovery als levensvat bare methode om de SAP-toepassingslaag te repliceren naar de Azure DR-regio. Zie ook [herstel na nood gevallen instellen voor een app-implementatie met SAP NetWeaver op meerdere lagen](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Als u besluit een gecombineerde HA/DR-configuratie te gebruiken, maakt het gebruik van [Azure-beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview) vertrouwd met de Azure-regio's waar Beschikbaarheidszones beschikbaar zijn en met beperkingen die kunnen worden geïntroduceerd door extra netwerk latentie tussen twee Beschikbaarheidszones  
3.  Klant/partner moet een inventarisatie maken van alle SAP-interfaces (SAP en niet-SAP). 
4.  Ontwerp van Foundation Services-ontwerp-dit ontwerp bevat items zoals
    1.  Active Directory-en DNS-ontwerp
    2.  Netwerk topologie binnen Azure en toewijzing van verschillende SAP-systemen
    3.  [Toegangs structuur op basis van rollen](https://docs.microsoft.com/azure/role-based-access-control/overview) voor uw verschillende teams die de infra structuur en SAP-toepassingen in azure beheren
    3.  Topologie van resource groep 
    4.  [Strategie voor Tags toevoegen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Naamgevings regels voor Vm's en andere infrastructuur onderdelen en/of logische namen
5.  Micro soft Premier Supporting-contract: Identificeer MS Technical Account Manager (TAM). Zie voor ondersteunings vereisten voor SAP-ondersteunings [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Definieer het aantal Azure-abonnementen en het kern quotum voor de verschillende abonnementen. [Open ondersteunings aanvragen om de quota van Azure-abonnementen indien nodig te verhogen](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 
7.  Gegevens reductie en gegevens migratie plan voor het migreren van SAP-gegevens naar Azure. SAP NetWeaver Systems bevat richt lijnen voor het beperken van het volume van een groot aantal gegevens. SAP heeft [deze progevondene-hand leiding](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) gepubliceerd over gegevens beheer in SAP ERP-systemen. Sommige inhoud is echter van toepassing op netweave en S/4HANA-systemen in het algemeen.
8.  Definieer en beslis de methode voor automatische implementatie. Doel van automatisering achter infrastructuur implementaties in Azure is om op een deterministische manier te implementeren en deterministische resultaten te krijgen. Veel klanten gebruiken scripts op basis van Power shell of CLI. Er zijn echter verschillende open-source technologieën die kunnen worden gebruikt voor het implementeren van Azure-infra structuur voor SAP en het installeren van SAP-software. Voor beelden vindt u in GitHub:
    1.  [Geautomatiseerde SAP-implementaties in azure Cloud](https://github.com/Azure/sap-hana)
    2.  [Installatie van SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definieer een regel matige ontwerp-en implementatie uitgebracht tussen u als klant, systeem integrator, micro soft en andere betrokken partijen

 
## <a name="pilot-phase-strongly-recommended"></a>Test fase (sterk aanbevolen)
 
De pilot kan worden uitgevoerd vóór of parallel aan project planning en-voor bereiding. De fase kan ook worden gebruikt om benaderingen en ontwerpen te testen die zijn aangebracht in de plannings-en voorbereidings fase. De test fase kan worden uitgerekt tot een echt bewijs van concepten. Het is raadzaam om een volledige HA/DR-oplossing en beveiligings ontwerp in te stellen en te valideren tijdens een proef implementatie. In sommige klant gevallen kunnen schaalbaarheids tests ook in deze fase worden uitgevoerd. Andere klanten gebruiken de implementatie van SAP-sandboxsystemen als pilot fase. We gaan ervan uit dat u een systeem hebt geïdentificeerd dat u wilt migreren naar Azure voor het uitvoeren van een pilot.

1. Optimaliseer de gegevens overdracht naar Azure. Zeer afhankelijk van klant gevallen die via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) van on-premises worden overgedragen, is het snelst als het snelle circuit voldoende band breedte heeft. Met andere klanten wordt het Internet teruggeleid om sneller te zijn
2. In het geval van een SAP-heterogene platform migratie, waarbij de gegevens van de Data Base worden geëxporteerd en geïmporteerd, de export-en import fase worden getest en geoptimaliseerd. Voor grote migraties met SQL Server als doel platform, kunt u [hier](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)aanbevelingen vinden. U kunt de benadering van de migratie monitor/SWPM maken voor het geval u geen gecombineerde release-upgrade of [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) -proces nodig hebt wanneer u de migratie combineert met een SAP-release-upgrade en bepaalde combi Naties van bron-en doel database platformen wilt uitvoeren als gedocumenteerd, bijvoorbeeld in [Data Base Migration Option (DMO) van SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exporteren naar bron, export bestand uploaden naar Azure en prestaties importeren.  De overlap ping tussen exporteren en importeren maximaliseren
   2.  Evalueer het volume van de data base tussen het doel en het doel platform om weer te geven in de infrastructuur grootte    
   3.  Timing valideren en optimaliseren 
3. Technische validatie 
   1. Soorten VM
      1.  Valideer de resources in SAP-ondersteunings notities, SAP HANA hardwarebronnen en SAP PAM opnieuw om er zeker van te zijn dat er geen wijzigingen zijn in ondersteunde Vm's voor Azure, ondersteunde versies van besturings systemen voor deze VM-typen en ondersteunde SAP-en DBMS-releases
      2.  Valideer opnieuw de grootte van uw toepassing en de infra structuur die u in azure implementeert. In het geval van het verplaatsen van bestaande toepassingen kunt u de benodigde SAP'S afleiden van de infra structuur die u gebruikt en de [SAP-benchmark pagina](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) en deze vergelijken met de sap's-nummers die worden vermeld in de SAP-ondersteunings opmerking [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Houd [dit artikel](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) ook in acht
      3.  Evalueer en test de grootte van uw Azure-Vm's met betrekking tot de maximale opslag doorvoer en netwerk doorvoer van de verschillende VM-typen die u in de plannings fase hebt gekozen. De gegevens vindt u in:
          1.  [Grootten voor virtuele Windows-machines in azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Het is belang rijk dat u rekening houdt met de Maxi maal beschik bare **schijf doorvoer** in de cache voor grootte
          2.  [Grootten voor virtuele Linux-machines in azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Het is belang rijk dat u rekening houdt met de Maxi maal beschik bare **schijf doorvoer** in de cache voor grootte
   2. Storage
      1.  [Azure Standard-SSD-opslag](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) gebruiken als mini maal voor VM'S die SAP-toepassings lagen vertegenwoordigen en voor niet-prestatie gevoelige DBMS-implementaties
      2.  U wordt aangeraden [Azure Standard-HDD-schijven](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) in het algemeen niet te gebruiken
      2.  [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) gebruiken voor de DBMS-vm's die op afstand gevoelig zijn
      2.  [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) gebruiken
      3.  Gebruik Azure Write Accelerator voor DBMS-logboek stations met een M-serie. Houd rekening met de limieten voor schrijf versneller en het gebruik zoals beschreven in [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Voor de verschillende DBMS-typen raadpleegt u de [algemene SAP gerelateerde DBMS-documentatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) en de DBMS-specifieke documentes van het algemene document waarnaar u kunt verwijzen
      5.  Voor SAP HANA worden meer details beschreven in [SAP Hana infrastructuur configuraties en-bewerkingen op Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Koppel nooit Azure-gegevens schijven aan een Azure Linux-VM met behulp van de apparaat-ID. Gebruik in plaats daarvan de UUID (Universally Unique Identifier). Wees voorzichtig wanneer u grafische hulpprogram ma's gebruikt om Azure-gegevens schijven te koppelen. Controleer de vermeldingen in bestand/etc/fstab om er zeker van te zijn dat de schijven zijn gekoppeld met behulp van de UUID
          1.  Meer informatie vindt [hier](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Netwerken
      1.  De VNet-infra structuur en de distributie van uw SAP-toepassingen op of binnen de verschillende virtuele Azure-netwerken testen en evalueren
          1.  Evalueer de aanpak van hub-en spoke Virtual Network-architectuur of microsegmentatie binnen één virtueel Azure-netwerk op basis van
              1.  Kosten als gevolg van gegevens uitwisseling tussen gepeerde [Azure VNets](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). [Virtual Network prijzen](https://azure.microsoft.com/pricing/details/virtual-network/) voor de kosten controleren
              2.  Voor deel van het snel verbreken van de peering tussen virtuele Azure-netwerken in vergelijking met het wijzigen van NSG om een subnet in een virtueel netwerk te isoleren voor gevallen waarin toepassingen of Vm's die worden gehost in een subnet van het virtuele netwerk een beveiligings risico vormen
              3.  Centrale logboek registratie en controle van netwerk verkeer tussen on-premises, buiten wereld en het virtuele Data Center dat u hebt gemaakt in azure
          2.  Evalueer en test het gegevenspad tussen SAP Application Layer en SAP DBMS-laag. 
              1.  Elke plaatsing van [virtuele Azure-netwerk apparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatie traject tussen de SAP-toepassing en de DBMS-laag van een SAP NetWeaver-, hybris-of S/4HANA-gebaseerde SAP-systemen wordt helemaal niet ondersteund
              2.  Het plaatsen van SAP Application Layer en SAP DBMS in verschillende virtuele netwerken van Azure die niet peered zijn, wordt niet ondersteund
              3.  [Azure ASG-en NSG-regels](https://docs.microsoft.com/azure/virtual-network/security-overview) worden ondersteund voor het definiëren van routes tussen de SAP Application Layer en de SAP DBMS-laag
          3.  Zorg ervoor dat [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de virtuele machines die worden gebruikt op de SAP-toepassingslaag en de SAP-DBMS-laag. Houd er bij dat er verschillende besturingssysteem niveaus nodig zijn voor de ondersteuning van versneld netwerken in Azure:
              1.  Windows Server 2012 R2 of nieuwere versies
              2.  SUSE Linux 12 SP3 of nieuwere versies
              3.  RHEL 7,4 of hoger
              4.  Oracle Linux 7,5. Met behulp van de RHCKL-kernel moet de release 3.10.0-862.13.1. EL7 zijn. U moet de Oracle UEK kernel release 5 gebruiken
          4.   Test en evalueer de netwerk latentie tussen SAP Application Layer-VM en DBMS-VM volgens SAP-ondersteuning opmerking [#500235](https://launchpad.support.sap.com/#/notes/500235) en SAP-ondersteunings opmerking [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Evalueer de resultaten op basis van de richt lijnen voor netwerk latentie van SAP-ondersteunings opmerking [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerk latentie moet in het gemiddelde en het goede bereik liggen. Uitzonde ringen zijn van toepassing op verkeer tussen virtuele machines en HANA grote instantie-eenheden, zoals [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) wordt beschreven
          5.   Zorg ervoor dat ILB-implementaties zijn ingesteld om direct server return te gebruiken. Met deze instelling wordt de latentie beperkt in gevallen waarin Azure ILBs worden gebruikt voor configuraties met hoge Beschik baarheid op de DBMS-laag
          6.   Als u Azure Load Balancer gebruikt in combi natie met Linux-gast besturingssystemen, controleert u of de Linux-netwerk parameter **net. IPv4. TCP _timestamps** is ingesteld op **0**. Voor de aanbevelingen in oudere versies van SAP Note [#2382421](https://launchpad.support.sap.com/#/notes/2382421). De SAP-Opmerking ondertussen wordt bijgewerkt met het feit dat de para meter moet worden ingesteld op 0 om samen te werken met Azure load balancers.
          7.   Overweeg het gebruik van [Azure proximity placement Group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md) om de meest optimale netwerk latentie te verkrijgen.
   4. Hoge Beschik baarheid en implementaties voor herstel na nood gevallen. 
      1. Als u de SAP-toepassingslaag implementeert zonder een specifieke Azure-beschikbaarheids zone te definiëren, moet u ervoor zorgen dat alle Vm's met SAP-dialoog instanties of middleware-exemplaren van één [](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)SAP-systeem worden geïmplementeerd in een beschikbaarheidsset. 
         1.   Als u geen hoge Beschik baarheid voor de SAP Central-Services en DBMS nodig hebt, kunnen deze virtuele machines worden geïmplementeerd in dezelfde Beschikbaarheidsset als de SAP-toepassingslaag
      2. Als u de SAP Central-Services en de DBMS-laag beveiligt voor maximale Beschik baarheid met passieve replica's, hebt u de twee knoop punten voor SAP Central-Services in één afzonderlijke Beschikbaarheidsset en het twee DBMS-knoop punt in een andere Beschikbaarheidsset
      3. Als u in Azure-beschikbaarheidszones implementeert, kunt u geen gebruik maken van beschikbaarheids sets. U moet er echter voor zorgen dat u de knoop punten actief en passieve centrale Services implementeert in twee verschillende Beschikbaarheidszones, waarin de kleinste latentie tussen zones wordt weer gegeven.
         1.   Houd er rekening mee dat u [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) moet gebruiken voor het maken van Windows-of pacemaker-failoverclusters voor de DBMS en SAP Central Services-laag voor alle Beschikbaarheidszones. [Basis Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) kan niet worden gebruikt voor zonegebonden-implementaties 
   5. Time-outinstellingen
      1. Controleer de SAP NetWeaver-ontwikkelaars traceringen van de verschillende SAP-instanties en zorg ervoor dat er geen verbindings onderbrekingen tussen de server in de wachtrij en de SAP-werk processen worden vermeld. Deze verbindings onderbrekingen kunnen worden vermeden door de volgende twee register parameters in te stellen:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000-Zie ook [dit artikel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000-Zie ook [dit artikel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Controleer of de volgende para meters zijn ingesteld in het standaard. PFL-of het exemplaar profiel om te voor komen dat GUI-time-outs tussen één-premises geïmplementeerde SAP-GUI-interface en SAP-toepassings lagen zijn geïmplementeerd in Azure:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. De para meter enque/encni/set_so_keepalive moet worden ingesteld op ' True ' om te voor komen dat de ingestelde verbindingen tussen het SAP-bewerkings proces en de SAP-workprocesse worden verstoord. Zie ook [SAP Note #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Als u een configuratie van een Windows-failovercluster gebruikt, moet u ervoor zorgen dat de tijd die moet worden gereageerd op niet-reagerende knoop punten correct is ingesteld voor Azure. In de micro soft-artikel [tuning failover cluster network drempels](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) worden de para meters weer gegeven en wordt uitgelegd hoe de failover-sensitivities. Ervan uitgaande dat de cluster knooppunten zich in hetzelfde subnet bevinden, moeten de volgende para meters worden gewijzigd:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Uw hoge Beschik baarheid en nood herstel procedure testen
   1. Simuleer failover-situaties door Vm's (Windows-gast besturingssysteem) af te sluiten of besturings systemen in de modus paniek (Linux-gast besturingssysteem) in te stellen, zodat u kunt nagaan of uw failover-configuraties werken zoals is ontworpen. 
   2. Meet de tijd die nodig is om een failover uit te voeren. Als de tijden te lang duren, kunt u het volgende overwegen:
      1.   Voor SUSE Linux gebruikt u SBD-apparaten in plaats van de Azure omheinings agent om failover te versnellen
      2.   Voor SAP HANA, als het opnieuw laden van gegevens te lang duurt, overweeg dan om meer opslag bandbreedte in te richten
   3. Testen van back-ups/herstel sequentie en timing, indien nodig. Zorg ervoor dat niet alleen back-uptijden voldoende zijn. Test ook de herstel bewerking en neem de timing over herstel activiteiten. Zorg ervoor dat de herstel tijden binnen uw RTO-Sla's vallen waarin uw RTO afhankelijk is van een Data Base of een VM-herstel proces
   4. Testen in regio DR-functionaliteit en-architectuur
5. Beveiligings controles
   1.  Test de geldigheid van de RBAC-architectuur (Azure Role based Access) die u hebt geïmplementeerd. Doel is om de toegang en machtigingen van verschillende teams te scheiden en te beperken. SAP-team leden moeten bijvoorbeeld Vm's kunnen implementeren en schijven vanuit Azure Storage toewijzen aan een bepaald Azure Virtual Network. Het SAP-basis team zou echter geen eigen virtuele netwerken mogen maken of de instellingen van bestaande virtuele netwerken kunnen wijzigen. Aan de andere kant kunnen leden van het netwerk team geen Vm's implementeren in virtuele netwerken waar de virtuele machines van SAP-toepassingen en-DBMS worden uitgevoerd. Ook kunnen leden van het netwerk team kenmerken van Vm's wijzigen of zelfs Vm's of schijven verwijderen.  
   2.  Controleren of [NSG-en ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) -regels werken zoals verwacht en de beveiligde bronnen afschermen
   3.  Zorg ervoor dat alle resources die moeten worden versleuteld, zijn versleuteld. Definieer en voer processen uit om een back-up te maken van certificaten, opslag en toegang te krijgen tot deze certificaten en herstel de versleutelde entiteiten. 
   4.  Gebruik [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) en/of voor besturingssysteem schijven waar mogelijk vanuit een besturings systeem wordt weer gegeven.
   5.  Controleer of er niet te veel versleutelings lagen zijn gebruikt. Het maakt niet alleen gebruik van Azure Disk Encryption en vervolgens op een van de transparante data base-versleutelings methoden van DBMS
6. Prestatietesten
   1.  In SAP op basis van SAP-tracering en-metingen, de Top 10 online-rapporten vergelijken met de huidige implementatie, indien van toepassing 
   2.  In SAP op basis van SAP-tracering en-metingen, de eerste 10 batch-taken vergelijken met de huidige implementatie, indien van toepassing 
   3.  In SAP op basis van SAP-tracering en-metingen, vergelijkt u gegevens overdracht via interfaces in het SAP-systeem. Richt u op de interfaces waar u weet dat de overdracht nu plaatsvindt tussen verschillende locaties, zoals van on-premises naar Azure 


## <a name="non-production-phase"></a>Niet-productie fase 
In deze fase gaan we ervan uit dat u, na een geslaagde pilot of haalbaarheids test, u begint met het implementeren van niet-productie SAP-systemen in Azure. Alle geleerde informatie en ervaringen van het concept van concepten moeten worden aangepast aan een dergelijke implementatie. Alle criteria en stappen die in de haalbaarheids lijst worden vermeld, zijn ook van toepassing op dit type implementaties. In deze fase implementeert u doorgaans Ontwikkel systemen, systemen voor eenheids tests en bedrijfs systemen voor regressie tests in Azure. Het is raadzaam om ten minste één niet-productie systeem in één SAP-toepassings lijn de volledige configuratie van hoge Beschik baarheid te laten hebben als het toekomstige productie systeem zal zijn. In deze fase moet u rekening houden met de volgende aanvullende stappen:  

1.  Voordat u systemen van het oude platform verplaatst naar Azure resource verbruiks gegevens verzamelen, zoals CPU-gebruik, opslag doorvoer en IOPS-gegevens. Met name van de DBMS-laag eenheden, maar ook uit de eenheden van de toepassingslaag. Meet ook netwerk-en opslag latentie.
2.  Registreer de tijd patronen voor het beschikbaarheids gebruik van de systemen. Doel is te bepalen of niet-productie systemen 24x7 beschikbaar moeten zijn of dat er niet-productie systemen zijn die kunnen worden afgesloten tijdens bepaalde fasen van een week of maand
3.  Test en geef aan of u eigen installatie kopieën voor het besturings systeem wilt maken voor uw virtuele machines in azure of dat u een installatie kopie uit de galerie met installatie kopieën van Azure wilt gebruiken. Als u een installatie kopie uit de Azure-Galerie gebruikt, zorg er dan voor dat u de juiste afbeelding hebt die het ondersteunings contract weerspiegelt met de leverancier van uw besturings systeem. Voor sommige leveranciers van het besturings systeem kunnen Azure-galerieën uw eigen licentie-installatie kopieën meenemen. Voor andere installatie kopieën van het besturings systeem is ondersteuning opgenomen in de prijs die door Azure wordt vermeld. Als u besluit om uw eigen installatie kopieën voor het besturings systeem te maken, kunt u de documentatie vinden in deze artikelen:
    1.  Op basis van [deze documentatie](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) kunt u een gegeneraliseerde installatie kopie maken van een Windows-VM die in Azure is geïmplementeerd
    2.  Op basis van [deze documentatie](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image) kunt u een gegeneraliseerde installatie kopie maken van een virtuele Linux-machine die is geïmplementeerd in azure
3.  Als u SUSE-en Red Hat Linux-installatie kopieën in de Azure VM-Galerie gebruikt, moet u de installatie kopieën voor SAP van de Linux-leveranciers gebruiken in de Azure VM-galerie
4.  Zorg ervoor dat u voldoet aan de ondersteunings vereisten SAP heeft betrekking op micro soft-ondersteunings overeenkomsten. Informatie vindt u in SAP-ondersteunings opmerking [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Voor HANA grote instanties raadpleegt u de vereisten voor het document [voorbereiden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Zorg ervoor dat de juiste personen [geplande onderhouds meldingen](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)ontvangen, zodat u de downtime en het opnieuw opstarten van vm's in de tijd kunt kiezen.
5.  De Azure-documentatie van micro soft-presentaties wordt steeds gecontroleerd op kanalen zoals [Channel 9](https://channel9.msdn.com/) voor nieuwe functionaliteit die mogelijk van toepassing is op uw implementaties
6.  Raadpleeg SAP-notities met betrekking tot Azure, zoals een ondersteunings opmerking [#1928533](https://launchpad.support.sap.com/#/notes/1928533) voor nieuwe VM-sku's, of onlangs ondersteund besturings systeem en DBMS-versie. Vergelijk nieuwe VM-typen met oudere VM-typen in prijzen, zodat u virtuele machines met de beste prijs-prestatie verhouding kunt implementeren
7.  Valideer de resources in SAP-ondersteunings notities, SAP HANA hardwarebronnen en SAP PAM opnieuw om ervoor te zorgen dat er geen wijzigingen zijn in ondersteunde Vm's voor Azure, ondersteunde besturingssysteem releases in deze Vm's en ondersteunde SAP-en DBMS-releases
8.  [Hier vindt](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) u een overzicht van nieuwe Hana-gecertificeerde Sku's in Azure en vergelijk prijzen met de kosten die u hebt gepland en uiteindelijk wijzigt om de eenheden met een betere prijs-prestatie verhouding te verkrijgen 
9.  Uw implementatie scripts aanpassen om gebruik te maken van nieuwe VM-typen en nieuwe functies van Azure toe te passen die u wilt gebruiken
10. Na de implementatie van de infra structuur test en evalueert u de netwerk latentie tussen de virtuele machine van SAP Application Layer en de DBMS-VM volgens SAP-ondersteunings opmerking [#500235](https://launchpad.support.sap.com/#/notes/500235) en SAP-ondersteunings opmerking [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Evalueer de resultaten op basis van de richt lijnen voor netwerk latentie van SAP-ondersteunings opmerking [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerk latentie moet in het gemiddelde en het goede bereik liggen. Uitzonde ringen zijn van toepassing op verkeer tussen virtuele machines en HANA grote instantie-eenheden, zoals [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)wordt beschreven. Zorg ervoor dat geen van de beperkingen die worden vermeld in [overwegingen voor azure virtual machines DBMS-implementatie voor SAP-werk belasting](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) en [SAP Hana infrastructuur configuraties en-bewerkingen in azure van](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) toepassing zijn op uw implementatie
11. Zorg ervoor dat de virtuele machines zijn geïmplementeerd met behulp van de juiste [Azure proximity-plaatsings groep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md)
11. Alle andere controles uitvoeren zoals vermeld in de fase concepten testen voordat u de werk belasting toepast
12. Als workload van toepassing is, noteert u het Resource verbruik van deze systemen in Azure en vergelijkt u met de records die u van uw oude platform hebt ontvangen. Pas de VM-grootte van toekomstige implementaties aan als u ziet dat u grotere verschillen hebt. Houd er rekening mee dat in het geval van Overweeg-, opslag-en netwerk bandbreedten van een virtuele machine ook worden verminderd:
    1.  [Grootten voor virtuele Windows-machines in azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Grootten voor Linux Virtual Machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Werk op systeem kopie functionaliteit en-processen. Doel is om het eenvoudig te maken om een ontwikkelings systeem of een test systeem te kopiëren, zodat project teams snel nieuwe systemen kunnen verkrijgen. U kunt [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) beschouwen als een hulp programma dat dergelijke taken uitvoert.
14. Optimaliseer en verdeel de Azure Role-toegang,-machtigingen en-processen van uw team om ervoor te zorgen dat u een schei ding van het recht aan de ene kant hebt. Aan de andere kant wilt u dat alle teams ingeschakeld zijn om hun taken uit te voeren in de Azure-infra structuur.
15. Ontwikkel, test en documenteer procedures voor hoge Beschik baarheid en herstel na nood gevallen om uw mede werkers in staat te stellen dergelijke taken uit te voeren. Tekortkomingen identificeren en nieuwe Azure-functionaliteit aanpassen die u integreert in uw implementaties

 
## <a name="production-preparation-phase"></a>Fase productie voorbereiding 
In deze fase wilt u alle ervaringen en kennis van uw niet-productie-implementaties verzamelen en deze Toep assen in de toekomstige productie-implementaties. Daarnaast moet u ook het werk van de gegevens overdracht tussen de huidige hosting locatie en Azure voorbereiden. 

1.  Gebruik de nodige SAP-release-upgrades van uw productie systemen voordat u overstapt op Azure
2.  Ga akkoord met de zakelijke eigen aren van de functionele en zakelijke tests die moeten worden uitgevoerd na de migratie van het productie systeem
    1.  Zorg ervoor dat al deze tests worden uitgevoerd met de bron systemen in de huidige hosting locatie. U wilt voor komen dat testen worden uitgevoerd voor de eerste keer wanneer het systeem is verplaatst naar Azure
2.  Test het productie migratie proces in Azure. Als niet alle productie systemen in hetzelfde tijds bestek naar Azure worden verplaatst, bouwt u groepen productie systemen die zich op dezelfde hosting locatie moeten bevinden. Gegevens migratie uitoefenen en testen. Lijst met veelvoorkomende methoden zoals:
    1.  DBMS-methoden zoals back-up/herstel gebruiken in combi natie met SQL Server AlwaysOn, HANA-systeem replicatie of logboek verzending voor het seeden en synchroniseren van database inhoud in azure
    2.  Back-up/herstel gebruiken voor kleinere data bases
    3.  SAP-migratie monitor gebruiken die is geïmplementeerd in het SAP SWPM-hulp programma voor het uitvoeren van heterogene migraties
    4.  Gebruik het [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) -proces als u wilt combi neren met een SAP-versie-upgrade. Houd er wel bij dat niet alle combi Naties tussen bron-en doel-DBMS worden ondersteund. Meer informatie vindt u in de specifieke SAP-ondersteunings opmerkingen voor de verschillende versies van DMO. Bijvoorbeeld, [Data Base Migration Option (DMO) van SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Test of gegevens overdracht via internet of via ExpressRoute beter is in de door Voer voor het geval u back-ups of SAP-export bestanden moet verplaatsen. Voor het verplaatsen van gegevens via internet, moet u mogelijk enkele van uw NSG/ASG-beveiligings regels wijzigen die u nodig hebt voor toekomstige productie systemen
3.  Voordat u systemen van het oude platform verplaatst naar Azure resource verbruiks gegevens verzamelen, zoals CPU-gebruik, opslag doorvoer en IOPS-gegevens. Met name van de DBMS-laag eenheden, maar ook uit de eenheden van de toepassingslaag. Meet ook netwerk-en opslag latentie.
4.  Valideer de resources in SAP-ondersteunings notities, SAP HANA hardwarebronnen en SAP PAM opnieuw om ervoor te zorgen dat er geen wijzigingen zijn in ondersteunde Vm's voor Azure, ondersteunde besturingssysteem releases in deze Vm's en ondersteunde SAP-en DBMS-releases 
4.  Implementatie scripts aanpassen aan de meest recente wijzigingen die u hebt gekozen voor VM-typen en Azure-functionaliteit
5.  Ga na de implementatie van de infra structuur en toepassing door een reeks controles om te controleren:
    1.  De juiste VM-typen zijn geïmplementeerd met de juiste kenmerken en opslag grootten
    2.  Controleer of de Vm's zich op de juiste en gewenste versies van het besturings systeem en patches bevinden en uniform zijn
    3.  Controleren of de virtuele machines zo nodig en uniform worden gehard
    4.  Controleer of de juiste toepassings releases en patches zijn geïnstalleerd en geïmplementeerd
    5.  De virtuele machines die zijn geïmplementeerd in azure-beschikbaarheids sets, zoals gepland
    6.  Azure Premium Storage is gebruikt voor de latentie gevoelige schijven of de [Sla voor één VM van 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) is vereist
    7.  Controleren op de juiste implementatie van Azure Write Accelerator
        1.  Zorg ervoor dat binnen de virtuele machine, opslag ruimten of stripesets op de juiste wijze is gebouwd op schijven waarvoor Azure Write Accelerator-ondersteuning nodig is
            1.  Controleer de [configuratie van software-RAID op Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Controleren [LVM op een Linux-vm in azure configureren](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) zijn exclusief gebruikt
    9.  Virtuele machines die zijn geïmplementeerd in de juiste beschikbaarheids sets en Beschikbaarheidszones
    10. Zorg ervoor dat [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de virtuele machines die worden gebruikt op de SAP-toepassingslaag en de SAP DBMS-laag
    11. Er zijn geen [virtuele Azure-netwerk apparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatie traject tussen de SAP-toepassing en de DBMS-laag van een SAP NetWeaver, hybris of S/4HANA op basis van SAP-systemen
    12. Met ASG-en NSG-regels kunnen communicatie zoals gewenst en gepland worden uitgevoerd en wordt de communicatie geblokkeerd, indien nodig
    13. De time-outinstellingen zoals eerder beschreven, zijn correct ingesteld
    14. Zorg ervoor dat de virtuele machines zijn geïmplementeerd met behulp van de juiste [Azure proximity-plaatsings groep](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) zoals beschreven in het artikel [Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen](sap-proximity-placement-scenarios.md)
    15. Test en evalueer de netwerk latentie tussen SAP Application Layer-VM en DBMS-VM volgens SAP-ondersteuning opmerking [#500235](https://launchpad.support.sap.com/#/notes/500235) en SAP-ondersteunings opmerking [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Evalueer de resultaten op basis van de richt lijnen voor netwerk latentie van SAP-ondersteunings opmerking [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). De netwerk latentie moet in het gemiddelde en het goede bereik liggen. Uitzonde ringen zijn van toepassing op verkeer tussen virtuele machines en HANA grote instantie-eenheden, zoals [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) wordt beschreven
    15. Controleer of de versleuteling zo nodig is geïmplementeerd en met de versleutelings methode vereist
    16. Controleren of interfaces en andere toepassingen verbinding kunnen maken met de zojuist geïmplementeerde infra structuur
6.  Maak een Playbook voor het reageren op geplande Azure-onderhoud. Definieer de volg orde van de systemen en Vm's die opnieuw moeten worden opgestart in het geval van gepland onderhoud
    

## <a name="go-live-phase"></a>Live-fase
Voor de Go-Live-fase moet u ervoor zorgen dat u voldoet aan uw playbooks die u in eerdere fasen hebt ontwikkeld. Voer de stappen uit die u hebt getest en getraind. Accepteer geen wijzigingen in de laatste minuut in configuraties en processen. Dit is niet alleen van toepassing op de volgende maat regelen:

1. Controleer of Azure Portal controle en andere controle hulpprogramma's werken.  Aanbevolen hulpprogram ma's zijn perfmon (Windows) of SAR (Linux): 
    1.  CPU-items 
        1.  Gemiddelde CPU-tijd – totaal (alle CPU)
        2.  Gemiddelde CPU-tijd: elke afzonderlijke processor (dus 128 processors op m128 VM)
        3.  CPU-tijd kernel: elke afzonderlijke processor
        4.  CPU-tijd gebruiker: elke afzonderlijke processor
    5.  Geheugen 
        1.  Beschikbaar geheugen
        2.  Geheugen pagina in per seconde
        3.  Geheugen pagina uit per seconde
    4.  Schijf 
        1.  Lees-en schrijf bewerkingen per seconde voor elke afzonderlijke schijf 
        2.  Lees bewerkingen per seconde op afzonderlijke schijven
        3.  Lees-en schrijf-naar-schijf-per afzonderlijke schijf
        4.  Geschreven en gelezen bytes per seconde voor elke afzonderlijke schijf 
        5.  Schrijf bewerkingen per seconde van de schijf op afzonderlijke schijven
        6.  Schrijf MS/Read-schijf-per afzonderlijke schijf
    5.  Netwerk 
        1.  Netwerk pakketten in per seconde
        2.  Netwerk pakketten per seconde
        3.  Netwerk KB per seconde
        4.  Netwerk KB uit per seconde 
2.  Voer na de migratie van de gegevens alle validatie tests uit die u hebt geaccepteerd bij de zakelijke eigen aren. Alleen resultaten van validatie tests accepteren waarbij u resultaten voor de oorspronkelijke bron systemen hebt
3.  Controleren of de interfaces functioneren en of andere toepassingen kunnen communiceren met de zojuist geïmplementeerde productie systemen
4.  Controleer het Trans Port-en correctie systeem via SAP Trans Action STMS
5.  Database back-ups uitvoeren zodra het systeem is vrijgegeven voor productie
6.  VM-back-ups uitvoeren voor de SAP Application Layer-Vm's zodra het systeem is vrijgegeven voor productie
7.  Voor SAP-systemen die geen deel uitmaken van de huidige Go-Live-fase, maar wel met de SAP-systemen die u in deze Go-Live hebt verplaatst, moet u de buffer van de hostnaam opnieuw instellen in SM51. Deze stap verwijdert de oude IP-adressen in de cache die zijn gekoppeld aan de namen van de toepassings exemplaren die u naar Azure hebt verplaatst  


## <a name="post-production"></a>Na productie
In deze fase gaat het om het systeem te bewaken, te gebruiken en te beheren. Vanuit een SAP-oogpunt zijn de gebruikelijke taken die u nodig had om uit te voeren met uw oude hosting locatie van toepassing. De specifieke Azure-taken die u wilt uitvoeren:

1. Azure-facturen voor hoogwaardige systemen analyseren
2. Optimaliseer de prijs-prestatie verhouding van de kant-en opslag zijde van de virtuele machine
3. Optimalisatie van tijd systemen kan worden afgesloten  


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de documentatie:

- [Azure Virtual Machines planning en implementatie voor SAP net-Weaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-implementatie voor SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

