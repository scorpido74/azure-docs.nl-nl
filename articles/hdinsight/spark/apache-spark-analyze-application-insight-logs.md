---
title: Logboeken van toepassingsinzicht analyseren met Spark - Azure HDInsight
description: Meer informatie over het exporteren van Application Insight-logboeken naar blobopslag en analyseren de logboeken met Spark op HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 6fd7682f56fbe446904a4acdb39e78525f2523a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435246"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>Analyseren Application Insights telemetrie logs met Apache Spark op HDInsight

Meer informatie over het gebruik van [Apache Spark](https://spark.apache.org/) op HDInsight om telemetriegegevens van Application Insight te analyseren.

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) is een analyseservice die uw webapplicaties monitort. Telemetriegegevens die worden gegenereerd door Application Insights kunnen worden geëxporteerd naar Azure Storage. Zodra de gegevens zich in Azure Storage bevinden, kan HDInsight worden gebruikt om deze te analyseren.

## <a name="prerequisites"></a>Vereisten

* Een toepassing die is geconfigureerd om Application Insights te gebruiken.

* Vertrouwdheid met het creëren van een Linux-gebaseerdhdinsight cluster. Zie [Apache Spark maken op HDInsight voor](apache-spark-jupyter-spark-sql.md)meer informatie.

* Een webbrowser.

De volgende bronnen zijn gebruikt bij het ontwikkelen en testen van dit document:

* De telemetriegegevens van Application Insights zijn gegenereerd met behulp van een [Node.js-web-app die is geconfigureerd om Application Insights te gebruiken.](../../azure-monitor/app/nodejs.md)

* Een Linux-gebaseerde Spark op HDInsight cluster versie 3.5 werd gebruikt om de gegevens te analyseren.

## <a name="architecture-and-planning"></a>Architectuur en planning

In het volgende diagram wordt de servicearchitectuur van dit voorbeeld geïllustreerd:

![Gegevens die van toepassingsinzichten naar blobopslag worden doorgegeven en vervolgens Spark](./media/apache-spark-analyze-application-insight-logs/application-insights.png)

### <a name="azure-storage"></a>Azure Storage

Application Insights kan worden geconfigureerd om continu telemetrie-informatie naar blobs te exporteren. HDInsight kan vervolgens gegevens lezen die zijn opgeslagen in de blobs. Er zijn echter een aantal vereisten die u moet volgen:

* **Locatie**: Als het opslagaccount en HDInsight zich op verschillende locaties bevinden, kan dit de latentie verhogen. Het verhoogt ook de kosten, omdat uitgaande kosten worden toegepast op gegevens die tussen regio's worden verplaatst.

    > [!WARNING]  
    > Het gebruik van een opslagaccount op een andere locatie dan HDInsight wordt niet ondersteund.

* **Blobtype:** HDInsight ondersteunt alleen blokblobs. Application Insights is standaard ingesteld op het gebruik van blokblobs, dus moet standaard werken met HDInsight.

Zie het document [Extra opslagaccounts toevoegen](../hdinsight-hadoop-add-storage.md) voor informatie over het toevoegen van opslagopslagaan een bestaand cluster.

### <a name="data-schema"></a>Gegevensschema

Application Insights biedt [gegevensover het exportgegevensmodel](../../azure-monitor/app/export-data-model.md) voor de telemetriegegevensindeling die naar blobs wordt geëxporteerd. De stappen in dit document gebruiken Spark SQL om met de gegevens te werken. Spark SQL kan automatisch een schema genereren voor de JSON-gegevensstructuur die is vastgelegd door Application Insights.

## <a name="export-telemetry-data"></a>Telemetriegegevens exporteren

Volg de stappen in [Continu exporteren configureren](../../azure-monitor/app/export-telemetry.md) om uw toepassingsinzichten te configureren om telemetriegegevens te exporteren naar een Azure Storage-blob.

