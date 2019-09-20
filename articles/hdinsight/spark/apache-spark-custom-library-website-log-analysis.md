---
title: Website logboeken analyseren met python-bibliotheken in Spark-Azure
description: Dit notitie blok laat zien hoe u logboek gegevens kunt analyseren met behulp van een aangepaste bibliotheek met Spark in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 6d23e8cfa8d20169d2b63723138b60dafb1069de
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146980"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Website logboeken analyseren met behulp van een aangepaste python-bibliotheek met Apache Spark cluster op HDInsight

Dit notitie blok laat zien hoe u logboek gegevens kunt analyseren met behulp van een aangepaste bibliotheek met Apache Spark op HDInsight. De aangepaste bibliotheek die wordt gebruikt, is een python-bibliotheek met de naam **iislogparser.py**.

> [!TIP]  
> Dit artikel is ook beschikbaar als een Jupyter-notebook op een Spark-cluster (Linux) dat u in HDInsight maakt. Met de laptop ervaring kunt u de python-fragmenten uit het notitie blok zelf uitvoeren. Als u het artikel vanuit een notitie blok wilt uitvoeren, maakt u een Spark-cluster, start`https://CLUSTERNAME.azurehdinsight.net/jupyter`u een Jupyter-Notebook () en voert u vervolgens het notitie blok **Logboeken analyseren met Spark met behulp van een aangepaste bibliotheek. ipynb** in de map **PySpark** .

**Vereisten:**

U moet het volgende hebben:

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies.

