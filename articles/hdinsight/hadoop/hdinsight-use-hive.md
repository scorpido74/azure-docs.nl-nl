---
title: Wat is Apache Hive en HiveQL - Azure HDInsight
description: Apache Hive is een data warehouse systeem voor Apache Hadoop. U gegevens die zijn opgeslagen in Hive opvragen met HiveQL, die vergelijkbaar is met Transact-SQL. In dit document leest u hoe u Hive en HiveQL gebruiken met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: f7dc7b520cba2bbf2351d93795a1a26b3b5124be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471350"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Wat is Apache Hive en HiveQL op Azure HDInsight?

[Apache Hive](https://hive.apache.org/) is een data warehouse systeem voor Apache Hadoop. Hive maakt gegevenssomsoming, query's en analyse van gegevens mogelijk. Hive-query's worden geschreven in HiveQL, een querytaal die vergelijkbaar is met SQL.

Met Hive u structuur projecteren op grotendeels ongestructureerde gegevens. Nadat u de structuur hebt gedefinieerd, u HiveQL gebruiken om de gegevens op te vragen zonder kennis van Java of MapReduce.

HDInsight biedt verschillende clustertypen, die zijn afgestemd op specifieke workloads. De volgende clustertypen worden het vaakst gebruikt voor Hive-query's:

|Clustertype |Beschrijving|
|---|---|
|Interactive Query|Een Hadoop-cluster dat [llap-functionaliteit (Low Latency Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP) biedt om de responstijden voor interactieve query's te verbeteren. Zie het document [Start with Interactive Query in HDInsight](../interactive-query/apache-interactive-query-get-started.md) voor meer informatie.|
|Hadoop|Een Hadoop-cluster dat is afgestemd op batchverwerkingsworkloads. Zie het document [Start with Apache Hadoop in HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) voor meer informatie.|
|Spark|Apache Spark heeft ingebouwde functionaliteit voor het werken met Hive. Zie het document [Start with Apache Spark op HDInsight](../spark/apache-spark-jupyter-spark-sql.md) voor meer informatie.|
|HBase|HiveQL kan worden gebruikt om gegevens op te vragen die zijn opgeslagen in Apache HBase. Zie het document [Start with Apache HBase op HDInsight voor](../hbase/apache-hbase-tutorial-get-started-linux.md) meer informatie.|

## <a name="how-to-use-hive"></a>Hive gebruiken

Gebruik de volgende tabel om de verschillende manieren te ontdekken om Hive met HDInsight te gebruiken:

| **Gebruik deze methode** als je wilt... | ... **interactieve** query's | ... **verwerking van partij** | ... van dit **besturingssysteem** van de client |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight-tools voor Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, Unix, Mac OS X of Windows |
| [HDInsight-tools voor Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive-weergave](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Elke (browser gebaseerd) |
| [Beeline-client](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, Unix, Mac OS X of Windows |
| [REST-API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, Unix, Mac OS X of Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>HiveQL-taalverwijzing

HiveQL taalverwijzing is beschikbaar in de [taalhandleiding](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Bijenkorf en gegevensstructuur

Hive begrijpt hoe te werken met gestructureerde en semi-gestructureerde gegevens. Tekstbestanden waarin de velden worden afgebakend door specifieke tekens. Met de volgende HiveQL-instructie wordt een tabel over gegevens met beperkte ruimte gedefinieerd:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive ondersteunt ook custom **serializer/deserializers (SerDe)** voor complexe of onregelmatig gestructureerde gegevens. Zie Voor meer informatie het [document Hoe u een aangepast JSON SerDe met HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) gebruikt.

Zie de [taalhandleiding voorhttps://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) meer informatie over bestandsindelingen die door Hive worden ondersteund (

### <a name="hive-internal-tables-vs-external-tables"></a>Interne tabellen voor bijenkorf versus externe tabellen

Er zijn twee soorten tabellen die u maken met Hive:

* __Intern__: Gegevens worden opgeslagen in het Hive-gegevensmagazijn. Het gegevensmagazijn bevindt zich op `/hive/warehouse/` de standaardopslag voor het cluster.

    Gebruik interne tabellen wanneer een van de volgende voorwaarden van toepassing is:

    * Gegevens zijn tijdelijk.
    * U wilt dat Hive de levenscyclus van de tabel en gegevens beheert.

* __Extern__: Gegevens worden buiten het datawarehouse opgeslagen. De gegevens kunnen worden opgeslagen op elke opslag die toegankelijk is voor het cluster.

    Gebruik externe tabellen wanneer een van de volgende voorwaarden van toepassing is:

    * De gegevens worden ook buiten Hive gebruikt. De gegevensbestanden worden bijvoorbeeld bijgewerkt door een ander proces (dat de bestanden niet vergrendelt.)
    * Gegevens moeten op de onderliggende locatie blijven, zelfs na het laten vallen van de tabel.
    * U hebt een aangepaste locatie nodig, zoals een niet-standaard opslagaccount.
    * Een ander programma dan hive beheert de gegevensindeling, locatie, enzovoort.

Zie de blogpost [Interne en Externe tabellen Intro](https://blogs.msdn.microsoft.com/cindygross/2013/02/05/hdinsight-hive-internal-and-external-tables-intro/) voor meer informatie.

## <a name="user-defined-functions-udf"></a>Door de gebruiker gedefinieerde functies (UDF)

Hive kan ook worden uitgebreid door **middel van door de gebruiker gedefinieerde functies (UDF)**. Met een UDF u functionaliteit of logica implementeren die niet gemakkelijk is gemodelleerd in HiveQL. Zie de volgende documenten voor een voorbeeld van het gebruik van UDF's met Hive:

* [Gebruik een door de Gebruiker op Java gedefinieerde functie met Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Een door de Python-gebruiker gedefinieerde functie gebruiken met Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Een door de Gebruiker gedefinieerde C#-functie gebruiken met Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Een aangepaste Apache Hive-functie toevoegen aan HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Een voorbeeld van de door apache hive door de gebruiker gedefinieerde functie om datum-/tijdnotaties om te zetten in Hive-tijdstempel](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Voorbeeldgegevens

Hive op HDInsight wordt geleverd met een `hivesampletable`interne tabel met de naam . HDInsight biedt ook voorbeeldgegevenssets die kunnen worden gebruikt met Hive. Deze datasets worden `/example/data` opgeslagen `/HdiSamples` in de mappen en mappen. Deze mappen bestaan in de standaardopslag voor uw cluster.

## <a name="example-hive-query"></a>Voorbeeld van Bijenkorf-query

De volgende HiveQL-instructies projecteren `/example/data/sample.log` kolommen op het bestand:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

In het vorige voorbeeld voeren de HiveQL-instructies de volgende acties uit:

|Verklaring |Beschrijving |
|---|---|
|KEUZELIJST LATEN VALLEN|Als de tabel al bestaat, verwijdert u deze.|
|EXTERNE TABEL MAKEN|Hiermee maakt u een nieuwe **externe** tabel in Hive. Externe tabellen slaan alleen de tabeldefinitie op in Hive. De gegevens worden achtergelaten op de oorspronkelijke locatie en in de oorspronkelijke indeling.|
|RIJNOTATIE|Hiermee vertelt U bijenkorf hoe de gegevens worden opgemaakt. In dit geval worden de velden in elk logboek gescheiden door een spatie.|
|OPGESLAGEN ALS TEXTFILE-LOCATIE|Hiermee vertelt Hive waar de `example/data` gegevens worden opgeslagen (de map) en dat deze als tekst zijn opgeslagen. De gegevens kunnen in één bestand zijn of verspreid zijn over meerdere bestanden in de map.|
|SELECT|Hiermee selecteert u een aantal rijen waarin de kolom **t4** de waarde **bevat [FOUT]**. Met deze instructie wordt een waarde van **3** geretourneerd omdat er drie rijen zijn die deze waarde bevatten.|
|INPUT__FILE__NAME VIND '.log' leuk|Hive probeert het schema toe te passen op alle bestanden in de map. In dit geval bevat de map bestanden die niet overeenkomen met het schema. Om te voorkomen dat garbage data in de resultaten, deze verklaring vertelt Hive dat we alleen gegevens moeten retourneren van bestanden die eindigen in .log.|

> [!NOTE]  
> Externe tabellen moeten worden gebruikt wanneer u verwacht dat de onderliggende gegevens worden bijgewerkt door een externe bron. Bijvoorbeeld een geautomatiseerd proces voor het uploaden van gegevens of mapreduce-bewerking.
>
> Als u een externe tabel laat vallen, worden de gegevens **niet** verwijderd, maar wordt alleen de tabeldefinitie verwijderd.

Als u een **interne** tabel wilt maken in plaats van extern, gebruikt u de volgende HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Met deze instructies worden de volgende acties uitgevoerd:

|Verklaring |Beschrijving |
|---|---|
|TABEL MAKEN ALS DEZE NIET BESTAAT|Als de tabel niet bestaat, maakt u deze. Omdat het **externe** trefwoord niet wordt gebruikt, maakt deze instructie een interne tabel. De tabel wordt opgeslagen in het Hive data warehouse en wordt volledig beheerd door Hive.|
|OPGESLAGEN ALS ORC|Slaat de gegevens op in de ORC-indeling (Optimized Row Columnar). ORC is een zeer geoptimaliseerd en efficiënt formaat voor het opslaan van Hive-gegevens.|
|INSERT OVERSCHRIJVEN ... Selecteer|Hiermee selecteert u rijen in de **log4jLogs-tabel** met **[FOUT]** en voegt u de gegevens in de **tabel errorLogs** in.|

> [!NOTE]  
> In tegenstelling tot externe tabellen worden bij het laten vallen van een interne tabel ook de onderliggende gegevens verwijderd.

## <a name="improve-hive-query-performance"></a>Hive-queryprestaties verbeteren

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) is een framework waarmee data-intensieve toepassingen, zoals Hive, veel efficiënter op schaal kunnen worden uitgevoerd. Tez is standaard ingeschakeld.  De [Ontwerpdocumenten apache hive op Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) bevat details over de implementatiekeuzes en tuningconfiguraties.

### <a name="low-latency-analytical-processing-llap"></a>Lage latentie analytische verwerking (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (ook bekend als Live Long and Process) is een nieuwe functie in Hive 2.0 die het mogelijk maakt in-memory caching van query's. LLAP maakt Hive-query's veel sneller, tot [26x sneller dan Hive 1.x in sommige gevallen.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)

HDInsight biedt LLAP in het clustertype Interactieve query. Zie het document [Start with Interactive Query](../interactive-query/apache-interactive-query-get-started.md) voor meer informatie.

## <a name="scheduling-hive-queries"></a>Hive-query's plannen

Er zijn verschillende services die kunnen worden gebruikt om Hive-query's uit te voeren als onderdeel van een geplande of on-demand workflow.

### <a name="azure-data-factory"></a>Azure Data Factory

Met Azure Data Factory u HDInsight gebruiken als onderdeel van een Data Factory-pijplijn. Zie De activiteit Gegevens transformeren met Hive in het azure [data factory-document](../../data-factory/transform-data-using-hadoop-hive.md) voor meer informatie over het gebruik van Hive vanuit een pijplijn.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-taken en SQL Server Integration Services

U SQL Server Integration Services (SSIS) gebruiken om een Hive-taak uit te voeren. Het Azure Feature Pack voor SSIS biedt de volgende componenten die werken met Hive-taken op HDInsight.

* [Azure HDInsight Hive-taak](https://docs.microsoft.com/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Azure Subscription Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Zie de documentatie [van](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis) het Azure Feature Pack voor meer informatie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie is een workflow- en coördinatiesysteem dat Hadoop-taken beheert. Zie de [Apache Oozie gebruiken om een werkstroomdocument te definiëren en uit te voeren voor](../hdinsight-use-oozie-linux-mac.md) meer informatie over het gebruik van Oozie met Hive.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd wat Hive is en hoe u het gebruiken met Hadoop in HDInsight, gebruikt u de volgende koppelingen om andere manieren te verkennen om met Azure HDInsight te werken.

* [Gegevens uploaden naar HDInsight](../hdinsight-upload-data.md)
* [Gebruik Python User Defined Functions (UDF) met Apache Hive en Apache Pig in HDInsight](./python-udf-hdinsight.md)
* [MapReduce-taken gebruiken met HDInsight](hdinsight-use-mapreduce.md)
