---
title: Spark-taken optimaliseren voor prestaties in azure Synapse Analytics
description: Dit artikel bevat een inleiding tot Apache Spark in azure Synapse Analytics en de verschillende concepten.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6ffe7f3d9faf82c892975e9ffa03b383d3610c36
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424620"
---
# <a name="optimize-apache-spark-jobs-preview-in-azure-synapse-analytics"></a>Apache Spark-taken (preview) optimaliseren in azure Synapse Analytics

Meer informatie over het optimaliseren van [Apache Spark](https://spark.apache.org/) cluster configuratie voor uw specifieke werk belasting.  De meest voorkomende uitdaging is geheugen druk, vanwege onjuiste configuraties (met name verkeerde uitvoeringen), langlopende bewerkingen en taken die resulteren in Cartesische bewerkingen. U kunt taken versnellen met de juiste caching en door [gegevens scheefheid](#optimize-joins-and-shuffles)toe te staan. Voor de beste prestaties controleert en bekijkt u langlopende en resource-Consumer-uitvoeringen met Spark-taken.

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
* **Rdd's**
  * U hoeft geen Rdd's te gebruiken, tenzij u een nieuwe aangepaste RDD wilt maken.
  * Geen query optimalisatie via Catalyst.
  * Geen code voor het genereren van de hele fase.
  * Hoge GC-overhead.
  * Moeten Spark 1. x verouderde Api's gebruiken.

## <a name="use-optimal-data-format"></a>Optimale gegevens indeling gebruiken

Spark ondersteunt veel indelingen, zoals CSV, JSON, XML, Parquet, Orc en AVRO. Spark kan worden uitgebreid ter ondersteuning van veel meer indelingen met externe gegevens bronnen. Zie [Apache Spark pakketten](https://spark-packages.org)voor meer informatie.

De beste prestatie-indeling is Parquet met *Snappy-compressie*. Dit is de standaard waarde in Spark 2. x. Parquet slaat gegevens op in de kolom indeling en is zeer geoptimaliseerd in Spark. Daarnaast kan *Snappy compressie* ertoe leiden dat bestanden groter worden dan de gzip-compressie. Door de splitsbaar van de bestanden, worden ze sneller gedecomprimeerd]

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt eigen systeem eigen caching-mechanismen die kunnen worden gebruikt via verschillende methoden, zoals `.persist()`, `.cache()`en `CACHE TABLE`. Deze systeem eigen cache is effectief met kleine gegevens sets en in ETL-pijp lijnen waar u tussenliggende resultaten moet opslaan in de cache. Spark native caching werkt echter niet goed met partitioneren, omdat het partitioneren van gegevens niet wordt bewaard in een tabel in de cache.

## <a name="use-memory-efficiently"></a>Efficiënt geheugen gebruiken

Spark werkt door gegevens in het geheugen te plaatsen, zodat het beheren van geheugen resources een belang rijk aspect is van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u kunt Toep assen om het geheugen van uw cluster efficiënt te gebruiken.

* Geniet de voor keur van kleinere gegevens partities en een account voor gegevens grootte, typen en distributie in uw strategie voor partitioneren.
* Bekijk de nieuwere, efficiëntere [Kryo-gegevens serialisatie](https://github.com/EsotericSoftware/kryo), in plaats van de standaard Java-serialisatie.
* Spark-configuratie-instellingen bewaken en afstemmen.

Ter referentie worden de Spark-geheugen structuur en enkele para meters voor de door Voer van de sleutel in de volgende afbeelding weer gegeven.

### <a name="spark-memory-considerations"></a>Overwegingen voor Spark-geheugen

Apache Spark in azure Synapse maakt gebruik van GARENs [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)beheert de maximale hoeveelheid geheugen die wordt gebruikt door alle containers op elk Spark-knoop punt.  In het volgende diagram ziet u de belangrijkste objecten en hun relaties.

![Vonk geheugen beheer van garen](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Probeer het volgende om berichten over onvoldoende geheugen te adresseren:

* DAG beheer in wille keurige volg orde controleren. Verlaag met de bron gegevens voor verminderen van de kaart, vooraf Partition (of buckets), Maxi maal één wille keurige volg orde en verminder de hoeveelheid verzonden gegevens.
* De `ReduceByKey` voor keur met de vaste geheugen `GroupByKey`limiet tot, die aggregaties, windowing en andere functies biedt, maar wel een niet-gebonden geheugen limiet heeft.
* Voor `TreeReduce`keur, waarmee u meer werk kunt doen aan de uitvoerders `Reduce`of partities, aan, dat alles op het stuur programma werkt.
* Gebruik DataFrames in plaats van RDD-objecten op lagere niveaus.
* Maak toe die acties inkapselen, zoals ' top N ', verschillende aggregaties of venster bewerkingen.

## <a name="optimize-data-serialization"></a>Gegevens serialisatie optimaliseren

Spark-taken worden gedistribueerd, zodat de juiste gegevens serialisatie belang rijk is voor de beste prestaties.  Er zijn twee opties voor serialisatie voor Spark:

* Java-serialisatie is de standaard instelling.
* Kryo serialisatie is een nieuwere indeling en kan leiden tot snellere en meer compacte serialisatie dan Java.  Kryo vereist dat u de klassen registreert in uw programma en nog geen ondersteuning biedt voor alle typen die kunnen worden geserialiseerd.

## <a name="use-bucketing"></a>Bucket gebruiken

Bucket is vergelijkbaar met het partitioneren van gegevens, maar elke Bucket kan een set kolom waarden bevatten in plaats van slechts één. Bucket werkt goed voor het partitioneren van grote aantallen (in miljoenen of meer waarden), zoals product-id's. Een Bucket wordt bepaald door de Bucket sleutel van de rij te hashen. Gebucketse tabellen bieden unieke optimalisaties, omdat ze meta gegevens opslaan over hoe ze zijn Bucket en gesorteerd.

Enkele geavanceerde functies voor Bucket zijn:

* Query optimalisatie op basis van het Bucket van meta gegevens.
* Geoptimaliseerde aggregaties.
* Geoptimaliseerde samen voegingen.

U kunt partitioneren en gelijktijdig buckets gebruiken.

## <a name="optimize-joins-and-shuffles"></a>Samen voegingen en wille keurige volg orde optimaliseren

Als u langzame taken hebt voor een samen voeging of een wille keurige volg orde, is de oorzaak waarschijnlijk *gegevens scheefer*, wat asymmetrie is in uw taak gegevens. Een toewijzings taak kan bijvoorbeeld 20 seconden duren, maar u kunt ook een taak uitvoeren waarbij de gegevens worden toegevoegd of een wille keurige volg orde. Als u gegevens scheefheid wilt verhelpen, moet u de volledige sleutel zouten of een *geïsoleerde Salt* gebruiken voor slechts een deel van de sleutels. Als u een geïsoleerd zout gebruikt, moet u verder filteren om uw subset van gezoute sleutels in kaart verbindingen te isoleren. Een andere optie is om eerst een Bucket kolom en vooraf aggregatie in buckets in te voeren.

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

Als u gekoppelde tabellen gebruikt, hebt u een derde jointype, de `Merge` koppeling. Een juiste vooraf gepartitioneerde en vooraf gesorteerde gegevensset slaat de dure Sorteer fase over van een `SortMerge` koppeling.

De volg orde van de kwesties, met name in complexere query's. Begin met de meeste selectieve samen voegingen. U kunt ook koppelingen verplaatsen die het aantal rijen na aggregaties verg Roten wanneer dat mogelijk is.

Als u de parallelle uitvoering voor Cartesische join's wilt beheren, kunt u geneste structuren en Vensters toevoegen en wellicht een of meer stappen in uw Spark-taak overs Laan.

### <a name="select-the-correct-executor-size"></a>Selecteer de juiste grootte voor de uitvoerder

Houd bij het bepalen van de configuratie van de uitvoerder rekening met de overhead van de Java garbage collection (GC).

* Factoren om de uitvoerings grootte te verkleinen:
  * Verminder de grootte van de heap onder 32 GB om de GC-overhead < 10% te houden.
  * Verminder het aantal kernen om de GC-overhead < 10% te houden.

* Factoren voor het verg Roten van de uitvoerder grootte:
  * Verminder de communicatie overhead tussen uitvoerender.
  * Verminder het aantal openstaande verbindingen tussen uitvoerende (N2) op grotere clusters (>100-uitvoerders).
  * Verg root de Heap-grootte zodat deze geschikt is voor geheugenintensieve taken.
  * Optioneel: de overhead per uitvoerder geheugen verlagen.
  * Optioneel: gebruik verhogen en gelijktijdigheid door de CPU te verlengen.

Als algemene vuist regel bij het selecteren van de grootte van de uitvoerder:

* Begin met 30 GB per uitvoerder en distribueer beschik bare machine kernen.
* Verhoog het aantal uitvoer kernen voor grotere clusters (> 100-uitvoerendeers).
* Wijzig grootte op basis van de test uitvoeringen en op de voor gaande factoren zoals GC-overhead.

Bij het uitvoeren van gelijktijdige query's moet u rekening houden met het volgende:

* Begin met 30 GB per uitvoerder en alle machine kernen.
* Maak meerdere parallelle Spark-toepassingen door de CPU (circa 30% latentie verbetering) te vervolledigen.
* Verdeel query's over parallelle toepassingen.
* Wijzig grootte op basis van de test uitvoeringen en op de voor gaande factoren zoals GC-overhead.

Bewaak de query prestaties voor uitbijters of andere prestatie problemen door te kijken naar de tijdlijn weergave, SQL-grafiek, taak statistieken, enzovoort. Soms zijn een of enkele van de uitvoerende partijen trager dan de andere, en duren taken veel langer om uit te voeren. Dit gebeurt vaak op grotere clusters (> 30 knoop punten). In dit geval moet u het werk delen in een groter aantal taken zodat de planner de taak kan compenseren voor trage taken. 

Stel bijvoorbeeld ten minste twee taken uit als het aantal uitvoer kernen in de toepassing. U kunt ook speculatieve uitvoering van taken inschakelen met `conf: spark.speculation = true`.

## <a name="optimize-job-execution"></a>Taak uitvoering optimaliseren

* Sla zo nodig een cache op, bijvoorbeeld als u de gegevens twee keer gebruikt en vervolgens opslaat in de cache.
* Broadcast variabelen naar alle uitvoerende organisaties. De variabelen worden slechts eenmaal geserialiseerd, wat resulteert in snellere zoek acties.
* Gebruik de thread pool op het stuur programma, wat resulteert in een snellere bewerking voor veel taken.

De sleutel tot Spark 2. x query prestaties is de Tungsten-engine, die afhankelijk is van het genereren van code in een hele fase. In sommige gevallen kan het genereren van code in hele fase worden uitgeschakeld. 

Als u bijvoorbeeld een niet-ververanderd type (`string`) in de expressie aggregatie gebruikt, `SortAggregate` wordt dit weer `HashAggregate`gegeven in plaats van. Als u bijvoorbeeld betere prestaties wilt, kunt u het volgende proberen en vervolgens code generatie opnieuw inschakelen:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Volgende stappen

- [Apache Spark afstemmen](https://spark.apache.org/docs/latest/tuning.html)
- [Uw Apache Spark taken effectief afstemmen zodat ze werken](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)
- [Kryo-serialisatie](https://github.com/EsotericSoftware/kryo)
