---
title: SAP NetWeaver-herstel na te stellen met Azure Site Recovery
description: Meer informatie over het instellen van noodherstel voor SAP NetWeaver met Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190799"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Disaster recovery instellen voor een implementatie van een SAP NetWeaver-app met meerdere lagen

De meeste grote en middelgrote SAP-implementaties maken gebruik van een of andere vorm van noodhersteloplossing. Het belang van robuuste en testbare oplossingen voor noodherstel is toegenomen naarmate meer kernactiviteiten worden verplaatst naar toepassingen zoals SAP. Azure Site Recovery is getest en geïntegreerd met SAP-toepassingen. Site Recovery overtreft de mogelijkheden van de meeste on-premises oplossingen voor noodherstel en tegen lagere totale eigendomskosten dan concurrerende oplossingen.

Met Site recovery u het doen met:
* Schakel beveiliging in van SAP NetWeaver- en niet-NetWeaver-productietoepassingen die on-premises worden uitgevoerd door onderdelen te repliceren naar Azure.
* Schakel beveiliging in van SAP NetWeaver- en niet-NetWeaver-productietoepassingen die op Azure worden uitgevoerd door onderdelen te repliceren naar een ander Azure-datacenter.
* Vereenvoudig de cloudmigratie door Site Recovery te gebruiken om uw SAP-implementatie te migreren naar Azure.
* Vereenvoudig sap-projectupgrades, -tests en -prototypen door een productiekloon on-demand te maken voor het testen van SAP-toepassingen.

U SAP NetWeaver-toepassingsimplementaties beveiligen met [Azure Site Recovery](site-recovery-overview.md). In dit artikel vindt u best practices voor het beveiligen van een SAP NetWeaver-implementatie met drie lagen op Azure wanneer u deze naar een ander Azure-datacenter repliceert met Siteherstel. In het artikel worden ondersteunde scenario's en configuraties beschreven en hoe u testfailovers (disaster recovery drills) en werkelijke failovers doen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de volgende taken moet uitvoeren:

