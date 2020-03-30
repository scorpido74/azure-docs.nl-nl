---
title: Spark-built machine learning-modellen operationaliseren - Team Data Science Process
description: Leermodellen laden en scoren die zijn opgeslagen in Azure Blob Storage (WASB) met Python.
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
ms.openlocfilehash: 3f02690d7c54581ed80b521e8222d1bd5964c878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718545"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Spark-gebouwde machine learning-modellen operationaliseren

In dit onderwerp wordt uitgelegd hoe u een opgeslagen machine learning-model (ML) operationaliseren met behulp van Python op HDInsight Spark-clusters. Het beschrijft hoe machine learning-modellen die zijn gebouwd met Spark MLlib en zijn opgeslagen in Azure Blob Storage (WASB), en hoe u ze scoren met gegevenssets die ook zijn opgeslagen in WASB. Het laat zien hoe u de invoergegevens vooraf verwerkt, functies transformeren met behulp van de indexerings- en coderingsfuncties in de MLlib-toolkit en hoe u een gelabeld puntgegevensobject maken dat kan worden gebruikt als invoer voor het scoren met de ML-modellen. De modellen die worden gebruikt voor het scoren zijn lineaire regressie, logistieke regressie, random forestmodellen en gradient boosting tree-modellen.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-clusters en Jupyter-notitieblokken
In deze walkthrough worden installatiestappen en de code voor het operationaliseren van een ML-model geleverd voor het gebruik van een HDInsight Spark 1.6-cluster en een Spark 2.0-cluster. De code voor deze procedures is ook opgenomen in Jupyter notebooks.

