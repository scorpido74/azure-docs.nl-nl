---
title: Cluster capaciteit plannen in azure HDInsight
description: Een HDInsight-cluster opgeven voor capaciteit en prestaties.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: 3d82846c02754f23b4a2e86a7881c952e503b36f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207151"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Capaciteits planning voor HDInsight-clusters

Voordat u een HDInsight-cluster implementeert, plant u de gewenste cluster capaciteit door de benodigde prestaties en schaal te bepalen. Deze planning helpt zowel de bruikbaarheid als de kosten te optimaliseren. Sommige beslissingen over cluster capaciteit kunnen niet worden gewijzigd na de implementatie. Als de prestatie parameters veranderen, kan een cluster worden gedemonteerd en opnieuw worden gemaakt zonder dat de opgeslagen gegevens verloren gaan.

De belangrijkste vragen om de capaciteits planning te vragen zijn:

* In welke geografische regio moet u uw cluster implementeren?
* Hoeveel opslag ruimte hebt u nodig?
* Welk cluster type moet u implementeren?
* Welke grootte en welk type virtuele machine (VM) moet uw cluster knooppunten gebruiken?
* Hoeveel worker-knoop punten moet uw cluster hebben?

## <a name="choose-an-azure-region"></a>Een Azure-regio kiezen

De Azure-regio bepaalt waar uw cluster fysiek is ingericht. Voor een minimale latentie van lees-en schrijf bewerkingen moet het cluster zich in de buurt van uw gegevens bevinden.

HDInsight is beschikbaar in veel Azure-regio's. Als u de dichtstbijzijnde regio wilt vinden, raadpleegt u de vermelding *HDInsight* onder *analyse* in [producten die per regio beschikbaar zijn](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Opslag locatie en-grootte kiezen

### <a name="location-of-default-storage"></a>Locatie van standaard opslag

De standaard opslag, een Azure Storage account of Azure Data Lake Storage, moet zich op dezelfde locatie bestaan als uw cluster. Azure Storage is beschikbaar op alle locaties. Data Lake Storage Gen1 is beschikbaar in sommige regio's: Zie de huidige Data Lake Storage Beschik baarheid onder *opslag* in [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Locatie van bestaande gegevens

Als u al een opslag account of Data Lake Storage hebt met uw gegevens en deze opslag wilt gebruiken als de standaard opslag van uw cluster, moet u uw cluster op diezelfde locatie implementeren.

### <a name="storage-size"></a>Opslaggrootte

Nadat u een HDInsight-cluster hebt geïmplementeerd, kunt u aanvullende Azure Storage accounts koppelen of andere Data Lake Storage gebruiken. Al uw opslag accounts moeten zich op dezelfde locatie bevinden als uw cluster. Een Data Lake Storage kan zich op een andere locatie bevinden, hoewel dit een latentie voor lezen/schrijven van gegevens kan veroorzaken.

