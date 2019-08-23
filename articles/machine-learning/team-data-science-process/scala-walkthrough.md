---
title: Gegevenswetenschap met Scala en Spark op Azure - Team Data Science Process
description: Het gebruik van Scala voor beheerde machine learning-taken met de Spark schaalbare MLlib en Spark ML-pakketten op een Azure HDInsight Spark-cluster.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b22d461d327e595908ea8cc18dd0d507fdc83ecd
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907701"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Gegevenswetenschap met Scala en Spark op Azure
In dit artikel wordt beschreven hoe u met Scala voor beheerde machine learning-taken met de Spark schaalbare MLlib en Spark ML-pakketten op een Azure HDInsight Spark-cluster. Dit helpt u bij de taken die deel uitmaken van de [Data Science process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): gegevensopname en verkennen, visualisatie, feature-engineering, modellen en model verbruik. De modellen in het artikel zijn onder andere logistieke en lineaire regressie, willekeurige forests en verloop boosted structuren (GBTs), naast de twee algemene beheerde machine learning-taken:

* Regressie probleem: Voor spelling van het fooien bedrag ($) voor een taxi reis
* Binaire classificatie: Voor speld uiteinde of fooien (1/0) voor een taxi

Het modelleringsproces is vereist voor training en evaluatie van een test-gegevensset en de nauwkeurigheid van de relevante metrische gegevens. In dit artikel leert u hoe deze modellen worden opgeslagen in Azure Blob-opslag en hoe u kunt beoordelen en evalueren van de voorspellende prestaties. Dit artikel behandelt ook de meer geavanceerde onderwerpen over het optimaliseren van modellen met behulp van kruisvalidatie en hyper-parameter sweeping. De gegevens die worden gebruikt, is een voorbeeld van de 2013 NYC taxi reis- en fare gegevensset beschikbaar op GitHub.

