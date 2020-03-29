---
title: Geavanceerde gegevensverkenning en modellering met Spark - Team Data Science Process
description: Gebruik HDInsight Spark om gegevensverkenning te doen en binaire classificatie- en regressiemodellen te trainen met cross-validatie en hyperparameteroptimalisatie.
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
ms.openlocfilehash: 15d9d186ef36ee9181a6ce0386aa9cc5de7838e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718647"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Met Spark verkennen en modelleren van geavanceerde gegevens

Deze walkthrough maakt gebruik van HDInsight Spark om gegevensverkenning te doen en binaire classificatie- en regressiemodellen te trainen met behulp van cross-validatie en hyperparameteroptimalisatie op een voorbeeld van de NYC-taxirit en tarief2013-gegevensset. Het leidt u door de stappen van het [Data Science-proces](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, met behulp van een HDInsight Spark-cluster voor verwerking en Azure blobs om de gegevens en de modellen op te slaan. Het proces verkent en visualiseert gegevens die zijn binnengebracht vanuit een Azure Storage Blob en bereidt vervolgens de gegevens voor om voorspellende modellen te bouwen. Python is gebruikt om de oplossing te coderen en om de relevante plots weer te geven. Deze modellen worden gebouwd met behulp van de Spark MLlib toolkit om binaire classificatie en regressie modellering taken uit te voeren. 

* De **binaire classificatie** taak is om te voorspellen of een tip wordt betaald voor de reis. 
* De **regressietaak** is het voorspellen van de hoeveelheid tip op basis van andere tipfuncties. 

De modelleringsstappen bevatten ook code die laat zien hoe u elk type model traint, evalueert en opslaat. Het onderwerp heeft betrekking op een aantal van dezelfde grond als de [Data exploratie en modellering met Spark](spark-data-exploration-modeling.md) onderwerp. Maar het is meer "geavanceerd" in die zin dat het ook cross-validatie met hyperparameter vegen gebruikt om optimaal nauwkeurige classificatie- en regressiemodellen te trainen. 

**Cross-validation (CV)** is een techniek die beoordeelt hoe goed een model getraind op een bekende set van gegevens generaliseert om de kenmerken van datasets waarop het niet is opgeleid te voorspellen.  Een gemeenschappelijke implementatie die hier wordt gebruikt is om een dataset te verdelen in K plooien en vervolgens het model te trainen in een round-robin mode op alle, maar een van de plooien. Het vermogen van het model om nauwkeurig te voorspellen wanneer getest tegen de onafhankelijke dataset in deze vouw niet gebruikt om het model te trainen wordt beoordeeld.

**Hyperparameter optimalisatie** is het probleem van het kiezen van een set hyperparameters voor een leeralgoritme, meestal met als doel het optimaliseren van een meting van de prestaties van het algoritme op een onafhankelijke gegevensset. **Hyperparameters** zijn waarden die buiten de modeltrainingsprocedure moeten worden opgegeven. Veronderstellingen over deze waarden kunnen van invloed zijn op de flexibiliteit en nauwkeurigheid van de modellen. Beslissingsbomen hebben hyperparameters, bijvoorbeeld, zoals de gewenste diepte en het aantal bladeren in de boom. Ondersteuning Vector Machines (SVMs) vereisen het instellen van een verkeerde classificatie boete term. 

Een veelvoorkomende manier om hyperparameteroptimalisatie uit te voeren die hier wordt gebruikt, is een rasterzoekopdracht of een **parametersweep.** Deze zoekopdracht gaat door een subset van de hyperparameterruimte voor een leeralgoritme. Cross validatie kan een prestatiestatistiek leveren om de optimale resultaten van het rasterzoekalgoritme te sorteren. CV dat wordt gebruikt bij het vegen van hyperparameter helpt problemen zoals het overpassen van een model aan trainingsgegevens te beperken, zodat het model de capaciteit behoudt om toe te passen op de algemene set gegevens waaruit de trainingsgegevens zijn geëxtraheerd.

De modellen die we gebruiken omvatten logistieke en lineaire regressie, willekeurige bossen en gradiënt gebooste bomen:

* [Lineaire regressie met SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) is een lineair regressiemodel dat een SGD-methode (Stochastic Gradient Descent) gebruikt en voor optimalisatie en functieschaling om de betaalde tipbedragen te voorspellen. 
* [Logistieke regressie met LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) of "logit" regressie, is een regressiemodel dat kan worden gebruikt wanneer de afhankelijke variabele categorisch is om gegevensclassificatie te doen. LBFGS is een quasi-Newton optimalisatie algoritme dat de Broyden-Fletcher-Goldfarb-Shanno (BFGS) algoritme benadert met behulp van een beperkte hoeveelheid computergeheugen en dat op grote schaal wordt gebruikt in machine learning.
* [Willekeurige bossen](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslisbomen.  Ze combineren veel beslisbomen om het risico op overfitting te verminderen. Willekeurige forests worden gebruikt voor regressie en classificatie en kunnen categorische functies verwerken en kunnen worden uitgebreid tot de classificatie-instelling voor meerdere klassen. Ze vereisen geen functieschaling en zijn in staat om niet-lineariteiten en functieinteracties vast te leggen. Willekeurige bossen zijn een van de meest succesvolle machine learning-modellen voor classificatie en regressie.
* [Gradiënt gebooste bomen](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) zijn ensembles van beslissingsbomen. GBTS traint beslissingsbomen iteratief om een verliesfunctie te minimaliseren. GBTS wordt gebruikt voor regressie en classificatie en kan categorische functies verwerken, vereisen geen functieschaling en zijn in staat om niet-lineariteiten en functieinteracties vast te leggen. Ze kunnen ook worden gebruikt in een multiclass-classificatie-instelling.

Voorbeelden van modelleringmet BEHULP van CV en Hyperparameter sweep worden weergegeven voor het binaire classificatieprobleem. Eenvoudigere voorbeelden (zonder parametersweeps) worden weergegeven in het hoofdonderwerp voor regressietaken. Maar in de appendix, validatie met behulp van elastisch e-net voor lineaire regressie en CV met parameter sweep met behulp van voor willekeurige bos regressie worden ook gepresenteerd. Het **elastische net** is een geregulariseerde regressiemethode voor het monteren van lineaire regressiemodellen die lineair de L1- en L2-statistieken combineert als sancties van de [lasso-](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) en [nokmethoden.](https://en.wikipedia.org/wiki/Tikhonov_regularization)   

<!-- -->

> [!NOTE]
> Hoewel de Spark MLlib toolkit is ontworpen om te werken op grote datasets, een relatief kleine steekproef (~ 30 Mb met behulp van 170K rijen, ongeveer 0,1% van de oorspronkelijke NYC dataset) wordt hier gebruikt voor het gemak. De oefening die hier wordt gegeven, loopt efficiënt (in ongeveer 10 minuten) op een HDInsight-cluster met 2 werkknooppunten. Dezelfde code, met kleine wijzigingen, kan worden gebruikt om grotere gegevenssets te verwerken, met de juiste wijzigingen voor het incachen van gegevens in het geheugen en het wijzigen van de clustergrootte.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Instellen: Spark-clusters en -notitieblokken
Setup stappen en code zijn voorzien in deze walkthrough voor het gebruik van een HDInsight Spark 1.6. Maar Jupyter-laptops zijn beschikbaar voor zowel HDInsight Spark 1.6- als Spark 2.0-clusters. Een beschrijving van de notitieblokken en links naar hen zijn voorzien in de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub repository met hen. Bovendien is de code hier en in de gekoppelde notitieblokken generiek en moet het werken op een Spark-cluster. Als u HDInsight Spark niet gebruikt, kunnen de clusterinstellingen en beheerstappen enigszins afwijken van wat hier wordt weergegeven. Voor het gemak, hier zijn de links naar de Jupyter notebooks voor Spark 1.6 en 2.0 worden uitgevoerd in de pyspark kernel van de Jupyter Notebook server:

### <a name="spark-16-notebooks"></a>Spark 1.6-notitieblokken

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Bevat onderwerpen in notebook #1 en modelontwikkeling met behulp van hyperparameter tuning en cross-validatie.

### <a name="spark-20-notebooks"></a>Spark 2.0-notitieblokken

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)Dit bestand biedt informatie over het uitvoeren van gegevensverkenning, modellering en scoren in Spark 2.0-clusters.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: opslaglocaties, bibliotheken en de vooraf ingestelde Spark-context
Spark kan lezen en schrijven naar Azure Storage Blob (ook wel WASB genoemd). Dus al uw bestaande gegevens die daar zijn opgeslagen, kunnen worden verwerkt met behulp van Spark en de resultaten worden opnieuw opgeslagen in WASB.

Om modellen of bestanden op te slaan in WASB, moet het pad correct worden opgegeven. De standaardcontainer die aan het Spark-cluster is gekoppeld, kan worden verwezen met behulp van een pad dat begint met: "wasb:///". Andere locaties worden verwezen door "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Adreslijstpaden instellen voor opslaglocaties in WASB
In het volgende codevoorbeeld wordt de locatie opgegeven van de te lezen gegevens en het pad voor de modelopslagmap waarop de modeluitvoer wordt opgeslagen:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**Output**

datum.datumtijd(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Bibliotheken importeren
Noodzakelijke bibliotheken importeren met de volgende code:

    # LOAD PYSPARK LIBRARIES
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
De PySpark-kernels die zijn voorzien van Jupyter-notebooks hebben een vooraf ingestelde context. U hoeft de Spark- of Hive-contexten dus niet expliciet in te stellen voordat u aan de slag gaat met de toepassing die u ontwikkelt. Deze contexten zijn standaard voor u beschikbaar. Deze contexten zijn:

* sc - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt een aantal vooraf gedefinieerde magics, die speciale opdrachten zijn die je aanroepen met %%. Er zijn twee van dergelijke commando's die worden gebruikt in deze code monsters.

* **%%lokaal** Hiermee geeft u op dat de code in de volgende regels lokaal moet worden uitgevoerd. Code moet een geldige Python-code zijn.
* **%%sql -o \<variabele naam>** Hiermee wordt een Hive-query uitgevoerd tegen de sqlContext. Als de parameter -o wordt doorgegeven, blijft het resultaat van de query bestaan in de context %%local Python als een Pandas DataFrame.

Voor meer informatie over de kernels voor Jupyter notebooks en de vooraf gedefinieerde "magics" die ze bieden, zie [Kernels beschikbaar voor Jupyter notebooks met HDInsight Spark Linux clusters op HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Gegevensopname van openbare blob:
De eerste stap in het data science-proces is het innemen van de gegevens die moeten worden geanalyseerd uit bronnen waar deze zich bevindt in uw omgeving voor gegevensverkenning en modellering. Deze omgeving is Spark in deze walkthrough. Deze sectie bevat de code om een reeks taken te voltooien:

* inname van het te modelleren gegevensmonster
* lezen in de invoergegevensset (opgeslagen als .tsv-bestand)
* de gegevens opmaken en schoonmaken
* objecten (RdD's of gegevensframes) maken en cachen in het geheugen
* registreren als een temp-tabel in SQL-context.

Hier is de code voor het innemen van gegevens.

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

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output**

Tijd die nodig is om boven de cel uit te voeren: 276,62 seconden

## <a name="data-exploration--visualization"></a>Visualisatie van gegevensverkenning & visualisatie
Zodra de gegevens in Spark zijn gebracht, is de volgende stap in het data science-proces om dieper inzicht te krijgen in de gegevens door middel van exploratie en visualisatie. In deze sectie onderzoeken we de taxigegevens met SQL-query's en plotten we de doelvariabelen en toekomstige functies voor visuele inspectie. Concreet zetten we de frequentie van het aantal passagiers in taxiritten, de frequentie van tipbedragen en hoe tips variëren per betalingsbedrag en type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Plot een histogram van passagierstellingfrequenties in de steekproef van taxireizen
Deze code en de daaropvolgende fragmenten gebruiken SQL-magie om het voorbeeld en de lokale magie op te vragen om de gegevens in kaart te brengen.

* **SQL-magie`%%sql`( )** De HDInsight PySpark kernel ondersteunt eenvoudige inline HiveQL-query's tegen de sqlContext. Het argument (-o VARIABLE_NAME) blijft de uitvoer van de SQL-query als een Pandas DataFrame op de Jupyter-server. Dit betekent dat het beschikbaar is in de lokale modus.
* De ** `%%local` magie** wordt gebruikt om code lokaal uit te voeren op de Jupyter-server, de headnode van het HDInsight-cluster. Meestal gebruikt `%%local` u magie `%%sql -o` nadat de magie is gebruikt om een query uit te voeren. De parameter -o blijft de uitvoer van de SQL-query lokaal aanhouden. Vervolgens `%%local` activeert de magie de volgende set codefragmenten om lokaal te worden uitgevoerd tegen de uitvoer van de SQL-query's die lokaal zijn uitgevoerd. De uitvoer wordt automatisch gevisualiseerd nadat u de code hebt uitgevoerd.

Deze query haalt de reizen op per aantal passagiers. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Met deze code wordt een lokaal gegevensframe op basis van de queryuitvoer uitgevoerd en worden de gegevens in een stand hersteld. De `%%local` magie creëert een lokaal `sqlResults`dataframe, dat kan worden gebruikt voor het plotten met matplotlib. 

<!-- -->

> [!NOTE]
> Deze PySpark magie wordt meerdere keren gebruikt in deze walkthrough. Als de hoeveelheid gegevens groot is, moet u een voorbeeld nemen om een gegevensframe te maken dat in het lokale geheugen past.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Hier is de code om de reizen plot door passagierstellingen

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**Output**

![Frequentie van reizen per aantal passagiers](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

U verschillende typen visualisaties (tabel, cirkel, lijn, gebied of balk) selecteren met behulp van de menuknoppen **Type** in het notitieblok. De Bar plot is hier te zien.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Plot een histogram van tip bedragen en hoe tip bedrag varieert per passagier tellen en tarief bedragen.
Gebruik een SQL-query om gegevens te samplen..

    # SQL SQUERY
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


Deze codecel gebruikt de SQL-query om drie plots van de gegevens te maken.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**Output:** 

![Tipbedragverdeling](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tipbedrag per aantal passagiers](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipbedrag per tarief Bedrag](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Functie-engineering, transformatie en gegevensvoorbereiding voor modellering
In deze sectie wordt de code beschreven en bevat u de code voor procedures die worden gebruikt om gegevens voor te bereiden op gebruik in ML-modellering. Het laat zien hoe u de volgende taken uitvoert:

* Een nieuwe functie maken door uren te verdelen in verkeerstijdopslaglocaties
* Index- en on-hot coderen categorische functies
* Gelabelde puntobjecten maken voor invoer in ML-functies
* Maak een willekeurige subsampling van de gegevens en splits deze op in trainings- en testsets
* Functie schalen
* Cacheobjecten in het geheugen

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Een nieuwe functie maken door de verkeerstijden in opslaglocaties te verdelen
Deze code laat zien hoe u een nieuwe functie maakt door de verkeerstijden in opslaglocaties te verdelen en vervolgens het resulterende gegevensframe in het geheugen in de cache op te zetten. Caching leidt tot een verbeterde uitvoeringstijd waarbij Resilient Distributed Datasets (RDD's) en dataframes herhaaldelijk worden gebruikt. We cachen RDD's en dataframes in verschillende fasen van deze walkthrough.

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

**Output**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index en one-hot coderen categorische functies
In deze sectie ziet u hoe u categorische functies indexeert of codeert voor invoer in de modelleringsfuncties. De modellerings- en voorspellende functies van MLlib vereisen dat functies met categorische invoergegevens worden geïndexeerd of gecodeerd voordat ze worden gebruikt. 

Afhankelijk van het model moet u ze op verschillende manieren indexeren of coderen. Logistieke en lineaire regressiemodellen vereisen bijvoorbeeld een hot-codering, waarbij bijvoorbeeld een functie met drie categorieën kan worden uitgebreid tot drie functiekolommen, waarbij elk 0 of 1 bevat, afhankelijk van de categorie van een observatie. MLlib biedt [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) functie om one-hot encoding te doen. Deze encoder brengt een kolom met labelindexen in kaart met een kolom van binaire vectoren, met maximaal één waarde. Met deze codering kunnen algoritmen die numerieke waardekenmerken verwachten, zoals logistieke regressie, worden toegepast op categorische functies.

Hier is de code om categorische functies te indexeren en te coderen:

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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


**Output**

Tijd die nodig is om boven de cel uit te voeren: 3,14 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde puntobjecten maken voor invoer in ML-functies
Deze sectie bevat code die laat zien hoe u categorische tekstgegevens indexeert als een gelabeld puntgegevenstype en hoe u deze coderen. Deze transformatie bereidt tekstgegevens voor die worden gebruikt om de logistieke regressie van MLlib en andere classificatiemodellen te trainen en te testen. Gelabelde puntobjecten zijn Resilient Distributed Datasets (RDD) opgemaakt op een manier die nodig is als invoergegevens door de meeste ML-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, dicht of schaars, gekoppeld aan een label/respons.

Hier is de code om tekstfuncties te indexeren en te coderen voor binaire classificatie.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Hier is de code om categorische tekstfuncties te coderen en te indexeren voor lineaire regressieanalyse.

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


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Maak een willekeurige subsampling van de gegevens en splits deze op in trainings- en testsets
Deze code maakt een willekeurige steekproef van de gegevens (25% wordt hier gebruikt). Hoewel dit bijvoorbeeld niet nodig is vanwege de grootte van de gegevensset, laten we zien hoe u de gegevens hier samplen. Dan weet je hoe je het moet gebruiken voor je eigen probleem als dat nodig is. Wanneer monsters groot zijn, kan bemonstering veel tijd besparen tijdens trainingsmodellen. Vervolgens splitsen we de steekproef in een trainingsonderdeel (75% hier) en een testonderdeel (25% hier) om te gebruiken in classificatie- en regressiemodellering.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

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

**Output**

Tijd die nodig is om boven de cel uit te voeren: 0,31 seconde

### <a name="feature-scaling"></a>Functie schalen
Feature scaling, ook wel bekend als data normalisatie, verzekert dat functies met op grote schaal uitbetaald waarden worden niet gegeven overmatige wegen in de objectieve functie. De code voor functieschaling gebruikt de [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) om de functies te schalen naar variantie van de eenheid. Het wordt geleverd door MLlib voor gebruik in lineaire regressie met Stochastic Gradient Descent (SGD). SGD is een populair algoritme voor het trainen van een breed scala aan andere machine learning-modellen, zoals geregulariseerde regressies of ondersteuningsvectormachines (SVM).   

> [!TIP]
> We hebben het LinearRegressionWithSGD-algoritme gevoelig gevonden voor feature scaling.   
> 
> 

Hier is de code om variabelen te schalen voor gebruik met het geregulariseerde lineaire SGD-algoritme.

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

**Output**

Tijd die nodig is om boven de cel uit te voeren: 11,67 seconden

### <a name="cache-objects-in-memory"></a>Cacheobjecten in het geheugen
De tijd die nodig is voor het trainen en testen van ML-algoritmen kan worden verkort door de invoergegevensframeobjecten die worden gebruikt voor classificatie, regressie en geschaalde functies in cache te plaatsen.

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

**Output** 

Tijd die nodig is om boven de cel uit te voeren: 0,13 seconde

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Voorspellen of een fooi wordt betaald met binaire classificatiemodellen
In dit gedeelte ziet u hoe het gebruik van drie modellen voor de binaire classificatietaak om te voorspellen of een fooi al dan niet wordt betaald voor een taxirit. De gepresenteerde modellen zijn:

* Logistieke regressie 
* Willekeurig bos
* Verloop stimuleren bomen

Elke sectie van de modelbouwcode wordt opgesplitst in stappen: 

1. **Trainingsgegevens modelleren** met één parameterset
2. **Evaluatie van het model** op een testgegevensset met statistieken
3. **Model opslaan** in blob voor toekomstig verbruik

We laten op twee manieren zien hoe je cross-validatie (CV) uitvoeren met parametervegen:

1. Met behulp van **generieke** aangepaste code die kan worden toegepast op elk algoritme in MLlib en op alle parametersets in een algoritme. 
2. De **functie pySpark CrossValidator-pijplijn gebruiken**. CrossValidator heeft een paar beperkingen voor Spark 1.5.0: 
   
   * Pijplijnmodellen kunnen niet worden opgeslagen of blijven bestaan voor toekomstig verbruik.
   * Kan niet worden gebruikt voor elke parameter in een model.
   * Kan niet worden gebruikt voor elk MLlib-algoritme.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Generieke cross validatie en hyperparameter vegen gebruikt met de logistieke regressie algoritme voor binaire classificatie
De code in deze sectie laat zien hoe je een logistiek regressiemodel traint, evalueert en opslaat met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) dat voorspelt of er al dan niet een fooi wordt betaald voor een reis in de NYC-taxirit en tariefgegevensset. Het model is getraind met behulp van cross validation (CV) en hyperparameter vegen geïmplementeerd met aangepaste code die kan worden toegepast op een van de leeralgoritmen in MLlib.   

<!-- -->

> [!NOTE]
> De uitvoering van deze aangepaste CV-code kan enkele minuten duren.

<!-- -->

**Train het logistieke regressiemodel met CV en hyperparameter vegen**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output**

Coëfficiënten: [0,0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0,111823113101, -0,32460912762, -0,204549780032, -1,36499216354, 0,591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Onderschepping: -0,0111216486893

Tijd die nodig is om boven de cel uit te voeren: 14,43 seconden

**Het binaire classificatiemodel evalueren met standaardstatistieken**

De code in deze sectie laat zien hoe u een logistiek regressiemodel evalueren aan de hand van een testgegevensset, inclusief een plot van de ROC-curve.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output**

Gebied onder PR = 0,985336538462

Gebied onder ROC = 0,983383274312

Overzichtsstatistieken

Precisie = 0,984174341679

Terugroepactie = 0,984174341679

F1 Score = 0,984174341679

Tijd die nodig is om boven de cel uit te voeren: 2,67 seconden

**Zet de ROC-curve in kaart.**

De *voorspellingAndLabelsDF* wordt geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kan worden gebruikt om query's en uitvoerresultaten uit te brengen in het sqlResults-gegevensframe voor het plotten. Hier is de code.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Hier is de code om voorspellingen te doen en plot de ROC-curve.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
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


**Output**

![Logistieke regressie ROC curve voor generieke aanpak](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Persist-model in een blob voor toekomstig verbruik**

De code in deze sectie laat zien hoe u het logistieke regressiemodel voor consumptie opslaan.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**Output**

Tijd die nodig is om boven de cel uit te voeren: 34,57 seconden

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Gebruik de CrossValidator-pijplijnfunctie van MLlib met het model van logistieke regressie (Elastic regression)
De code in deze sectie laat zien hoe je een logistiek regressiemodel traint, evalueert en opslaat met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) dat voorspelt of er al dan niet een fooi wordt betaald voor een reis in de NYC-taxirit en tariefgegevensset. Het model is getraind met behulp van cross validation (CV) en hyperparameter vegen geïmplementeerd met de MLlib CrossValidator pipeline functie voor CV met parameter sweep.   

<!-- -->

> [!NOTE]
> De uitvoering van deze MLlib CV code kan enkele minuten duren.

<!-- -->

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**Output**

Tijd die nodig is om boven de cel uit te voeren: 107,98 seconden

**Zet de ROC-curve in kaart.**

De *voorspellingAndLabelsDF* wordt geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kan worden gebruikt om query's en uitvoerresultaten uit te brengen in het sqlResults-gegevensframe voor het plotten. Hier is de code.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Hier is de code om de ROC curve plot.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
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


**Output**

![Logistieke regressie ROC curve met MLlib's CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Willekeurige bosclassificatie
De code in deze sectie laat zien hoe je een willekeurige bosregressie traint, evalueert en opslaat die voorspelt of er al dan niet een fooi wordt betaald voor een reis in de NYC-taxirit en tariefgegevensset.

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
    ## UN-COMMENT IF YOU WANT TO PRING TREES
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


**Output**

Gebied onder ROC = 0,985336538462

Tijd die nodig is om boven de cel uit te voeren: 26,72 seconden

### <a name="gradient-boosting-trees-classification"></a>Verloop stimuleren bomen classificatie
De code in deze sectie laat zien hoe je een verloop stimuleren bomen model dat voorspelt of een tip wordt betaald voor een reis in de NYC taxirit en tarief dataset op te slaan.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
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

**Output**

Gebied onder ROC = 0,985336538462

Tijd die nodig is om boven de cel uit te voeren: 28,13 seconden

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Tipbedrag voorspellen met regressiemodellen (geen CV gebruiken)
In dit gedeelte ziet u hoe drie modellen worden gebruikt voor de regressietaak: het tipbedrag dat voor een taxirit wordt betaald, voorspellen op basis van andere tipfuncties. De gepresenteerde modellen zijn:

* Geregulariseerde lineaire regressie
* Willekeurig bos
* Verloop stimuleren bomen

Deze modellen werden beschreven in de inleiding. Elke sectie van de modelbouwcode wordt opgesplitst in stappen: 

1. **Trainingsgegevens modelleren** met één parameterset
2. **Evaluatie van het model** op een testgegevensset met statistieken
3. **Model opslaan** in blob voor toekomstig verbruik   

<!-- -->

> [!NOTE] 
> Cross-validatie wordt niet gebruikt met de drie regressiemodellen in deze sectie, omdat dit in detail werd getoond voor de logistieke regressiemodellen. Een voorbeeld van het gebruik van CV met Elastic Net voor lineaire regressie is opgenomen in de bijlage van dit onderwerp.

<!-- -->

<!-- -->

> [!NOTE] 
> In onze ervaring kunnen er problemen zijn met de convergentie van LinearRegressionWithSGD-modellen en moeten parameters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen helpt aanzienlijk bij convergentie. Elastische netto regressie, weergegeven in de bijlage bij dit onderwerp, kan ook worden gebruikt in plaats van LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD
De code in deze sectie laat zien hoe geschaalde functies worden gebruikt om een lineaire regressie te trainen die stochastische gradiëntdescent (SGD) gebruikt voor optimalisatie en hoe u het model scoren, evalueren en opslaan in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kunnen er problemen zijn met de convergentie van LinearRegressionWithSGD-modellen en moeten parameters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen helpt aanzienlijk bij convergentie.
> 
> 

    # LINEAR REGRESSION WITH SGD 

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

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output**

Coëfficiënten: [0,0141707753435, -0,0252930927087, -0,0231442517137, 0,247070902996, 0,312544147152, 0,360296120645, 0,0122079566092, -0,0045649888241, -0,0898228505177, 0,0714046248793, 0,102171263868, - 0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Onderschepping: 0,854507624459

RMSE = 1,23485131376

R-sqr = 0,597963951127

Tijd die nodig is om boven de cel uit te voeren: 38,62 seconden

### <a name="random-forest-regression"></a>Willekeurige forestregressie
De code in deze sectie laat zien hoe je een willekeurig bosmodel traint, evalueert en opslaat dat het tipbedrag voor de gegevens van de NYC-taxirit voorspelt.   

<!-- -->

> [!NOTE]
> Cross-validatie met parameter vegen met behulp van aangepaste code is opgenomen in de bijlage.

<!-- -->

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

**Output**

RMSE = 0,931981967875

R-sqr = 0,733445485802

Tijd die nodig is om boven de cel uit te voeren: 25,98 seconden

### <a name="gradient-boosting-trees-regression"></a>Verloop stimuleren bomen regressie
De code in deze sectie laat zien hoe te trainen, evalueren en opslaan van een gradiënt stimuleren bomen model dat tip bedrag voorspelt voor de NYC taxi reis gegevens.

**Trainen en evalueren**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output**

RMSE = 0,928172197114

R-sqr = 0,732680354389

Tijd die nodig is om boven de cel uit te voeren: 20,9 seconden

**Plot**

*tmp_results* is geregistreerd als hive-tabel in de vorige cel. Resultaten uit de tabel zijn uitvoer naar het *sqlResults-gegevensframe* voor plotten. Hier is de code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Hier is de code om de gegevens plot met behulp van de Jupyter server.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Werkelijke-vs-voorspelde-tip-bedragen](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Bijlage: Extra regressietaken met kruisvalidatie met parametersweeps
Deze bijlage bevat code die laat zien hoe cv te doen met Elastic net voor lineaire regressie en hoe cv te doen met parameter sweep met behulp van aangepaste code voor willekeurige bosregressie.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Kruisvalidatie met Elastic net voor lineaire regressie
De code in deze sectie laat zien hoe cross validatie te doen met Elastic net voor lineaire regressie en hoe het model te evalueren tegen testgegevens.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISTER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output**

Tijd die nodig is om boven de cel uit te voeren: 161,21 seconden

**Evalueren met R-SQR-statistiek**

*tmp_results* is geregistreerd als hive-tabel in de vorige cel. Resultaten uit de tabel zijn uitvoer naar het *sqlResults-gegevensframe* voor plotten. Hier is de code

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Hier is de code om R-sqr te berekenen.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**Output**

R-sqr = 0,619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kruisvalidatie met parametersweep met aangepaste code voor willekeurige forestregressie
De code in deze sectie laat zien hoe u cross validatie met parameter sweep met behulp van aangepaste code voor willekeurige forest regressie en hoe het model te evalueren tegen testgegevens.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output**

RMSE = 0,906972198262

R-sqr = 0,740751197012

Tijd die nodig is om boven de cel uit te voeren: 69,17 seconden

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Objecten opschonen uit geheugen- en afdrukmodellocaties
Hiermee `unpersist()` u objecten verwijderen die in het geheugen zijn opgeslagen.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

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


**Output**

PythonRDD[122] bij RDD bij PythonRDD.scala: 43

**Uitvoerpad naar modelbestanden die in het verbruiksnotitieblok moeten worden gebruikt. ** Als u een onafhankelijke gegevensset wilt gebruiken en scoren, moet u deze bestandsnamen kopiëren en plakken in het 'Verbruiksnotitieblok'.

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Output**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Volgende stappen
Nu u regressie- en classificatiemodellen hebt gemaakt met de Spark MlLib, bent u klaar om te leren scoren en evalueren van deze modellen.

**Modelverbruik:** Zie [Spark-built machine learning-modellen beoordelen voor](spark-model-consumption.md)meer informatie over het scoren en evalueren van de classificatie- en regressiemodellen die in dit onderwerp zijn gemaakt.

