---
title: Geavanceerde gegevensverkenning en modellering met Spark - Team Data Science Process
description: HDInsight Spark gegevens verkennen en trainen binaire classificatie- en regressiemodellen modellen met behulp van kruisvalidatie en hyperparameter optimalisatie gebruiken.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718647"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Met Spark verkennen en modelleren van geavanceerde gegevens

In dit scenario maakt gebruik van HDInsight Spark gegevens verkennen en binaire classificatie en met behulp van kruisvalidatie regressiemodellen trainen en hyperparameter optimalisatie van een steekproef van de NYC taxi reis en ritbedrag 2013-gegevensset. U wordt begeleid bij de stappen van het [Data Science proces](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, met behulp van een HDInsight Spark-cluster voor verwerking en Azure-blobs voor het opslaan van de gegevens en de modellen. Het proces worden verkend en visualiseert gegevens voorgelegd aan van een Azure Storage-Blob en vervolgens de gegevens voor het bouwen van voorspellende modellen voorbereid. Python is gebruikt om de oplossing en om de relevante grafieken weer te geven. Deze modellen zijn bouwen met behulp van de toolkit Spark MLlib binaire classificatie- en regressiemodellen modelleren om taken te voeren. 

* De **binaire classificatie** taak is om te voors pellen of een tip voor de reis wordt betaald. 
* De **regressie** taak is om de hoeveelheid fooien te voors pellen op basis van andere tip-functies. 

De stappen modellering bevatten ook code waarin wordt getoond hoe om te trainen, evalueren en elk type model opslaan. Het onderwerp behandelt een deel van hetzelfde terrein als het [verkennen van gegevens en het model leren met het Spark](spark-data-exploration-modeling.md) -onderwerp. Maar dit is meer "Geavanceerde" in dat deze ook kruisvalidatie gebruikt met hyperparameter verstrekkende optimaal nauwkeurige modellen voor classificatie- en regressiemodellen trainen. 

**Kruis validatie (AVK)** is een techniek waarmee wordt beoordeeld hoe goed een model dat is getraind op een bekende set gegevens, het voors pellen van de functies van gegevens sets waarop het niet is getraind.  Een algemene implementatie hier gebruikt is het onderverdelen van een gegevensset in K vouwen en vervolgens het model in een round robin besturingsaanvraag op alle maar een van de vouwen te trainen. De mogelijkheid van het model prediction nauwkeurig wanneer getest op basis van de onafhankelijke gegevensset in deze niet gebruikt voor het trainen van het model vouwen worden beoordeeld.

**Afstemming Optimization** is het probleem bij het kiezen van een set Hyper parameters voor een leer algoritme, meestal met het doel om een meting van de prestaties van het algoritme op een onafhankelijke gegevensset te optimaliseren. **Hyper parameters** zijn waarden die buiten de model trainings procedure moeten worden opgegeven. Veronderstellingen over deze waarden kunnen invloed hebben op de flexibiliteit en de nauwkeurigheid van de modellen. Beslisbomen hebben hyperparameters, bijvoorbeeld, zoals de gewenste omvang en aantal bladeren in de structuur. Support Vector Machines (SVMs) is vereist dat een misclassification boete term. 

Een veelgebruikte manier om de afstemming-optimalisatie uit te voeren die hier wordt gebruikt, is een raster zoekactie of een **parameter sweep**. Deze zoek opdracht gaat door een subset van de afstemming ruimte voor een leer algoritme. Cross-validatie kan een metrische gegevens voor prestaties voor het sorteren van de optimale resultaten die worden geproduceerd door de algoritme van de zoekopdracht raster leveren. CV gebruikt met ingrijpende helpt hyperparameter limiet problemen, zoals een model te trainen van gegevens overfitting zodat het model behoudt de capaciteit toe te passen op de algemene set gegevens waaruit de trainingsgegevens is opgehaald.

De modellen we gebruiken bevatten logistieke en lineaire regressie, willekeurige forests en kleurovergang boosted structuren:

* [Lineaire regressie met SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) is een lineair regressie model dat gebruikmaakt van een stochastische Gradient DAAL (SGD)-methode en voor Optima Lise ring en functie schaaling om de fooien te voors pellen. 
* [Logistiek regressie met LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) of ' logit-regressie ' is een regressie model dat kan worden gebruikt wanneer de afhankelijke variabele categorische is voor het uitvoeren van de gegevens classificatie. LBFGS is een quasi toegepast optimalisatie-algoritme dat benadert de Broyden – Fletcher – Goldfarb – Shanno (BFGS)-algoritme met behulp van een beperkte hoeveelheid van het geheugen van de computer en die wordt veel gebruikt in machine learning.
* [Wille keurige forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslissings structuren.  Ze combineren veel beslissingsstructuren om het risico te beperken. Willekeurige forests worden gebruikt voor regressie en classificatie en categorische functies kunnen worden verwerkt en kunnen worden uitgebreid naar de instelling voor multiklassen classificatie. Ze vereisen geen functie schaalaanpassing en kunnen niet-mogelijkheid tot vastleggen en functie van interacties. Willekeurige forests vormen een van de meest succesvolle machine learning-modellen voor classificatie- en regressiemodellen.
* GBTS ( [Gradient, boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) zijn ensembles van beslissings structuren. GBTSer boom structuren van de trein om een verlies functie te minimaliseren. GBTS wordt gebruikt voor regressie en classificatie, en kan categorische-functies afhandelen, geen functie schaaling vereisen en niet-lineaire en functie-interacties kunnen vastleggen. Ze kunnen ook worden gebruikt in een multiklasse-classificatie.

Voorbeelden van het gebruik van CV en Hyperparameter modelleren vegen worden weergegeven voor het probleem binaire classificatie. Eenvoudiger voorbeelden worden (zonder de parameter vervalmechanisme) weergegeven in het onderwerp voor regressie taken. Maar in de bijlage validatie met elastische net voor lineaire regressie en CV parameter vegen gebruikt voor willekeurige forest regressie ook worden weergegeven. Het **elastische net** is een geregelde regressie methode voor het aanpassen van lineaire regressie modellen die de L1-en L2-metrische gegevens lineair combi neren als boete van de [Lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) -en [gleuf](https://en.wikipedia.org/wiki/Tikhonov_regularization) -methoden.   

<!-- -->

> [!NOTE]
> Hoewel de toolkit Spark MLlib is ontworpen voor gebruik op grote gegevenssets, wordt hier een relatief klein aantal (ongeveer 30 Mb met behulp van 170K rijen, ongeveer 0,1% van de oorspronkelijke NYC gegevensset) gebruikt voor het gemak. De hier getoonde oefening efficiënt (in ongeveer 10 minuten) op een HDInsight-cluster met 2 worker-knooppunten wordt uitgevoerd. Dezelfde code, met kleine wijzigingen kan worden gebruikt voor het verwerken van grotere-gegevenssets, met de juiste wijzigingen voor gegevens in het geheugen cachen en de clustergrootte te wijzigen.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Instellen: Spark-clusters en -laptops
Installatiestappen uit en code vindt u in dit scenario voor het gebruik van een HDInsight Spark 1.6. Maar Jupyter-notebooks voor HDInsight Spark 1.6- en Spark 2.0-clusters worden geleverd. Er wordt een beschrijving van de notitie blokken en koppelingen naar deze notebooks gegeven in de [README.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslag plaats waarin deze zijn opgenomen. Bovendien is de code hier en in de gekoppelde notebooks is algemeen en werkt op een Spark-cluster. Als u niet met behulp van HDInsight Spark, configureren van het cluster en beheertaken uit kunnen enigszins afwijken van wat wordt hier weergegeven. Voor het gemak vindt u hier de koppelingen naar de Jupyter-notebooks voor Spark 1.6 en 2.0 moet worden uitgevoerd in de pyspark-kernel van de Jupyter-Notebook-server:

### <a name="spark-16-notebooks"></a>Spark 1.6-laptops

[pySpark-machine learning-data-Science-Spark-Advanced-Data-Explore-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): bevat onderwerpen in notebook #1 en model ontwikkeling met afstemming tuning en kruislings validatie.

### <a name="spark-20-notebooks"></a>Spark 2.0-laptops

[Spark 2.0-pySpark3-machine-learning-data-wetenschappen-Spark-Advanced-Data-Explore-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): dit bestand bevat informatie over het verkennen, model leren en scoren van gegevens in Spark 2,0-clusters.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: opslaglocaties, bibliotheken en de vooraf ingestelde Spark-context
Spark kan lezen en schrijven naar Azure Storage-Blob (ook wel bekend als WASB). Dus een van uw bestaande gegevens die zijn opgeslagen kunnen er worden verwerkt met behulp van Spark en de resultaten weer in WASB worden opgeslagen.

Om op te slaan modellen of de bestanden in WASB, moet het pad correct worden opgegeven. De standaardcontainer die is gekoppeld aan het Spark-cluster kan worden verwezen met behulp van een pad begint met: "wasb: / / / '. Andere locaties wordt verwezen door "wasb: / / '.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Directory-paden voor opslaglocaties in WASB instellen
Het volgende codevoorbeeld geeft de locatie van de gegevens te lezen en het pad voor de model-opslag-map waarin de uitvoer van het model is opgeslagen:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**UITVOER**

DateTime.DateTime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Import-bibliotheken
Importeer de benodigde bibliotheken door de volgende code:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Vooraf ingestelde Spark-context en PySpark magics
De PySpark-kernels die worden geleverd met Jupyter-notebooks hebben een vooraf ingestelde context. Zo hoeft u niet om in te stellen van de Spark of Hive-contexten expliciet voordat u begint met het werken met de toepassing die u ontwikkelt. Deze contexten zijn standaard voor u beschikbaar. Deze context zijn:

* SC - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt enkele vooraf gedefinieerde "kunt", die zijn speciale opdrachten die u met aanroepen kunt %%. Er zijn twee dergelijke opdrachten die worden gebruikt in deze voorbeelden van code.

* **%% Local** Geeft aan dat de code in volgende regels lokaal moet worden uitgevoerd. Code moet geldige Python-code.
* **%% SQL-o \<naam van variabele >** Voert een Hive-query uit op basis van de sqlContext. Als de parameter -o wordt doorgegeven, het resultaat van de query worden bewaard de %% lokale Python-context als een Pandas DataFrame.

Zie [kernels die beschikbaar zijn voor Jupyter-notebooks met Hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)voor meer informatie over de kernels voor Jupyter-notebooks en de vooraf gedefinieerde ' magics ' die ze bieden.

## <a name="data-ingestion-from-public-blob"></a>Opname van gegevens van een openbare blob:
De eerste stap in het data science process is om op te nemen van de gegevens uit bronnen waar deze zich in uw gegevens verkennen en de omgeving voor cloudmodellering bevindt worden geanalyseerd. Deze omgeving is Spark in dit scenario. Deze sectie bevat de code voor het voltooien van een reeks taken:

* het voorbeeld van gegevens moet worden gemodelleerd opnemen
* Lees in de invoergegevensset (opgeslagen als een bestand .tsv)
* indeling en alleen de gegevens opgeschoond
* maken en objecten (rdd's of gegevensframes) in het geheugen in de cache
* registreert u dit als een tijdelijk bestand-tabel in SQL-context.

Hier volgt de code voor opname van gegevens.

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


**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 276.62 seconden

## <a name="data-exploration--visualization"></a>Gegevens verkennen en visualisatie
Zodra de gegevens in Spark is ingesteld, is de volgende stap in het data science process dieper inzicht te krijgen van de gegevens via het verkennen en visualiseren. In deze sectie we de gegevens over taxi's is met behulp van SQL-query's controleren en tekenen van de doelvariabelen en toekomstige functies voor visueel inspecteren. We tekenen met name de frequentie van de passagiers telt het aantal in de gegevens over taxi's, de frequentie van de tip bedragen en hoe tips variëren per te betalingsbedrag en het type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Een histogram met aantal-frequenties passagier in het voorbeeld van gegevens over taxi's tekenen
Deze code en de volgende fragmenten gebruiken SQL magic om op te vragen van de voorbeeld- en lokale Magic-pakket voor de gegevens worden uitgezet.

* **SQL Magic (`%%sql`)** De HDInsight PySpark-kernel ondersteunt eenvoudige inline HiveQL-query's op de sqlContext. De (-o naam_variabele) argument als een Pandas DataFrame op de Jupyter-server de uitvoer van de SQL-query zich blijft voordoen. Dit betekent dat deze is beschikbaar in de lokale modus.
* De **`%%local` Magic** wordt gebruikt om code lokaal uit te voeren op de Jupyter-server. Dit is de hoofd knooppunt van het HDInsight-cluster. Normaal gesp roken gebruikt u `%%local` Magic nadat de `%%sql -o` Magic wordt gebruikt om een query uit te voeren. De parameter -o zou de uitvoer van de SQL-query lokaal behouden. Vervolgens activeert de `%%local` Magic de volgende set code fragmenten die lokaal worden uitgevoerd op basis van de uitvoer van de SQL-query's die lokaal zijn opgeslagen. De uitvoer wordt automatisch weergegeven nadat u de code uitvoeren.

Deze query worden de gegevens op telling van de passagiers opgehaald. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Deze code maakt een lokale gegevensframe van de query-uitvoer en de gegevens die zichtbaar zijn. Met `%%local` Magic maakt u een lokaal gegevens frame, `sqlResults`, dat kan worden gebruikt voor het uitzetten met matplotlib. 

<!-- -->

> [!NOTE]
> Deze magic PySpark wordt meerdere malen gebruikt in dit scenario. Als de hoeveelheid gegevens te groot is, moet u een steekproef voor het maken van een gegevensframe die passen in het lokale geheugen.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Hier volgt de code voor het tekenen van de trips door passagier tellingen

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

**UITVOER**

![Frequentie van trips op passagier telling](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

U kunt verschillende typen visualisaties (tabel, cirkel, lijn, vlak of staaf) selecteren met behulp van de menu knoppen van het **type** in het notitie blok. De balk plot wordt hier weergegeven.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Een histogram van de tip bedragen en hoe tip bedrag is afhankelijk van de passagiers aantal en de fare bedragen tekenen.
Gebruik een SQL-query om de voorbeeldgegevens...

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


Deze codecel gebruikt de SQL-query om de gegevens voor drie grafieken maken.

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


**UITVOER** 

![Tip bedragdistributie](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tip bedrag per passagier tellen](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip bedrag door fare bedrag](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Functie-engineering, transformatie en gegevens voorbereiden voor modellering
In deze sectie wordt beschreven en vindt de code voor procedures voor het voorbereiden van gegevens voor gebruik in ML-modellen. U leert hoe u de volgende taken uitvoeren:

* Een nieuwe functie maken door te partitioneren uur in verkeer tijd opslaglocaties
* Index- en categorische functies op hot coderen
* Gelabelde point-objecten voor invoer in ML-functies maken
* Een wille keurige subsampling van de gegevens maken en deze splitsen in trainings-en test sets
* Functie schalen
* Cacheobjecten in het geheugen

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Een nieuwe functie maken door te partitioneren keren dat verkeer naar opslaglocaties
Deze code laat zien hoe u een nieuwe functie maakt u verkeer tijden in bins en hoe u de resulterende gegevensframe in het geheugen in de cache. Opslaan in cache leidt tot verbeterde uitvoeringstijd waar Resilient Distributed Datasets (rdd's) en gegevensframes herhaaldelijk worden gebruikt. Dus cache we rdd's en gegevensframes in verschillende stadia tijdens deze stapsgewijze kennismaking.

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

### <a name="index-and-one-hot-encode-categorical-features"></a>Index- en één hot categorische functies coderen
Deze sectie wordt beschreven hoe om te indexeren of categorische functies voor invoer in de modelfuncties coderen. Het maken van modellering en functies van MLlib vereisen dat functies met categorische invoergegevens worden geïndexeerd of gecodeerde voorafgaand aan gebruik te voorspellen. 

Afhankelijk van het model moet u indexeren of ze op verschillende manieren te coderen. Bijvoorbeeld, Logistic en lineaire regressie modellen vereisen een hot-codering, waar, bijvoorbeeld, een functie met drie categorieën kan worden uitgebreid naar drie kolommen van de functie, met elke met 0 of 1, afhankelijk van de categorie van een opmerking. MLlib biedt [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) -functie voor het uitvoeren van één Hot encoding. Een kolom van het label indexen dit coderingsprogramma toegewezen aan een kolom van de binaire vectoren, met maximaal één één-waarde. Deze codering kunt algoritmen die verwacht dat de numerieke waarden functies, zoals logistieke regressie, moet worden toegepast op categorische functies.

Dit is de code om te indexeren en coderen categorische functies:

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


**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 3.14 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde point-objecten voor invoer in ML-functies maken
In deze sectie bevat code waarmee wordt uitgelegd hoe categorische gegevens als een gegevenstype gelabelde punt index en het coderen. Deze trans formatie bereidt tekst gegevens voor die moeten worden gebruikt voor het trainen en testen van MLlib en andere classificatie modellen. Gelabelde point-objecten zijn Resilient Distributed Datasets (RDD) geformatteerd op een manier die is nodig als invoer voor de meeste van ML-algoritmes in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, een compacte of een sparse, die is gekoppeld aan een label/antwoord.

Dit is de code om te indexeren en het coderen van tekstfuncties voor de binaire indeling.

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


Hier volgt de code voor het coderen en te indexeren categorische tekstfuncties voor lineaire regressie-analyse.

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
Deze code maakt een willekeurige steekproef van de gegevens (25% wordt hier gebruikt). Maar dit niet vereist voor dit voorbeeld vanwege de grootte van de gegevensset is, laten we zien hoe kunt u hier de gegevens een steekproef. Weet u hoe u kunt gebruiken voor uw eigen probleem indien nodig. Wanneer steek proeven groot zijn, kan de steek proef aanzienlijke tijd besparen bij het trainen van modellen. Volgende splitsen we het voorbeeld in een training-onderdeel (hier 75%) en testen deel (25% hier) in classificatie en modellering regressie te gebruiken.

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

**UITVOER**

Benodigde tijd voor het uitvoeren van de cel: 0,31 seconde

### <a name="feature-scaling"></a>Functie schalen
Functie schalen, ook wel bekend als de gegevens normaliseren, weet u zeker dat functies met veel betaald waarden zijn niet opgegeven overmatige wegen de servicedoelstelling functie. De code voor functie schaling maakt gebruik van de [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) om de functies te schalen op eenheids afwijking. Wordt geleverd door MLlib voor gebruik in de lineaire regressie met stochastische kleurovergang afkomst (SGD). SGD is een populaire algoritme voor het trainen van een breed scala aan andere machine learning-modellen, zoals overgegaan regressie of support vector machines (SVM).   

> [!TIP]
> We hebben het algoritme LinearRegressionWithSGD gevoelig zijn voor de functie schalen worden gevonden.   
> 
> 

Hier volgt de code op schaal variabelen voor gebruik met de regularized lineaire SGD algoritme.

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

Gebruikte tijd voor het uitvoeren van boven cel: 11.67 seconden

### <a name="cache-objects-in-memory"></a>Cacheobjecten in het geheugen
De gebruikte tijd voor training en het testen van ML-algoritmes kan worden teruggebracht door het frame invoergegevens objecten die worden gebruikt voor classificatie, regressie, en uitgebreide functies opslaan in cache.

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

Benodigde tijd voor het uitvoeren van de cel: 0,13 seconde

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Te voorspellen of een tip wordt betaald met binaire classificatie-modellen
Deze sectie wordt beschreven hoe gebruiken drie modellen voor de taak binaire classificatie van het voorspellen van al dan niet een tip voor een reis taxi wordt betaald. De modellen die zijn gepresenteerd zijn:

* Logistieke regressie 
* Willekeurige forest
* Kleurovergang Gradient Boosting structuren

Elke sectie met voorbeeldcode bouwen model is opgesplitst in stappen: 

1. **Trainings gegevens model leren** met één parameterset
2. **Model evaluatie** van een test gegevensverzameling met metrische gegevens
3. **Model opslaan** in BLOB voor toekomstig gebruik

We laten zien hoe kruisvalidatie (CV) doen met de parameter verstrekkende op twee manieren:

1. Het gebruik van **algemene** aangepaste code die kan worden toegepast op elk algoritme in MLlib en op alle parameter sets in een algoritme. 
2. Met de **pijplijn functie PySpark CrossValidator**. CrossValidator heeft enkele beperkingen voor Spark 1.5.0: 
   
   * Pijplijn modellen kunnen niet worden opgeslagen of bewaard voor toekomstig gebruik.
   * Kan niet worden gebruikt voor elke parameter in een model.
   * Kan niet worden gebruikt voor elke MLlib-algoritme.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Algemene cross-validatie en hyperparameter sweeping gebruikt met de algoritme voor logistieke regressie voor binaire classificatie
De code in deze sectie laat zien hoe u een logistiek regressie model traint, evalueert en opslaat met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) die voor spelt of er een tip wordt betaald voor een reis in de NYCe taxi en ritbedrag-gegevensset. Het model wordt getraind met behulp van cross-validatie (CV) en hyperparameter sweeping geïmplementeerd met aangepaste code die kan worden toegepast op een van de learning-algoritmen in MLlib.   

<!-- -->

> [!NOTE]
> De uitvoering van deze aangepaste CV-code kan enkele minuten duren.

<!-- -->

**Het logistiek-regressie model trainen met AVK en afstemming sweep**

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


**UITVOER**

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept:-0.0111216486893

Gebruikte tijd voor het uitvoeren van boven cel: 14.43 seconden

**Het binaire classificatie model evalueren met standaard metrische gegevens**

De code in deze sectie wordt beschreven hoe een model voor logistieke regressie op basis van een test-verzameling, met inbegrip van een diagram van de ROC-curve evalueren.

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


**UITVOER**

Gebied onder de pull-aanvraag 0.985336538462 =

Gebied onder ROC 0.983383274312 =

Samenvattende statistieken

Precisie 0.984174341679 =

Intrekken 0.984174341679 =

F1 Score = 0.984174341679

Gebruikte tijd voor het uitvoeren van boven cel: 2.67 seconden

**De ROC-curve uitzetten.**

De *predictionAndLabelsDF* is geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kunnen worden gebruikt om query's uit te voeren en resultaten uit te voeren in het sqlResults-gegevens frame voor het uitzetten. Dit is de code.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Hier is de code voor voorspellingen en de ROC-curve tekenen.

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


**UITVOER**

![Logistieke regressie ROC-curve voor algemene methode](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Model in een BLOB persistent maken voor toekomstig verbruik**

De code in deze sectie bevat informatie over het opslaan van het model voor logistieke regressie voor gebruik.

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


**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 34.57 seconden

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Gebruik de MLlib CrossValidator pijplijn functie met model voor logistieke regressie (elastische regressie)
De code in deze sectie laat zien hoe u een logistiek regressie model traint, evalueert en opslaat met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) die voor spelt of er een tip wordt betaald voor een reis in de NYCe taxi en ritbedrag-gegevensset. Het model wordt getraind met behulp van cross-validatie (CV) en hyperparameter sweeping geïmplementeerd met de functie van de pijplijn MLlib CrossValidator voor CV met parameteropschoning.   

<!-- -->

> [!NOTE]
> De uitvoering van deze code MLlib CV kan enkele minuten duren.

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

**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 107.98 seconden

**De ROC-curve uitzetten.**

De *predictionAndLabelsDF* is geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* kunnen worden gebruikt om query's uit te voeren en resultaten uit te voeren in het sqlResults-gegevens frame voor het uitzetten. Dit is de code.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Hier volgt de code voor het tekenen van de ROC-curve.

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


**UITVOER**

![Logistieke regressie ROC-curve met behulp van MLlib CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Willekeurige forest classificatie
De code in deze sectie wordt beschreven hoe trainen, evalueren en opslaan van een willekeurige forest regressie die al dan niet een tip wordt betaald voor een reis in de gegevensset NYC taxi reis- en fare worden voorspeld.

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


**UITVOER**

Gebied onder ROC 0.985336538462 =

Gebruikte tijd voor het uitvoeren van boven cel: 26.72 seconden

### <a name="gradient-boosting-trees-classification"></a>Kleurovergang Gradient boosting structuren classificatie
De code in deze sectie wordt beschreven hoe trainen, evalueren, en slaat u een kleurovergang Gradient boosting structuren model waarmee voorspeld of een tip wordt betaald voor een reis in de NYC taxi reis en ritbedrag gegevensset.

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

**UITVOER**

Gebied onder ROC 0.985336538462 =

Gebruikte tijd voor het uitvoeren van boven cel: 28.13 seconden

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Tip bedrag voorspellen met regressiemodellen (niet via CV)
Deze sectie wordt beschreven hoe gebruiken drie modellen voor de taak regressie: de tip betaalde bedrag voor een taxi reis op basis van andere functies tip voorspellen. De modellen die zijn gepresenteerd zijn:

* Lineaire regressie overgegaan
* Willekeurige forest
* Kleurovergang Gradient Boosting structuren

Deze modellen zijn beschreven in de inleiding. Elke sectie met voorbeeldcode bouwen model is opgesplitst in stappen: 

1. **Trainings gegevens model leren** met één parameterset
2. **Model evaluatie** van een test gegevensverzameling met metrische gegevens
3. **Model opslaan** in BLOB voor toekomstig gebruik   

<!-- -->

> [!NOTE] 
> Kruis validatie wordt niet gebruikt in combi natie met de drie regressie modellen in deze sectie, omdat deze in detail zijn opgenomen voor de logistieke regressie modellen. Een voorbeeld waarin wordt getoond hoe het gebruik van CV met elastische Net voor lineaire regressie is opgegeven in de bijlage van dit onderwerp.

<!-- -->

<!-- -->

> [!NOTE] 
> In onze ervaring kan er sprake zijn van problemen met de convergentie van LinearRegressionWithSGD-modellen en moeten de para meters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Schalen van variabelen aanzienlijk helpt bij de convergentie. Elastische net regressie, die wordt weergegeven in de bijlage aan dit onderwerp, kan ook worden gebruikt in plaats van LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD
De code in deze sectie leest over het gebruik van uitgebreide functies met het trainen van een lineaire regressie die gebruikmaakt van de toolkit leren met stochastische gradiëntdaling (SGD) om te optimaliseren en om te beoordelen, evalueren en slaat u het model in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kunnen er problemen met de convergentie van LinearRegressionWithSGD modellen en parameters moeten worden gewijzigd/geoptimaliseerd zorgvuldig voor het verkrijgen van een geldig model. Schalen van variabelen aanzienlijk helpt bij de convergentie.
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

**UITVOER**

Coefficients: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Intercept: 0.854507624459

RMSE = 1.23485131376

R-sqr 0.597963951127 =

Gebruikte tijd voor het uitvoeren van boven cel: 38.62 seconden

### <a name="random-forest-regression"></a>Willekeurige Forest regressie
De code in deze sectie wordt beschreven hoe trainen, evalueren en een willekeurige forestmodel die tip bedrag voor de NYC taxi-reisgegevens voorspelt op te slaan.   

<!-- -->

> [!NOTE]
> Kruisvalidatie met parameter verstrekkende met behulp van aangepaste code is opgenomen in de bijlage.

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

**UITVOER**

RMSE 0.931981967875 =

R-sqr 0.733445485802 =

Gebruikte tijd voor het uitvoeren van boven cel: 25.98 seconden

### <a name="gradient-boosting-trees-regression"></a>Kleurovergang Gradient boosting structuren regressie
De code in deze sectie wordt beschreven hoe trainen, evalueren en slaat u een kleurovergang Gradient boosting structuren model waarmee tip bedrag voor de reisgegevens NYC taxi worden voorspeld.

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


**UITVOER**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Gebruikte tijd voor het uitvoeren van boven cel: 20.9 seconden

**Ontwerp**

*tmp_results* is geregistreerd als een Hive-tabel in de vorige cel. De resultaten van de tabel worden uitgevoerd in het *sqlResults* -gegevens frame voor het uitzetten. Dit is de code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Hier volgt de code voor het tekenen van de gegevens met behulp van de Jupyter-server.

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

![Werkelijke-vs-voorspeld-tip-bedragen](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Bijlage: Extra regressie taken met de parameter sweeps met behulp van cross-validatie
Deze bijlage bevat de code wordt weergegeven hoe u met elastische net voor lineaire regressie CV doet en hoe u CV doet met parameteropschoning met behulp van aangepaste code voor willekeurige forest regressie.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Kruisvalidatie elastische net voor lineaire regressie gebruiken
De code in deze sectie toont hoe kruisvalidatie met elastische net voor lineaire regressie en hoe het model met de testgegevens evalueren.

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


**UITVOER**

Gebruikte tijd voor het uitvoeren van boven cel: 161.21 seconden

**Evalueren met R-SQR-metriek**

*tmp_results* is geregistreerd als een Hive-tabel in de vorige cel. De resultaten van de tabel worden uitgevoerd in het *sqlResults* -gegevens frame voor het uitzetten. Dit is de code

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Dit is de code voor het berekenen van R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**UITVOER**

R-sqr 0.619184907088 =

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kruisvalidatie met parameteropschoning met behulp van aangepaste code voor willekeurige forest regressie
De code in deze sectie toont hoe u kruisvalidatie met parameteropschoning met behulp van aangepaste code voor willekeurige forest regressie en het model met de testgegevens evalueren.

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


**UITVOER**

RMSE = 0.906972198262

R-sqr = 0.740751197012

Gebruikte tijd voor het uitvoeren van boven cel: 69.17 seconden

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Opschonen van de objecten uit het geheugen en afdrukken model locaties
Gebruik `unpersist()` om objecten in cache in het geheugen te verwijderen.

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


**UITVOER**

PythonRDD [122] op RDD op PythonRDD.scala: 43

\* * Uitvoer traject naar model bestanden dat moet worden gebruikt in het verbruiks notitieblok. ** Als u wilt gebruiken en te beoordelen van een onafhankelijke gegevensset, moet u kopiëren en plakken van deze namen in het notitieblok"verbruik'.

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**UITVOER**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Volgende stappen
Nu u een regressie en classificatie-modellen met de MlLib Spark hebt gemaakt, bent u klaar om te leren hoe u kunt beoordelen en evalueren van deze modellen.

**Model verbruik:** Voor meer informatie over het beoordelen en evalueren van de classificatie en regressie modellen die in dit onderwerp zijn gemaakt, raadpleegt u door [Spark ontwikkelde machine learning modellen te beoordelen en evalueren](spark-model-consumption.md).