## <a name="configure-hdinsight-to-access-the-data"></a>HDInsight configureren om toegang te krijgen tot de gegevens

Als u een HDInsight-cluster maakt, voegt u het opslagaccount toe tijdens het maken van het cluster.

Als u het Azure Storage-account wilt toevoegen aan een bestaand cluster, gebruikt u de gegevens in het document [Extra opslagaccounts toevoegen.](../hdinsight-hadoop-add-storage.md)

## <a name="analyze-the-data-pyspark"></a>De gegevens analyseren: PySpark

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/jupyter` naar de plaats waar CLUSTERNAME de naam van uw cluster is.

2. Selecteer in de rechterbovenhoek van de pagina Jupyter de optie **Nieuw**en vervolgens **PySpark**. Er wordt een nieuw browsertabblad geopend met een Jupyter-notitieblok op basis van Python.

3. Voer in het eerste veld (een **cel genoemd)** op de pagina de volgende tekst in:

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    Met deze code configureert Spark om opnieuw toegang te krijgen tot de directorystructuur voor de invoergegevens. Application Insights telemetrie wordt vastgelegd in `/{telemetry type}/YYYY-MM-DD/{##}/`een directorystructuur die vergelijkbaar is met de .

4. Gebruik **SHIFT+ENTER** om de code uit te voeren. Aan de linkerkant van de\*cel verschijnt een ' ' tussen de haakjes om aan te geven dat de code in deze cel wordt uitgevoerd. Zodra het is voltooid, wordt de '\*' wijzigingen in een getal, en de uitvoer vergelijkbaar met de volgende tekst wordt weergegeven onder de cel:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Een nieuwe cel wordt gemaakt onder de eerste. Voer de volgende tekst in de nieuwe cel in. Vervang `CONTAINER` `STORAGEACCOUNT` en door de naam van het Azure Storage-account en de naam van blobcontainer die toepassingsstatistieken bevat.

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Gebruik **SHIFT+ENTER** om deze cel uit te voeren. U ziet een resultaat dat vergelijkbaar is met de volgende tekst:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Het geretourneerde wasbspad is de locatie van de telemetriegegevens van Application Insights. Wijzig `hdfs dfs -ls` de regel in de cel om het geretourneerde wespenpad te gebruiken en gebruik **Shift+ENTER** om de cel opnieuw uit te voeren. Deze keer moeten de resultaten de mappen weergeven die telemetriegegevens bevatten.

   > [!NOTE]  
   > Voor de rest van de stappen `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` in deze sectie is de map gebruikt. Uw directorystructuur kan anders zijn.

6. Voer in de volgende cel de `WASB_PATH` volgende code in: Vervang het pad uit de vorige stap.

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    Met deze code wordt een gegevensframe aanmaakt van de JSON-bestanden die worden geëxporteerd door het continue exportproces. Gebruik **SHIFT+ENTER** om deze cel uit te voeren.
7. Voer in de volgende cel het volgende in en voer deze uit om het schema weer te geven dat Spark heeft gemaakt voor de JSON-bestanden:

   ```python
   jsonData.printSchema()
   ```

    Het schema voor elk type telemetrie is anders. Het volgende voorbeeld is het schema dat wordt gegenereerd `Requests` voor webaanvragen (gegevens die zijn opgeslagen in de submap):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Gebruik het volgende om het gegevensframe als tijdelijke tabel te registreren en een query uit te voeren op basis van de gegevens:

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    Met deze query worden de stadsgegevens geretourneerd voor de top 20 records waarin context.location.city niet null is.

   > [!NOTE]  
   > De contextstructuur is aanwezig in alle telemetrie die is vastgelegd door Application Insights. Het element stad wordt mogelijk niet ingevuld in uw logboeken. Gebruik het schema om andere elementen te identificeren die u opvragen en die mogelijk gegevens voor uw logboeken bevatten.

    Met deze query worden informatie geretourneerd die vergelijkbaar is met de volgende tekst:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-scala"></a>De gegevens analyseren: Scala

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/jupyter` naar de plaats waar CLUSTERNAME de naam van uw cluster is.

2. Selecteer in de rechterbovenhoek van de pagina Jupyter de optie **Nieuw**en vervolgens **Scala**. Er verschijnt een nieuw browsertabblad met een Op Scala gebaseerd Jupyter-notitieblok.

3. Voer in het eerste veld (een **cel genoemd)** op de pagina de volgende tekst in:

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    Met deze code configureert Spark om opnieuw toegang te krijgen tot de directorystructuur voor de invoergegevens. Application Insights telemetrie wordt vastgelegd `/{telemetry type}/YYYY-MM-DD/{##}/`in een directorystructuur die vergelijkbaar is met .

4. Gebruik **SHIFT+ENTER** om de code uit te voeren. Aan de linkerkant van de\*cel verschijnt een ' ' tussen de haakjes om aan te geven dat de code in deze cel wordt uitgevoerd. Zodra het is voltooid, wordt de '\*' wijzigingen in een getal, en de uitvoer vergelijkbaar met de volgende tekst wordt weergegeven onder de cel:

        Creating SparkContext as 'sc'

        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Een nieuwe cel wordt gemaakt onder de eerste. Voer de volgende tekst in de nieuwe cel in. Vervang `CONTAINER` `STORAGEACCOUNT` en door de naam van het Azure Storage-account en de naam van blobcontainer die logboeken met Toepassingsstatistieken bevat.

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    Gebruik **SHIFT+ENTER** om deze cel uit te voeren. U ziet een resultaat dat vergelijkbaar is met de volgende tekst:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831

    Het geretourneerde wasbspad is de locatie van de telemetriegegevens van Application Insights. Wijzig `hdfs dfs -ls` de regel in de cel om het geretourneerde wespenpad te gebruiken en gebruik **Shift+ENTER** om de cel opnieuw uit te voeren. Deze keer moeten de resultaten de mappen weergeven die telemetriegegevens bevatten.

   > [!NOTE]  
   > Voor de rest van de stappen `wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` in deze sectie is de map gebruikt. Deze map bestaat mogelijk niet, tenzij uw telemetriegegevens voor een web-app zijn.

6. Voer in de volgende cel de `WASB\_PATH` volgende code in: Vervang het pad uit de vorige stap.

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    Met deze code wordt een gegevensframe aanmaakt van de JSON-bestanden die worden geëxporteerd door het continue exportproces. Gebruik **SHIFT+ENTER** om deze cel uit te voeren.

7. Voer in de volgende cel het volgende in en voer deze uit om het schema weer te geven dat Spark heeft gemaakt voor de JSON-bestanden:

   ```scala
   jsonData.printSchema
   ```

    Het schema voor elk type telemetrie is anders. Het volgende voorbeeld is het schema dat wordt gegenereerd `Requests` voor webaanvragen (gegevens die zijn opgeslagen in de submap):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Gebruik het volgende om het gegevensframe als tijdelijke tabel te registreren en een query uit te voeren op basis van de gegevens:

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    Met deze query worden de stadsgegevens geretourneerd voor de top 20 records waarin context.location.city niet null is.

   > [!NOTE]  
   > De contextstructuur is aanwezig in alle telemetrie die is vastgelegd door Application Insights. Het element stad wordt mogelijk niet ingevuld in uw logboeken. Gebruik het schema om andere elementen te identificeren die u opvragen en die mogelijk gegevens voor uw logboeken bevatten.

    Met deze query worden informatie geretourneerd die vergelijkbaar is met de volgende tekst:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer voorbeelden van het gebruik van Apache Spark om met gegevens en services in Azure te werken:

* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analyse met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

Zie de volgende documenten voor informatie over het maken en uitvoeren van Spark-toepassingen:

* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Taken op afstand uitvoeren op een Apache Spark-cluster met Livy](apache-spark-livy-rest-interface.md)
