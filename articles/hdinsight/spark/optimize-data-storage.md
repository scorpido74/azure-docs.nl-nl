---
title: Gegevens opslag optimaliseren voor Apache Spark-Azure HDInsight
description: Meer informatie over het optimaliseren van gegevens opslag voor gebruik met Apache Spark in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 7162e2e8c42f3e83a47c46d739f93cfc4cfcaac6
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737628"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Optimalisatie van gegevens opslag voor Apache Spark

In dit artikel worden strategieën beschreven voor het optimaliseren van gegevens opslag voor efficiënte Apache Spark uitvoering van taken op Azure HDInsight.

## <a name="overview"></a>Overzicht

Spark ondersteunt veel indelingen, zoals CSV, JSON, XML, Parquet, Orc en AVRO. Spark kan worden uitgebreid ter ondersteuning van veel meer indelingen met externe gegevens bronnen. Zie [Apache Spark pakketten](https://spark-packages.org)voor meer informatie.

De beste prestatie-indeling is Parquet met *Snappy-compressie*. Dit is de standaard waarde in Spark 2. x. Parquet slaat gegevens op in de kolom indeling en is zeer geoptimaliseerd in Spark.

## <a name="choose-data-abstraction"></a>Gegevens abstractie kiezen

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

## <a name="select-default-storage"></a>Standaard opslag selecteren

Wanneer u een nieuw Spark-cluster maakt, kunt u Azure Blob Storage of Azure Data Lake Storage als de standaard opslag van uw cluster selecteren. Beide opties bieden u het voor deel van lange termijn opslag voor tijdelijke clusters. Uw gegevens worden dus niet automatisch verwijderd wanneer u uw cluster verwijdert. U kunt een tijdelijk cluster opnieuw maken en toch toegang krijgen tot uw gegevens.

| Winkeltype | Bestandssysteem | Snelheid | Wijk | Gebruiksscenario's |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standard** | Yes | Tijdelijk cluster |
| Azure Blob Storage (beveiligd) | **wasbs:**//URL/ | **Standard** | Yes | Tijdelijk cluster |
| Azure Data Lake Storage Gen 2| **abfs:**//URL/ | **Gereed** | Yes | Tijdelijk cluster |
| Azure Data Lake Storage gen 1| **ADL:**//URL/ | **Gereed** | Yes | Tijdelijk cluster |
| Lokale HDFS | **hdfs:**//URL/ | **Snelste** | No | Interactive 24/7-cluster |

Zie [opslag opties vergelijken voor gebruik met Azure HDInsight-clusters](../hdinsight-hadoop-compare-storage-options.md)voor een volledige beschrijving van opslag opties.

## <a name="use-the-cache"></a>De cache gebruiken

Spark biedt eigen systeem eigen caching-mechanismen die kunnen worden gebruikt via verschillende methoden, zoals `.persist()` , `.cache()` en `CACHE TABLE` . Deze systeem eigen cache is effectief met kleine gegevens sets en in ETL-pijp lijnen waar u tussenliggende resultaten in de cache moet opslaan. Spark native caching werkt echter niet goed met partitioneren, omdat het partitioneren van gegevens niet wordt bewaard in een tabel in de cache. Een meer generieke en betrouw bare cache techniek is het opslaan van de *opslag laag in de cache*.

* Systeem eigen Spark-caching (niet aanbevolen)
    * Geschikt voor kleine gegevens sets.
    * Werkt niet voor partitionering, wat in toekomstige Spark-releases kan veranderen.

* Caching op opslag niveau (aanbevolen)
    * Kan worden geïmplementeerd op HDInsight met behulp van de [i/o-cache](apache-spark-improve-performance-iocache.md) functie.
    * Maakt gebruik van in-Memory en SSD-caching.

* Lokale HDFS (aanbevolen)
    * `hdfs://mycluster`programmapad.
    * Maakt gebruik van SSD-caching.
    * Gegevens in de cache gaan verloren wanneer u het cluster verwijdert, waardoor een cache opnieuw moet worden opgebouwd.

## <a name="optimize-data-serialization"></a>Gegevens serialisatie optimaliseren

Spark-taken worden gedistribueerd, zodat de juiste gegevens serialisatie belang rijk is voor de beste prestaties.  Er zijn twee opties voor serialisatie voor Spark:

* Java-serialisatie is de standaard instelling.
* `Kryo`serialisatie is een nieuwere indeling en kan leiden tot snellere en meer compacte serialisatie dan Java.  `Kryo`Hiervoor moet u de klassen in het programma registreren en wordt niet alle serialiseerbare typen ondersteund.

## <a name="use-bucketing"></a>Bucket gebruiken

Bucket is vergelijkbaar met het partitioneren van gegevens. Elke Bucket kan echter een set kolom waarden bevatten in plaats van slechts één. Deze methode werkt goed voor het partitioneren van grote aantallen (in miljoenen of meer) waarden, zoals product-id's. Een Bucket wordt bepaald door de Bucket sleutel van de rij te hashen. Gebucketse tabellen bieden unieke optimalisaties, omdat ze meta gegevens opslaan over hoe ze zijn Bucket en gesorteerd.

Enkele geavanceerde functies voor Bucket zijn:

* Query optimalisatie op basis van het Bucket van meta gegevens.
* Geoptimaliseerde aggregaties.
* Geoptimaliseerde samen voegingen.

U kunt partitioneren en gelijktijdig buckets gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
* [Cluster configuratie voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
