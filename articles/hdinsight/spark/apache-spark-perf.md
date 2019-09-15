---
title: Spark-taken optimaliseren voor prestaties-Azure HDInsight
description: Algemene strategieën weer geven voor de beste prestaties van Apache Spark clusters in azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 673c83c861e1f044a521786a903dd7b21db8c170
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995570"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark-taken in HDInsight optimaliseren

Meer informatie over het optimaliseren van [Apache Spark](https://spark.apache.org/) cluster configuratie voor uw specifieke werk belasting.  De meest voorkomende uitdaging is geheugen druk, vanwege onjuiste configuraties (met name verkeerde uitvoeringen), langlopende bewerkingen en taken die leiden tot Cartesische bewerkingen. U kunt taken versnellen met de juiste caching en door [gegevens scheefheid](#optimize-joins-and-shuffles)toe te staan. Voor de beste prestaties controleert en bekijkt u langlopende en resource-Consumer-uitvoeringen met Spark-taken.

In de volgende secties worden veelvoorkomende Spark-taak optimalisaties en-aanbevelingen beschreven.

## <a name="choose-the-data-abstraction"></a>De gegevens abstractie kiezen

Eerdere Spark-versies gebruiken Rdd's tot abstracte gegevens, Spark 1,3 en 1,6, respectievelijk DataFrames en gegevens sets. Houd rekening met de volgende relatieve voor delen:

* **DataFrames**
    * Beste keuze in de meeste situaties.
    * Biedt query optimalisatie via Catalyst.
    * Genereren van code in hele fase.
    * Directe geheugen toegang.
    * Overhead van lage Garbage Collection (GC).
    * Niet als gegevens sets voor ontwikkel aars, omdat er geen compilatie controles of het Program meren van domein objecten zijn.
* **Sets**
    * Goed in complexe ETL-pijp lijnen waarbij de prestaties worden beïnvloed.
    * Niet goed in aggregaties waarbij de prestatie-impact aanzienlijk kan zijn.
    * Biedt query optimalisatie via Catalyst.
    * Ontwikkel aars-vriendelijk door het opgeven van controle en compilatie van domein objecten.
    * Overhead voor serialisatie/deserialisatie toevoegen.
    * Hoge GC-overhead.
    * Het genereren van code met hele fase verbreken.
* **RDDs**
    * U hoeft Rdd's alleen te gebruiken als u een nieuwe aangepaste RDD wilt maken.
    * Geen query optimalisatie via Catalyst.
    * Geen code voor het genereren van de hele fase.
    * Hoge GC-overhead.
    * Moeten Spark 1. x verouderde Api's gebruiken.

## <a name="use-optimal-data-format"></a>Optimale gegevens indeling gebruiken

Spark ondersteunt veel indelingen, zoals CSV, JSON, XML, Parquet, Orc en AVRO. Spark kan worden uitgebreid ter ondersteuning van veel meer indelingen met externe gegevens bronnen. Zie [Apache Spark pakketten](https://spark-packages.org)voor meer informatie.

De beste prestatie-indeling is Parquet met *Snappy-compressie*. Dit is de standaard waarde in Spark 2. x. Parquet slaat gegevens op in de kolom indeling en is zeer geoptimaliseerd in Spark.

## <a name="select-default-storage"></a>Standaard opslag selecteren

Wanneer u een nieuw Spark-cluster maakt, hebt u de mogelijkheid om Azure Blob Storage of Azure Data Lake Storage te selecteren als de standaard opslag van uw cluster. Beide opties bieden u het voor deel van lange termijn opslag voor tijdelijke clusters, zodat uw gegevens niet automatisch worden verwijderd wanneer u uw cluster verwijdert. U kunt een tijdelijk cluster opnieuw maken en toch toegang krijgen tot uw gegevens.

| Opslag type | Bestandssysteem | Snelheid | Wijk | Gebruiksvoorbeelden |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:** //url/ | **Standard** | Ja | Tijdelijk cluster |
| Azure Data Lake Storage gen 2| **abfs [s]:** //URL/ | **Faster** | Ja | Tijdelijk cluster |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **Faster** | Ja | Tijdelijk cluster |
| Lokale HDFS | **hdfs:** //url/ | **Fastest** | Nee | Interactive 24/7-cluster |

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt eigen systeem eigen caching-mechanismen die kunnen worden gebruikt via verschillende methoden, zoals `.persist()`, `.cache()`en `CACHE TABLE`. Deze systeem eigen cache is effectief met kleine gegevens sets en in ETL-pijp lijnen waar u tussenliggende resultaten moet opslaan in de cache. Spark native caching werkt momenteel niet goed met partitioneren, omdat een tabel in de cache de partitie gegevens niet behoudt. Een meer generieke en betrouw bare cache techniek is het opslaan van de *opslag laag in de cache*.

* Systeem eigen Spark-caching (niet aanbevolen)
    * Geschikt voor kleine gegevens sets.
    * Werkt niet met partitioneren, wat in toekomstige Spark-releases kan veranderen.

* Caching op opslag niveau (aanbevolen)
    * Kan worden geïmplementeerd met behulp van [Alluxio](https://www.alluxio.org/).
    * Maakt gebruik van in-Memory en SSD-caching.

* Lokale HDFS (aanbevolen)
    * `hdfs://mycluster`programmapad.
    * Maakt gebruik van SSD-caching.
    * Gegevens in de cache gaan verloren wanneer u het cluster verwijdert, waardoor een cache opnieuw moet worden opgebouwd.

## <a name="use-memory-efficiently"></a>Efficiënt geheugen gebruiken

Spark werkt door gegevens in het geheugen te plaatsen, zodat het beheren van geheugen resources een belang rijk aspect is van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u kunt Toep assen om het geheugen van uw cluster efficiënt te gebruiken.

* Geniet de voor keur van kleinere gegevens partities en een account voor gegevens grootte, typen en distributie in uw strategie voor partitioneren.
* Bekijk de nieuwere, efficiëntere [Kryo-gegevens serialisatie](https://github.com/EsotericSoftware/kryo), in plaats van de standaard Java-serialisatie.
* Geniet van de `spark-submit` voor keur aan garens, omdat deze door batch worden gescheiden.
* Spark-configuratie-instellingen bewaken en afstemmen.

Ter referentie worden de Spark-geheugen structuur en enkele para meters voor de door Voer van de sleutel in de volgende afbeelding weer gegeven.

### <a name="spark-memory-considerations"></a>Overwegingen voor Spark-geheugen

Als u [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)gebruikt, beheert garen de maximale hoeveelheid geheugen die wordt gebruikt door alle containers op elk Spark-knoop punt.  In het volgende diagram ziet u de belangrijkste objecten en hun relaties.

![Vonk geheugen beheer van garen](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Probeer het volgende om berichten over onvoldoende geheugen te adresseren:

* DAG beheer in wille keurige volg orde controleren. Verlaag met rebuising aan de kaart zijde, pre-Partition (of buckets) bron gegevens, Maximaliseer enkele wille keurige volg orde en verminder de hoeveelheid verzonden gegevens.
* De `ReduceByKey` voor keur met de vaste geheugen `GroupByKey`limiet tot, die aggregaties, windowing en andere functies biedt, maar wel een niet-gebonden geheugen limiet heeft.
* Voor `TreeReduce`keur, waarmee u meer werk kunt doen aan de uitvoerders `Reduce`of partities, aan, dat alles op het stuur programma werkt.
* Gebruik DataFrames in plaats van RDD-objecten op lagere niveaus.
* Maak toe die acties inkapselen, zoals ' top N ', verschillende aggregaties of venster bewerkingen.

## <a name="optimize-data-serialization"></a>Gegevens serialisatie optimaliseren

Spark-taken worden gedistribueerd, zodat de juiste gegevens serialisatie belang rijk is voor de beste prestaties.  Er zijn twee opties voor serialisatie voor Spark:

* Java-serialisatie is de standaard instelling.
* Kryo serialisatie is een nieuwere indeling en kan leiden tot snellere en meer compacte serialisatie dan Java.  Kryo vereist dat u de klassen in het programma registreert en biedt nog geen ondersteuning voor alle typen die kunnen worden geserialiseerd.

## <a name="use-bucketing"></a>Bucket gebruiken

Bucket is vergelijkbaar met het partitioneren van gegevens, maar elke Bucket kan een set kolom waarden bevatten in plaats van slechts één. Bucket werkt goed voor het partitioneren van grote aantallen (in miljoenen of meer waarden), zoals product-id's. Een Bucket wordt bepaald door de Bucket sleutel van de rij te hashen. Gebucketse tabellen bieden unieke optimalisaties, omdat ze meta gegevens opslaan over hoe ze zijn Bucket en gesorteerd.

Enkele geavanceerde functies voor Bucket zijn:

* Query optimalisatie op basis van het Bucket van meta gegevens.
* Geoptimaliseerde aggregaties.
* Geoptimaliseerde samen voegingen.

U kunt partitioneren en gelijktijdig buckets gebruiken.

## <a name="optimize-joins-and-shuffles"></a>Samen voegingen en wille keurige volg orde optimaliseren

Als u langzame taken hebt voor een samen voeging of een wille keurige volg orde, is de oorzaak waarschijnlijk *gegevens scheefer*, wat asymmetrie is in uw taak gegevens. Een toewijzings taak kan bijvoorbeeld 20 seconden duren, maar u kunt ook een taak uitvoeren waarbij de gegevens worden toegevoegd of een wille keurige volg orde.   Als u gegevens scheefheid wilt verhelpen, moet u de volledige sleutel zouten of een *geïsoleerde Salt* gebruiken voor slechts een deel van de sleutels.  Als u een geïsoleerd zout gebruikt, moet u verder filteren om de subset van gezoute sleutels in kaart verbindingen te isoleren. Een andere optie is om eerst een Bucket kolom en vooraf aggregatie in buckets in te voeren.

Een andere factor veroorzaakt langzame samen voegingen zou het jointype kunnen zijn. Spark maakt standaard gebruik van het `SortMerge` jointype. Dit type samen voeging is het meest geschikt voor grote gegevens sets, maar is een andere reken kracht, omdat het eerst de linker-en rechter zijde van gegevens moet sorteren voordat u deze samenvoegt.

Een `Broadcast` samen voeging is het meest geschikt voor kleinere gegevens sets, of waarbij een zijde van de samen voeging veel kleiner is dan de andere kant. Dit type deelname zendt één zijde naar alle uitvoerende organisaties, en vereist daarom meer geheugen voor broadcasts in het algemeen.

U kunt het jointype in uw configuratie wijzigen door in te `spark.sql.autoBroadcastJoinThreshold`stellen, of u kunt een samenvoegings Hint instellen met behulp van de data frame-api's (`dataframe.join(broadcast(df2))`).

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

Als u verwerkte tabellen gebruikt, hebt u een derde jointype, de `Merge` koppeling. Een juiste vooraf gepartitioneerde en vooraf gesorteerde gegevensset slaat de dure Sorteer fase over van een `SortMerge` koppeling.

De volg orde van de kwesties, met name in complexere query's. Begin met de meeste selectieve samen voegingen. U kunt ook koppelingen verplaatsen die het aantal rijen na aggregaties verg Roten wanneer dat mogelijk is.

Als u parallelle uitvoering wilt beheren, met name in het geval van Cartesisch samen voegen, kunt u geneste structuren toevoegen, venster en wellicht een of meer stappen in uw Spark-taak overs Laan.

## <a name="customize-cluster-configuration"></a>Cluster configuratie aanpassen

Afhankelijk van de werk belasting van het Spark-cluster kunt u bepalen dat een niet-standaard Spark-configuratie zou leiden tot een meer geoptimaliseerde Spark-taak uitvoering.  Voer Bench Mark-tests uit met voorbeeld werkbelastingen om alle niet-standaard cluster configuraties te valideren.

Hier volgen enkele algemene para meters die u kunt aanpassen:

* `--num-executors`Hiermee stelt u het juiste aantal uitvoerender in.
* `--executor-cores`Hiermee stelt u het aantal kernen in voor elke uitvoerder. Normaal gesp roken moet u middelste uitvoerings modules hebben, aangezien andere processen een deel van het beschik bare geheugen verbruiken.
* `--executor-memory`Hiermee stelt u de grootte van het geheugen voor elke uitvoerder, waarmee de heapgrootte op GARENs wordt beheerd. U moet geheugen voor de overhead van uitvoeringen laten staan.

### <a name="select-the-correct-executor-size"></a>Selecteer de juiste grootte voor de uitvoerder

Houd bij het bepalen van de configuratie van de uitvoerder rekening met de overhead van de Java garbage collection (GC).

* Factoren om de uitvoerings grootte te verkleinen:
    1. Verminder de grootte van de heap onder 32 GB om de GC-overhead < 10% te houden.
    2. Verminder het aantal kernen om de GC-overhead < 10% te houden.

* Factoren voor het verg Roten van de uitvoerder grootte:
    1. Verminder de communicatie overhead tussen uitvoerender.
    2. Verminder het aantal openstaande verbindingen tussen uitvoerders (N2) op grotere clusters (> 100 uitvoerendeers).
    3. Verg root de Heap-grootte zodat deze geschikt is voor geheugenintensieve taken.
    4. Optioneel: Verminder de geheugen overhead per uitvoerder.
    5. Optioneel: Verg root het gebruik en de gelijktijdigheid door de CPU te verlengen.

Als algemene vuist regel bij het selecteren van de grootte van de uitvoerder:
    
1. Begin met 30 GB per uitvoerder en distribueer beschik bare machine kernen.
2. Verhoog het aantal uitvoer kernen voor grotere clusters (> 100-uitvoerendeers).
3. Grootte verg Roten of verkleinen op basis van de test uitvoeringen en op de voor gaande factoren zoals GC-overhead.

Bij het uitvoeren van gelijktijdige query's moet u rekening houden met het volgende:

1. Begin met 30 GB per uitvoerder en alle machine kernen.
2. Maak meerdere parallelle Spark-toepassingen door de CPU (circa 30% latentie verbetering) te vervolledigen.
3. Verdeel query's over parallelle toepassingen.
4. Grootte verg Roten of verkleinen op basis van de test uitvoeringen en op de voor gaande factoren zoals GC-overhead.

Bewaak de query prestaties voor uitbijters of andere prestatie problemen door te kijken naar de tijdlijn weergave, SQL-grafiek, taak statistieken, enzovoort. Soms zijn een of enkele van de uitvoerende partijen trager dan de andere, en duren taken veel langer om uit te voeren. Dit gebeurt vaak op grotere clusters (> 30 knoop punten). In dit geval moet u het werk delen in een groter aantal taken zodat de planner de taak kan compenseren voor trage taken. Stel bijvoorbeeld ten minste twee taken uit als het aantal uitvoer kernen in de toepassing. U kunt ook speculatieve uitvoering van taken inschakelen met `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Taak uitvoering optimaliseren

* Sla zo nodig een cache op, bijvoorbeeld als u de gegevens twee keer gebruikt en vervolgens opslaat in de cache.
* Broadcast variabelen naar alle uitvoerende organisaties. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere zoek acties.
* Gebruik de thread pool op het stuur programma, wat resulteert in een snellere bewerking voor veel taken.

Bewaak uw actieve taken regel matig voor prestatie problemen. Als u meer inzicht wilt in bepaalde problemen, kunt u een van de volgende hulpprogram ma's voor prestatie profilering overwegen:

* Het [Intel PAL-hulp programma](https://github.com/intel-hadoop/PAT) bewaakt CPU, opslag en netwerk bandbreedte gebruik.
* De beheer profielen voor de [missie van Oracle Java 8](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) Spark en de uitvoerder code.

De sleutel tot Spark 2. x query prestaties is de Tungsten-engine, die afhankelijk is van het genereren van code in een hele fase. In sommige gevallen kan het genereren van code in hele fase worden uitgeschakeld. Als u bijvoorbeeld een niet-ververanderd type (`string`) in de expressie aggregatie gebruikt, `SortAggregate` wordt dit weer gegeven in plaats van. `HashAggregate` Als u bijvoorbeeld betere prestaties wilt, kunt u het volgende proberen en vervolgens code generatie opnieuw inschakelen:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

* [Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight](apache-spark-job-debugging.md)
* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [De Apache Spark REST API gebruiken om externe taken te verzenden naar een Apache Spark-cluster](apache-spark-livy-rest-interface.md)
* [Apache Spark afstemmen](https://spark.apache.org/docs/latest/tuning.html)
* [Uw Apache Spark taken effectief afstemmen zodat ze werken](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
* [Kryo-serialisatie](https://github.com/EsotericSoftware/kryo)
