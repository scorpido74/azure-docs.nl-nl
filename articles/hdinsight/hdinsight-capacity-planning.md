---
title: Cluster capaciteit plannen in azure HDInsight
description: Bepaal de belangrijkste vragen voor capaciteit en prestaties plannen van een Azure HDInsight-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78932677"
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

HDInsight is beschikbaar in veel Azure-regio's. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)om de dichtstbijzijnde regio te vinden.

## <a name="choose-storage-location-and-size"></a>Opslag locatie en-grootte kiezen

### <a name="location-of-default-storage"></a>Locatie van standaard opslag

De standaard opslag, een Azure Storage account of Azure Data Lake Storage, moet zich op dezelfde locatie bestaan als uw cluster. Azure Storage is beschikbaar op alle locaties. Data Lake Storage Gen1 is beschikbaar in sommige regio's: Bekijk de huidige [Data Lake Storage Beschik baarheid](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Locatie van bestaande gegevens

Als u al een opslag account of Data Lake Storage hebt met uw gegevens en deze opslag wilt gebruiken als de standaard opslag van uw cluster, moet u uw cluster op diezelfde locatie implementeren.

### <a name="storage-size"></a>Opslag grootte

Nadat u een HDInsight-cluster hebt geïmplementeerd, kunt u aanvullende Azure Storage accounts koppelen of andere Data Lake Storage gebruiken. Al uw opslag accounts moeten zich op dezelfde locatie bevinden als uw cluster. Een Data Lake Storage kan zich op een andere locatie bevinden, hoewel dit een latentie voor lezen/schrijven van gegevens kan veroorzaken.

Azure Storage heeft een aantal [capaciteits limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), terwijl data Lake Storage gen1 vrijwel onbeperkt is.

Een cluster kan toegang krijgen tot een combi natie van verschillende opslag accounts. Typische voor beelden zijn:

* Wanneer de hoeveelheid gegevens waarschijnlijk de opslag capaciteit van één Blob Storage-container overschrijdt.
* Wanneer de snelheid van toegang tot de BLOB-container de drempel waarde kan overschrijden wanneer er sprake is van beperking.
* Wanneer u gegevens wilt maken, bent u al geüpload naar een BLOB-container die beschikbaar is voor het cluster.
* Als u verschillende onderdelen van de opslag ruimte wilt isoleren om redenen van beveiliging, of om het beheer te vereenvoudigen.

Gebruik slechts één container per opslag account voor betere prestaties.

## <a name="choose-a-cluster-type"></a>Een cluster type kiezen

Het cluster type bepaalt de werk belasting die uw HDInsight-cluster is geconfigureerd om te worden uitgevoerd, zoals [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)of [Apache Spark](https://spark.apache.org/). Zie [Inleiding tot Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight)voor een gedetailleerde beschrijving van de beschik bare cluster typen. Elk cluster type heeft een specifieke implementatie topologie met vereisten voor de grootte en het aantal knoop punten.

## <a name="choose-the-vm-size-and-type"></a>De grootte en het type van de virtuele machine kiezen

Elk cluster type heeft een reeks knooppunt typen en elk knooppunt type heeft specifieke opties voor de VM-grootte en het bijbehorende type.

Als u de optimale cluster grootte voor uw toepassing wilt bepalen, kunt u een bench Mark-cluster capaciteit opgeven en de opgegeven grootte verg Roten. U kunt bijvoorbeeld een gesimuleerde werk belasting of een *Canarische query*gebruiken. Met een gesimuleerde werk belasting voert u uw verwachte workloads uit op verschillende clusters, waardoor de grootte geleidelijk wordt verhoogd tot de gewenste prestaties zijn bereikt. Een Canarische query kan regel matig worden ingevoegd tussen de andere productie query's om weer te geven of het cluster voldoende bronnen heeft.

Zie [de juiste VM-grootte voor uw cluster selecteren](hdinsight-selecting-vm-size.md)voor meer informatie over het kiezen van de juiste VM-serie voor uw werk belasting.

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

## <a name="quotas"></a>Quota

Nadat u de VM-grootte,-schaal en-type van het doel cluster hebt bepaald, controleert u de huidige limieten voor quotum capaciteit van uw abonnement. Wanneer u een quotum limiet bereikt, kunt u mogelijk geen nieuwe clusters implementeren of bestaande clusters uitschalen door meer werk knooppunten toe te voegen. De enige quotum limiet is het quotum voor CPU-kernen dat voor elk abonnement bestaat op het niveau van de regio. Het is bijvoorbeeld mogelijk dat uw abonnement 30 kern limieten heeft in de regio VS-Oost. 

Voer de volgende stappen uit om de beschik bare kernen te controleren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar de **overzichts** pagina voor het HDInsight-cluster. 
3. Klik in het menu links op **quotum limieten**.

   Op de pagina worden het aantal gebruikte kernen, het aantal beschikbare kernen en het totale aantal kernen weergegeven.

Als u een quota verhoging wilt aanvragen, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Help en ondersteuning** aan de linkerkant van de pagina.
1. Selecteer **Nieuwe ondersteuningsaanvraag**.
1. Selecteer op de pagina **nieuwe ondersteunings aanvraag** , onder tabblad **basis beginselen** , de volgende opties:

   - **Probleem type**: **service-en abonnements limieten (quota's)**
   - **Abonnement**: het abonnement dat u wilt wijzigen
   - **Quotum type**: **HDInsight**

     ![Een ondersteunings aanvraag maken om het HDInsight-kern quotum te verhogen](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Selecteer **volgende: oplossingen > >** .
1. Voer op de pagina **Details** een beschrijving van het probleem in, selecteer de ernst van het probleem, uw favoriete contact wijze en andere vereiste velden.
1. Selecteer **volgende: controleren + > > maken**.
1. Selecteer op het tabblad **controleren en maken** de optie **maken**.

> [!NOTE]  
> Als u het HDInsight core-quotum moet verhogen in een privé gebied, [dient u een white list-aanvraag](https://aka.ms/canaryintwhitelist)in.

U kunt [contact opnemen met de ondersteuning om een quotum verhoging aan te vragen](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Er zijn echter enkele vaste quota limieten, bijvoorbeeld één Azure-abonnement kan Maxi maal 10.000 kernen hebben. Zie [Azure-abonnement en service limieten, quota's en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)voor meer informatie over deze limieten.

## <a name="next-steps"></a>Volgende stappen

* [Stel clusters in hdinsight in met Apache Hadoop, Spark, Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md): informatie over het instellen en configureren van clusters in HDInsight met Apache Hadoop, Spark, Kafka, Interactive Hive, HBASE, ml Services of storm.
* [Prestaties van het cluster bewaken](hdinsight-key-scenarios-to-monitor.md): meer informatie over de belangrijkste scenario's voor het bewaken van uw HDInsight-cluster die van invloed kunnen zijn op de capaciteit van uw cluster.
