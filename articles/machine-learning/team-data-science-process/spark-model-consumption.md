---
title: Met Spark gebouwde machine learning-modellen - Team Data Science Process
description: Het laden en die zijn opgeslagen in Azure Blob Storage (WASB) met Python learning-modellen te beoordelen.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718545"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Met Spark gebouwde machine learning-modellen

In dit onderwerp laat zien hoe om te zetten van een opgeslagen machine learning-model (ML) met behulp van Python op HDInsight Spark-clusters. Het wordt beschreven hoe u machine learning-modellen die zijn gebouwd met behulp van Spark MLlib laden en opgeslagen in Azure Blob Storage (WASB), en hoe ze met gegevenssets die ook zijn opgeslagen in WASB te beoordelen. Het laat zien hoe u de ingevoerde gegevens vooraf te verwerken, transformeren met behulp van de functies indexering en codering in de werkset MLlib functies, en over het maken van een object voor gelabelde punt die kan worden gebruikt als invoer voor het scoren met het ML-modellen. De modellen die worden gebruikt voor het scoren bevatten lineaire regressie, logistieke regressie, willekeurige Forest-modellen en kleurovergang versterking structuur modellen.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-clusters en Jupyter notebooks
Installatiestappen uit en de code voor het operationeel maken van een ML-model zijn opgegeven in dit scenario voor het gebruik van een HDInsight Spark 1.6-cluster, evenals een cluster van Spark 2.0. De code voor deze procedures is ook beschikbaar in Jupyter-notebooks.