[Scala](https://www.scala-lang.org/), een taal op basis van de virtuele Java-machine, objectgeoriënteerd en functionele Taalconcepten integreert. Het is een schaalbare taal die zeer geschikt voor gedistribueerde verwerking in de cloud en wordt uitgevoerd op Azure Spark-clusters.

[Spark](https://spark.apache.org/) is een open source-framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor gegevensanalyse van big data te verbeteren. De Spark-verwerkingsengine is gebouwd voor snelheid, gebruiksgemak, en geavanceerde analyses. Gedistribueerde berekening in-memory-mogelijkheden van Spark kunt een goede keuze voor zich herhalende algoritmen in machine learning- en grafiekberekeningen. De [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) pakket biedt een uniforme reeks op hoog niveau API's die zijn gebaseerd op gegevens frames waarmee u kunnen maken en af te stemmen praktische machine learning-pijplijnen. [MLlib](https://spark.apache.org/mllib/) is Spark van schaalbare machine learning-bibliotheek, die zorgt voor modellering in deze gedistribueerde omgeving.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) is van de aanbieding wordt gehost op Azure van open-source Spark. Ook biedt ondersteuning voor Jupyter Scala-notebooks in het Spark-cluster en Spark SQL-interactieve query's om te transformeren, filteren en visualiseren van gegevens die zijn opgeslagen in Azure Blob-opslag kunnen worden uitgevoerd. De Scala codefragmenten in dit artikel die de oplossingen bieden en weergeven van de relevante grafieken om de gegevens te visualiseren uitvoeren in Jupyter-notebooks geïnstalleerd op de Spark-clusters. De stappen modellen in de volgende onderwerpen hebben code waarin wordt uitgelegd hoe u trainen, evalueren, opslaan en gebruiken van elk type model.

De installatiestappen uit en de code in dit artikel zijn voor Azure HDInsight 3.4 Spark 1.6. Echter, de code in dit artikel en klik in de [Scala Jupyter-Notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) zijn algemeen en werkt op een Spark-cluster. De cluster-stappen voor installatie en het beheer mogelijk enigszins afwijken van wat wordt weergegeven in dit artikel als u niet met behulp van HDInsight Spark.

> [!NOTE]
> Zie voor een onderwerp waarin wordt uitgelegd hoe u Python in plaats van Scala gebruikt om taken voor een end-to-end Data Science-proces te voltooien, [Gegevenswetenschap met Spark op Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Vereisten
* U hebt een abonnement op Azure nodig. Als u nog geen hebt, [ophalen van een gratis proefversie van Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* U moet een Azure HDInsight 3.4 Spark 1.6-cluster om de volgende procedures te voltooien. Als u een cluster wilt maken, raadpleegt [u de instructies in aan de slag: Maak Apache Spark in azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Instellen van de cluster van het type en de versie op de **clustertype selecteren** menu.

![Configuratie van het HDInsight-cluster](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Zie voor een beschrijving van de NYC taxi reisgegevens en instructies over hoe u code uitvoeren van een Jupyter-notebook in Spark-cluster, de relevante secties in [overzicht van Gegevenswetenschap met Spark op Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Scala code uitvoeren van een Jupyter-notebook in Spark-cluster
U kunt een Jupyter-notebook vanuit de Azure-portal starten. Het Spark-cluster op uw dashboard zoeken en klik erop om in te voeren van de beheerpagina voor uw cluster. Klik vervolgens op **Clusterdashboards**, en klik vervolgens op **Jupyter-Notebook** om het notitieblok die zijn gekoppeld aan het Spark-cluster te openen.

![Cluster-dashboard en Jupyter notebooks](./media/scala-walkthrough/spark-jupyter-on-portal.png)

U kunt Jupyter notebooks op https:// ook openen&lt;clustername&gt;.azurehdinsight.net/jupyter. Vervang *clustername* met de naam van uw cluster. U moet het wachtwoord voor de administrator-account voor toegang tot de Jupyter-notebooks.

![Ga naar de Jupyter-notebooks met behulp van de naam van het cluster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selecteer **Scala** om te zien van een map met een paar voorbeelden van voorverpakte laptops die gebruikmaken van de PySpark-API. Voorbeelden van de exploratie modelleren en scoren met behulp van Scala.ipynb-notebook waarop de code voor deze reeks Spark onderwerpen vindt u op [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

U kunt de notebook rechtstreeks vanuit GitHub met de Jupyter-Notebook-server op uw Spark-cluster uploaden. Klik op uw startpagina Jupyter de **uploaden** knop. In de Verkenner, plak de URL van de GitHub (onbewerkte inhoud) van de notebook Scala, en klik vervolgens op **Open**. De notebook Scala is beschikbaar op de volgende URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Sample Vooraf ingestelde Spark-en Hive-contexten, Spark-magics en Spark-bibliotheken
### <a name="preset-spark-and-hive-contexts"></a>Vooraf ingestelde Spark en Hive-contexten
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


De Spark-kernels die worden geleverd met Jupyter-notebooks hebben vooraf ingestelde contexten. U hoeft niet expliciet in te stellen de Spark- of Hive-contexten voordat u begint met het werken met de toepassing die u ontwikkelt. De vooraf ingestelde contexten zijn:

* `sc` voor SparkContext
* `sqlContext` voor HiveContext

### <a name="spark-magics"></a>Spark-magics
De kernel Spark biedt enkele vooraf gedefinieerde 'magics,' die zijn speciale opdrachten die u met aanroepen kunt `%%`. Twee van deze opdrachten worden gebruikt in de volgende voorbeelden van code.

* `%%local` Hiermee geeft u op dat de code in de volgende regels lokaal wordt uitgevoerd. De code moet geldige Scala-code.
* `%%sql -o <variable name>` voert een Hive-query op `sqlContext`. Als de `-o` parameter is doorgegeven, het resultaat van de query worden bewaard de `%%local` Scala context als een Spark-gegevensframe.

Voor meer informatie over de kernels voor Jupyter notebooks en hun vooraf gedefinieerde "magics" die u aanroept met `%%` (bijvoorbeeld `%%local`), Zie [beschikbare Kernels voor Jupyter-notebooks met HDInsight Spark Linux-clusters op HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Import-bibliotheken
Importeer de Spark MLlib en andere bibliotheken u moet, met behulp van de volgende code.

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
De eerste stap in de Data Science-proces is om op te nemen van de gegevens die u wilt analyseren. U brengt de gegevens uit externe bronnen of systemen waarin deze zich bevindt in de omgeving van uw gegevens verkennen en modelleren. In dit artikel is de gegevens die u een gekoppelde 0,1% voorbeeld van het taxi reis- en tarief-bestand (opgeslagen als een bestand .tsv). De gegevens verkennen en modelleren omgeving is Spark. Deze sectie bevat de code voor het voltooien van de volgende taken uit:

1. Directory-paden voor opslag van gegevens en het model instellen.
2. Lees in de invoerset van de gegevens (opgeslagen als een bestand .tsv).
3. Een schema voor de gegevens definiëren en alleen de gegevens opgeschoond.
4. Een opgeschoonde gegevensframe maken en opslaan in cache deze in het geheugen.
5. De gegevens als een tijdelijke tabel in SQLContext registreren.
6. Query uitvoeren op de tabel en de resultaten in een gegevensframe importeren.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Directory-paden voor opslaglocaties instellen in Azure Blob-opslag
Spark kan lezen en schrijven naar Azure Blob-opslag. U kunt Spark gebruiken voor het verwerken van een van uw bestaande gegevens en bewaart u de resultaten weer in Blob-opslag.

Om op te slaan modellen of de bestanden in Blob-opslag, moet u het pad correct opgeven. Verwijzen naar de standaard-container die zijn gekoppeld aan het Spark-cluster met behulp van een pad dat met begint `wasb:///`. Verwijzen naar andere locaties door `wasb://`.

Het volgende codevoorbeeld geeft de locatie van de ingevoerde gegevens te lezen en het pad naar de Blob-opslag die is gekoppeld aan het Spark-cluster waarin het model moet worden opgeslagen.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Gegevens importeren, maakt u een RDD en een gegevensframe volgens het schema definiëren
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


**De uitvoer:**

Tijd om de cel uit te voeren: 8 seconden.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Query uitvoeren op de tabel en resulteert in een gegevensframe importeren
Vervolgens query uitvoeren op de tabel voor fare, particuliere en tip-gegevens. filteren van gegevens beschadigd en afgelegen; en afdrukken van verschillende rijen.

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

**De uitvoer:**

| fare_amount | passenger_count | tip_amount | punt |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Gegevens verkennen en visualiseren
Nadat u de gegevens in Spark brengt, wordt de volgende stap in de Data Science process is een dieper inzicht te krijgen van de gegevens via het verkennen en visualiseren. In deze sectie kunt u de gegevens over taxi's controleren met behulp van SQL-query's. De resultaten vervolgens importeren in een gegevensframe om de doelvariabelen en toekomstige functies voor visueel inspecteren met behulp van de functie auto-visualisatie van Jupyter tekenen.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Lokale en SQL Magic-pakket gebruiken om te tekenen van gegevens
De uitvoer van elk stukje code die u vanaf een Jupyter-notebook uitvoert is standaard beschikbaar zijn binnen de context van de sessie die is opgeslagen op de worker-knooppunten. Als u wilt opslaan een reis naar de worker-knooppunten voor elke berekening en als alle gegevens die u nodig hebt voor uw berekening lokaal op het serverknooppunt met Jupyter (dit is het hoofdknooppunt) beschikbaar is, kunt u de `%%local` Magic-pakket uit te voeren van het codefragment op de Jupyter de server.

* **SQL magic** (`%%sql`). De kernel HDInsight Spark biedt ondersteuning voor eenvoudige inline HiveQL query's op SQLContext. De (`-o VARIABLE_NAME`) argument als een frame Pandas gegevens op de Jupyter-server de uitvoer van de SQL-query zich blijft voordoen. Dit betekent dat de functie is beschikbaar in de lokale modus.
* `%%local` **Magic**. De `%%local` Magic-pakket voor de code wordt lokaal uitgevoerd op de Jupyter-server, waarop het hoofdknooppunt van het HDInsight-cluster. Meestal gebruikt u `%%local` magic in combinatie met de `%%sql` magic met de `-o` parameter. De `-o` parameter zou de uitvoer van de SQL-query lokaal, behouden en vervolgens `%%local` Magic-pakket voor de volgende set codefragment lokaal uitvoeren op basis van de uitvoer van de SQL-query's die lokaal is opgeslagen wilt activeren.

### <a name="query-the-data-by-using-sql"></a>De gegevens op te vragen met behulp van SQL
Deze query worden de gegevens over taxi's door fare bedrag, aantal van de passagiers en tip bedrag opgehaald.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

In de volgende code wordt de `%%local` Magic-pakket wordt gemaakt van een lokale gegevensframe sqlResults. U kunt sqlResults gebruiken om te tekenen met behulp van matplotlib.

> [!TIP]
> Lokale magic wordt meerdere malen gebruikt in dit artikel. Voorbeeld voor het maken van een gegevensframe die passen in het lokale geheugen als uw gegevensset te groot is, is.
> 
> 

### <a name="plot-the-data"></a>Tekenen van de gegevens
U kunt met behulp van Python-code nadat het dataframe in de lokale context als een gegevensframe Pandas is tekenen.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 De uitvoer (HiveQL) SQL-query's visualiseert de Spark-kernel automatisch nadat u de code uitvoeren. U kunt kiezen uit verschillende soorten visualisaties:

* Tabel
* Cirkel
* Regel
* Onderwerp
* Balk

Dit is de code voor het tekenen van de gegevens:

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


**De uitvoer:**

![Tip bedrag histogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tip bedrag per passagier tellen](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tip bedrag per fare bedrag](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Functies maken en transformeren van functies en klikt u vervolgens voorbereiden van gegevens voor de invoer voor het modelleren van functies
U hebt voor de functies van de structuur op basis van modellen van Spark ML en MLlib, om voor te bereiden doel en functies met behulp van verschillende technieken, zoals het binning, indexeren, een hot-codering en vectorisatie in. Hier volgen de procedures te volgen in deze sectie:

1. Maak een nieuwe functie door **binning** uur verkeer tijd buckets.
2. Van toepassing **indexeren en een hot-codering** categorische functies.
3. **Voorbeeld en splitst de gegevensset** in trainings- en testset delen.
4. **Geef training-variabele en functies**, en vervolgens maken geïndexeerde of één hot gecodeerd trainen en testen invoer gelabelde punt resilient distributed gegevenssets (rdd's) of gegevensframes.
5. Automatisch **categoriseren en functies en -doelen vectorize** moet worden gebruikt als invoer voor machine learning-modellen.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Maakt een nieuwe functie door binning uur in verkeer tijd buckets
Deze code leest u over het maken van een nieuwe functie door binning uur in verkeer tijd buckets en de resulterende gegevensframe in het geheugen in de cache. Waar de rdd's en gegevens frames zo vaak worden gebruikt in cache opslaan leidt tot verbeterde uitvoertijd. U zult daarom rdd's en gegevensframes cache in verschillende stadia in de volgende procedures.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexeren en de categorische functies een hot-codering
Het maken van modellering en functies van MLlib functies met categorische invoergegevens worden geïndexeerd of gecodeerde vóór gebruik nodig hebben te voorspellen. In deze sectie leest u hoe om te indexeren of categorische functies voor invoer in de modelfuncties coderen.

U moet index of uw modellen coderen op verschillende manieren, afhankelijk van het model. Bijvoorbeeld, logistieke en lineaire regressiemodellen vereisen dat een hot-codering. Een functie met drie categorieën kan bijvoorbeeld worden uitgebreid naar drie kolommen van de functie. 0 of 1, afhankelijk van de categorie van een observatie van bevat elke kolom. MLlib biedt de [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) functie voor het coderen van één hot. Een kolom van het label indexen dit coderingsprogramma toegewezen aan een kolom van de binaire vectoren met maximaal één één-waarde. Met deze codering, kunnen de algoritmen die verwacht dat de numerieke waarden functies, zoals logistieke regressie, worden toegepast op categorische functies.

Hier kunt u alleen de vier variabelen om weer te geven voorbeelden zijn tekenreeksen transformeren. U kunt ook andere variabelen, zoals weekdag, vertegenwoordigd door de numerieke waarden als categorische variabelen index.

Gebruik voor indexeren, `StringIndexer()`, en gebruik voor een hot-codering, `OneHotEncoder()` functies van MLlib. Dit is de code om te indexeren en coderen categorische functies:

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


**De uitvoer:**

Tijd om de cel uit te voeren: 4 seconden.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Voorbeeld en de gegevensset in trainings- en testset breuken splitsen
Deze code maakt een willekeurige steekproef van de gegevens (in dit voorbeeld 25%). Hoewel steekproeven niet vereist voor dit voorbeeld vanwege de grootte van de gegevensset is, het artikel wordt beschreven hoe u kunt een steekproef zodat u hoe u deze gebruiken voor uw eigen problemen weet wanneer dat nodig is. Als voorbeelden groot zijn, kan dit aanzienlijke tijd besparen tijdens het trainen van modellen. Splits vervolgens het voorbeeld in een training deel (in dit voorbeeld 75%) en een test deel (25%, in dit voorbeeld) als u wilt gebruiken in de classificatie en regressie modelleren.

Een willekeurig getal (tussen 0 en 1) toevoegen aan elke rij (in een kolom 'rand') die kan worden gebruikt voor het selecteren van kruisvalidatie vouwen dat tijdens de training.

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


**De uitvoer:**

Tijd om de cel uit te voeren: 2 seconden.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Training-variabele en functies, en maak geïndexeerde of een hot-gecodeerd trainings- en testdoeleinden met het label punt rdd's of gegevens frames invoer
In deze sectie bevat de code u hoe u categorische gegevens als een gegevenstype gelabelde punt te indexeren en het coderen ziet, zodat u deze gebruiken kunt om te trainen en testen MLlib logistieke regressie en andere modellen voor classificatie. Gelabelde point-objecten zijn rdd's die zijn geformatteerd op een manier die door de meeste machine learning-algoritmen in MLlib nodig is als invoergegevens. Een [met het label punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, compacte of sparse, die zijn gekoppeld aan een label/antwoord.

In deze code geeft u de doelvariabele (afhankelijke) en de functies te gebruiken met het trainen van modellen. Vervolgens maakt u geïndexeerde of een hot-gecodeerd trainings- en testdoeleinden invoer frames punt rdd's of gegevens met het label.

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


**De uitvoer:**

Tijd om de cel uit te voeren: 4 seconden.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatisch categoriseren en vectorize functies en doelen kunnen gebruiken als invoer voor machine learning-modellen
Gebruik Spark ML voor het categoriseren van het doel en de functies die moeten worden gebruikt in de boomstructuur op basis van modellen functies. De code voltooit twee taken:

* Hiermee maakt u een binaire doel voor classificatie door een waarde van 0 of 1 toewijzen aan elk gegevenspunt tussen 0 en 1 met behulp van een waarde voor drempel van 0,5.
* Automatisch categoriseren functies. Als het aantal afzonderlijke numerieke waarden voor de functies die kleiner is dan 32 is, wordt deze functie wordt gecategoriseerd.

Dit is de code voor deze twee taken.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binair classificatie model: Voor spelt of een tip moet worden betaald
In deze sectie maakt maken u drie soorten binaire classificatie modellen om te voorspellen of een tip moet worden betaald:

* Een **model voor logistieke regressie** met behulp van de Spark ML `LogisticRegression()` functie
* Een **willekeurige forest classificeringsmodel** met behulp van de Spark ML `RandomForestClassifier()` functie
* Een **kleurovergang Gradient boosting structuur classificeringsmodel** met behulp van de MLlib `GradientBoostedTrees()` functie

### <a name="create-a-logistic-regression-model"></a>Een model voor logistieke regressie te maken
Maak vervolgens een model voor logistieke regressie met behulp van de Spark ML `LogisticRegression()` functie. U het model voor het bouwen van code in een reeks stappen hebt gemaakt:

1. **Het model te trainen** gegevens met één parameter is ingesteld.
2. **Het model evalueren** op een verzameling test met metrische gegevens.
3. **Sla het model** in Blob-opslag voor toekomstig gebruik.
4. **Het model beoordelen** tegen testgegevens.
5. **De resultaten tekenen** met ontvanger kenmerk (ROC) curven besturingssysteem.

Dit is de code voor deze procedures:

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

Laden, beoordelen en de resultaten worden opgeslagen.

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


**De uitvoer:**

ROC testgegevens 0.9827381497557599 =

Python gebruiken voor lokale Pandas gegevensframes waarmee de ROC-curve wordt uitgezet.

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


**De uitvoer:**

![Tip of geen tip ROC-curve](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Maken van een willekeurige indeling forestmodel
Maak vervolgens een willekeurige forestmodel classificatie met behulp van de Spark ML `RandomForestClassifier()` functioneren en vervolgens het model op testgegevens evalueren.

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


**De uitvoer:**

ROC testgegevens 0.9847103571552683 =

### <a name="create-a-gbt-classification-model"></a>Een classificatie GBT model maken
Maak vervolgens een classificeringsmodel GBT met behulp van MLlib `GradientBoostedTrees()` functioneren en vervolgens het model op testgegevens evalueren.

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


**De uitvoer:**

Gebied onder ROC-curve: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressie model: Aantal fooien voor spel
In deze sectie maakt maken u twee typen regressiemodellen om te voorspellen van de hoeveelheid tip:

* Een **overgegaan lineair regressiemodel** met behulp van de Spark ML `LinearRegression()` functie. U slaat u het model en het model op testgegevens evalueren.
* Een **gradient boosting-structuur regressiemodel** met behulp van de Spark ML `GBTRegressor()` functie.

### <a name="create-a-regularized-linear-regression-model"></a>Een overgegaan lineair regressiemodel maken
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


**De uitvoer:**

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


**De uitvoer:**

R-sqr testgegevens 0.5960320470835743 =

Vervolgens de resultaten van de query als een gegevensframe en AutoVizWidget en matplotlib gebruiken om deze te visualiseren.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

De code maakt een lokale gegevensframe van de query-uitvoer en de gegevens die zichtbaar zijn. De `%%local` Magic-pakket wordt gemaakt van een lokale gegevensframe `sqlResults`, die u kunt gebruiken om te tekenen met matplotlib.

> [!NOTE]
> Deze magic Spark wordt meerdere malen gebruikt in dit artikel. Als de hoeveelheid gegevens te groot is, moet u een steekproef voor het maken van een gegevensframe die passen in het lokale geheugen.
> 
> 

Grafieken maken met behulp van Python matplotlib.

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

**De uitvoer:**

![Aantal fooien: Werkelijk versus voor speld](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Een regressiemodel GBT maken
Een regressiemodel GBT maken met behulp van de Spark ML `GBTRegressor()` functioneren en vervolgens het model op testgegevens evalueren.

[Kleurovergang boosted structuren](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) ensembles van beslissingsstructuren zijn. GBTs trainen beslisbomen iteratief te minimaliseren, een functie verloren gaan. U kunt GBTs gebruiken voor regressie en classificatie. Ze categorische functies kunnen worden verwerkt, vereisen geen functie schaalaanpassing en nonlinearities en functie interacties kunnen vastleggen. Ook kunt u ze in een multiklasse-classificatie.

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


**De uitvoer:**

Test-R-Sqr is: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Hulpprogramma's voor geavanceerde modellen voor optimalisatie
In deze sectie gebruikt u machine learning-hulpprogramma's die ontwikkelaars wordt vaak voor de optimalisatie van het model gebruikt. Specifiek, kunt u drie verschillende manieren voor machine learning-modellen met behulp van parameter sweeping en kruisvalidatie optimaliseren:

* De gegevens splitsen in train en validatie aan de sets, het model optimaliseren met behulp van hyper-parameter sweeping op een trainingsset en evalueren in een set validatie (lineaire regressie)
* Het model optimaliseren met behulp van kruisvalidatie en hyper-parameter verstrekkende met behulp van Spark ML CrossValidator functie (binaire classificatie)
* Het model optimaliseren door het gebruik van aangepaste code van kruisvalidatie en de parameter-sweeping een machine learning-functie en de parameter set (lineaire regressie)

**Kruisvalidatie** is een techniek die beoordeelt hoe goed een model is getraind op een bekende set gegevens wordt generalize om te voorspellen van de functies van data-sets waarop deze is niet getraind. Algemeen idee achter deze techniek is dat een model wordt getraind op een verzameling van bekende gegevens, en vervolgens de nauwkeurigheid van de voorspellingen is getest op basis van een onafhankelijke set van gegevens. Een veelvoorkomende implementatie is het verdelen van een gegevensset in *k*-vouwen en vervolgens het model in een round robin besturingsaanvraag op alle maar een van de vouwen te trainen.

**Optimalisatie van de Hyper-parameter** is het probleem van het kiezen van een set van hyper-parameters voor een leeralgoritme gewoonlijk met het doel van een maateenheid voor prestaties op een onafhankelijke set gegevens van de algoritme te optimaliseren. Een hyper-parameter is een waarde die u buiten de training model procedure moet opgeven. Veronderstellingen over hyper-parameterwaarden kunnen invloed hebben op de flexibiliteit en de nauwkeurigheid van het model. Beslisbomen hebben bijvoorbeeld hyper-parameters, zoals de gewenste omvang en aantal bladeren in de structuur. U moet een misclassification boete term voor een support vector machine (SVM) instellen.

Een veelgebruikte manier voor het uitvoeren van hyper-parameter optimalisatie is met een zoekopdracht raster, ook wel een **parameteropschoning**. In een zoekopdracht grid, wordt een intensieve zoekactie uitgevoerd via de waarden van een opgegeven subset van de hyper-parameter ruimte voor een leeralgoritme. Kruisvalidatie kan een metrische gegevens voor prestaties voor het sorteren van de optimale resultaten die worden geproduceerd door de algoritme van de zoekopdracht grid leveren. Als u kruisvalidatie hyper-parameter sweeping gebruikt, kunt u helpen problemen, zoals overfitting van een model te trainen van gegevens beperken. Op deze manier de capaciteit toe te passen op de algemene set gegevens waaruit de trainingsgegevens is geëxtraheerd wordt bewaard, het model.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Een lineair regressiemodel met hyper-parameter sweeping optimaliseren
Vervolgens gegevens splitsen in train en validatie aan de sets, gebruikt hyper-parameter verstrekkende op een training is ingesteld op het model te optimaliseren en evalueren in een set validatie (lineaire regressie).

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


**De uitvoer:**

Test-R-Sqr is: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>De binaire classificeringsmodel optimaliseren met behulp van kruisvalidatie en hyper-parameter sweeping
Deze sectie leest u hoe u kunt een binair classificeringsmodel optimaliseren met behulp van kruisvalidatie en hyper-parameter sweeping. Dit maakt gebruik van de Spark ML `CrossValidator` functie.

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


**De uitvoer:**

Tijd om de cel uit te voeren: 33 seconden.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Het lineaire regressiemodel optimaliseren met behulp van aangepaste code van kruisvalidatie en de parameter-sweeping
Vervolgens het model optimaliseren met behulp van aangepaste code en identificeren van de Modelparameters van het beste met behulp van het criterium van de hoogste nauwkeurigheid. Vervolgens maakt u het uiteindelijke model, het model op testgegevens evalueren en slaat u het model in Blob-opslag. Ten slotte laden van het model, testgegevens te beoordelen en evalueren van nauwkeurigheid.

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


**De uitvoer:**

Tijd om de cel uit te voeren: 61 seconden.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Gebruiken met Spark gebouwde machine learning-modellen automatisch met Scala
Zie voor een overzicht van onderwerpen waarin wordt uitgelegd dat u de taken die deel uitmaken van de Data Science process in Azure, [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Team Data Science Process walkthroughs](walkthroughs.md) andere end-to-end-scenario's die laten zien van de stappen in het Team Data Science Process voor specifieke scenario's worden beschreven. De scenario's laten ook zien hoe u cloud en on-premises hulpprogramma's en services combineren in een werkstroom of een pijplijn te maken van een intelligente toepassingen.

[Met Spark gebouwde machine learning-modellen te beoordelen](spark-model-consumption.md) ziet u hoe u Scala code gebruikt om automatisch te laden en scoren van nieuwe gegevenssets met machine learning-modellen in Spark ingebouwd en opgeslagen in Azure Blob-opslag. U kunt Volg de instructies en vervangt u gewoon de Python-code door Scala-code in dit artikel voor geautomatiseerde verbruik.