Azure Storage heeft een [](../azure-subscription-service-limits.md#storage-limits)aantal capaciteits limieten, terwijl data Lake Storage gen1 vrijwel onbeperkt is.

Een cluster kan toegang krijgen tot een combi natie van verschillende opslag accounts. Typische voor beelden zijn:

* Wanneer de hoeveelheid gegevens waarschijnlijk de opslag capaciteit van één Blob Storage-container overschrijdt.
* Wanneer de snelheid van toegang tot de BLOB-container de drempel waarde kan overschrijden wanneer er sprake is van beperking.
* Wanneer u gegevens wilt maken, bent u al geüpload naar een BLOB-container die beschikbaar is voor het cluster.
* Als u verschillende onderdelen van de opslag ruimte wilt isoleren om redenen van beveiliging, of om het beheer te vereenvoudigen.

Voor een cluster met een 48-knoop punt raden we u aan 4 tot 8 opslag accounts aan te bieden. Hoewel er mogelijk al voldoende totale opslag ruimte beschikbaar is, biedt elk opslag account extra netwerk bandbreedte voor de reken knooppunten. Wanneer u meerdere opslag accounts hebt, gebruikt u een wille keurige naam voor elk opslag account, zonder voor voegsel. Het doel van een wille keurige naam is het verminderen van de kans op opslag knelpunten (beperking) of fouten in de algemene modus voor alle accounts. Gebruik slechts één container per opslag account voor betere prestaties.

## <a name="choose-a-cluster-type"></a>Een cluster type kiezen

Het cluster type bepaalt de werk belasting die uw HDInsight-cluster is geconfigureerd om te worden uitgevoerd, zoals [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)of [Apache Spark](https://spark.apache.org/). Zie [Inleiding tot Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight)voor een gedetailleerde beschrijving van de beschik bare cluster typen. Elk cluster type heeft een specifieke implementatie topologie met vereisten voor de grootte en het aantal knoop punten.

## <a name="choose-the-vm-size-and-type"></a>De grootte en het type van de virtuele machine kiezen

Elk cluster type heeft een reeks knooppunt typen en elk knooppunt type heeft specifieke opties voor de VM-grootte en het bijbehorende type.

Als u de optimale cluster grootte voor uw toepassing wilt bepalen, kunt u een bench Mark-cluster capaciteit opgeven en de opgegeven grootte verg Roten. U kunt bijvoorbeeld een gesimuleerde werk belasting of een *Canarische query*gebruiken. Met een gesimuleerde werk belasting voert u uw verwachte workloads uit op verschillende clusters, waardoor de grootte geleidelijk wordt verhoogd tot de gewenste prestaties zijn bereikt. Een Canarische query kan regel matig worden ingevoegd tussen de andere productie query's om aan te geven of het cluster voldoende bronnen heeft.

De grootte en het type van de virtuele machine zijn afhankelijk van CPU-verwerkings kracht, RAM-grootte en netwerk latentie:

* CPU: De VM-grootte bepaalt het aantal kernen. Hoe meer kernen, des te groter de mate van parallelle reken kracht van elk knoop punt kan worden gerealiseerd. Daarnaast hebben sommige VM-typen snellere kernen.

* RAM: De VM-grootte bepaalt ook de hoeveelheid RAM-geheugen dat beschikbaar is in de virtuele machine. Voor werk belastingen waarbij gegevens in het geheugen worden opgeslagen voor verwerking, in plaats van de schijf te lezen, moet u ervoor zorgen dat uw worker-knoop punten voldoende geheugen hebben om de gegevens te passen.

* Netwerk Voor de meeste cluster typen worden de gegevens die door het cluster worden verwerkt, niet op de lokale schijf opgeslagen, maar in een externe opslag service, zoals Data Lake Storage of Azure Storage. Houd rekening met de netwerk bandbreedte en door Voer tussen de VM van het knoop punt en de opslag service. De netwerk bandbreedte die beschikbaar is voor een virtuele machine neemt doorgaans toe met grotere grootten. Zie [overzicht van VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)-grootten voor meer informatie.

## <a name="choose-the-cluster-scale"></a>De cluster schaal kiezen

De schaal van een cluster wordt bepaald door het aantal VM-knoop punten. Voor alle cluster typen zijn er knooppunt typen met een specifieke schaal en knooppunt typen die ondersteuning bieden voor uitschalen. Een cluster kan bijvoorbeeld precies drie [Apache ZooKeeper](https://zookeeper.apache.org/) knoop punten of twee hoofd knooppunten hebben. Werk knooppunten die gegevens verwerking uitvoeren op een gedistribueerde manier kunnen profiteren van uitbrei ding door extra worker-knoop punten toe te voegen.

Afhankelijk van het type cluster, voegt het aantal worker-knoop punten extra reken capaciteit toe (zoals meer kernen), maar kan ook worden toegevoegd aan de totale hoeveelheid geheugen die nodig is voor het hele cluster om in-Memory opslag te ondersteunen van de gegevens die worden verwerkt. Net als bij de keuze van de grootte en het type van de virtuele machine, wordt het selecteren van de juiste cluster schaal meestal empirisch bereikt, met behulp van gesimuleerde werk belastingen of Canarische query's.

U kunt uw cluster uitschalen om te voldoen aan de maximale belasting vereisten en vervolgens weer opschalen wanneer deze extra knoop punten niet meer nodig zijn. Met de functie voor automatisch [schalen](hdinsight-autoscale-clusters.md) kunt u uw cluster op basis van vooraf ingestelde metrische gegevens en tijdstippen aanpassen. Zie [HDInsight-clusters schalen](hdinsight-scaling-best-practices.md)voor meer informatie over het hand matig schalen van clusters.

### <a name="cluster-lifecycle"></a>Cluster levenscyclus

Er worden kosten in rekening gebracht voor de levens duur van een cluster. Als er alleen specifieke tijden zijn dat u uw cluster actief moet hebben, kunt u [op aanvraag clusters maken met behulp van Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). U kunt ook Power shell-scripts maken die uw cluster inrichten en verwijderen, en deze scripts vervolgens plannen met behulp van [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Wanneer een cluster wordt verwijderd, wordt de standaard Hive-metastore ook verwijderd. Als u de meta Store voor het opnieuw maken van het volgende cluster wilt behouden, gebruikt u een externe metagegevensarchiefmethode, zoals Azure data base of [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Cluster taak fouten isoleren

Soms kunnen er fouten optreden vanwege de parallelle uitvoering van meerdere toewijzingen en het verminderen van de onderdelen op een cluster met meerdere knoop punten. Om het probleem op te lossen, kunt u gedistribueerde tests uitvoeren door gelijktijdige meerdere taken uit te voeren op een knoop punt cluster met één worker en vervolgens deze benadering uit te vouwen om meerdere taken gelijktijdig uit te voeren op clusters met meer dan één knoop punt. Als u een HDInsight-cluster met één knoop punt in azure wilt maken, gebruikt u de optie *aangepast (grootte, instellingen, apps)* en gebruikt u een waarde van 1 voor het *aantal worker-knoop punten* in de sectie **cluster grootte** bij het inrichten van een nieuw cluster in de portal.

U kunt ook een ontwikkel omgeving met één knoop punt op uw lokale computer installeren en daar de oplossing testen. Hortonworks biedt een lokale ontwikkel omgeving met één knoop punt voor op Hadoop gebaseerde oplossingen die nuttig is voor de eerste ontwikkeling, het testen van concepten en tests. Zie [Hortonworks sandbox](https://hortonworks.com/products/hortonworks-sandbox/)voor meer informatie.

Als u het probleem wilt identificeren op een lokaal cluster met één knoop punt, kunt u mislukte taken opnieuw uitvoeren en de invoer gegevens aanpassen of kleinere gegevens sets gebruiken. Hoe u deze taken uitvoert, is afhankelijk van het platform en het type toepassing.

## <a name="quotas"></a>Quota

Nadat u de VM-grootte,-schaal en-type van het doel cluster hebt bepaald, controleert u de huidige limieten voor quotum capaciteit van uw abonnement. Wanneer u een quotum limiet bereikt, kunt u mogelijk geen nieuwe clusters implementeren of bestaande clusters uitschalen door meer werk knooppunten toe te voegen. De enige quotum limiet is het quotum voor CPU-kernen dat voor elk abonnement bestaat op het niveau van de regio. Het is bijvoorbeeld mogelijk dat uw abonnement 30 kern limieten heeft in de regio VS-Oost. Als u een quota verhoging wilt aanvragen, voert u de volgende stappen uit:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer **Help en ondersteuning** aan de linkerkant van de pagina.
1. Selecteer een **nieuwe ondersteunings aanvraag**.
1. Selecteer op de pagina **nieuwe ondersteunings aanvraag** , onder tabblad **basis beginselen** , de volgende opties:
   - **Type probleem**: **Service-en abonnements limieten (quota's)**
   - **Abonnement**: het abonnement dat u wilt wijzigen
   - **Quotum type**: **HDInsight**
    
     ![Een ondersteunings aanvraag maken om het HDInsight-kern quotum te verhogen](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecteer **Volgende: Oplossingen > >** .
1. Voer op de pagina **Details** een beschrijving van het probleem in, selecteer de ernst van het probleem, uw favoriete contact wijze en andere vereiste velden.
1. Selecteer **Volgende: Bekijk + Create > >** .
1. Selecteer op het tabblad **controleren en maken** de optie **maken**.

> [!NOTE]  
> Als u het HDInsight core-quotum moet verhogen in een privé gebied, [dient u een white list-aanvraag](https://aka.ms/canaryintwhitelist)in.

U kunt [contact opnemen met de ondersteuning om een quotum verhoging aan te vragen](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Er zijn echter enkele vaste quota limieten, bijvoorbeeld één Azure-abonnement kan Maxi maal 10.000 kernen hebben. Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-subscription-service-limits)voor meer informatie over deze limieten.

## <a name="next-steps"></a>Volgende stappen

* [Stel clusters in HDInsight in met Apache Hadoop, Spark, Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md): Meer informatie over het instellen en configureren van clusters in HDInsight met Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services of storm.
* [Cluster prestaties bewaken](hdinsight-key-scenarios-to-monitor.md): Meer informatie over de belangrijkste scenario's voor het bewaken van uw HDInsight-cluster die van invloed kunnen zijn op de capaciteit van uw cluster.