## <a name="save-raw-data-as-an-rdd"></a>Onbewerkte gegevens opslaan als een RDD
In deze sectie gebruiken we het [Jupyter](https://jupyter.org) -notebook dat is gekoppeld aan een Apache Spark-cluster in HDInsight om taken uit te voeren die uw onbewerkte voorbeeld gegevens verwerken en deze op te slaan als een Hive-tabel. De voorbeeld gegevens zijn standaard een CSV-bestand (HVAC. CSV) dat beschikbaar is op alle clusters.

Zodra de gegevens zijn opgeslagen als een Apache Hive tabel, wordt in de volgende sectie verbinding gemaakt met de Hive-tabel met behulp van BI-hulpprogram ma's zoals Power BI en tableau.

1. Klik vanaf het Startboard in [Azure Portal](https://portal.azure.com/) op de tegel voor uw Spark-cluster (als u deze aan het Startboard hebt vastgemaakt). U kunt ook naar uw cluster navigeren onder **Bladeren** > **HDInsight-clusters**.

2. Klik vanuit de blade Spark-cluster op **Cluster-dashboard** en vervolgens op **Jupyter Notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

   > [!NOTE]
   > Mogelijk bereikt u de Jupyter-notebook voor uw cluster ook door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Maak een nieuwe notebook. Klik op **Nieuw** en klik vervolgens op **PySpark**.

    ![Een nieuwe Apache Jupyter-notebook maken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Een nieuwe Jupyter-notebook maken")

4. Er wordt een nieuwe notebook gemaakt en geopend met de naam Untitled.pynb. Klik bovenaan op de naam van de notebook en wijzig deze in een beschrijvende naam.

    ![Een naam opgeven voor de notebook](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Een naam opgeven voor de notebook")
5. Omdat u de notebook met behulp van de PySpark-kernel hebt gemaakt, hoeft u niet expliciet contexten te maken. De Spark- en Hive-contexten worden automatisch voor u gemaakt tijdens het uitvoeren van de eerste codecel. U kunt beginnen met het importeren van de typen die vereist zijn voor dit scenario. Plak het volgende codefragment in een lege cel en druk op **Shift+Enter**.

        from pyspark.sql import Row
        from pyspark.sql.types import *

6. Maak een RDD met behulp van de voorbeeld logboek gegevens die al beschikbaar zijn op het cluster. U kunt toegang krijgen tot de gegevens in het standaard opslag account dat is gekoppeld aan het cluster op **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')

7. Haal een voor beeld van een logboekset op om te controleren of de vorige stap is voltooid.

        logs.take(5)

    Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Logboek gegevens analyseren met behulp van een aangepaste python-bibliotheek

1. In de bovenstaande uitvoer bevatten de eerste paar regels de header-informatie en wordt elke resterende regel overeenkomt met het schema dat in die header wordt beschreven. Het parseren van dergelijke logboeken kan gecompliceerd zijn. Daarom gebruiken we een aangepaste python-bibliotheek (**iislogparser.py**) die het parseren van dergelijke logboeken veel eenvoudiger maakt. Deze bibliotheek is standaard opgenomen in uw Spark-cluster op HDInsight op **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    Deze bibliotheek bevindt zich echter niet `PYTHONPATH` in de zodat deze niet kan worden gebruikt met behulp `import iislogparser`van een import instructie zoals. Als u deze bibliotheek wilt gebruiken, moeten we deze distribueren naar alle worker-knoop punten. Voer het volgende code fragment uit.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')

1. `iislogparser`biedt een functie `parse_log_line` die retourneert `None` of een logboek regel een koprij is en een exemplaar van de `LogLine` klasse retourneert als er een logboek regel wordt aangetroffen. Gebruik de `LogLine` -klasse om alleen de logboek regels uit de RDD te extra heren:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()

1. Haal een aantal geëxtraheerde logboek regels op om te controleren of de stap is voltooid.

       logLines.take(2)

   De uitvoer moet er ongeveer als volgt uitzien:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]

1. De `LogLine` klasse heeft op zijn beurt een aantal handige methoden, zoals `is_error()`, die retourneert of een logboek vermelding een fout code heeft. Gebruik deze voor het berekenen van het aantal fouten in de uitgepakte logboek regels en meld alle fouten vervolgens aan een ander bestand.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
1. U kunt **matplotlib** ook gebruiken om een visualisatie van de gegevens te maken. Als u bijvoorbeeld de oorzaak van aanvragen wilt isoleren die gedurende een lange periode worden uitgevoerd, kunt u de bestanden die het meest het meest worden gebruikt, voor het eerst vinden.
   In het onderstaande fragment worden de belangrijkste 25 resources opgehaald die de meeste tijd nodig hebben om een aanvraag te doen.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

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

1. U kunt deze informatie ook in de vorm van het plot presen teren. Als eerste stap voor het maken van een tekening maakt u eerst een tijdelijke tabel **AverageTime**. De tabel groepeert de logboeken op tijd om te zien of er ongebruikelijke latentie pieken op een bepaald moment zijn.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

1. Vervolgens kunt u de volgende SQL-query uitvoeren om alle records in de **AverageTime** -tabel op te halen.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   Het `%%sql` Magic wordt gevolgd `-o averagetime` door ervoor te zorgen dat de uitvoer van de query lokaal wordt opgeslagen op de Jupyter-server (doorgaans de hoofd knooppunt van het cluster). De uitvoer wordt persistent gemaakt als een [Panda](https://pandas.pydata.org/) data frame met de opgegeven naam **averagetime**.

   Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

   ![jupyter SQL qyery-uitvoer hdinsight](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL-query-uitvoer")

   Zie `%%sql` [para meters die worden ondersteund met%% SQL Magic](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over het Magic.

1. U kunt nu matplotlib, een bibliotheek die wordt gebruikt om visualisatie van gegevens te maken, gebruiken om een plot te creëren. Omdat het waarnemings punt moet worden gemaakt van de lokaal blijvende persistente **averagetime** -data frame, moet het code fragment `%%local` beginnen met het Magic. Dit zorgt ervoor dat de code lokaal wordt uitgevoerd op de Jupyter-server.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   Als het goed is, wordt ongeveer de volgende uitvoer weergegeven:

   ![analyse diagram voor Apache Spark-Weblogboeken](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib uitvoer")

1. Wanneer u klaar bent met het uitvoeren van de toepassing, moet u de notebook afsluiten om de resources vrij te geven. Dit doet u door in het menu **Bestand** in de notebook te klikken op **Sluiten en stoppen**. Hiermee wordt de notebook afgesloten.

## <a name="seealso"></a>Zie ook
* [Krijgt Apache Spark in azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenario's
* [Apache Spark met BI: Interactieve gegevens analyse uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>Toepassingen maken en uitvoeren
* [Een zelfstandige toepassing maken met behulp van Scala](apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools en uitbreidingen
* [De invoeg toepassing HDInsight tools for IntelliJ idee gebruiken om Apache Spark scala-toepassingen te maken en in te dienen](apache-spark-intellij-tool-plugin.md)
* [De invoeg toepassing HDInsight tools for IntelliJ-idee gebruiken om op afstand fouten in Apache Spark toepassingen op te sporen](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-notebooks gebruiken met een Apache Spark-cluster in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels die beschikbaar zijn voor Jupyter notebook in Apache Spark cluster voor HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Externe pakketten gebruiken met Jupyter-notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter op uw computer installeren en verbinding maken met een HDInsight Spark-cluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Resources beheren
* [Resources beheren voor het Apache Spark-cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Taken die worden uitgevoerd in een Apache Spark-cluster in HDInsight, traceren en er fouten in oplossen](apache-spark-job-debugging.md)