* [Een virtuele machine repliceren naar Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Een herstelnetwerk ontwerpen](site-recovery-azure-to-azure-networking-guidance.md)
* [Een testfailover naar Azure](azure-to-azure-walkthrough-test-failover.md)
* [Een failover naar Azure](site-recovery-failover.md)
* [Een domeincontroller repliceren](site-recovery-active-directory.md)
* [Een SQL Server-exemplaar repliceren](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

U Siteherstel gebruiken om een oplossing voor noodherstel te implementeren in de volgende scenario's:
* U hebt SAP-systemen die in één Azure-datacenter worden uitgevoerd en u repliceert ze naar een ander Azure-datacenter (Azure-naar-Azure-noodherstel). 
   Zie [Azure-to-Azure-replicatiearchitectuur voor](https://aka.ms/asr-a2a-architecture)meer informatie.
* U beschikt over SAP-systemen die on-premises op VMware -servers (of fysieke) servers draaien. U repliceert de SAP-systemen ook naar een rampherstelsite in een Azure-datacenter (VMware-naar-Azure-noodherstel). 
   Dit scenario vereist een aantal extra componenten. Zie [VMware-naar-Azure-replicatiearchitectuur](https://aka.ms/asr-v2a-architecture)voor meer informatie.
* U beschikt over SAP-systemen die op Hyper-V on-premises draaien. U repliceert de SAP-systemen ook naar een rampherstelsite in een Azure-datacenter (Hyper-V-naar-Azure-noodherstel).
   Dit scenario vereist een aantal extra componenten. Zie [Hyper-V-to-Azure-replicatiearchitectuur](https://aka.ms/asr-h2a-architecture)voor meer informatie.

In dit artikel gebruiken we een **Azure-naar-Azure-scenario** voor noodherstel. In het scenario ziet u de SAP-noodherstelmogelijkheden van Site Recovery. Omdat replicatie van siteherstel niet toepassingsspecifiek is, is het proces dat wordt beschreven naar verwachting ook van toepassing op andere scenario's.

### <a name="required-foundation-services"></a>Vereiste funderingsdiensten
In het scenario dat we in dit artikel bespreken, worden de volgende foundationservices geïmplementeerd:
* Azure ExpressRoute of Azure VPN-gateway
* Ten minste één Azure Active Directory-domeincontroller en DNS-server, uitgevoerd in Azure

We raden u aan deze infrastructuur in te stellen voordat u Site Recovery implementeert.

## <a name="reference-sap-application-deployment"></a>Implementatie van SAP-toepassingen naverwijzing

Met deze referentiearchitectuur wordt SAP NetWeaver uitgevoerd in een Windows-omgeving op Azure met een hoge beschikbaarheid. Deze architectuur wordt geïmplementeerd met specifieke vm-formaten (virtual machine) die u wijzigen om aan de behoeften van uw organisatie te voldoen.

![Diagram van een typisch SAP-implementatiepatroon](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Overwegingen voor herstel na noodgeval

Voor herstel na noodgevallen moet u kunnen falen naar een secundaire regio. Elke laag gebruikt een andere strategie om bescherming tegen herstel na noodgevallen te bieden.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>VM's met SAP-webdispatchergroepen

De component Web Dispatcher werkt als load balancer voor SAP-verkeer tussen de SAP-toepassingsservers. Azure Load Balancer implementeert de parallelle Web Dispatcher-installatie om een hoge beschikbaarheid voor de component Web Dispatcher te bereiken. Web Dispatcher maakt gebruik van een round-robin configuratie voor HTTP(S) verkeer distributie onder de beschikbare Web Dispatchers in de balancers pool.

#### <a name="vms-running-application-servers-pools"></a>VM's met toepassingsservers
De SMLG-transactie beheert inloggroepen voor ABAP-toepassingsservers. Het maakt gebruik van de load-balancing functie binnen de berichtenserver van de Central Services om workload te distribueren over SAP-applicatieserverpools voor SAPGUI's en RFC-verkeer. U dit beheer repliceren met Siteherstel.

#### <a name="vms-running-sap-central-services-clusters"></a>VM's met SAP Central Services-clusters
Deze referentiearchitectuur voert Central Services uit op VM's in de toepassingslaag. Central Services is een potentieel single point of failure in één VM. Typische implementatie en hoge beschikbaarheid zijn geen vereisten.

Als u een oplossing met hoge beschikbaarheid wilt implementeren, u een gedeeld schijfcluster of een cluster voor bestandsshare gebruiken. Als u VM's wilt configureren voor een gedeeld schijfcluster, gebruikt u Windows Server Failover-cluster. Wij raden u aan de cloudgetuige als quorumgetuige te gebruiken.

 > [!NOTE]
 > Omdat Site Recovery de cloudgetuige niet repliceert, raden we u aan de cloudgetuige in het gebied voor noodherstel te implementeren.

Om de failoverclusteromgeving te ondersteunen, heeft [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) de clustervolumefunctie. In de functie repliceert SIOS DataKeeper Cluster onafhankelijke schijven die eigendom zijn van de clusterknooppunten. Omdat Azure geen standaard gedeelde schijven ondersteunt, zijn er oplossingen van SIOS nodig.

U ook clustering afhandelen door een cluster voor bestandsshare te implementeren. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) heeft onlangs het implementatiepatroon van Central Services aangepast om toegang te krijgen tot de /sapmnt global directories via een UNC-pad. We raden u nog steeds aan ervoor te zorgen dat het /sapmnt UNC-aandeel zeer beschikbaar is. U uw instantie Van de Centrale Diensten controleren. Gebruik windows server failovercluster met Scale Out File Server (SOFS) en de functie Storage Spaces Direct (S2D) in Windows Server 2016.

 > [!NOTE]
 > Site Recovery ondersteunt momenteel alleen crash-consistent puntreplicatie van virtuele machines die direct opslagruimten gebruiken en het passieve knooppunt van SIOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Meer overwegingen bij het herstel van de ramp

U Siteherstel gebruiken om de failover van volledige SAP-implementatie in Azure-regio's te orkestreren.
Hieronder volgen de stappen voor het instellen van het herstel na noodgevallen:

1. Virtuele machines repliceren
1. Een herstelnetwerk ontwerpen
1. Een domeincontroller repliceren
1. Gegevensbasislaag repliceren
1. Een testfailover uitvoeren
1. Een failover uitvoeren

Hieronder volgt de aanbeveling voor noodherstel van elke laag die in dit voorbeeld wordt gebruikt.

 **SAP-lagen** | **Aanbeveling**
 --- | ---
**SAP-webdispatcherpool** |  Repliceren met siteherstel 
**GROEP SAP-toepassingsserver** |  Repliceren met siteherstel 
**SAP Central Services-cluster** |  Repliceren met siteherstel 
**Active directory virtuele machines** |  Active directory-replicatie gebruiken 
**SQL Database-servers** |  SQL Server Always On-replicatie gebruiken

## <a name="replicate-virtual-machines"></a>Virtuele machines repliceren

Als u wilt beginnen met het repliceren van alle virtuele machines van de SAP-toepassing naar het Azure-noodhersteldatacenter, volgt u de richtlijnen voor [het repliceren van een virtuele machine naar Azure.](azure-to-azure-walkthrough-enable-replication.md)

* Voor richtlijnen voor het beveiligen van Active Directory en DNS leest u [hoe u Active Directory en DNS beschermt.](site-recovery-active-directory.md)

* Lees voor meer informatie over het beveiligen van databaselagen die op SQL Server worden uitgevoerd, [hoe u SQL Server beschermt.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Netwerkconfiguratie

Als u een statisch IP-adres gebruikt, u het IP-adres opgeven dat de virtuele machine moet gebruiken. Als u het IP-adres wilt instellen, gaat u naar de**netwerkinterfacekaart** **voor gegevensinstellingen en netwerkinstellingen** > .

![Schermafbeelding van het instellen van een privé-IP-adres in het deelvenster Site Recovery Network-interface](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Een herstelplan maken

Een herstelplan ondersteunt de volgorde van verschillende lagen in een multi-tier toepassing tijdens een failover. Sequencing helpt de consistentie van de toepassing te behouden. Wanneer u een herstelplan maakt voor een multi-tier webtoepassing, voert u de stappen uit die zijn beschreven in [Een herstelplan maken met Siteherstel](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failovergroepen

1. Maak een herstelplan door de vm's van de toepassingsserver, de webdispatcher en de SAP Central-services toe te voegen.
1. Selecteer **Aanpassen** om de VM's te groeperen. Standaard maken alle VM's deel uit van groep 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstelplan
Als u uw toepassingen correct wilt laten functioneren, moet u mogelijk bepaalde bewerkingen uitvoeren op de virtuele Azure-machines. Doe deze bewerkingen na de failover of tijdens een testfailover. U ook een aantal post-failoverbewerkingen automatiseren. Werk bijvoorbeeld de DNS-vermelding bij en wijzig bindingen en verbindingen door bijbehorende scripts toe te voegen aan het herstelplan.

U de meest gebruikte Site Recovery-scripts implementeren in uw Azure Automation-account door Deploy te selecteren **in Azure.** Wanneer u een gepubliceerd script gebruikt, volgt u de richtlijnen in het script.

[![Implementeren naar Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg een pre-action script toe aan Groep 1 om te mislukken over de SQL Server beschikbaarheidsgroep. Gebruik het ASR-SQL-FailoverAG-script dat is gepubliceerd in de voorbeeldscripts. Volg de richtlijnen in het script en breng de vereiste wijzigingen in het script op de juiste manier aan.
1. Voeg een post-action script toe om een load balancer toe te voegen aan de mislukte virtuele machines van de weblaag (Groep 1). Gebruik het ASR-AddSingleLoadBalancer-script dat is gepubliceerd in de voorbeeldscripts. Volg de richtlijnen in het script en breng indien nodig de vereiste wijzigingen in het script aan.

![SAP-herstelplan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Selecteer in de Azure-portal de kluis Van Herstelservices.
1. Selecteer het herstelplan dat u hebt gemaakt voor SAP-toepassingen.
1. Selecteer **Failover testen**.
1. Als u het failoverproces wilt starten, selecteert u het herstelpunt en het virtuele Azure-netwerk.
1. Wanneer de secundaire omgeving is up, voert u validaties uit.
1. Wanneer validaties zijn voltooid, maakt u de failover-omgeving schoon door **failover van de opschoningstest te**selecteren.

Zie [Failover testen naar Azure in Siterecovery voor](site-recovery-test-failover-to-azure.md)meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1. Selecteer in de Azure-portal de kluis Van Herstelservices.
1. Selecteer het herstelplan dat u hebt gemaakt voor SAP-toepassingen.
1. Selecteer **Failover**.
1. Als u het failoverproces wilt starten, selecteert u het herstelpunt.

Zie [Failover in Siteherstel](site-recovery-failover.md)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het bouwen van een oplossing voor noodherstel voor SAP NetWeaver-implementaties met behulp van Site recovery. Zie de downloadbare whitepaper [SAP NetWeaver: Een disaster recovery-oplossing bouwen met siteherstel.](https://aka.ms/asr_sap) De whitepaper bespreekt aanbevelingen voor verschillende SAP-architecturen. U ondersteunde toepassingen en VM-typen voor SAP op Azure bekijken. Er zijn ook planopties voor het testen van uw oplossing voor noodherstel.
* Meer informatie over [het repliceren van andere workloads](site-recovery-workload.md) met Site Recovery.
