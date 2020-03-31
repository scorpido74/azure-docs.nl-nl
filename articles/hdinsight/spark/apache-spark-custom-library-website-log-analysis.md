---
title: Websitelogboeken analyseren met Python-bibliotheken in Spark - Azure
description: In dit notitieblok wordt uitgelegd hoe u logboekgegevens analyseren met behulp van een aangepaste bibliotheek met Spark op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552709"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Websitelogboeken analyseren met behulp van een aangepaste Python-bibliotheek met Apache Spark-cluster op HDInsight

Dit notitieblok laat zien hoe logboekgegevens kunnen worden geanalyseerd met behulp van een aangepaste bibliotheek met Apache Spark op HDInsight. De aangepaste bibliotheek die we gebruiken is een Python-bibliotheek genaamd **iislogparser.py.**

## <a name="prerequisites"></a>Vereisten

Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="save-raw-data-as-an-rdd"></a>Ruwe gegevens opslaan als een RDD

In deze sectie gebruiken we het [Jupyter-notitieblok](https://jupyter.org) dat is gekoppeld aan een Apache Spark-cluster in HDInsight om taken uit te voeren die uw ruwe voorbeeldgegevens verwerken en opslaan als een Hive-tabel. De voorbeeldgegevens zijn standaard een CSV-bestand (hvac.csv) dat beschikbaar is voor alle clusters.

Zodra uw gegevens zijn opgeslagen als een Apache Hive-tabel, maken we in de volgende sectie verbinding met de Hive-tabel met BI-tools zoals Power BI en Tableau.

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/jupyter`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Maak een nieuwe notebook. Selecteer **Nieuw**en vervolgens **PySpark**.

    ![Een nieuw Apache Jupyter-notitieblok maken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Een nieuwe Jupyter-notebook maken")

1. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Selecteer de naam van het notitieblok bovenaan en voer een vriendelijke naam in.

    ![Een naam opgeven voor de notebook](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Een naam opgeven voor de notebook")

1. Omdat u een notitieblok hebt gemaakt met de PySpark-kernel, hoeft u geen contexten expliciet te maken. De Spark- en Hive-contexten worden automatisch voor u gemaakt tijdens het uitvoeren van de eerste codecel. U beginnen met het importeren van de typen die nodig zijn voor dit scenario. Plak het volgende fragment in een lege cel en druk op **Shift + Enter**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Maak een RDD met behulp van de voorbeeldlogboekgegevens die al beschikbaar zijn op het cluster. U hebt toegang tot de gegevens in het `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log`standaardopslagaccount dat aan het cluster is gekoppeld op . Voer de volgende code uit:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Een voorbeeldlogboekset ophalen om te controleren of de vorige stap is voltooid.

    ```pyspark
    logs.take(5)
    ```

    U ziet een uitvoer die vergelijkbaar is met de volgende tekst:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Loggegevens analyseren met een aangepaste Python-bibliotheek

1. In de bovenstaande uitvoer bevatten de eerste paarregels de koptekstgegevens en elke resterende regel komt overeen met het schema dat in die koptekst wordt beschreven. Het ontpareren van dergelijke logboeken kan ingewikkeld zijn. Dus, we gebruiken een aangepaste Python-bibliotheek **(iislogparser.py**) die het ontleden van dergelijke logs veel gemakkelijker maakt. Standaard wordt deze bibliotheek meegeleverd met uw Spark-cluster op HDInsight op `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py`.

    Deze bibliotheek bevindt zich `PYTHONPATH` echter niet in de bibliotheek, dus `import iislogparser`we kunnen deze niet gebruiken met behulp van een importverklaring zoals . Om deze bibliotheek te gebruiken, moeten we deze distribueren naar alle werknemersknooppunten. Voer het volgende fragment uit.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser`biedt een `parse_log_line` functie `None` die retourneert als een logboekregel een `LogLine` koptekstrij is en een instantie van de klasse retourneert als deze een logboekregel tegenkomt. Gebruik `LogLine` de klasse om alleen de logboekregels uit de RDD te extraheren:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Haal een paar uitgepakte logboekregels op om te controleren of de stap is voltooid.

    ```pyspark
    logLines.take(2)
    ```

   De uitvoer moet vergelijkbaar zijn met de volgende tekst:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. De `LogLine` klasse heeft op zijn beurt een `is_error()`aantal nuttige methoden, zoals , die retourneert of een logboekvermelding een foutcode heeft. Gebruik deze klasse om het aantal fouten in de uitgepakte logboekregels te berekenen en vervolgens alle fouten in een ander bestand aan te melden.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    De uitvoer `There are 30 errors and 646 log entries`moet worden vermeld .

1. U **Matplotlib** ook gebruiken om een visualisatie van de gegevens te maken. Als u bijvoorbeeld de oorzaak wilt isoleren van aanvragen die lange tijd worden uitgevoerd, u de bestanden vinden die gemiddeld de meeste tijd in beslag nemen. In het onderstaande fragment worden de 25 beste bronnen opgehaald die de meeste tijd nodig hadden om een aanvraag te serveren.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   U ziet een uitvoer zoals de volgende tekst:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. U deze informatie ook presenteren in de vorm van plot. Als eerste stap om een plot te maken, laten we eerst een tijdelijke tabel **AverageTime**maken. De tabel groepeert de logboeken op tijd om te zien of er ongebruikelijke latentiepieken op een bepaald moment waren.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. U vervolgens de volgende SQL-query uitvoeren om alle records in de **tabel AverageTime** op te halen.

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   De `%%sql` magie `-o averagetime` die wordt gevolgd door zorgt ervoor dat de uitvoer van de query lokaal wordt gehandhaafd op de Jupyter-server (meestal de headnode van het cluster). De uitvoer wordt gehandhaafd als een [Pandas-gegevensframe](https://pandas.pydata.org/) met de opgegeven **naamgemiddeldetijd**.

   U ziet een uitvoer zoals de volgende afbeelding:

   ![hdinsight jupyter sql query output](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-queryuitvoer")

   Zie Parameters die `%%sql` worden [ondersteund met de %%sql-magie](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over de magie.

1. U nu Matplotlib, een bibliotheek die wordt gebruikt om visualisatie van gegevens te construeren, gebruiken om een plot te maken. Omdat de plot moet worden gemaakt van het lokaal aanhoudende **gemiddelde tijdgegevensframe,** moet het codefragment beginnen met de `%%local` magie. Dit zorgt ervoor dat de code lokaal wordt uitgevoerd op de Jupyter-server.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   U ziet een uitvoer zoals de volgende afbeelding:

   ![apache spark web log analyse plot](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib-uitvoer")

1. Nadat u klaar bent met het uitvoeren van de toepassing, moet u het notitieblok afsluiten om de bronnen vrij te geven. Selecteer hiervoor **Sluiten en stoppen** in het menu **Bestand** van het notebook. Met deze actie wordt het notitieblok afgesloten en gesloten.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen:

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)