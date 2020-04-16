---
title: Spark-taken optimaliseren voor prestaties in Azure Synapse Analytics
description: In dit artikel vindt u een inleiding tot Apache Spark in Azure Synapse Analytics en de verschillende concepten.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6ffe7f3d9faf82c892975e9ffa03b383d3610c36
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424620"
---
# <a name="optimize-apache-spark-jobs-preview-in-azure-synapse-analytics"></a>Apache Spark-taken optimaliseren (preview) in Azure Synapse Analytics

Meer informatie over het optimaliseren van [de clusterconfiguratie](https://spark.apache.org/) van Apache Spark voor uw specifieke workload.  De meest voorkomende uitdaging is geheugendruk, vanwege onjuiste configuraties (met name fout-sized uitvoerders), langlopende bewerkingen en taken die resulteren in Cartesiaanse bewerkingen. U taken versnellen met de juiste caching en door [gegevensscheefte .](#optimize-joins-and-shuffles) Controleer en bekijk langlopende en resource-verbruikende Spark-taakuitvoeringen voor de beste prestaties.

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

Het beste formaat voor prestaties is parket met *pittige compressie,* dat is de standaard in Spark 2.x. Parket slaat gegevens op in columnar-formaat en is sterk geoptimaliseerd in Spark. Bovendien, terwijl *pittige compressie* kan resulteren in grotere bestanden dan zeggen gzip compressie. Vanwege de splittable aard van die bestanden zullen ze sneller decomprimeren]

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt zijn eigen native caching mechanismen, die kunnen `.persist()`worden `.cache()`gebruikt `CACHE TABLE`door middel van verschillende methoden, zoals , , en . Deze native caching is effectief met kleine gegevenssets en in ETL-pijplijnen waar u tussentijdse resultaten moet cachen. Spark native caching werkt momenteel echter niet goed met partitioneren, omdat een tabel in de cache de partitioneringsgegevens niet bewaart.

## <a name="use-memory-efficiently"></a>Geheugen efficiënt gebruiken

Spark werkt door het plaatsen van gegevens in het geheugen, dus het beheren van geheugenbronnen is een belangrijk aspect van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u toepassen om het geheugen van uw cluster efficiënt te gebruiken.

* Geef de voorkeur aan kleinere gegevenspartities en houd rekening met de grootte, typen en distributie van gegevens in uw partitioneringsstrategie.
* Overweeg de nieuwere, efficiëntere [Kryo-gegevensserialisatie](https://github.com/EsotericSoftware/kryo), in plaats van de standaard Java-serialisatie.
* Spark-configuratie-instellingen controleren en afstemmen.

Ter referentie worden de Spark-geheugenstructuur en enkele belangrijke uitvoergeheugenparameters weergegeven in de volgende afbeelding.

### <a name="spark-memory-considerations"></a>Overwegingen voor het geheugen

Apache Spark in Azure Synapse maakt gebruik van YARN [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), YARN regelt de maximale som van het geheugen die wordt gebruikt door alle containers op elk Spark-knooppunt.  In het volgende diagram worden de belangrijkste objecten en hun relaties weergegeven.

![YARN Spark-geheugenbeheer](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Als u 'out of memory'-berichten wilt aanpakken, probeert u:

* Review DAG Management Shuffles. Verminder door brongegevens aan de kaartzijde te verminderen, brongegevens vooraf te partitioneren (of te bucketen), enkele shuffles te maximaliseren en de hoeveelheid verzonden gegevens te verminderen.
* Geef `ReduceByKey` de voorkeur aan `GroupByKey`de vaste geheugenlimiet voor , die aggregaties, windowing en andere functies biedt, maar het heeft ann onbegrensde geheugenlimiet.
* Liever `TreeReduce`, die meer werk doet op de `Reduce`uitvoerders of partities, om , die alle werk doet op de bestuurder.
* Gebruik DataFrames in plaats van de RDD-objecten op een lager niveau.
* Complexetypen maken die acties inkapselen, zoals 'Top N', verschillende aggregaties of vensterbewerkingen.

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

### <a name="select-the-correct-executor-size"></a>De juiste executorgrootte selecteren

Houd bij het bepalen van uw configuratie van de uitvoerder rekening met de overhead van java-garbage collection (GC).

* Factoren om de grootte van de executeur te verkleinen:
  * Verminder de heapgrootte onder de 32 GB om gc-overhead < 10% te houden.
  * Verminder het aantal cores om GC-overhead < 10% te houden.

* Factoren om de uitvoerdergrootte te vergroten:
  * Verminder de communicatieoverhead tussen uitvoerders.
  * Verminder het aantal open verbindingen tussen uitvoerders (N2) op grotere clusters (>100 uitvoerders).
  * Vergroot de heapgrootte om aan te passen aan geheugenintensieve taken.
  * Optioneel: verminder de overhead overhead per executeur geheugen.
  * Optioneel: Verhoog het gebruik en de gelijktijdigheid door cpu's te overschrijven.

Als algemene vuistregel bij het selecteren van de grootte van de executeur:

* Begin met 30 GB per uitvoerder en distribueer beschikbare machinecores.
* Verhoog het aantal executorkernen voor grotere clusters (> 100 uitvoerders).
* Wijzig de grootte op basis van zowel proefuitvoeringen als op de voorgaande factoren zoals GC-overhead.

Houd bij het uitvoeren van gelijktijdige query's rekening met het volgende:

* Begin met 30 GB per uitvoerder en alle machinecores.
* Maak meerdere parallelle Spark-toepassingen door cpu te overschrijven (ongeveer 30% latentieverbetering).
* Query's distribueren over parallelle toepassingen.
* Wijzig de grootte op basis van zowel proefuitvoeringen als op de voorgaande factoren zoals GC-overhead.

Controleer uw queryprestaties op uitschieters of andere prestatieproblemen door te kijken naar de tijdlijnweergave, SQL-grafiek, taakstatistieken, enzovoort. Soms zijn een of enkele van de uitvoerders langzamer dan de andere, en taken duren veel langer uit te voeren. Dit gebeurt vaak op grotere clusters (> 30 knooppunten). In dit geval verdeelt u het werk in een groter aantal taken, zodat de planner trage taken kan compenseren. 

Hebben bijvoorbeeld minstens twee keer zoveel taken als het aantal executorkernen in de toepassing. U ook speculatieve uitvoering `conf: spark.speculation = true`van taken inschakelen met.

## <a name="optimize-job-execution"></a>Taakuitvoering optimaliseren

* Cache indien nodig, bijvoorbeeld als u de gegevens twee keer gebruikt, en deze vervolgens in de cache.
* Variabelen uitzenden naar alle uitvoerders. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere lookups.
* Gebruik de thread pool op het stuurprogramma, wat resulteert in een snellere werking voor veel taken.

De sleutel tot de queryprestaties van Spark 2.x is de Tungsten-engine, die afhankelijk is van het genereren van code in hele fase. In sommige gevallen kan het genereren van code in hele fase worden uitgeschakeld. 

Als u bijvoorbeeld een niet-mutable`string`type ( ) `SortAggregate` gebruikt in `HashAggregate`de aggregatieexpressie, wordt deze weergegeven in plaats van . Probeer bijvoorbeeld het volgende en schakel codegeneratie opnieuw in:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

- [Apache Spark afstemmen](https://spark.apache.org/docs/latest/tuning.html)
- [Hoe je eigenlijk tune uw Apache Spark Jobs, zodat ze werken](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo Serialisatie](https://github.com/EsotericSoftware/kryo)
