---
title: Spark-taken optimaliseren voor prestaties - Azure HDInsight
description: Toon algemene strategieën voor de beste prestaties van Apache Spark-clusters in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 3d8f4a28961be7e0ece517e00026d9711d8f67e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198868"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark-taken optimaliseren in HDInsight

Meer informatie over het optimaliseren van [de clusterconfiguratie](https://spark.apache.org/) van Apache Spark voor uw specifieke workload.  De meest voorkomende uitdaging is geheugendruk, vanwege onjuiste configuraties (met name fout-sized uitvoerders), langlopende bewerkingen en taken die resulteren in Cartesiaanse bewerkingen. U taken versnellen met de juiste caching en door [gegevensscheefte .](#optimize-joins-and-shuffles) Controleer en bekijk langlopende en resource-verbruikende Spark-taakuitvoeringen voor de beste prestaties. Zie [Apache Spark-cluster maken met Azure-portal](apache-spark-jupyter-spark-sql-use-portal.md)voor informatie over aan de slag gaan met Apache Spark op HDInsight.

In de volgende secties worden veelvoorkomende spark-taakoptimalisaties en -aanbevelingen beschreven.

## <a name="choose-the-data-abstraction"></a>Kies de gegevensabstractie

Eerdere Spark-versies gebruiken RDD's om gegevens te abstraheren, Spark 1.3 en 1.6 introduceerden respectievelijk DataFrames en DataSets. Houd rekening met de volgende relatieve verdiensten:

* **DataFrames**
    * Beste keuze in de meeste situaties.
    * Biedt queryoptimalisatie via Catalyst.
    * Hele fase code generatie.
    * Directe toegang tot het geheugen.
    * Lage garbage collection (GC) overhead.
    * Niet zo projectvriendelijk als DataSets, omdat er geen compile-time checks of domeinobjectprogrammering zijn.
* **Datasets**
    * Goed in complexe ETL-pijplijnen waar de impact op de prestaties acceptabel is.
    * Niet goed in aggregaties waar de impact op de prestaties aanzienlijk kan zijn.
    * Biedt queryoptimalisatie via Catalyst.
    * Ontwikkelaarvriendelijk door het aanbieden van domeinobjectprogrammering en compile-time checks.
    * Hiermee voegt u serialisatie/deserialisatieoverhead toe.
    * Hoge GC overhead.
    * Breekt hele fase code generatie.
* **DTO's**
    * U hoeft geen RDD's te gebruiken, tenzij u een nieuwe aangepaste RDD moet bouwen.
    * Geen query optimalisatie via Catalyst.
    * Geen hele fase code generatie.
    * Hoge GC overhead.
    * Moet Spark 1.x legacy API's gebruiken.

## <a name="use-optimal-data-format"></a>Optimale gegevensindeling gebruiken

Spark ondersteunt vele formaten, zoals csv, json, xml, parket, orc en avro. Spark kan worden uitgebreid om veel meer formaten te ondersteunen met externe gegevensbronnen - voor meer informatie, zie [Apache Spark-pakketten](https://spark-packages.org).

Het beste formaat voor prestaties is parket met *pittige compressie,* dat is de standaard in Spark 2.x. Parket slaat gegevens op in columnar-formaat en is sterk geoptimaliseerd in Spark.

## <a name="select-default-storage"></a>Standaardopslag selecteren

Wanneer u een nieuw Spark-cluster maakt, u Azure Blob Storage of Azure Data Lake Storage selecteren als de standaardopslag van uw cluster. Beide opties bieden u het voordeel van langdurige opslag voor tijdelijke clusters, zodat uw gegevens niet automatisch worden verwijderd wanneer u uw cluster verwijdert. U een tijdelijk cluster opnieuw maken en toch toegang krijgen tot uw gegevens.

| Winkeltype | Bestandssysteem | Snelheid | Voorbijgaande | Use Cases |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**,,url/ | **Standaard** | Ja | Transiënt cluster |
| Azure Blob-opslag (veilig) | **wasbs:**/url/ | **Standaard** | Ja | Transiënt cluster |
| Azure Data Lake Storage Gen 2| **abfs:**/url/ | **Sneller** | Ja | Transiënt cluster |
| Azure Data Lake Storage Gen 1| **adl:**,,url/ | **Sneller** | Ja | Transiënt cluster |
| Lokale HDFS | **hdfs:**/url/ | **Snelste** | Nee | Interactief 24/7 cluster |

Zie [Opslagopties vergelijken voor gebruik met Azure HDInsight-clusters voor](../hdinsight-hadoop-compare-storage-options.md)een volledige beschrijving van de beschikbare opslagopties voor HDInsight-clusters.

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt zijn eigen native caching mechanismen, die kunnen `.persist()`worden `.cache()`gebruikt `CACHE TABLE`door middel van verschillende methoden, zoals , , en . Deze native caching is effectief met kleine gegevenssets en in ETL-pijplijnen waar u tussentijdse resultaten moet cachen. Spark native caching werkt momenteel echter niet goed met partitioneren, omdat een tabel in de cache de partitioneringsgegevens niet bewaart. Een meer generieke en betrouwbare caching techniek is *opslag laag caching*.

* Native Spark caching (niet aanbevolen)
    * Goed voor kleine datasets.
    * Werkt niet met partitionering, wat kan veranderen in toekomstige Spark-releases.

* Cache van opslagniveau (aanbevolen)
    * Kan worden geïmplementeerd op HDInsight met behulp van de [IO-cache](apache-spark-improve-performance-iocache.md) functie.
    * Gebruikt in-memory en SSD caching.

* Lokale HDFS (aanbevolen)
    * `hdfs://mycluster`Pad.
    * Maakt gebruik van SSD caching.
    * Gegevens in de cache gaan verloren wanneer u het cluster verwijdert, waardoor een cache moet worden hersteld.

## <a name="use-memory-efficiently"></a>Geheugen efficiënt gebruiken

Spark werkt door het plaatsen van gegevens in het geheugen, dus het beheren van geheugenbronnen is een belangrijk aspect van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u toepassen om het geheugen van uw cluster efficiënt te gebruiken.

* Geef de voorkeur aan kleinere gegevenspartities en houd rekening met de grootte, typen en distributie van gegevens in uw partitioneringsstrategie.
* Overweeg de nieuwere, efficiëntere [Kryo-gegevensserialisatie](https://github.com/EsotericSoftware/kryo), in plaats van de standaard Java-serialisatie.
* Liever met behulp van `spark-submit` GAREN, omdat het scheidt per partij.
* Spark-configuratie-instellingen controleren en afstemmen.

Ter referentie worden de Spark-geheugenstructuur en enkele belangrijke uitvoergeheugenparameters weergegeven in de volgende afbeelding.

### <a name="spark-memory-considerations"></a>Overwegingen voor het geheugen

Als u [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)gebruikt, regelt YARN de maximale som geheugen die door alle containers op elk Spark-knooppunt wordt gebruikt.  In het volgende diagram worden de belangrijkste objecten en hun relaties weergegeven.

![YARN Spark-geheugenbeheer](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Als u 'out of memory'-berichten wilt aanpakken, probeert u:

* Review DAG Management Shuffles. Verminder door map-side reducting, pre-partitie (of bucketize) brongegevens, maximaliseren van enkele shuffles, en vermindering van de hoeveelheid verzonden gegevens.
* Geef `ReduceByKey` de voorkeur aan `GroupByKey`de vaste geheugenlimiet voor , die aggregaties, windowing en andere functies biedt, maar het heeft ann onbegrensde geheugenlimiet.
* Liever `TreeReduce`, die meer werk doet op de `Reduce`uitvoerders of partities, om , die alle werk doet op de bestuurder.
* Gebruik DataFrames in plaats van de RDD-objecten op een lager niveau.
* Complexetypen maken die acties inkapselen, zoals 'Top N', verschillende aggregaties of vensterbewerkingen.

Zie Uitzonderingen op [OutOfMemoryError voor Apache Spark in Azure HDInsight voor](apache-spark-troubleshoot-outofmemory.md)aanvullende stappen voor het oplossen van problemen.

## <a name="optimize-data-serialization"></a>Gegevensserialisatie optimaliseren

Spark-taken worden gedistribueerd, dus passende gegevensserialisatie is belangrijk voor de beste prestaties.  Er zijn twee serialisatieopties voor Spark:

* Java serialisatie is de standaardinstelling.
* Kryo serialisatie is een nieuwer formaat en kan resulteren in snellere en compactere serialisatie dan Java.  Kryo vereist dat u de lessen in uw programma registreert en het ondersteunt nog niet alle Serializable-typen.

## <a name="use-bucketing"></a>Bucketing gebruiken

Bucketing is vergelijkbaar met gegevenspartitionering, maar elke bucket kan een set kolomwaarden bevatten in plaats van slechts één. Bucketing werkt goed voor het partitioneren op grote (in de miljoenen of meer) aantallen waarden, zoals product-id's. Een emmer wordt bepaald door hashing de emmersleutel van de rij. Bucketed tables bieden unieke optimalisaties omdat ze metadata opslaan over hoe ze zijn geemmerd en gesorteerd.

Enkele geavanceerde bucketing functies zijn:

* Queryoptimalisatie op basis van bucketing meta-informatie.
* Geoptimaliseerde aggregaties.
* Geoptimaliseerde joins.

U partitionering en bucketing tegelijkertijd gebruiken.

## <a name="optimize-joins-and-shuffles"></a>Joins en shuffles optimaliseren

Als u langzame taken hebt op een Join of Shuffle, is de oorzaak waarschijnlijk *gegevensscheeftrekking*, wat asymmetrie is in uw taakgegevens. Een kaarttaak kan bijvoorbeeld 20 seconden duren, maar het uitvoeren van een taak waarbij de gegevens worden samengevoegd of geschud, duurt uren. Als u gegevensscheefheid wilt oplossen, moet u de hele sleutel zoutmaken of een *geïsoleerd zout* gebruiken voor slechts enkele subset sleutels. Als u een geïsoleerd zout gebruikt, moet u verder filteren om uw subset van gezouten sleutels in kaartjoins te isoleren. Een andere optie is om eerst een emmerkolom en pre-aggregaat in buckets in te voeren.

Een andere factor die langzame joins veroorzaakt, kan het jointype zijn. Spark gebruikt standaard `SortMerge` het jointype. Dit type join is het meest geschikt voor grote gegevenssets, maar is verder rekenkundig duur omdat het eerst de linker- en rechterkant van gegevens moet sorteren voordat ze worden samengevoegd.

Een `Broadcast` join is het meest geschikt voor kleinere gegevenssets, of waar de ene kant van de join veel kleiner is dan de andere kant. Dit type van join zendt één kant aan alle uitvoerders uit, en vereist zo meer geheugen voor uitzendingen in het algemeen.

U het jointype in `spark.sql.autoBroadcastJoinThreshold`uw configuratie wijzigen door in te stellen of`dataframe.join(broadcast(df2))`u een joinhint instellen met behulp van de DataFrame API's ( ).

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Als u bucketed tabellen gebruikt, hebt u een `Merge` derde jointype, de join. Een correct vooraf gepartitioneerde en vooraf gesorteerde gegevensset slaat `SortMerge` de dure sorteerfase van een join over.

De volgorde van joins zaken, met name in meer complexe query's. Begin met de meest selectieve joins. Verplaats ook joins die het aantal rijen na aggregaties verhogen wanneer mogelijk.

Als u parallellisme voor Cartesiaanse joins wilt beheren, u geneste structuren toevoegen, venster plaatsen en misschien een of meer stappen overslaan in uw Spark-taak.

## <a name="customize-cluster-configuration"></a>Clusterconfiguratie aanpassen

Afhankelijk van de spark-clusterworkload u bepalen dat een niet-standaard Spark-configuratie zou resulteren in een meer geoptimaliseerde Spark-taakuitvoering.  Voer benchmarktests uit met voorbeeldworkloads om niet-standaardclusterconfiguraties te valideren.

Hier volgen enkele veelvoorkomende parameters die u aanpassen:

* `--num-executors`hiermee wordt het juiste aantal uitvoerders ingesteld.
* `--executor-cores`hiermee wordt het aantal kernen voor elke uitvoerder ingesteld. Meestal moet u middelgrote uitvoerders hebben, omdat andere processen een deel van het beschikbare geheugen verbruiken.
* `--executor-memory`hiermee wordt de geheugengrootte ingesteld voor elke executeur, die de heapgrootte op GAREN regelt. Je moet wat geheugen achterlaten voor de uitvoering overhead.

### <a name="select-the-correct-executor-size"></a>De juiste executorgrootte selecteren

Houd bij het bepalen van uw configuratie van de uitvoerder rekening met de overhead van java-garbage collection (GC).

* Factoren om de grootte van de executeur te verkleinen:
    1. Verminder de heapgrootte onder de 32 GB om gc-overhead < 10% te houden.
    2. Verminder het aantal cores om GC-overhead < 10% te houden.

* Factoren om de uitvoerdergrootte te vergroten:
    1. Verminder de communicatieoverhead tussen uitvoerders.
    2. Verminder het aantal open verbindingen tussen uitvoerders (N2) op grotere clusters (>100 uitvoerders).
    3. Vergroot de heapgrootte om aan te passen aan geheugenintensieve taken.
    4. Optioneel: verminder de overhead overhead per executeur geheugen.
    5. Optioneel: Verhoog het gebruik en de gelijktijdigheid door cpu's te overschrijven.

Als algemene vuistregel bij het selecteren van de grootte van de executeur:

1. Begin met 30 GB per uitvoerder en distribueer beschikbare machinecores.
2. Verhoog het aantal executorkernen voor grotere clusters (> 100 uitvoerders).
3. Wijzig de grootte op basis van zowel proefuitvoeringen als op de voorgaande factoren zoals GC-overhead.

Houd bij het uitvoeren van gelijktijdige query's rekening met het volgende:

1. Begin met 30 GB per uitvoerder en alle machinecores.
2. Maak meerdere parallelle Spark-toepassingen door cpu te overschrijven (ongeveer 30% latentieverbetering).
3. Query's distribueren over parallelle toepassingen.
4. Wijzig de grootte op basis van zowel proefuitvoeringen als op de voorgaande factoren zoals GC-overhead.

Zie [Apache Spark-instellingen - Spark-uitvoerders](apache-spark-settings.md#configuring-spark-executors)voor meer informatie over het gebruik van Ambari om uitvoerders te configureren.

Controleer uw queryprestaties op uitschieters of andere prestatieproblemen door te kijken naar de tijdlijnweergave, SQL-grafiek, taakstatistieken, enzovoort. Zie [Debug Apache Spark-taken die worden uitgevoerd op Azure HDInsight](apache-spark-job-debugging.md)voor informatie over het opsporen van Spark-taken met GAREN en de Spark-geschiedenisserver. Zie [Access Apache Hadoop YARN-toepassingslogboeken](../hdinsight-hadoop-access-yarn-app-logs-linux.md)voor tips over het gebruik van YARN Timeline Server.

Soms zijn een of enkele van de uitvoerders langzamer dan de andere, en taken duren veel langer uit te voeren. Dit gebeurt vaak op grotere clusters (> 30 knooppunten). In dit geval verdeelt u het werk in een groter aantal taken, zodat de planner trage taken kan compenseren. Hebben bijvoorbeeld minstens twee keer zoveel taken als het aantal executorkernen in de toepassing. U ook speculatieve uitvoering `conf: spark.speculation = true`van taken inschakelen met.

## <a name="optimize-job-execution"></a>Taakuitvoering optimaliseren

* Cache indien nodig, bijvoorbeeld als u de gegevens twee keer gebruikt, en deze vervolgens in de cache.
* Variabelen uitzenden naar alle uitvoerders. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere lookups.
* Gebruik de thread pool op het stuurprogramma, wat resulteert in een snellere werking voor veel taken.

Houd uw hardlooptaken regelmatig in de gaten voor prestatieproblemen. Als u meer inzicht in bepaalde problemen nodig hebt, u een van de volgende tools voor prestatieprofilering overwegen:

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) bewaakt het gebruik van CPU,opslag en netwerkbandbreedte.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) profielen Spark en executor code.

De sleutel tot de queryprestaties van Spark 2.x is de Tungsten-engine, die afhankelijk is van het genereren van code in hele fase. In sommige gevallen kan het genereren van code in hele fase worden uitgeschakeld. Als u bijvoorbeeld een niet-mutable`string`type ( ) `SortAggregate` gebruikt in `HashAggregate`de aggregatieexpressie, wordt deze weergegeven in plaats van . Probeer bijvoorbeeld het volgende en schakel codegeneratie opnieuw in:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

* [Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight](apache-spark-job-debugging.md)
* [Resources beheren voor een Apache Spark-cluster op HDInsight](apache-spark-resource-manager.md)
* [De Apache Spark REST API gebruiken om externe taken in te dienen bij een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [Apache Spark afstemmen](https://spark.apache.org/docs/latest/tuning.html)
* [Hoe je eigenlijk tune uw Apache Spark Jobs, zodat ze werken](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo Serialisatie](https://github.com/EsotericSoftware/kryo)