### <a name="notebook-for-spark-16"></a>Laptop voor Spark 1.6
De [pySpark-machine learning-data-Science-Spark-ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter notebook laat zien hoe u een opgeslagen model kunt operationeel maken met behulp van python op HDInsight-clusters. 

### <a name="notebook-for-spark-20"></a>Laptop voor Spark 2.0
Als u de Jupyter-notebook voor Spark 1,6 wilt wijzigen voor gebruik met een HDInsight Spark 2,0-cluster, vervangt u het python-code bestand door [dit bestand](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Deze code laat zien hoe de modellen die zijn gemaakt in Spark 2.0 gebruiken.


## <a name="prerequisites"></a>Vereisten

1. U moet een Azure-account en een Spark 1.6 (of Spark 2.0) HDInsight-cluster voor dit scenario. Bekijk het [overzicht van data Science met behulp van Spark in azure HDInsight](spark-overview.md) voor instructies over het voldoen aan deze vereisten. Dit onderwerp bevat ook een beschrijving van de hier gebruikte NYC 2013 Taxi-gegevens en instructies over hoe u code uitvoeren van een Jupyter-notebook in Spark-cluster. 
2. Maak de machine learning modellen die u hier kunt beoordeelt door de [gegevens te verkennen en te model leren met het Spark](spark-data-exploration-modeling.md) -onderwerp voor het Spark 1,6-cluster of de Spark 2,0-notebooks. 
3. De Spark 2.0-notebooks gebruiken een aanvullende gegevensset voor de classificatietaak, de bekende gegevensset op tijd vertrek luchtvaartmaatschappij in 2011 en 2012. Er wordt een beschrijving van de notitie blokken en koppelingen naar deze notebooks gegeven in de [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslag plaats waarin deze zijn opgenomen. Bovendien is de code hier en in de gekoppelde notebooks is algemeen en werkt op een Spark-cluster. Als u niet met behulp van HDInsight Spark, configureren van het cluster en beheertaken uit kunnen enigszins afwijken van wat wordt hier weergegeven. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: opslaglocaties, bibliotheken en de vooraf ingestelde Spark-context
Spark kan lezen en schrijven naar een Azure Storage-Blob (WASB). Dus een van uw bestaande gegevens die zijn opgeslagen kunnen er worden verwerkt met behulp van Spark en de resultaten weer in WASB worden opgeslagen.

Om op te slaan modellen of de bestanden in WASB, moet het pad correct worden opgegeven. Er kan naar de standaard container die aan het Spark-cluster is gekoppeld, een pad worden gebruikt dat begint met: *' wasb///'* . Het volgende codevoorbeeld geeft de locatie van de gegevens te lezen en het pad voor de model-opslag-map waarin de uitvoer van het model is opgeslagen. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Directory-paden voor opslaglocaties in WASB instellen
Modellen worden opgeslagen in: "wasb: / / / gebruiker/remoteuser/NYCTaxi/modellen '. Als dit pad is niet correct ingesteld, worden de modellen niet voor het scoren van geladen.

De beoordeelde resultaten zijn opgeslagen in: "wasb: / / / gebruiker/remoteuser/NYCTaxi/ScoredResults '. Als het pad naar map onjuist is, zijn geen resultaten opgeslagen in die map.   

> [!NOTE]
> De locatie van het bestandspad kan worden gekopieerd en in de tijdelijke aanduidingen in deze code worden geplakt vanuit de uitvoer van de laatste cel van de **machine learning-data-Science-Spark-data-Explore-Modeling. ipynb** notebook.   
> 
> 

Dit is de code voor het instellen van directory-paden: 

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

**UITVOER**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Import-bibliotheken
Spark-context instellen en importeren van de vereiste bibliotheken door de volgende code

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Vooraf ingestelde Spark-context en PySpark magics
De PySpark-kernels die worden geleverd met Jupyter-notebooks hebben een vooraf ingestelde context. Daarom hoeft u de Spark-of Hive-contexten expliciet niet in te stellen voordat u begint te werken met de toepassing die u ontwikkelt. Deze contexten zijn standaard beschikbaar:

* SC - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt enkele vooraf gedefinieerde "kunt", die zijn speciale opdrachten die u met aanroepen kunt %%. Er zijn twee dergelijke opdrachten die worden gebruikt in deze voorbeelden van code.

* **%% Local** Opgegeven dat de code in volgende regels lokaal wordt uitgevoerd. Code moet geldige Python-code.
* **%% SQL-o \<naam van variabele >** 
* Voert een Hive-query op de sqlContext. Als de parameter -o wordt doorgegeven, het resultaat van de query worden bewaard de %% lokale Python-context als een Pandas dataframe.

Zie [kernels die beschikbaar zijn voor Jupyter-notebooks met Hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)voor meer informatie over de kernels voor Jupyter-notebooks en de vooraf gedefinieerde ' magics ' die ze bieden.

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Opname van gegevens en een opgeschoonde gegevensframe maken
In deze sectie bevat de code voor een reeks taken die vereist zijn voor opname van de gegevens die moeten worden beoordeeld. Lees in een gekoppelde 0,1% voorbeeld van het taxi reis- en tarief-bestand (opgeslagen als een bestand .tsv) indeling de gegevens en maakt vervolgens een schone gegevensframe.

De taxi-en ritbedrag bestanden zijn gekoppeld aan de hand van de procedure in het [team data Science process in actie: het onderwerp HDInsight Hadoop-clusters gebruiken](hive-walkthrough.md) .

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 46.37 seconden

## <a name="prepare-data-for-scoring-in-spark"></a>Gegevens voorbereiden voor het scoren in Spark
Deze sectie wordt beschreven hoe u indexeren, coderen en schalen van categorische functies voor te bereiden voor gebruik in MLlib onder supervisie learning-algoritmen voor de classificatie- en regressiemodellen.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Functie transformatie: te indexeren en categorische functies voor invoer in modellen voor het scoren coderen
In deze sectie wordt beschreven hoe u categorische gegevens indexeert met behulp van een `StringIndexer` en hoe u functies codeert met `OneHotEncoder` invoer in de modellen.

De [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) codeert een teken reeks kolom van labels naar een kolom met label indexen. De indexen zijn geordend op label frequenties. 

De [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) wijst een kolom met label indexen toe aan een kolom met binaire vectoren, met Maxi maal één waarde. Deze codering kunt algoritmen die verwacht dat de continue belangrijke functies, zoals logistieke regressie, moet worden toegepast op categorische functies.

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 5,37 seconden

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>RDD-objecten met een functie-matrices voor invoer in modellen maken
In deze sectie bevat de code die laat hoe u categorische gegevens als een object RDD index en deze een hot coderen zien, zodat deze kan worden gebruikt om te trainen en testen MLlib logistieke regressie en modellen op basis van een structuur. De geïndexeerde gegevens worden opgeslagen in [rdd-objecten (robuuste gedistribueerde gegevensset)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . De Rdd's zijn de basis abstractie in Spark. Een RDD-object vertegenwoordigt een onveranderbare, gepartitioneerde verzameling van elementen die kunnen worden uitgevoerd op in combinatie met Spark.

Het bevat ook code die laat zien hoe u gegevens kunt schalen met de `StandardScalar` van MLlib voor gebruik in lineaire regressie met stochastische Gradient Daal (SGD), een populair algoritme voor het trainen van een breed scala aan machine learning modellen. De [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) wordt gebruikt om de functies te schalen op eenheids afwijking. Functie schalen, ook wel bekend als de gegevens normaliseren, weet u zeker dat functies met veel betaald waarden zijn niet opgegeven overmatige wegen de servicedoelstelling functie. 

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 11.72 seconden

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Met het logistieke regressiemodel te beoordelen en uitvoer naar de blob opslaan
De code in deze sectie laat zien hoe u een Model voor logistieke regressie dat is opgeslagen in Azure blob-opslag laden en deze gebruiken om te voorspellen of een tip wordt betaald op een reis taxi, deze score met metrische gegevens over een standaard classificatie, en vervolgens opslaan en de resultaten naar de blobopslag stora tekenen ge. De beoordeelde resultaten worden opgeslagen in RDD-objecten. 

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 19.22 seconden

## <a name="score-a-linear-regression-model"></a>Een lineair regressiemodel score
We hebben [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) gebruikt om een lineair regressie model te trainen met de stochastische Gradient DAAL (SGD) voor Optima Lise ring om de hoeveelheid fooien te voors pellen. 

De code in deze sectie toont hoe u een lineair regressiemodel laden uit Azure blob-opslag, score met behulp van de variabelen voor de schaal en sla vervolgens de resultaten terug naar de blob.

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


**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 16.63 seconden

## <a name="score-classification-and-regression-random-forest-models"></a>Classificatie- en regressiemodellen willekeurige Forest-modellen te beoordelen
De code in deze sectie wordt uitgelegd hoe de opgeslagen classificatie worden geladen en Regressiemodellen willekeurige Forest die zijn opgeslagen in Azure blob-opslag, hun prestaties met een standard-classificatie en regressie maatregelen te beoordelen en de resultaten vervolgens terug naar de blob-opslag opslaan.

[Wille keurige forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslissings structuren.  Ze combineren veel beslissingsstructuren om het risico te beperken. Categorische functies kunnen worden verwerkt door willekeurige forests uitbreiden naar de instelling voor multiklassen classificatie, vereisen geen functie schaalaanpassing en kunnen niet-mogelijkheid tot vastleggen en functie van interacties. Willekeurige forests vormen een van de meest succesvolle machine learning-modellen voor classificatie- en regressiemodellen.

[Spark. mllib](https://spark.apache.org/mllib/) ondersteunt wille keurige forests voor binaire en multiklasse-classificatie en voor regressie, met behulp van doorlopende en categorische-functies. 

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 31.07 seconden

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Classificatie- en regressiemodellen kleurovergang versterking structuur-modellen te beoordelen
De code in deze sectie wordt beschreven hoe classificatie- en regressiemodellen kleurovergang versterking structuur Modellen laden vanuit Azure blob storage, hun prestaties met een standard-classificatie en regressie maatregelen te beoordelen en de resultaten vervolgens terug naar de blob-opslag opslaan. 

**Spark. mllib** ondersteunt GBTS voor binaire classificatie en voor regressie, met behulp van doorlopende en categorische-functies. 

GBTS ( [Gradient Boosting trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) zijn ensembles van beslissings structuren. GBTSer boom structuren van de trein om een verlies functie te minimaliseren. GBTS kan categorische-functies afhandelen, geen functie schaaling vereisen en niet-lineaire en functie-interacties vastleggen. Dit algoritme kan ook worden gebruikt in een instelling met een classificatie met een hoge klasse.

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 14.6 seconden

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Opschonen van de objecten uit het geheugen en beoordeelde bestandslocaties afdrukken
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


**UITVOER**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spark-modellen gebruiken via een webinterface.
Spark biedt een mechanisme om in te dienen op afstand batch-taken of interactieve query's via een REST-interface met een component, genaamd Livy. Livy is standaard ingeschakeld op uw HDInsight Spark-cluster. Zie voor meer informatie over livy: [Spark-taken extern verzenden met behulp van livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

U kunt Livy gebruiken om in te dienen op afstand een taak die door de batch scores een bestand dat is opgeslagen in een Azure-blob en vervolgens schrijft de resultaten naar een andere blob. U doet dit door uploaden u de Python-script uit  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) naar de blob van het Spark-cluster. U kunt een hulp programma als **Microsoft Azure Storage Explorer** of **AzCopy** gebruiken om het script naar de cluster-BLOB te kopiëren. In ons geval hebben we het script geüpload naar ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> De toegangssleutel die u nodig hebt vindt u in de portal voor het opslagaccount dat is gekoppeld aan het Spark-cluster. 
> 
> 

Na het uploaden naar deze locatie, is dit script wordt uitgevoerd binnen het Spark-cluster in een gedistribueerde context. Het model geladen en voorspellingen op invoerbestanden op basis van het model wordt uitgevoerd.  

U kunt dit script extern aanroepen door een eenvoudige HTTPS/REST-aanvraag van Livy.  Hier volgt een curl-opdracht om de HTTP-aanvraag om aan te roepen van het Python-script op afstand samen te stellen. CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME vervangen door de juiste waarden voor uw Spark-cluster.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

U kunt elke taal op het externe systeem gebruiken om aan te roepen van het Spark-taak via Livy door het maken van een simpele aanroep van HTTPS met basisverificatie.   

> [!NOTE]
> Het normaal zou zijn handig is dat de aanvragen van de Python-clientbibliotheek gebruiken bij het maken van deze HTTP-aanroep, maar deze momenteel niet standaard ingeschakeld in Azure Functions is geïnstalleerd. Zodat oudere HTTP-bibliotheken in plaats daarvan worden gebruikt.   
> 
> 

Hier volgt de Python-code voor de HTTP-aanroep:

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


U kunt deze python-code ook toevoegen aan [Azure functions](https://azure.microsoft.com/documentation/services/functions/) om een Spark-taak inzending te activeren die een BLOB verstuurt op basis van verschillende gebeurtenissen, zoals een timer, het maken of bijwerken van een blob. 

Als u de voor keur geeft aan een gratis client ervaring, gebruikt u de [Azure Logic apps](https://azure.microsoft.com/documentation/services/app-service/logic/) om de Spark batch score te roepen door een http-actie te definiëren op de **Logic apps Designer** en de para meters in te stellen. 

* Maak vanuit Azure Portal een nieuwe logische app door **+ new** -> **Web en mobiel** -> **Logic app**te selecteren. 
* Als u de **Logic apps Designer**wilt weer geven, voert u de naam van de logische app in en app service plan.
* Selecteer een HTTP-actie en voer de parameters weergegeven in de volgende afbeelding:

![Ontwerper van logische apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Volgende stappen
**Kruis validatie en afstemming sweep**: Zie [geavanceerde gegevens exploratie en model leren met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met kruis validatie en Hyper-para meter sweep.

