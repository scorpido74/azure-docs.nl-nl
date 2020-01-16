---
title: Herstel na nood gevallen voor SAP NetWeaver instellen met Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen voor SAP NetWeaver met Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0cef6332a169b71d7812efdc41247443fbc194f2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982364"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Herstel na nood geval instellen voor een implementatie van SAP NetWeaver met meerdere lagen

De meeste implementaties met een hoge grootte en middel grote SAP-oplossing gebruiken een vorm van herstel na nood gevallen. Het belang van robuuste en testable nood herstel oplossingen is toegenomen naarmate er meer essentiële bedrijfs processen worden verplaatst naar toepassingen als SAP. Azure Site Recovery is getest en geïntegreerd met SAP-toepassingen. Site Recovery overschrijdt de mogelijkheden van de meeste on-premises nood herstel oplossingen en een lagere total cost of ownership (TCO) dan concurrerende oplossingen.

Met Site Recovery kunt u het volgende doen:
* **Schakel de beveiliging in van SAP NetWeaver en niet-NetWeaver-productie toepassingen die on-premises worden uitgevoerd** door onderdelen naar Azure te repliceren.
* **Schakel de beveiliging in van SAP NetWeaver en niet-NetWeaver-productie toepassingen die worden uitgevoerd op Azure** door onderdelen te repliceren naar een ander Azure-Data Center.
* **Vereenvoudig de Cloud migratie** met behulp van site Recovery om uw SAP-implementatie naar Azure te migreren.
* **Vereenvoudig SAP-project upgrades, testen en prototypen** door een productie kloon op aanvraag te maken voor het testen van SAP-toepassingen.

In dit artikel wordt beschreven hoe u implementaties van SAP NetWeaver-toepassingen kunt beveiligen met behulp van [Azure site Recovery](site-recovery-overview.md). In het artikel worden aanbevolen procedures beschreven voor het beveiligen van een SAP NetWeaver-implementatie met drie lagen door te repliceren naar een ander Azure-Data Center met behulp van Site Recovery. Hierin worden de ondersteunde scenario's en configuraties beschreven en wordt uitgelegd hoe u testfailover (nood herstel analyse) en werkelijke failovers uitvoert.

## <a name="prerequisites"></a>Vereisten
Voordat u begint, moet u ervoor zorgen dat u weet hoe u de volgende taken moet uitvoeren:

