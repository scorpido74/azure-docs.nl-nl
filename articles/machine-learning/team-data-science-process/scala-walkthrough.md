---
title: Data Science met Scala en Spark op Azure - Team Data Science Process
description: Scala gebruiken voor begeleide machine learning-taken met de spark-schaalbare MLlib- en Spark ML-pakketten op een Azure HDInsight Spark-cluster.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b36a3faab49ee8d51c25aa18879e6f5d1db8c2fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716756"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Gegevenswetenschap met Scala en Spark op Azure
In dit artikel ziet u hoe u Scala gebruiken voor begeleide machine learning-taken met de spark-schaalbare MLlib- en Spark ML-pakketten op een Azure HDInsight Spark-cluster. Het leidt u door de taken die het [Data Science-proces](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)vormen: gegevensopname en -exploratie, visualisatie, functieengineering, modellering en modelverbruik. De modellen in het artikel omvatten logistieke en lineaire regressie, willekeurige bossen en gradiënt-boosted bomen (GBT's), naast twee gemeenschappelijke onder toezicht gecontroleerde machine learning taken:

* Regressieprobleem: Voorspelling van het tipbedrag ($) voor een taxirit
* Binaire classificatie: Voorspelling van tip of geen tip (1/0) voor een taxirit

Het modelleringsproces vereist training en evaluatie van een testgegevensset en relevante nauwkeurigheidsstatistieken. In dit artikel u leren hoe u deze modellen opslaan in Azure Blob-opslag en hoe u de voorspellende prestaties scoren en evalueren. Dit artikel behandelt ook de meer geavanceerde onderwerpen van het optimaliseren van modellen met behulp van cross-validatie en hyper-parameter vegen. De gebruikte gegevens is een voorbeeld van de 2013 NYC taxi reis en tarief gegevens set beschikbaar op GitHub.

[Scala](https://www.scala-lang.org/), een taal gebaseerd op de Java virtuele machine, integreert object-georiënteerde en functionele taalconcepten. Het is een schaalbare taal die zeer geschikt is voor gedistribueerde verwerking in de cloud en wordt uitgevoerd op Azure Spark-clusters.

[Spark](https://spark.apache.org/) is een open-source parallelverwerkingsframework dat in-memory processing ondersteunt om de prestaties van big data analytics-toepassingen te verbeteren. De Spark processing engine is gebouwd voor snelheid, gebruiksgemak en geavanceerde analyses. Spark's in-memory gedistribueerde rekenmogelijkheden maken het een goede keuze voor iteratieve algoritmen in machine learning en grafiekberekeningen. Het [spark.ml-pakket](https://spark.apache.org/docs/latest/ml-guide.html) biedt een uniforme set API's op hoog niveau die bovenop gegevensframes zijn gebouwd en die u kunnen helpen bij het maken en afstemmen van praktische machine learning-pijplijnen. [MLlib](https://spark.apache.org/mllib/) is spark's schaalbare machine learning-bibliotheek, die modelleringsmogelijkheden naar deze gedistribueerde omgeving brengt.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) is het door Azure gehoste aanbod van open-source Spark. Het bevat ook ondersteuning voor Jupyter Scala-notitieblokken op het Spark-cluster en kan interactieve Spark SQL-query's uitvoeren om gegevens die zijn opgeslagen in Azure Blob-opslag te transformeren, te filteren en te visualiseren. De Scala-codefragmenten in dit artikel die de oplossingen bieden en de relevante plots weergeven om de gegevens te visualiseren die worden uitgevoerd in Jupyter-notitieblokken die zijn geïnstalleerd op de Spark-clusters. De modelleringsstappen in deze onderwerpen bevatten code die u laat zien hoe u elk type model trainen, evalueren, opslaan en consumeren.

De installatiestappen en -code in dit artikel zijn voor Azure HDInsight 3.4 Spark 1.6. De code in dit artikel en in het [Scala Jupyter-notitieblok](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) is echter generiek en moet werken op een Spark-cluster. De clusterinstellingen en beheerstappen kunnen enigszins afwijken van wat in dit artikel wordt weergegeven als u HDInsight Spark niet gebruikt.

> [!NOTE]
> Zie [Data Science met Spark op Azure HDInsight](spark-overview.md)voor een onderwerp dat u python gebruiken in plaats van Scala om taken te voltooien voor een end-to-end Data Science-proces.
> 
> 

## <a name="prerequisites"></a>Vereisten
* U hebt een abonnement op Azure nodig. Als u er nog geen hebt, [krijgt u een gratis proefversie van Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* U hebt een Azure HDInsight 3.4 Spark 1.6-cluster nodig om de volgende procedures te voltooien. Als u een cluster wilt maken, raadpleegt u de instructies in [Aan de slag: Apache Spark maken op Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Stel het clustertype en de versie in het menu **Clustertype selecteren** in.

![HDInsight-clustertypeconfiguratie](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Zie de relevante secties in [Overzicht van Gegevenswetenschap met Spark op Azure HDInsight](spark-overview.md)voor een beschrijving van de gegevens van de NYC-taxirit en instructies voor het uitvoeren van code vanuit een Jupyter-notitieblok op het Spark-cluster.  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Scala-code uitvoeren vanuit een Jupyter-notitieblok op het Spark-cluster
U een Jupyter-notitieblok starten vanuit de Azure-portal. Zoek het Spark-cluster op uw dashboard en klik erop om de beheerpagina voor uw cluster in te voeren. Klik vervolgens op **Clusterdashboards**en klik vervolgens op **Jupyter-notitieblok** om het notitieblok te openen dat is gekoppeld aan het Spark-cluster.

![Clusterdashboard en Jupyter-notitieblokken](./media/scala-walkthrough/spark-jupyter-on-portal.png)

U hebt ook toegang tot Jupyter-notitieblokken op https://&lt;clusternaam&gt;.azurehdinsight.net/jupyter. Vervang *clusternaam* door de naam van uw cluster. U hebt het wachtwoord voor uw beheerdersaccount nodig om toegang te krijgen tot de Jupyter-notitieblokken.

![Ga naar Jupyter-notitieblokken met de clusternaam](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecteer **Scala** om een map te zien met een paar voorbeelden van voorverpakte notitieblokken die de PySpark API gebruiken. Het zoekmodellerings- en scoremodel met Scala.ipynb-notitieblok met de codevoorbeelden voor deze reeks Spark-onderwerpen is beschikbaar op [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)

U het notitieblok rechtstreeks van GitHub uploaden naar de Jupyter Notebook-server op uw Spark-cluster. Klik op de startpagina van Jupyter op de knop **Uploaden.** Plak in de verkenner de URL van GitHub (raw content) van het Scala-notitieblok en klik op **Openen**. Het Scala-notitieblok is beschikbaar op de volgende URL:

[Exploratie-Modellering-en-Scoreeren-met behulp van Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Setup: Vooraf ingestelde Spark- en Hive-contexten, Spark-magics en Spark-bibliotheken
### <a name="preset-spark-and-hive-contexts"></a>Vooraf ingestelde Spark- en Hive-contexten
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


De Spark-kernels die zijn voorzien van Jupyter-notitieblokken hebben vooraf ingestelde contexten. U hoeft de Spark- of Hive-contexten niet expliciet in te stellen voordat u aan de slag gaat met de toepassing die u ontwikkelt. De vooraf ingestelde contexten zijn:

* `sc`voor SparkContext
* `sqlContext`voor HiveContext

### <a name="spark-magics"></a>Vonk magie
De Spark-kernel biedt een aantal vooraf gedefinieerde magics, die `%%`speciale commando's zijn waarmee u bellen. Twee van deze opdrachten worden gebruikt in de volgende codevoorbeelden.

* `%%local`hiermee wordt opgegeven dat de code in volgende regels lokaal wordt uitgevoerd. De code moet een geldige Scala-code hebben.
* `%%sql -o <variable name>`hiermee wordt een `sqlContext`Hive-query uitgevoerd tegen . Als `-o` de parameter wordt doorgegeven, blijft het `%%local` resultaat van de query bestaan in de Scala-context als een Spark-gegevensframe.

Voor meer informatie over de kernels voor Jupyter notebooks en hun `%%` vooraf gedefinieerde `%%local`"magics" die u belt met (bijvoorbeeld), zie [Kernels beschikbaar voor Jupyter notebooks met HDInsight Spark Linux clusters op HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Bibliotheken importeren
Importeer de Spark, MLlib en andere bibliotheken die u nodig hebt met behulp van de volgende code.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Gegevensopname
De eerste stap in het Data Science-proces is het innemen van de gegevens die u wilt analyseren. U brengt de gegevens uit externe bronnen of systemen waar ze zich bevinden in uw data-exploratie- en modelleringsomgeving. In dit artikel, de gegevens die u inneemt is een samengevoegde 0,1% monster van de taxi rit en tarief bestand (opgeslagen als een .tsv bestand). De omgeving voor gegevensverkenning en modellering is Spark. Deze sectie bevat de code om de volgende reeks taken te voltooien:

1. Stel mappaden in voor gegevens- en modelopslag.
2. Lees in de invoergegevensset (opgeslagen als .tsv-bestand).
3. Definieer een schema voor de gegevens en maak de gegevens schoon.
4. Maak een opgeschoond gegevensframe en cache het in het geheugen.
5. Registreer de gegevens als tijdelijke tabel in SQLContext.
6. Query de tabel en importeer de resultaten in een gegevensframe.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Adreslijstpaden instellen voor opslaglocaties in Azure Blob-opslag
Spark kan lezen en schrijven naar Azure Blob-opslag. U Spark gebruiken om een van uw bestaande gegevens te verwerken en de resultaten vervolgens opnieuw op te slaan in Blob-opslag.

Als u modellen of bestanden wilt opslaan in Blob-opslag, moet u het pad correct opgeven. Raadpleeg de standaardcontainer die aan het Spark-cluster `wasb:///`is gekoppeld met behulp van een pad dat begint met . Verwijs naar andere `wasb://`locaties met behulp van .

In het volgende codevoorbeeld wordt de locatie opgegeven van de te lezen invoergegevens en het pad naar blobopslag dat is gekoppeld aan het Spark-cluster waar het model wordt opgeslagen.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Gegevens importeren, een RDD maken en een gegevensframe definiëren volgens het schema
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tijd om de cel uit te voeren: 8 seconden.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>De tabel opvragen en resultaten importeren in een gegevensframe
Vraag vervolgens de tabel op naar tarief-, passagiers- en tipgegevens; beschadigde en afgelegen gegevens uit filteren; en meerdere rijen afdrukken.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Output:**

| fare_amount | passenger_count | tip_amount | Getipt |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Gegevensverkenning en visualisatie
Nadat u de gegevens in Spark hebt gebracht, is de volgende stap in het Data Science-proces om een beter inzicht te krijgen in de gegevens door middel van exploratie en visualisatie. In deze sectie onderzoekt u de taxigegevens met SQL-query's. Importeer vervolgens de resultaten in een gegevensframe om de doelvariabelen en toekomstige functies voor visuele inspectie in kaart te brengen met behulp van de functie Automatische visualisatie Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Lokale en SQL-magie gebruiken om gegevens te plotten
Standaard is de uitvoer van een codefragment dat u uitvoert vanuit een Jupyter-notitieblok beschikbaar in de context van de sessie die op de werknemersknooppunten wordt uitgevoerd. Als u een reis naar de werknemersknooppunten wilt opslaan voor elke berekening en als alle gegevens die u voor uw berekening nodig hebt, lokaal beschikbaar `%%local` zijn op het knooppunt van de Jupyter-server (dat is het hoofdknooppunt), u de magie gebruiken om het codefragment op de Jupyter-server uit te voeren.

* **SQL-magie** (`%%sql`). De HDInsight Spark-kernel ondersteunt eenvoudige Inline HiveQL-query's tegen SQLContext. Het`-o VARIABLE_NAME`argument ( ) blijft de uitvoer van de SQL-query als een Pandas-gegevensframe op de Jupyter-server. Deze instelling betekent dat de uitvoer beschikbaar is in de lokale modus.
* `%%local`**magie.** De `%%local` magie voert de code lokaal uit op de Jupyter-server, het hoofdknooppunt van het HDInsight-cluster. Meestal gebruikt `%%local` u magie in `%%sql` combinatie met `-o` de magie met de parameter. De `-o` parameter blijft de uitvoer van de SQL-query lokaal aanhouden en vervolgens `%%local` activeert magie de volgende set codefragment om lokaal uit te voeren tegen de uitvoer van de SQL-query's die lokaal worden aangehouden.

### <a name="query-the-data-by-using-sql"></a>De gegevens opvragen met SQL
Deze query haalt de taxiritten op op tariefbedrag, aantal passagiers en het tipbedrag.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

In de volgende `%%local` code maakt de magie een lokaal gegevensframe, sqlResults. U sqlResults gebruiken om te plotten met matplotlib.

> [!TIP]
> Lokale magie wordt meerdere keren gebruikt in dit artikel. Als uw gegevensset groot is, u een voorbeeld nemen om een gegevensframe te maken dat past in het lokale geheugen.
> 
> 

### <a name="plot-the-data"></a>De gegevens in kaart brengen
U plotten met behulp van Python-code nadat het gegevensframe zich in lokale context bevindt als een Pandas-gegevensframe.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 De Spark-kernel visualiseert automatisch de uitvoer van SQL-query's (HiveQL) nadat u de code hebt uitgevoerd. U kiezen uit verschillende typen visualisaties:

* Tabel
* Cirkeldiagram
* Lijn
* Onderwerp
* Staafdiagram

Hier is de code om de gegevens te plotten:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Output:**

![Tip bedrag histogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tipbedrag per aantal passagiers](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tipbedrag per tariefbedrag](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Functies maken en functies transformeren en vervolgens gegevens voorbereiden voor invoer in modelleringsfuncties
Voor op bomen gebaseerde modelleringsfuncties van Spark ML en MLlib moet u doel en functies voorbereiden met behulp van verschillende technieken, zoals binning, indexering, one-hot codering en vectorisatie. Hier zijn de procedures te volgen in deze sectie:

1. Maak een nieuwe functie door **uren in** de buckets van de verkeerstijd te plaatsen.
2. Pas **indexering en one-hot codering** toe op categorische functies.
3. **Bemonster en spliter de gegevensset** in trainings- en testfracties.
4. **Geef trainingsvariabele en -functies op**en maak vervolgens geïndexeerde of eenhot gecodeerde training en testinvoer met het label Point resilient distributed datasets (RDD's) of gegevensframes.
5. **Categoriseer en vectorize functies en doelen** automatisch om te gebruiken als invoer voor machine learning-modellen.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Een nieuwe functie maken door uren in de buckets van het verkeer te plaatsen
Deze code laat u zien hoe u een nieuwe functie maakt door uren in de buckets van de verkeerstijd te plaatsen en hoe u het resulterende gegevensframe in het geheugen in de cache opslaan. Waar RDD's en dataframes herhaaldelijk worden gebruikt, leidt caching tot verbeterde uitvoeringstijden. Daarom cachet u RDD's en gegevensframes in verschillende fasen in de volgende procedures.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexering en one-hot codering van categorische functies
De modellerings- en voorspellende functies van MLlib vereisen functies met categorische invoergegevens die vóór gebruik moeten worden geïndexeerd of gecodeerd. In deze sectie ziet u hoe u categorische functies indexeert of codeert voor invoer in de modelleringsfuncties.

Afhankelijk van het model moet u uw modellen op verschillende manieren indexeren of coderen. Logistieke en lineaire regressiemodellen vereisen bijvoorbeeld one-hot encoding. Een functie met drie categorieën kan bijvoorbeeld worden uitgebreid tot drie functiekolommen. Elke kolom zou 0 of 1 bevatten, afhankelijk van de categorie van een waarneming. MLlib biedt de [OneHotEncoder-functie](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) voor one-hot encoding. Deze encoder brengt een kolom met labelindexen in kaart met een kolom van binaire vectoren met maximaal één waarde. Met deze codering kunnen algoritmen die numerieke waardefuncties verwachten, zoals logistieke regressie, worden toegepast op categorische functies.

Hier transformeert u slechts vier variabelen om voorbeelden weer te geven, die tekentekenreeksen zijn. U ook andere variabelen, zoals weekdag, die door numerieke waarden worden weergegeven, als categorische variabelen indexeren.

Gebruik `OneHotEncoder()` functies van `StringIndexer()`MLlib voor indexering, gebruik en voor one-hot-codering. Hier is de code om categorische functies te indexeren en te coderen:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tijd om de cel uit te voeren: 4 seconden.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>De gegevensset bemonsteren en splitsen in trainings- en testfracties
Deze code maakt een willekeurige steekproef van de gegevens (25%, in dit voorbeeld). Hoewel bemonstering niet vereist is voor dit voorbeeld vanwege de grootte van de gegevensset, laat het artikel u zien hoe u samplen, zodat u weet hoe u deze gebruiken voor uw eigen problemen wanneer dat nodig is. Wanneer monsters groot zijn, kan dit veel tijd besparen terwijl u modellen traint. Splits vervolgens het voorbeeld op in een trainingsonderdeel (75%, in dit voorbeeld) en een testonderdeel (25%, in dit voorbeeld) om te gebruiken in classificatie- en regressiemodellering.

Voeg een willekeurig getal (tussen 0 en 1) toe aan elke rij (in een randkolom) die kan worden gebruikt om kruisvalidatieplooien te selecteren tijdens de training.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tijd om de cel uit te voeren: 2 seconden.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Geef trainingsvariabele en -functies op en maak vervolgens geïndexeerde of eenhete gecodeerde trainings- en testinvoer met het label punt-RDD's of gegevensframes
Deze sectie bevat code die u laat zien hoe u categorische tekstgegevens indexeren als een gelabeld puntgegevenstype en deze coderen, zodat u deze gebruiken om de logistieke regressie van MLlib en andere classificatiemodellen te trainen en te testen. Gelabelde puntobjecten zijn RDD's die zijn opgemaakt op een manier die nodig is als invoergegevens door de meeste machine learning-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, dicht of schaars, gekoppeld aan een label/respons.

In deze code geeft u de doelvariabele (afhankelijke) variabele en de functies op die u moet gebruiken om modellen te trainen. Vervolgens maakt u geïndexeerde of een hot gecodeerde training en testen input gelabeld punt RDDs of data frames.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tijd om de cel uit te voeren: 4 seconden.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatisch functies en doelen categoriseren en vectoriseren om te gebruiken als invoer voor machine learning-modellen
Gebruik Spark ML om het doel en de functies te categoriseren die moeten worden gebruikt in op bomen gebaseerde modelleringsfuncties. De code voltooit twee taken:

* Hiermee maakt u een binair doel voor classificatie door een waarde van 0 of 1 toe te wijzen aan elk gegevenspunt tussen 0 en 1 met behulp van een drempelwaarde van 0,5.
* Categoriseert automatisch functies. Als het aantal afzonderlijke numerieke waarden voor een functie kleiner is dan 32, wordt die functie gecategoriseerd.

Hier is de code voor deze twee taken.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binair classificatiemodel: Voorspel of een fooi moet worden betaald
In deze sectie maakt u drie typen binaire classificatiemodellen om te voorspellen of er al dan niet een tip moet worden betaald:

* Een **logistiek regressiemodel** met `LogisticRegression()` de functie Spark ML
* Een **willekeurig forestclassificatiemodel** met `RandomForestClassifier()` de functie Spark ML
* Een **verloopverhogend boomclassificatiemodel** met de `GradientBoostedTrees()` functie MLlib

### <a name="create-a-logistic-regression-model"></a>Een logistiek regressiemodel maken
Maak vervolgens een logistiek regressiemodel met `LogisticRegression()` de functie Spark ML. U maakt de modelbouwcode in een reeks stappen:

1. **Train de modelgegevens** met één parameterset.
2. **Evalueer het model** op een testgegevensset met statistieken.
3. **Sla het model op** in Blob-opslag voor toekomstig verbruik.
4. **Scoor het model** aan de hand van testgegevens.
5. **Zet de resultaten in** kaart met de gebogen karakteristiek (ROC) curven van de ontvanger.

Hier is de code voor deze procedures:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Laad, scoor en sla de resultaten op.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Output:**

ROC op testgegevens = 0,9827381497557599

Gebruik Python op lokale Panda's-gegevensframes om de ROC-curve in kaart te brengen.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**Output:**

![Tip of geen tip ROC curve](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Een willekeurig forestclassificatiemodel maken
Maak vervolgens een willekeurig forestclassificatiemodel met `RandomForestClassifier()` de functie Spark ML en evalueer het model op testgegevens.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Output:**

ROC op testgegevens = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>Een GBT-classificatiemodel maken
Maak vervolgens een GBT-classificatiemodel met de `GradientBoostedTrees()` functie van MLlib en evalueer het model vervolgens op testgegevens.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Output:**

Gebied onder ROC-curve: 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressiemodel: Tipbedrag voorspellen
In deze sectie maakt u twee typen regressiemodellen om het tipbedrag te voorspellen:

* Een **geregulariseerd lineair regressiemodel** `LinearRegression()` met de functie Spark ML. U slaat het model op en evalueert het model op testgegevens.
* Een **verloopverhogend boomregressiemodel** met de `GBTRegressor()` functie Spark ML.

### <a name="create-a-regularized-linear-regression-model"></a>Een geregulariseerd lineair regressiemodel maken
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tijd om de cel uit te voeren: 13 seconden.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Output:**

R-sqr op testgegevens = 0,5960320470835743

Vervolgens bevraag je de testresultaten als een gegevensframe en gebruik je AutoVizWidget en matplotlib om het te visualiseren.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

De code maakt een lokaal gegevensframe op basis van de queryuitvoer en stelt de gegevens in een stand. De `%%local` magie creëert een `sqlResults`lokaal gegevensframe, dat u gebruiken om te plotten met matplotlib.

> [!NOTE]
> Deze Spark magie wordt meerdere keren gebruikt in dit artikel. Als de hoeveelheid gegevens groot is, moet u een voorbeeld nemen om een gegevensframe te maken dat in het lokale geheugen past.
> 
> 

Maak plots met Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Output:**

![Tipbedrag: Werkelijke vs. voorspeld](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Een GBT-regressiemodel maken
Maak een GBT-regressiemodel met `GBTRegressor()` de functie Spark ML en evalueer het model vervolgens op testgegevens.

[Gradiënt-boosted bomen](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) zijn ensembles van beslisbomen. GBTS traint beslissingsbomen iteratief om een verliesfunctie te minimaliseren. U GBTS gebruiken voor regressie en classificatie. Ze kunnen categorische functies verwerken, vereisen geen functieschaling en kunnen niet-lineariteiten en functieinteracties vastleggen. U ze ook gebruiken in een instelling voor meerdere klassen.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Output:**

Test R-sqr is: 0,7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Geavanceerde modelleringshulpprogramma's voor optimalisatie
In deze sectie gebruikt u machine learning-hulpprogramma's die ontwikkelaars vaak gebruiken voor modeloptimalisatie. U machine learning-modellen op drie verschillende manieren optimaliseren door parametervegen en cross-validatie te gebruiken:

* Splits de gegevens op in trein- en validatiesets, optimaliseer het model met behulp van hyperparametervegen op een trainingsset en evalueer op een validatieset (lineaire regressie)
* Optimaliseer het model met behulp van cross-validatie en hyper-parameter vegen met behulp van Spark ML's CrossValidator functie (binaire classificatie)
* Optimaliseer het model met behulp van aangepaste cross-validatie en parameter-veegcode om een machine learning-functie en parameterset te gebruiken (lineaire regressie)

**Cross-validatie** is een techniek die beoordeelt hoe goed een model getraind op een bekende set van gegevens zal generaliseren om de kenmerken van gegevenssets waarop het niet is opgeleid voorspellen. Het algemene idee achter deze techniek is dat een model is getraind op een gegevensset van bekende gegevens, en vervolgens wordt de nauwkeurigheid van de voorspellingen getest op basis van een onafhankelijke gegevensset. Een gemeenschappelijke implementatie is om een gegevensset in *k-plooien*te verdelen en het model vervolgens op een ronde-robin-manier op alle, maar één van de plooien, te trainen.

**Hyper-parameter optimalisatie** is het probleem van het kiezen van een set van hyper-parameters voor een leeralgoritme, meestal met het doel van het optimaliseren van een meting van de prestaties van het algoritme op een onafhankelijke gegevensset. Een hyperparameter is een waarde die u buiten de modeltrainingsprocedure moet opgeven. Veronderstellingen over hyperparameterwaarden kunnen de flexibiliteit en nauwkeurigheid van het model beïnvloeden. Beslissingsbomen hebben hyperparameters, bijvoorbeeld, zoals de gewenste diepte en het aantal bladeren in de boom. U moet een foutiestrafinstellen voor een ondersteuningsvectormachine (SVM).

Een veel voorkomende manier om hyper-parameter optimalisatie uit te voeren is het gebruik van een raster zoeken, ook wel een **parameter sweep**. In een rasterzoekopdracht wordt een uitputtende zoekopdracht uitgevoerd door de waarden van een opgegeven subset van de hyperparameterruimte voor een leeralgoritme. Cross-validatie kan een prestatiestatistiek leveren om de optimale resultaten van het rasterzoekalgoritme te sorteren. Als u hyperparametervegen met kruisvalidatie gebruikt, u problemen zoals het overpassen van een model beperken tot trainingsgegevens. Op deze manier behoudt het model de capaciteit om toe te passen op de algemene set gegevens waaruit de trainingsgegevens zijn geëxtraheerd.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Een lineair regressiemodel optimaliseren met hyperparametervegen
Splits vervolgens gegevens op in trein- en validatiesets, gebruik hyperparametervegen op een trainingsset om het model te optimaliseren en evalueer op een validatieset (lineaire regressie).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Output:**

Test R-sqr is: 0,6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimaliseer het binaire classificatiemodel met behulp van cross-validatie en hyperparametervegen
In deze sectie ziet u hoe u een binair classificatiemodel optimaliseren met behulp van crossvalidatie en hyperparametervegen. Dit maakt gebruik `CrossValidator` van de functie Spark ML.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tijd om de cel uit te voeren: 33 seconden.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Het lineaire regressiemodel optimaliseren met behulp van aangepaste crossvalidatie- en parameterveegcode
Optimaliseer vervolgens het model met behulp van aangepaste code en identificeer de beste modelparameters met behulp van het criterium van de hoogste nauwkeurigheid. Maak vervolgens het uiteindelijke model, evalueer het model op testgegevens en sla het model op in Blob-opslag. Laad ten slotte het model, scoor testgegevens en evalueer de nauwkeurigheid.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Output:**

Tijd om de cel uit te voeren: 61 seconden.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Gebruik spark-built machine learning-modellen automatisch met Scala
Zie [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)voor een overzicht van onderwerpen die u door de taken leiden die het Data Science-proces in Azure omvatten.

[Team Data Science Process walkthroughs](walkthroughs.md) beschrijft andere end-to-end walkthroughs die de stappen in het Team Data Science Process voor specifieke scenario's demonstreren. De walkthroughs illustreren ook hoe cloud- en on-premises tools en services kunnen worden gecombineerd in een workflow of pijplijn om een intelligente toepassing te maken.

[Score Spark-built machine learning-modellen](spark-model-consumption.md) laten zien hoe u Scala-code gebruiken om automatisch nieuwe gegevenssets te laden en te scoren met machine learning-modellen die zijn gebouwd in Spark en worden opgeslagen in Azure Blob-opslag. U de instructies die er zijn, en vervang gewoon de Python-code met Scala-code in dit artikel voor geautomatiseerd verbruik.

