---
title: Gegevens exploratie en-modellering met Spark-team data Science process
description: Geeft een overzicht van de mogelijkheden voor gegevens exploratie en-modellering van de Spark MLlib Toolkit op HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: d3761977d3234e19f0df24aec45451b234a569e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84332015"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Met Spark gegevens verkennen en modelleren

Meer informatie over het gebruik van HDInsight Spark voor het trainen van machine learning modellen voor de voor spelling van een taxi-ritbedrag met behulp van Spark MLlib.

In dit voor beeld worden de verschillende stappen in het [team data Science proces](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)gedemonstreerd. Er wordt een subset van de NYC-gegevensset voor de taxi en de ritbedrag 2013 gebruikt om gegevens te laden, te verkennen en voor te bereiden. De binaire classificatie en regressie modellen worden vervolgens getraind met behulp van Spark MLlib om te voors pellen of een tip wordt betaald voor de reis en het fooien bedrag te schatten.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-account en een HDInsight-cluster met Spark 1,6 (of Spark 2,0) nodig om deze procedure te volt ooien. Bekijk het [overzicht van data Science met behulp van Spark in azure HDInsight](spark-overview.md) voor instructies over het voldoen aan deze vereisten. Dit onderwerp bevat ook een beschrijving van de NYC 2013-taxi gegevens die hier worden gebruikt en instructies voor het uitvoeren van code uit een Jupyter-notebook op het Spark-cluster. 

### <a name="spark-clusters-and-notebooks"></a>Spark-clusters en-notebooks