### <a name="notebook-for-spark-16"></a>Notitieblok voor Spark 1.6
De [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook laat zien hoe je een opgeslagen model operationaliseren met Python op HDInsight-clusters. 

### <a name="notebook-for-spark-20"></a>Notitieblok voor Spark 2.0
Als u het Jupyter-notitieblok voor Spark 1.6 wilt wijzigen om te gebruiken met een HDInsight Spark 2.0-cluster, vervangt u het Python-codebestand door [dit bestand](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Deze code laat zien hoe u de modellen gebruikt die zijn gemaakt in Spark 2.0.


## <a name="prerequisites"></a>Vereisten

1. U hebt een Azure-account en een Spark 1.6 (of Spark 2.0) HDInsight-cluster nodig om deze walkthrough te voltooien. Zie het [overzicht van Data Science met Spark op Azure HDInsight](spark-overview.md) voor instructies over hoe u aan deze vereisten voldoen. Dat onderwerp bevat ook een beschrijving van de NYC 2013 Taxi gegevens hier gebruikt en instructies over hoe code uit te voeren van een Jupyter notebook op de Spark cluster. 
2. Maak de machine learning-modellen die hier moeten worden gescoord door het [gegevensverkennings- en modelleringsonderwerp met spark-onderwerp](spark-data-exploration-modeling.md) voor het Spark 1.6-cluster of de Spark 2.0-notitieblokken te doorlopen. 
3. De Spark 2.0-notitieblokken gebruiken een extra gegevensset voor de classificatietaak, de bekende luchtvaartgegevensset voor vertrek van de luchtvaartmaatschappij uit 2011 en 2012. Een beschrijving van de notitieblokken en links naar hen zijn voorzien in de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub repository met hen. Bovendien is de code hier en in de gekoppelde notitieblokken generiek en moet het werken op een Spark-cluster. Als u HDInsight Spark niet gebruikt, kunnen de clusterinstellingen en beheerstappen enigszins afwijken van wat hier wordt weergegeven. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: opslaglocaties, bibliotheken en de vooraf ingestelde Spark-context
Spark kan lezen en schrijven naar een Azure Storage Blob (WASB). Dus al uw bestaande gegevens die daar zijn opgeslagen, kunnen worden verwerkt met behulp van Spark en de resultaten worden opnieuw opgeslagen in WASB.

Om modellen of bestanden op te slaan in WASB, moet het pad correct worden opgegeven. De standaardcontainer die aan het Spark-cluster is gekoppeld, kan worden verwezen met behulp van een pad dat begint met: *"wasb//"*. In het volgende codevoorbeeld wordt de locatie opgegeven van de te lezen gegevens en het pad voor de modelopslagmap waarop de modeluitvoer wordt opgeslagen. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Adreslijstpaden instellen voor opslaglocaties in WASB
Modellen worden opgeslagen in: "wasb:///user/remoteuser/NYCTaxi/Models". Als dit pad niet goed is ingesteld, worden modellen niet geladen om te scoren.

De gescoorde resultaten zijn opgeslagen in: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Als het pad naar de map onjuist is, worden de resultaten niet opgeslagen in die map.   

> [!NOTE]
> De locaties van het bestandspad kunnen worden gekopieerd en geplakt op de tijdelijke aanduidingen in deze code uit de uitvoer van de laatste cel van het **machine-learning-data-science-spark-data-exploration-modeling.ipynb-notitieblok.**   
> 
> 

Hier is de code om mappaden in te stellen: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**Output:**

datum.datumtijd(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Bibliotheken importeren
Spark-context instellen en noodzakelijke bibliotheken importeren met de volgende code

    #IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Vooraf ingestelde Spark-context en PySpark-magie
De PySpark-kernels die zijn voorzien van Jupyter-notebooks hebben een vooraf ingestelde context. Daarom hoeft u de Spark- of Hive-contexten niet expliciet in te stellen voordat u aan de slag gaat met de toepassing die u ontwikkelt. Deze contexten zijn standaard beschikbaar:

* sc - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt een aantal vooraf gedefinieerde magics, die speciale opdrachten zijn die je aanroepen met %%. Er zijn twee van dergelijke commando's die worden gebruikt in deze code monsters.

* **%%lokaal** Opgegeven dat de code in volgende regels lokaal wordt uitgevoerd. Code moet een geldige Python-code zijn.
* **%%sql -o \<variabele naam>** 
* Hiermee wordt een Hive-query uitgevoerd tegen de sqlContext. Als de parameter -o wordt doorgegeven, blijft het resultaat van de query bestaan in de context %%local Python als een Pandas-gegevensframe.

Voor meer informatie over de kernels voor Jupyter notebooks en de vooraf gedefinieerde "magics" die ze bieden, zie [Kernels beschikbaar voor Jupyter notebooks met HDInsight Spark Linux clusters op HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Gegevens innemen en een opgeschoond gegevensframe maken
Deze sectie bevat de code voor een reeks taken die nodig zijn om de te scoren gegevens in te nemen. Lees in een samengevoegde 0,1% voorbeeld van de taxirit en het tariefbestand (opgeslagen als een .tsv-bestand), maak de gegevens op en maakt vervolgens een schoon gegevensframe.

De taxirit en tariefbestanden werden samengevoegd op basis van de procedure in het: [The Team Data Science Process in actie: met behulp van HDInsight Hadoop clusters](hive-walkthrough.md) onderwerp.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 46,37 seconden

## <a name="prepare-data-for-scoring-in-spark"></a>Gegevens voorbereiden op scoren in Spark
In deze sectie ziet u hoe u categorische functies indexeren, coderen en schalen om ze voor te bereiden op gebruik in mllib-algoritmen onder toezicht voor classificatie en regressie.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Functietransformatie: indexeren en coderen categorische functies voor invoer in modellen om te scoren
In deze sectie ziet u `StringIndexer` hoe u categorische gegevens indexeren met behulp van een en functies coderen met `OneHotEncoder` invoer in de modellen.

De [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) codeert een tekenreekskolom met labels tot een kolom met labelindexen. De indexen worden besteld op labelfrequenties. 

De [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) brengt een kolom met labelindexen in kaart met een kolom van binaire vectoren, met maximaal één waarde. Met deze codering kunnen algoritmen die verwachten dat continue gewaardeerde functies, zoals logistieke regressie, worden toegepast op categorische functies.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 5,37 seconden

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Rdd-objecten maken met functiearrays voor invoer in modellen
Deze sectie bevat code die laat zien hoe u categorische tekstgegevens indexeren als een RDD-object en deze met één heet wordt coderen, zodat het kan worden gebruikt om mllib-logistieke regressie- en boomgebaseerde modellen te trainen en te testen. De geïndexeerde gegevens worden opgeslagen in [RDD-objecten (Resilient Distributed Dataset).](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) De RDD's zijn de basisabstractie in Spark. Een RDD-object vertegenwoordigt een onveranderlijke, verdeelde verzameling elementen die parallel met Spark kunnen worden gebruikt.

Het bevat ook code die laat `StandardScalar` zien hoe gegevens te schalen met de door MLlib voor gebruik in lineaire regressie met Stochastic Gradient Descent (SGD), een populair algoritme voor het trainen van een breed scala van machine learning-modellen. De [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) wordt gebruikt om de functies te schalen naar variantie van de eenheid. Feature scaling, ook wel bekend als data normalisatie, verzekert dat functies met op grote schaal uitbetaald waarden worden niet gegeven overmatige wegen in de objectieve functie. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 11,72 seconden

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Score met het Logistieke Regressiemodel en sla uitvoer op in blob
De code in deze sectie laat zien hoe u een logistiek regressiemodel laadt dat is opgeslagen in Azure blob-opslag en deze gebruikt om te voorspellen of een fooi wordt betaald op een taxirit, deze te scoren met standaardclassificatiestatistieken en vervolgens de resultaten op te slaan en te plotten in blob-opslag . De gescoorde resultaten worden opgeslagen in RDD-objecten. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 19,22 seconden

## <a name="score-a-linear-regression-model"></a>Een lineair regressiemodel scoren
We gebruikten [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) om een lineair regressiemodel te trainen met stochastic Gradient Descent (SGD) voor optimalisatie om de hoeveelheid betaalde fooi te voorspellen. 

De code in deze sectie laat zien hoe u een lineair regressiemodel laadt vanuit Azure blob-opslag, scoort met geschaalde variabelen en vervolgens de resultaten opslaat in de blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output:**

Tijd die nodig is om boven de cel uit te voeren: 16,63 seconden

## <a name="score-classification-and-regression-random-forest-models"></a>Score classificatie en regressie Random Forest Models
De code in deze sectie laat zien hoe u de opgeslagen classificatie en regressie random forestmodellen laadt die zijn opgeslagen in Azure blob-opslag, hun prestaties scoren met standaardclassificatie- en regressiemetingen en vervolgens de resultaten opslaan in blobopslag.

[Willekeurige bossen](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslisbomen.  Ze combineren veel beslisbomen om het risico op overfitting te verminderen. Willekeurige forests kunnen categorische functies verwerken, zich uitbreiden tot de classificatie-instelling van meerdere klassen, vereisen geen functieschaling en kunnen niet-lineariteiten en functieinteracties vastleggen. Willekeurige bossen zijn een van de meest succesvolle machine learning-modellen voor classificatie en regressie.

[spark.mllib](https://spark.apache.org/mllib/) ondersteunt willekeurige forests voor binaire en meerklassenclassificatie en voor regressie, met behulp van zowel continue als categorische functies. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 31,07 seconden

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Scoreclassificatie en regressie Gradient Boosting Tree Models
De code in deze sectie laat zien hoe classificatie en regressie Gradiënt boosten de structuurmodellen uit Azure blob-opslag laden, hun prestaties scoren met standaardclassificatie- en regressiemetingen en vervolgens de resultaten opslaan in blobopslag. 

**spark.mllib** ondersteunt GBTS voor binaire classificatie en voor regressie, met behulp van zowel continue als categorische functies. 

[Gradient Boosting Trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) zijn ensembles van beslisbomen. GBTS traint beslissingsbomen iteratief om een verliesfunctie te minimaliseren. GBTS kan categorische functies verwerken, vereisen geen functieschaling en kan niet-lineariteiten en functieinteracties vastleggen. Dit algoritme kan ook worden gebruikt in een multiclass-classificatie-instelling.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 14,6 seconden

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Objecten opschonen uit geheugen en op gescoorde bestandslocaties afdrukken
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**Output:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark-modellen gebruiken via een webinterface
Spark biedt een mechanisme om batchtaken of interactieve query's op afstand in te dienen via een REST-interface met een component genaamd Livy. Livy is standaard ingeschakeld op uw HDInsight Spark-cluster. Voor meer informatie over Livy, zie: [Stuur Spark vacatures op afstand met behulp van Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

U Livy gebruiken om op afstand een taak in te dienen waarin batch een bestand scoort dat is opgeslagen in een Azure-blob en vervolgens de resultaten naar een andere blob schrijft. Hiervoor upload je het Python-script  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) naar de blob van het Spark-cluster. U een hulpprogramma zoals **Microsoft Azure Storage Explorer** of **AzCopy** gebruiken om het script naar de clusterblob te kopiëren. In ons geval hebben we het script geüpload naar ***wasb:///example/python/ConsumeGBNYCReg.py.***   

> [!NOTE]
> De toegangssleutels die u nodig hebt, zijn te vinden op de portal voor het opslagaccount dat is gekoppeld aan het Spark-cluster. 
> 
> 

Eenmaal geüpload naar deze locatie, wordt dit script uitgevoerd binnen het Spark-cluster in een gedistribueerde context. Het laadt het model en voert voorspellingen uit op invoerbestanden op basis van het model.  

U dit script op afstand aanroepen door een eenvoudig HTTPS/REST-verzoek in te dienen op Livy.  Hier is een opdracht krul om het HTTP-verzoek te construeren om het Python-script op afstand aan te roepen. Vervang CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME door de juiste waarden voor uw Spark-cluster.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

U elke taal op het externe systeem gebruiken om de Spark-taak via Livy aan te roepen door een eenvoudig HTTPS-gesprek te voeren met Basisverificatie.   

> [!NOTE]
> Het zou handig zijn om de Python Requests-bibliotheek te gebruiken bij het maken van deze HTTP-oproep, maar deze is momenteel niet standaard geïnstalleerd in Azure-functies. Dus oudere HTTP-bibliotheken worden in plaats daarvan gebruikt.   
> 
> 

Hier is de Python-code voor de HTTP-oproep:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


U deze Python-code ook toevoegen aan [Azure-functies](https://azure.microsoft.com/documentation/services/functions/) om een Spark-taakindiening te activeren die een blob scoort op basis van verschillende gebeurtenissen zoals een timer, creatie of update van een blob. 

Als u de voorkeur geeft aan een codevrije clientervaring, gebruikt u de [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) om de Spark-batchscore aan te roepen door een HTTP-actie op de Logic Apps **Designer** te definiëren en de parameters in te stellen. 

* Maak vanuit Azure-portal een nieuwe Logische App door **+New** -> **Web + Mobile** -> **Logic App te**selecteren. 
* Als u de **Logic Apps Designer wilt**weergeven, voert u de naam van het Logic App- en App-serviceplan in.
* Selecteer een HTTP-actie en voer de parameters in de volgende afbeelding in:

![Logic Apps Designer](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Volgende stappen
**Cross-validatie en hyperparameter vegen:** Zie [Geavanceerde gegevens exploratie en modellering met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met behulp van cross-validatie en hyper-parameter vegen.