* [Een virtuele machine repliceren naar Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Een herstel netwerk ontwerpen](site-recovery-azure-to-azure-networking-guidance.md)
* [Een testfailover naar Azure uitvoeren](azure-to-azure-walkthrough-test-failover.md)
* [Een failover naar Azure uitvoeren](site-recovery-failover.md)
* [Een domein controller repliceren](site-recovery-active-directory.md)
* [SQL Server repliceren](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Ondersteunde scenario's
U kunt Site Recovery gebruiken voor het implementeren van een oplossing voor herstel na nood geval in de volgende scenario's:
* SAP-systemen die worden uitgevoerd in een Azure-Data Center dat wordt gerepliceerd naar een ander Azure-Data Center (nood herstel van Azure naar Azure). Zie [Azure-to-Azure-replicatie architectuur](https://aka.ms/asr-a2a-architecture)voor meer informatie.
* SAP-systemen die worden uitgevoerd op VMware (of fysieke) servers op locatie die repliceren naar een nood herstel site in een Azure-Data Center (nood herstel van VMware naar Azure). Voor dit scenario zijn enkele extra onderdelen vereist. Zie voor meer informatie [VMware-naar-Azure-replicatie architectuur](https://aka.ms/asr-v2a-architecture).
* SAP-systemen die worden uitgevoerd op Hyper-V on-premises die repliceren naar een nood herstel site in een Azure-Data Center (Hyper-V-naar-Azure-nood herstel). Voor dit scenario zijn enkele extra onderdelen vereist. Zie voor meer informatie [Hyper-V-naar-Azure-replicatie architectuur](https://aka.ms/asr-h2a-architecture).

In dit artikel gebruiken we een **Azure-to-Azure** -scenario voor herstel na nood gevallen om de SAP-mogelijkheden voor herstel na nood gevallen van site Recovery te demonstreren. Omdat Site Recovery replicatie niet specifiek is voor een toepassing, wordt verwacht dat het proces dat wordt beschreven ook van toepassing is op andere scenario's.

### <a name="required-foundation-services"></a>Vereiste Foundation-Services
In het scenario dat in dit artikel wordt besproken, worden de volgende Foundation-Services geïmplementeerd:
* Azure ExpressRoute of Azure VPN Gateway
* Ten minste één Active Directory domein controller en DNS-server, die wordt uitgevoerd in azure

U wordt aangeraden deze infra structuur in te stellen voordat u Site Recovery implementeert.

## <a name="reference-sap-application-deployment"></a>Naslag informatie over SAP-toepassingen implementeren

In deze referentie architectuur wordt SAP NetWeaver uitgevoerd in een Windows-omgeving op Azure met hoge Beschik baarheid.  Deze architectuur wordt geïmplementeerd met specifieke groottes van virtuele machines (VM) die kunnen worden gewijzigd om tegemoet te komen aan de behoeften van uw organisatie.

![Diagram van een typisch SAP-implementatie patroon](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Aandachtspunten voor herstel na nood gevallen

Voor herstel na noodgevallen (DR), moet u een failover uitvoeren naar een secundaire regio kunnen zijn. Voor elke laag wordt een andere strategie gebruikt om DR (Disaster Recovery, herstel na noodgeval) te bieden.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Vm's met een SAP Web dispatcher-pool 
Het onderdeel Web Dispatcher wordt gebruikt als een load balancer voor SAP-verkeer tussen de SAP-toepassingsservers. Voor een hoge Beschik baarheid van het onderdeel webdispatcher, Azure Load Balancer wordt gebruikt voor het implementeren van de installatie van de parallelle webdispatcher in een Round-Robin configuratie voor HTTP (S)-verkeer distributie over de beschik bare webdispatchers in de Balancer-pool. Dit wordt gerepliceerd met Site Recovery en automatiserings scripts worden gebruikt voor het configureren van load balancer op het gebied voor herstel na nood gevallen. 

#### <a name="vms-running-application-servers-pool"></a>Virtuele machines met een groep toepassings servers
Voor het beheren van aanmeldingsgroepen voor ABAP-toepassingsservers, wordt de transactie SMLG gebruikt. De load balancer-functie binnen de berichtenserver van de centrale Services wordt gebruikt voor het distribueren van werkbelasting tussen SAP-toepassingen voor servers voor SAPGUIs en RFC verkeer. Dit wordt gerepliceerd met behulp van Site Recovery.

#### <a name="vms-running-sap-central-services-cluster"></a>Vm's met een SAP Central Services-cluster
Deze referentiearchitectuur centrale Services worden uitgevoerd op virtuele machines in de toepassingslaag. De centrale Services is een potentieel single point of failure (SPOF) bij de implementatie op een enkele virtuele machine, normale implementatie als hoge beschikbaarheid geen vereiste is.<br>

Als u een oplossing met hoge Beschik baarheid wilt implementeren, kunt u een gedeeld schijf cluster of een bestands share-cluster gebruiken. Als u Vm's wilt configureren voor een gedeeld schijf cluster, gebruikt u Windows Server-failovercluster. Cloud-witness wordt aanbevolen als een quorumwitness.
 > [!NOTE]
 > Site Recovery repliceert de Cloud-Witness daarom het is raadzaam om de cloudwitness in het gebied voor nood herstel te implementeren.

Ter ondersteuning van de failover-clusteromgeving [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) het cluster shared volume-functie wordt uitgevoerd door onafhankelijke schijven die eigendom zijn van de clusterknooppunten te repliceren. Azure biedt geen systeemeigen ondersteuning voor gedeelde schijven en daarom vereist SIOS-oplossingen.

Een andere manier om clustering af te handelen is het implementeren van een bestands share cluster. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) het patroon van de implementatie Central Services voor toegang tot de algemene mappen /sapmnt via een UNC-pad die onlangs zijn gewijzigd. Het is echter nog steeds raadzaam om ervoor te zorgen dat de/sapmnt UNC-share Maxi maal beschikbaar is. Dit kan worden gedaan in het centrale Services-exemplaar met behulp van Windows Server-failovercluster met scale out file server (SOFS) en de functie Opslagruimten Direct (S2D) in Windows Server 2016.
 > [!NOTE]
 > Momenteel Site Recovery ondersteunen alleen crash consistente punt replicatie van virtuele machines met behulp van opslag ruimten direct en passief knoop punt SIOS data keeper


## <a name="disaster-recovery-considerations"></a>Overwegingen voor herstel na noodgeval

U kunt Site Recovery gebruiken om de failover van een volledige SAP-implementatie in azure-regio's te organiseren.
Hieronder vindt u de stappen voor het instellen van herstel na nood gevallen 

1. Virtuele machines repliceren
2. Een herstel netwerk ontwerpen
3.  Een domein controller repliceren
4.  Data base-laag repliceren
5.  Een testfailover uitvoeren
6.  Een failover uitvoeren

Hieronder ziet u de aanbeveling voor herstel na nood gevallen van elke laag die in dit voor beeld wordt gebruikt.

 **SAP-lagen** | **Aanbeveling**
 --- | ---
**SAP Web dispatcher-pool** |  Repliceren met Site Recovery 
**SAP-toepassings server groep** |  Repliceren met Site Recovery 
**SAP Central Services-cluster** |  Repliceren met Site Recovery 
**Virtuele machines van Active Directory** |  Active Directory-replicatie 
**SQL database-servers** |  SQL always on Replication

## <a name="replicate-virtual-machines"></a>Virtuele machines repliceren

Als u de virtuele machines van de SAP-toepassing wilt repliceren naar het Azure nood herstel Data Center, volgt u de richt lijnen in [repliceren van een virtuele machine naar Azure](azure-to-azure-walkthrough-enable-replication.md).


* Raadpleeg voor meer informatie over het beveiligen van Active Directory en DNS het document [Active Directory en DNS beveiligen](site-recovery-active-directory.md) .

* Raadpleeg SQL Server-document [beveiligen](site-recovery-sql.md) voor meer informatie over het beveiligen van de database tier die op SQL Server wordt uitgevoerd.

## <a name="networking-configuration"></a>Netwerk configuratie

Als u een statisch IP-adres gebruikt, kunt u het IP-adres opgeven dat u wilt dat de virtuele machine moet overnemen. Als u het IP-adres wilt instellen, gaat u naar **reken-en netwerk instellingen** > **netwerk interface kaart**.

![Scherm afbeelding van het instellen van een privé-IP-adres in het deel venster Site Recovery netwerk interface kaart](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Een herstel plan maken
Een herstel plan ondersteunt het sequentiëren van verschillende lagen in een toepassing met meerdere lagen tijdens een failover. Met sequentiëren kunt u de consistentie van toepassingen onderhouden. Wanneer u een herstel plan voor een webtoepassing met meerdere lagen maakt, voert u de stappen uit die worden beschreven in [een herstel plan maken met behulp van site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failover-groepen

1.  Maak een herstel plan door de Vm's toepassings server, webdispatcher en SAP Central Services toe te voegen.
2.  Klik op aanpassen om de Vm's te groeperen. Standaard maken alle Vm's deel uit van ' groep 1 '.



### <a name="add-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstel plan
Voor een goede werking van uw toepassingen moet u mogelijk bepaalde bewerkingen uitvoeren op de virtuele Azure-machines na de failover of tijdens een testfailover. U kunt bewerkingen na de failover automatiseren. U kunt de DNS-vermelding bijvoorbeeld bijwerken en bindingen en verbindingen wijzigen door de bijbehorende scripts toe te voegen aan het herstel plan.


U kunt de meest gebruikte Site Recovery scripts implementeren in uw Automation-account door te klikken op de knop implementeren naar Azure hieronder. Wanneer u een gepubliceerd script gebruikt, zorg er dan voor dat u de instructies in het script volgt.

[![Implementeren in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg een pre-action script aan groep 1 toe aan een failover-SQL-beschikbaarheids groep. Gebruik het script ASR-SQL-FailoverAG dat is gepubliceerd in de voorbeeld scripts. Zorg ervoor dat u de instructies in het script volgt en breng de vereiste wijzigingen in het script op de juiste wijze aan.
2. Voeg een bericht actie script toe om een load balancer toe te voegen aan de virtuele machines waarvoor een failover is uitgevoerd op de weblaag (groep 1). Gebruik het script ASR-AddSingleLoadBalancer dat is gepubliceerd in de voorbeeld scripts. Zorg ervoor dat u de instructies in het script volgt en breng de vereiste wijzigingen in het script op de juiste wijze aan.

![SAP-herstel plan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1.  Selecteer uw Recovery Services kluis in de Azure Portal.
2.  Selecteer het herstel plan dat u hebt gemaakt voor SAP-toepassingen.
3.  Selecteer **Failover testen**.
4.  Als u het proces testfailover wilt starten, selecteert u het herstel punt en het virtuele Azure-netwerk.
5.  Voer validaties uit wanneer de secundaire omgeving actief is.
6.  Wanneer de validaties zijn voltooid, selecteert u testfailover **opschonen**om de failover-omgeving op te schonen.

Zie [failover testen naar Azure in site Recovery](site-recovery-test-failover-to-azure.md)voor meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1.  Selecteer uw Recovery Services kluis in de Azure Portal.
2.  Selecteer het herstel plan dat u hebt gemaakt voor SAP-toepassingen.
3.  Selecteer **Failover**.
4.  Als u het failoverproces wilt starten, selecteert u het herstel punt.

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* Voor meer informatie over het bouwen van een oplossing voor herstel na nood gevallen voor SAP NetWeaver-implementaties met behulp van Site Recovery raadpleegt u het Download bare White paper [SAP NetWeaver: een oplossing voor nood herstel met site Recovery bouwen](https://aka.ms/asr_sap). In het technisch document vindt u aanbevelingen voor diverse SAP-architecturen, een lijst met ondersteunde toepassingen en VM-typen voor SAP on Azure en een beschrijving van de test plan opties voor uw oplossing voor herstel na nood gevallen.
* Meer informatie over het [repliceren van andere werk belastingen](site-recovery-workload.md) met behulp van site Recovery.