Setup-stappen en-code zijn opgenomen in dit overzicht voor het gebruik van een HDInsight Spark 1,6. Maar er zijn Jupyter-notebooks voor zowel HDInsight Spark 1,6-als Spark 2,0-clusters. Er wordt een beschrijving van de notitie blokken en koppelingen naar deze notebooks gegeven in de [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslag plaats waarin deze zijn opgenomen. Bovendien zijn de code hier en in de gekoppelde notitie blokken Gene riek en moeten ze werken op een Spark-cluster. Als u geen gebruik maakt van HDInsight Spark, kunnen de stappen voor het instellen en beheren van het cluster enigszins afwijken van wat hier wordt weer gegeven. Hier volgen de koppelingen naar de Jupyter-notebooks voor Spark 1,6 (om te worden uitgevoerd in de pySpark-kernel van de Jupyter Notebook-server) en Spark 2,0 (wordt uitgevoerd in de pySpark3-kernel van de Jupyter Notebook-server):

- [Spark 1,6-notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): bieden informatie over het verkennen, model leren en scoren van gegevens met verschillende algoritmen.
- [Spark 2,0-notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): bieden informatie over het uitvoeren van regressie-en classificatie taken. Gegevens sets kunnen verschillen, maar de stappen en concepten zijn van toepassing op verschillende gegevens sets.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> De onderstaande beschrijvingen zijn gerelateerd aan het gebruik van Spark 1,6. Voor Spark 2,0-versies gebruikt u de hierboven beschreven notebooks en gekoppeld. 

## <a name="setup"></a>Instellen

Spark kan lezen van en schrijven naar Azure Storage Blob (ook wel bekend als WASB). Een van de bestaande opgeslagen gegevens kan worden verwerkt met behulp van Spark en de resultaten die opnieuw worden opgeslagen in WASB.

Om modellen of bestanden in WASB op te slaan, moet het pad correct worden opgegeven. Er kan naar de standaard container die aan het Spark-cluster is gekoppeld, een pad worden gebruikt dat begint met: ' wasb:///'. Er wordt naar andere locaties verwezen door ' wasb://'.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Mappaden instellen voor opslag locaties in WASB

In het volgende code voorbeeld geeft u de locatie op van de gegevens die moeten worden gelezen en het pad voor de map voor het model opslag waarnaar de model uitvoer wordt opgeslagen:


    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotheken importeren

Voor het instellen moet ook de benodigde bibliotheken worden geïmporteerd. Stel Spark-context in en importeer de benodigde bibliotheken met de volgende code:

    # IMPORT LIBRARIES
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

De PySpark-kernels die worden meegeleverd met Jupyter-notebooks hebben een vooraf ingestelde context. Het is dus niet nodig om de Spark-of Hive-contexten expliciet in te stellen voordat u begint te werken met de toepassing die u ontwikkelt. Deze contexten zijn standaard voor u beschikbaar. Deze contexten zijn:

* SC-voor Spark 
* sqlContext-voor-Hive

De PySpark-kernel biedt enkele vooraf gedefinieerde ' magics '. Dit zijn speciale opdrachten die u kunt aanroepen met%%. Er zijn twee opdrachten die worden gebruikt in deze code voorbeelden.

* **%% Local** Geeft aan dat de code in volgende regels lokaal moet worden uitgevoerd. Code moet geldige python-code zijn.
* **%% SQL-o \<variable name> ** Voert een Hive-query uit op basis van de sqlContext. Als de-o-para meter wordt door gegeven, wordt het resultaat van de query persistent gemaakt in de lokale python-context%% als een Panda data frame.

Zie [kernels die beschikbaar zijn voor Jupyter-notebooks met Hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)voor meer informatie over Jupyter notebook-kernels en de vooraf gedefinieerde ' magics '.

## <a name="load-the-data"></a>De gegevens laden

De eerste stap in het data Science-proces is het opnemen van de gegevens die moeten worden geanalyseerd uit bronnen waar zich in uw omgeving voor gegevens exploratie en-modellering bevindt. De omgeving is Spark in dit overzicht. Deze sectie bevat de code voor het volt ooien van een reeks taken:

* het gegevens voorbeeld opnemen om te worden gemodelleerd
* lezen in de invoer gegevensset (opgeslagen als een TSV-bestand)
* de gegevens opmaken en opschonen
* objecten (Rdd's of Data-frames) in het geheugen maken en opslaan
* Registreer deze als een tijdelijke tabel in SQL-context.

Dit is de code voor gegevens opname.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**UITVOER**

Benodigde tijd voor het uitvoeren van de cel: 51,72 seconden

## <a name="explore-the-data"></a>De gegevens verkennen

Zodra de gegevens in Spark zijn gebracht, is de volgende stap in het data Science-proces het verkrijgen van diep gaande informatie over de gegevens via verkennen en visualisatie. In deze sectie onderzoeken we de taxi gegevens met behulp van SQL-query's en zetten ze de doel variabelen en de potentiële functies voor visuele inspectie uit. In het bijzonder zetten we de frequentie van het aantal reizigers in taxi reizen, de frequentie van fooien en de manier waarop tips variëren per betalings bedrag en type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Een histogram met aantal reizigers-frequenties uitzetten in het voor beeld van taxi reizen

Deze code en volgende fragmenten gebruiken SQL Magic om het voor beeld te doorzoeken en om de gegevens te tekenen.

* **SQL Magic ( `%%sql` )** de HDInsight PySpark-kernel ondersteunt eenvoudige inline HiveQL-Query's op de sqlContext. Het argument (-o VARIABLE_NAME) persistent de uitvoer van de SQL-query als een Panda data frame op de Jupyter-server. Met deze instelling wordt de uitvoer beschikbaar in de lokale modus.
* Het ** `%%local` Magic** wordt gebruikt om code lokaal uit te voeren op de Jupyter-server. Dit is de hoofd knooppunt van het HDInsight-cluster. Normaal gesp roken gebruikt u `%%local` Magic in combi natie met de `%%sql` para meter Magic met-o. Met de para meter-o blijft de uitvoer van de SQL-query lokaal behouden en vervolgens%% Local Magic de volgende set code fragment wordt geactiveerd om lokaal uit te voeren op basis van de uitvoer van de SQL-query's die lokaal blijven bestaan

De uitvoer wordt automatisch gevisualiseerd nadat u de code hebt uitgevoerd.

Met deze query worden de TRIPS opgehaald op basis van het aantal reizigers. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Met deze code wordt een lokaal gegevens frame gemaakt op basis van de uitvoer van de query en worden de gegevens getekend. `%%local`Met Magic maakt u een lokaal gegevens frame, `sqlResults` dat kan worden gebruikt voor het uitzetten met matplotlib. 

> [!NOTE]
> Deze PySpark Magic wordt meerdere keren gebruikt in dit overzicht. Als de hoeveelheid gegevens groot is, kunt u het beste een voor beeld maken van een gegevens frame dat past in het lokale geheugen.

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Hier volgt de code voor het uitzetten van de trips op het aantal reizigers

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**UITVOER**

![Reis frequentie per passagier-aantal](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

U kunt verschillende typen visualisaties (tabel, cirkel, lijn, vlak of staaf) selecteren met behulp van de menu knoppen van het **type** in het notitie blok. Het balk-plot wordt hier weer gegeven.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Maak een histogram met fooien en de mate van fooien per aantal reizigers en ritbedrag bedragen.

Gebruik een SQL-query om gegevens te bemonsteren.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25

In deze tabelcel wordt de SQL-query gebruikt om de gegevens in drie grafieken te maken.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**UITVOER** 

![Verdeling fooie bedragen](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Aantal fooien per passagier](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Aantal fooien per tarief](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>De gegevens voorbereiden

In deze sectie wordt beschreven hoe en hoe de code wordt gebruikt om gegevens voor te bereiden voor gebruik in ML-modellen. U ziet hoe u de volgende taken kunt uitvoeren:

* Een nieuwe functie door binning uur maken in tijds verzamelingen voor verkeer
* Categorische-functies indexeren en coderen
* Gelabelde punt objecten maken voor invoer in ML-functies
* Een wille keurige subsampling van de gegevens maken en deze splitsen in trainings-en test sets
* Functie schalen
* Cache objecten in het geheugen

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Een nieuwe functie door binning uur maken in tijds verzamelingen voor verkeer

Deze code laat zien hoe u een nieuwe functie maakt door binning uur in tijds verzamelingen voor verkeer en hoe u het resulterende gegevens frame in het geheugen kunt opslaan. Waar robuuste gedistribueerde gegevens sets (Rdd's) en data-frames herhaaldelijk worden gebruikt, wordt het in de cache plaatsen van prestaties tot betere uitvoerings tijden. Daarom slaan we Rdd's-en data-frames op in verschillende fasen in het overzicht. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**UITVOER**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Categorische-functies voor invoer in model functies indexeren en coderen

In deze sectie wordt beschreven hoe u categorische-functies indexeert of versleutelt voor invoer in de modelleer functies. Voor het model leren en voors pellen van MLlib zijn functies met categorische-invoer gegevens vereist die moeten worden geïndexeerd of gecodeerd voordat ze kunnen worden gebruikt. Afhankelijk van het model moet u deze op verschillende manieren indexeren of coderen:  

* Voor **model lering op basis van een structuur** moeten categorieën worden gecodeerd als numerieke waarden (bijvoorbeeld een functie met drie categorieën kan worden gecodeerd met 0, 1, 2). Dit algoritme wordt verschaft door de [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) -functie van MLlib. Deze functie codeert een teken reeks kolom met labels naar een kolom met label indexen die worden geordend op label frequenties. Hoewel geïndexeerd met numerieke waarden voor invoer en gegevens verwerking, kunnen de structuur algoritmen worden opgegeven om ze op de juiste wijze als categorieën te behandelen. 
* **Logistiek-en lineaire regressie modellen** vereisen een hot encoding, waarbij bijvoorbeeld een functie met drie categorieën kan worden uitgebreid naar drie functie kolommen, waarbij elk 0 of 1, afhankelijk van de categorie van een waarneming, bevat. MLlib biedt [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) -functie voor het uitvoeren van één Hot encoding. Met dit coderings programma wordt een kolom met label indexen toegewezen aan een kolom met binaire vectoren, met Maxi maal één waarde. Met deze code ring kunnen algoritmen die numerieke kenmerken verwachten, zoals logistiek regressie, worden toegepast op categorische-functies.

Hier volgt de code voor het indexeren en coderen van categorische-functies:

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

Benodigde tijd voor het uitvoeren van de cel: 1,28 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde punt objecten maken voor invoer in ML-functies

Deze sectie bevat code die laat zien hoe u categorische tekst gegevens indexeert als een gelabeld punt gegevens type en hoe u deze kunt coderen zodat deze kan worden gebruikt voor het trainen en testen van MLlib logistiek-regressie en andere classificatie modellen. Gelabelde punt objecten zijn robuuste gedistribueerde gegevens sets (RDD) die zijn opgemaakt als invoer gegevens met de meeste ML-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, een compacte of een sparse, die is gekoppeld aan een label/antwoord.  

Deze sectie bevat code die laat zien hoe u categorische tekst gegevens indexeert als een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) gegevens type en hoe u deze kunt coderen zodat deze kan worden gebruikt voor het trainen en testen van MLlib logistiek-regressie en andere classificatie modellen. Gelabelde punt objecten zijn robuuste gedistribueerde gegevens sets (RDD) die bestaan uit een label (doel/reactie variabele) en functie Vector. Deze indeling is vereist voor de invoer van een groot aantal ML-algoritmen in MLlib.

Hier volgt de code voor het indexeren en coderen van tekst functies voor binaire classificatie.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Hier volgt de code voor het coderen en indexeren van categorische-tekst functies voor lineaire regressie analyse.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Een wille keurige subsampling van de gegevens maken en deze splitsen in trainings-en test sets

Deze code maakt een wille keurige steek proef van de gegevens (25% wordt hier gebruikt). Hoewel dit voor beeld niet vereist is vanwege de grootte van de gegevensset, laten we zien hoe u hier een voor beeld van kunt nemen, zodat u weet hoe u dit voor uw eigen probleem moet gebruiken wanneer dat nodig is. Wanneer steek proeven groot zijn, kan de steek proef aanzienlijke tijd besparen bij het trainen van modellen. Vervolgens splitsen we het voor beeld in een trainings onderdeel (75% hier) en een test onderdeel (25% hier) om te gebruiken in de classificatie en regressie modellen.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

Benodigde tijd voor het uitvoeren van de cel: 0,24 seconde

### <a name="feature-scaling"></a>Functie schalen

Het schalen van functies, ook wel bekend als gegevens normalisatie, verzekert dat onderdelen met veel uitbetaalde waarden geen buitensporige weging van de functie doel hebben. De code voor functie schaling maakt gebruik van de [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) om de functies te schalen op eenheids afwijking. Het wordt verstrekt door MLlib voor gebruik in lineaire regressie met stochastische Gradient Daal (SGD), een populair algoritme voor het trainen van een breed scala aan andere machine learning modellen, zoals geregelde regressies of ondersteunde vector machines (SVM).

> [!NOTE]
> We hebben het LinearRegressionWithSGD-algoritme gevonden dat gevoelig is voor het schalen van functies.

Hier volgt de code voor het schalen van variabelen voor gebruik met het gemarkeerde lineaire SGD-algoritme.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

Benodigde tijd voor het uitvoeren van de cel: 13,17 seconden

### <a name="cache-objects-in-memory"></a>Cache objecten in het geheugen

De tijd die nodig is voor het trainen en testen van ML-algoritmen kan worden beperkt door de invoer gegevens frame objecten in de cache te plaatsen die worden gebruikt voor classificatie, regressie en geschaalde functies.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER** 

Benodigde tijd voor het uitvoeren van de cel: 0,15 seconde

## <a name="train-a-binary-classification-model"></a>Een binair classificatie model trainen

In deze sectie wordt beschreven hoe u drie modellen gebruikt voor het voors pellen van de binaire classificatie taak, ongeacht of er een tip voor een taxi wordt betaald. De gepresenteerde modellen zijn:

* Normale logistiek regressie 
* Wille keurig forest model
* Versterkings bomen met overgangen

Elke model code sectie is onderverdeeld in stappen: 

1. **Trainings gegevens model leren** met één parameterset
2. **Model evaluatie** van een test gegevensverzameling met metrische gegevens
3. **Model opslaan** in BLOB voor toekomstig gebruik

### <a name="classification-using-logistic-regression"></a>Classificatie met behulp van logistiek regressie

De code in deze sectie laat zien hoe u een logistiek regressie model traint, evalueert en opslaat met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) die voor spelt of er een tip wordt betaald voor een reis in de NYCe taxi en ritbedrag-gegevensset.

**Het logistiek-regressie model trainen met AVK en afstemming sweep**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**UITVOER** 

Coëfficiënten: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Interceptie:-0.0111216486893

Benodigde tijd voor het uitvoeren van de cel: 14,43 seconden

**Het binaire classificatie model evalueren met standaard metrische gegevens**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**UITVOER** 

Opper vlak onder PR = 0.985297691373

Gebied onder ROC = 0.983714670256

Samenvattings statistieken

Precisie = 0.984304060189

Intrekken = 0.984304060189

F1-score = 0.984304060189

Benodigde tijd voor het uitvoeren van de cel: 57,61 seconden

**De ROC-curve uitzetten.**

De *predictionAndLabelsDF* is geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kunnen worden gebruikt om query's uit te voeren en resultaten uit te voeren in het sqlResults-gegevens frame voor het uitzetten. Dit is de code.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Hier volgt de code voor het maken van voor spellingen en het uitzetten van de ROC-curve.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
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


**UITVOER**

![curve.png van logistiek regressie ROC](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Wille keurige forest-classificatie

De code in deze sectie laat zien hoe u een wille keurig forest model traint, evalueert en opslaat waarmee wordt voor speld of een tip wordt betaald voor een reis in de NYCe taxi en ritbedrag-gegevensset.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

Gebied onder ROC = 0.985297691373

Benodigde tijd voor het uitvoeren van de cel: 31,09 seconden

### <a name="gradient-boosting-trees-classification"></a>Classificatie voor versterking van bomen met overgang

De code in deze sectie laat zien hoe u een kleuren model voor het boosten van een kleurovergangsbeëindiging traint, evalueert en opslaat waarmee wordt gedicteerd of een tip wordt betaald voor een reis in de NYCe taxi en ritbedrag-gegevensset.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**UITVOER**

Gebied onder ROC = 0.985297691373

Benodigde tijd voor het uitvoeren van de cel: 19,76 seconden

## <a name="train-a-regression-model"></a>Een regressiemodel trainen

In deze sectie wordt uitgelegd hoe u drie modellen gebruikt voor de regressie taak voor het voors pellen van de hoeveelheid fooien die is betaald voor een taxi op basis van andere tip-functies. De gepresenteerde modellen zijn:

* Geregelde lineaire regressie
* Wille keurig forest
* Versterkings bomen met overgangen

Deze modellen zijn beschreven in de inleiding. Elke model code sectie is onderverdeeld in stappen: 

1. **Trainings gegevens model leren** met één parameterset
2. **Model evaluatie** van een test gegevensverzameling met metrische gegevens
3. **Model opslaan** in BLOB voor toekomstig gebruik

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD

De code in deze sectie laat zien hoe u geschaalde functies gebruikt om een lineaire regressie te trainen die gebruikmaakt van stochastische Gradient Daal (SGD) voor Optima Lise ring, en hoe u het model kunt beoordelen, evalueren en opslaan in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kan er sprake zijn van problemen met de convergentie van LinearRegressionWithSGD-modellen en moeten de para meters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen helpt u aanzienlijk bij het convergentie.

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

Coëfficiënten: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Interceptie: 0.853872718283

RMSE = 1.24190115863

R-Sqr = 0.608017146081

Benodigde tijd voor het uitvoeren van de cel: 58,42 seconden

### <a name="random-forest-regression"></a>Regressie van wille keurig forest

De code in deze sectie laat zien hoe u een wille keurige regressie kunt trainen, evalueren en opslaan waarmee de fooien voor de NYC worden voor speld.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

RMSE = 0.891209218139

R-Sqr = 0.759661334921

Benodigde tijd voor het uitvoeren van de cel: 49,21 seconden

### <a name="gradient-boosting-trees-regression"></a>Scha kering van bomen regressie verhogen

De code in deze sectie laat zien hoe u een kleuren model voor het boosten van een kleur overgang kunt trainen, evalueren en opslaan waarmee fooien voor de gegevens van de taxi van de NYC worden voor speld.

**Trainen en evalueren**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**UITVOER**

RMSE = 0.908473148639

R-Sqr = 0.753835096681

Benodigde tijd voor het uitvoeren van de cel: 34,52 seconden

**Ontwerp**

*tmp_results* is geregistreerd als een Hive-tabel in de vorige cel. De resultaten van de tabel worden uitgevoerd in het *sqlResults* -gegevens frame voor het uitzetten. Dit is de code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Hier volgt de code voor het uitzetten van de gegevens met behulp van de Jupyter-server.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**UITVOER**

![Werkelijk-VS-voor speld Tip-bedragen](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Objecten uit het geheugen opschonen

Gebruiken `unpersist()` voor het verwijderen van objecten in cache in het geheugen.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()

## <a name="save-the-models"></a>De modellen opslaan

Als u een onafhankelijke gegevensset wilt gebruiken die wordt beschreven in de score en evalueren van een onderwerp met [vonk gebouwde machine learning modellen](spark-model-consumption.md) , moet u deze bestands namen kopiëren en plakken met de opgeslagen modellen die u hier maakt in de Jupyter-notebook. Hier volgt de code voor het afdrukken van de paden voor de model bestanden die u nodig hebt.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";

**UITVOER**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0317 _03_ 23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0317 _05_ 21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0317 _04_ 11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0317 _06_ 08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0317 _04_ 36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0317 _06_ 51.737282"

## <a name="whats-next"></a>Volgende stappen

Nu u regressie-en classificatie modellen met de Spark MlLib hebt gemaakt, bent u klaar om te leren hoe u deze modellen kunt beoordelen en evalueren. De geavanceerde gegevens verkennen en model leren Dives diep in, waaronder Kruis validatie, Hyper-para meters afruimen en model evaluatie. 

**Model verbruik:** Voor meer informatie over het beoordelen en evalueren van de classificatie en regressie modellen die in dit onderwerp zijn gemaakt, raadpleegt u door [Spark ontwikkelde machine learning modellen te beoordelen en evalueren](spark-model-consumption.md).

**Kruis validatie en afstemming verruimen**: Zie [geavanceerde gegevens verkennen en model leren met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met kruis validatie en Hyper-para meters opruimen