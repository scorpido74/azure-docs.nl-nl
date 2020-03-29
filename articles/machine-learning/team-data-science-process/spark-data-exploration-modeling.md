---
title: Data-exploratie en modellering met Spark - Team Data Science Process
description: Toont de mogelijkheden voor gegevensverkenning en modellering van de Spark MLlib-toolkit op Azure.
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
ms.openlocfilehash: 208f176ca942fb382ff2ed81d872602f7229b0a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718630"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Met Spark gegevens verkennen en modelleren

Deze walkthrough maakt gebruik van HDInsight Spark om gegevensexploratie en binaire classificatie- en regressiemodelleringstaken uit te voeren op een voorbeeld van de NYC-taxirit en de gegevensset tarief 2013.  Het leidt u door de stappen van het [Data Science-proces](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, met behulp van een HDInsight Spark-cluster voor verwerking en Azure blobs om de gegevens en de modellen op te slaan. Het proces verkent en visualiseert gegevens die zijn binnengebracht vanuit een Azure Storage Blob en bereidt vervolgens de gegevens voor om voorspellende modellen te bouwen. Deze modellen worden gebouwd met behulp van de Spark MLlib toolkit om binaire classificatie en regressie modellering taken uit te voeren.

* De **binaire classificatie** taak is om te voorspellen of een tip wordt betaald voor de reis. 
* De **regressietaak** is het voorspellen van de hoeveelheid tip op basis van andere tipfuncties. 

De modellen die we gebruiken omvatten logistieke en lineaire regressie, willekeurige bossen en gradiënt gebooste bomen:

* [Lineaire regressie met SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) is een lineair regressiemodel dat een SGD-methode (Stochastic Gradient Descent) gebruikt en voor optimalisatie en functieschaling om de betaalde tipbedragen te voorspellen. 
* [Logistieke regressie met LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) of "logit" regressie, is een regressiemodel dat kan worden gebruikt wanneer de afhankelijke variabele categorisch is om gegevensclassificatie te doen. LBFGS is een quasi-Newton optimalisatie algoritme dat de Broyden-Fletcher-Goldfarb-Shanno (BFGS) algoritme benadert met behulp van een beperkte hoeveelheid computergeheugen en dat op grote schaal wordt gebruikt in machine learning.
* [Willekeurige bossen](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) zijn ensembles van beslisbomen.  Ze combineren veel beslisbomen om het risico op overfitting te verminderen. Willekeurige forests worden gebruikt voor regressie en classificatie en kunnen categorische functies verwerken en kunnen worden uitgebreid tot de classificatie-instelling voor meerdere klassen. Ze vereisen geen functieschaling en zijn in staat om niet-lineariteiten en functieinteracties vast te leggen. Willekeurige bossen zijn een van de meest succesvolle machine learning-modellen voor classificatie en regressie.
* [Gradiënt gebooste bomen](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) zijn ensembles van beslissingsbomen. GBTS traint beslissingsbomen iteratief om een verliesfunctie te minimaliseren. GBTS wordt gebruikt voor regressie en classificatie en kan categorische functies verwerken, vereisen geen functieschaling en zijn in staat om niet-lineariteiten en functieinteracties vast te leggen. Ze kunnen ook worden gebruikt in een multiclass-classificatie-instelling.

De modelleringsstappen bevatten ook code die laat zien hoe u elk type model traint, evalueert en opslaat. Python is gebruikt om de oplossing te coderen en om de relevante plots weer te geven.   

> [!NOTE]
> Hoewel de Spark MLlib toolkit is ontworpen om te werken op grote datasets, een relatief kleine steekproef (~ 30 Mb met behulp van 170K rijen, ongeveer 0,1% van de oorspronkelijke NYC dataset) wordt hier gebruikt voor het gemak. De oefening die hier wordt gegeven, loopt efficiënt (in ongeveer 10 minuten) op een HDInsight-cluster met 2 werkknooppunten. Dezelfde code, met kleine wijzigingen, kan worden gebruikt om grotere gegevenssets te verwerken, met de juiste wijzigingen voor het incachen van gegevens in het geheugen en het wijzigen van de clustergrootte.
> 
> 

## <a name="prerequisites"></a>Vereisten
U hebt een Azure-account en een Spark 1.6 (of Spark 2.0) HDInsight-cluster nodig om deze walkthrough te voltooien. Zie het [overzicht van Data Science met Spark op Azure HDInsight](spark-overview.md) voor instructies over hoe u aan deze vereisten voldoen. Dat onderwerp bevat ook een beschrijving van de NYC 2013 Taxi gegevens hier gebruikt en instructies over hoe code uit te voeren van een Jupyter notebook op de Spark cluster. 

## <a name="spark-clusters-and-notebooks"></a>Spark-clusters en notitieblokken
Setup stappen en code zijn voorzien in deze walkthrough voor het gebruik van een HDInsight Spark 1.6. Maar Jupyter-laptops zijn beschikbaar voor zowel HDInsight Spark 1.6- als Spark 2.0-clusters. Een beschrijving van de notitieblokken en links naar hen zijn voorzien in de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub repository met hen. Bovendien is de code hier en in de gekoppelde notitieblokken generiek en moet het werken op een Spark-cluster. Als u HDInsight Spark niet gebruikt, kunnen de clusterinstellingen en beheerstappen enigszins afwijken van wat hier wordt weergegeven. Voor het gemak, hier zijn de links naar de Jupyter notebooks voor Spark 1.6 (te draaien in de pySpark kernel van de Jupyter Notebook server) en Spark 2.0 (worden uitgevoerd in de pySpark3 kernel van de Jupyter Notebook server):

### <a name="spark-16-notebooks"></a>Spark 1.6-notitieblokken

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)Biedt informatie over het uitvoeren van gegevensverkenning, modellering en scoren met verschillende algoritmen.

### <a name="spark-20-notebooks"></a>Spark 2.0-notitieblokken
De regressie- en classificatietaken die worden geïmplementeerd met een Spark 2.0-cluster bevinden zich in afzonderlijke notitieblokken en het classificatienotitieblok gebruikt een andere gegevensset:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)Dit bestand biedt informatie over het uitvoeren van gegevensverkenning, modellering en scoren in Spark 2.0-clusters met behulp van de NYC Taxi-reis en tariefgegevensset [die hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)worden beschreven. Deze notebook kan een goed uitgangspunt zijn voor het snel verkennen van de code die we hebben verstrekt voor Spark 2.0. Voor een meer gedetailleerde notebook analyseert de NYC Taxi gegevens, zie de volgende notebook in deze lijst. Bekijk de notities die deze lijst volgen en die deze notitieblokken vergelijkt. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Dit bestand laat zien hoe je data wrangling (Spark SQL en dataframe operaties), exploratie, modellering en scoren met behulp van de NYC Taxi reis en tarief data-set [hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data)beschreven uit te voeren .
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Dit bestand laat zien hoe je datawrangling (Spark SQL en dataframe-bewerkingen) uitvoeren, verkenning, modellering en scoren met behulp van de bekende Airline On-time vertrekdataset uit 2011 en 2012. We hebben de gegevensset van de luchtvaartmaatschappij geïntegreerd met de weergegevens van de luchthaven (bijvoorbeeld windsnelheid, temperatuur, hoogte enz.) voorafgaand aan het modelleren, zodat deze weerfuncties in het model kunnen worden opgenomen.

<!-- -->

> [!NOTE]
> De gegevensset van de luchtvaartmaatschappij is toegevoegd aan de Spark 2.0-notitieblokken om het gebruik van classificatiealgoritmen beter te illustreren. Zie de volgende links voor informatie over de vluchtgegevensset voor vertrek van luchtvaartmaatschappijen en de gegevensset weer:
> 
> - Vluchtgegevens op tijd:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Luchthaven weergegevens:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> De Spark 2.0-laptops op de gegevenssets voor vertraging van de nyc-taxi en luchtvaartmaatschappij kunnen 10 minuten of meer duren (afhankelijk van de grootte van uw HDI-cluster). De eerste notebook in de bovenstaande lijst toont vele aspecten van de gegevensverkenning, visualisatie en ML model training in een notebook die minder tijd kost om te draaien met down-sampled NYC dataset, waarin de taxi en tarief bestanden zijn vooraf toegetreden: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Deze notebook duurt een veel kortere tijd om te voltooien (2-3 minuten) en kan een goed uitgangspunt voor het snel verkennen van de code die we hebben verstrekt voor Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> De onderstaande beschrijvingen zijn gerelateerd aan het gebruik van Spark 1.6. Voor Spark 2.0-versies gebruikt u de hierboven beschreven en gekoppelde notitieblokken. 

<!-- -->

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotheken importeren
Voor het instellen zijn ook noodzakelijke bibliotheken nodig. Stel de vonkcontext in en importeer noodzakelijke bibliotheken met de volgende code:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Vooraf ingestelde Spark-context en PySpark-magie
De PySpark-kernels die zijn voorzien van Jupyter-notebooks hebben een vooraf ingestelde context. U hoeft de Spark- of Hive-contexten dus niet expliciet in te stellen voordat u aan de slag gaat met de toepassing die u ontwikkelt. Deze contexten zijn standaard voor u beschikbaar. Deze contexten zijn:

* sc - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt een aantal vooraf gedefinieerde magics, die speciale opdrachten zijn die je aanroepen met %%. Er zijn twee van dergelijke commando's die worden gebruikt in deze code monsters.

* **%%lokaal** Hiermee geeft u op dat de code in de volgende regels lokaal moet worden uitgevoerd. Code moet een geldige Python-code zijn.
* **%%sql -o \<variabele naam>** Hiermee wordt een Hive-query uitgevoerd tegen de sqlContext. Als de parameter -o wordt doorgegeven, blijft het resultaat van de query bestaan in de context %%local Python als een Pandas DataFrame.

Zie [Kernels beschikbaar voor Jupyter-laptops met HDInsight Spark Linux-clusters op HDInsight voor](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)meer informatie over Jupyter-notebookkernels en de vooraf gedefinieerde "magics".

## <a name="data-ingestion-from-public-blob"></a>Gegevensopname van openbare blob
De eerste stap in het data science-proces is het innemen van de gegevens die moeten worden geanalyseerd uit bronnen waar zich bevindt in uw data-exploratie- en modelleringsomgeving. De omgeving is Spark in deze walkthrough. Deze sectie bevat de code om een reeks taken te voltooien:

* inname van het te modelleren gegevensmonster
* lezen in de invoergegevensset (opgeslagen als .tsv-bestand)
* de gegevens opmaken en schoonmaken
* objecten (RdD's of gegevensframes) maken en cachen in het geheugen
* registreren als een temp-tabel in SQL-context.

Hier is de code voor het innemen van gegevens.

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

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 51,72 seconden

## <a name="data-exploration--visualization"></a>Visualisatie van gegevensverkenning & visualisatie
Zodra de gegevens in Spark zijn gebracht, is de volgende stap in het data science-proces om dieper inzicht te krijgen in de gegevens door middel van exploratie en visualisatie. In deze sectie onderzoeken we de taxigegevens met SQL-query's en plotten we de doelvariabelen en toekomstige functies voor visuele inspectie. Concreet zetten we de frequentie van het aantal passagiers in taxiritten, de frequentie van tipbedragen en hoe tips variëren per betalingsbedrag en type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Plot een histogram van passagierstellingfrequenties in de steekproef van taxireizen
Deze code en de daaropvolgende fragmenten gebruiken SQL-magie om het voorbeeld en de lokale magie op te vragen om de gegevens in kaart te brengen.

* **SQL-magie`%%sql`( )** De HDInsight PySpark kernel ondersteunt eenvoudige inline HiveQL-query's tegen de sqlContext. Het argument (-o VARIABLE_NAME) blijft de uitvoer van de SQL-query als een Pandas DataFrame op de Jupyter-server. Met deze instelling is de uitvoer beschikbaar in de lokale modus.
* De ** `%%local` magie** wordt gebruikt om code lokaal uit te voeren op de Jupyter-server, de headnode van het HDInsight-cluster. Meestal gebruikt `%%local` u magie in `%%sql` combinatie met de magie met -o parameter. De parameter -o blijft de uitvoer van de SQL-query lokaal aanhouden en vervolgens zou lokale magie %%de volgende set codefragment activeren om lokaal uit te voeren tegen de uitvoer van de SQL-query's die lokaal worden aangehouden

De uitvoer wordt automatisch gevisualiseerd nadat u de code hebt uitgevoerd.

Deze query haalt de reizen op per aantal passagiers. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Met deze code wordt een lokaal gegevensframe op basis van de queryuitvoer uitgevoerd en worden de gegevens in een stand hersteld. De `%%local` magie creëert een lokaal `sqlResults`dataframe, dat kan worden gebruikt voor het plotten met matplotlib. 

> [!NOTE]
> Deze PySpark magie wordt meerdere keren gebruikt in deze walkthrough. Als de hoeveelheid gegevens groot is, moet u een voorbeeld nemen om een gegevensframe te maken dat in het lokale geheugen past.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Hier is de code om de reizen plot door passagierstellingen

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

**Output:**

![Reisfrequentie per aantal passagiers](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

U verschillende typen visualisaties (tabel, cirkel, lijn, gebied of balk) selecteren met behulp van de menuknoppen **Type** in het notitieblok. De Bar plot is hier te zien.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Plot een histogram van tip bedragen en hoe tip bedrag varieert per passagier tellen en tarief bedragen.
Gebruik een SQL-query om gegevens te samplen.

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


Deze codecel gebruikt de SQL-query om drie plots van de gegevens te maken.

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


**Output:** 

![Tipbedragverdeling](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tipbedrag per aantal passagiers](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tipbedrag per tariefbedrag](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Functie-engineering, transformatie en gegevensvoorbereiding voor modellering
In deze sectie wordt de code beschreven en bevat u de code voor procedures die worden gebruikt om gegevens voor te bereiden op gebruik in ML-modellering. Het laat zien hoe u de volgende taken uitvoert:

* Een nieuwe functie maken door uren in de buckets van het verkeer te plaatsen
* Categorische functies indexeren en coderen
* Gelabelde puntobjecten maken voor invoer in ML-functies
* Maak een willekeurige subsampling van de gegevens en splits deze op in trainings- en testsets
* Functie schalen
* Cacheobjecten in het geheugen

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Een nieuwe functie maken door uren in de buckets van het verkeer te plaatsen
Deze code laat zien hoe u een nieuwe functie maakt door uren in de buckets van de verkeerstijd te plaatsen en vervolgens hoe u het resulterende gegevensframe in het geheugen in de cache opslaan. Waar Resilient Distributed Datasets (RDD's) en dataframes herhaaldelijk worden gebruikt, leidt caching tot verbeterde uitvoeringstijden. Daarom cachen we RDD's en dataframes in verschillende fasen van de walkthrough. 

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

**Output:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Categorische functies indexeren en coderen voor invoer in modelleringsfuncties
In deze sectie ziet u hoe u categorische functies indexeert of codeert voor invoer in de modelleringsfuncties. De modellerings- en voorspellende functies van MLlib vereisen functies met categorische invoergegevens die vóór gebruik moeten worden geïndexeerd of gecodeerd. Afhankelijk van het model moet u ze op verschillende manieren indexeren of coderen:  

* **Op bomen gebaseerde modellering** vereist dat categorieën worden gecodeerd als numerieke waarden (een functie met drie categorieën kan bijvoorbeeld worden gecodeerd met 0, 1, 2). Dit algoritme wordt geleverd door de [StringIndexer-functie van](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) MLlib. Deze functie codeert een tekenreekskolom met labels tot een kolom met labelindexen die zijn geordend op basis van labelfrequenties. Hoewel geïndexeerd met numerieke waarden voor invoer en gegevensverwerking, kunnen de op bomen gebaseerde algoritmen worden opgegeven om ze op de juiste manier als categorieën te behandelen. 
* **Logistieke en lineaire regressiemodellen** vereisen eenhot codering, waarbij bijvoorbeeld een functie met drie categorieën kan worden uitgebreid tot drie functiekolommen, waarbij elk 0 of 1 bevat, afhankelijk van de categorie van een observatie. MLlib biedt [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) functie om one-hot encoding te doen. Deze encoder brengt een kolom met labelindexen in kaart met een kolom van binaire vectoren, met maximaal één waarde. Met deze codering kunnen algoritmen die numerieke waardekenmerken verwachten, zoals logistieke regressie, worden toegepast op categorische functies.

Hier is de code om categorische functies te indexeren en te coderen:

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

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 1,28 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde puntobjecten maken voor invoer in ML-functies
Deze sectie bevat code die laat zien hoe categorische tekstgegevens te indexeren als een gelabeld puntgegevenstype en deze te coderen, zodat deze kan worden gebruikt om de logistieke regressie van MLlib en andere classificatiemodellen te trainen en te testen. Gelabelde puntobjecten zijn Resilient Distributed Datasets (RDD) opgemaakt op een manier die nodig is als invoergegevens door de meeste ML-algoritmen in MLlib. Een [gelabeld punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, dicht of schaars, gekoppeld aan een label/respons.  

Deze sectie bevat code die laat zien hoe [categorische](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) tekstgegevens te indexeren als een gelabeld puntgegevenstype en deze te coderen, zodat deze kan worden gebruikt om de logistieke regressie van MLlib en andere classificatiemodellen te trainen en te testen. Gelabelde puntobjecten zijn Resilient Distributed Datasets (RDD) bestaande uit een label (doel/responsvariabele) en functievector. Dit formaat is nodig als input door veel ML-algoritmen in MLlib.

Hier is de code om tekstfuncties te indexeren en te coderen voor binaire classificatie.

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
Deze code maakt een willekeurige steekproef van de gegevens (25% wordt hier gebruikt). Hoewel dit bijvoorbeeld niet nodig is vanwege de grootte van de gegevensset, laten we zien hoe u hier samplen, zodat u weet hoe u deze gebruiken voor uw eigen probleem wanneer dat nodig is. Wanneer monsters groot zijn, kan bemonstering veel tijd besparen tijdens trainingsmodellen. Vervolgens splitsen we de steekproef in een trainingsonderdeel (75% hier) en een testonderdeel (25% hier) om te gebruiken in classificatie- en regressiemodellering.

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

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 0,24 seconde

### <a name="feature-scaling"></a>Functie schalen
Feature scaling, ook wel bekend als data normalisatie, verzekert dat functies met op grote schaal uitbetaald waarden worden niet gegeven overmatige wegen in de objectieve functie. De code voor functieschaling gebruikt de [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) om de functies te schalen naar variantie van de eenheid. Het wordt geleverd door MLlib voor gebruik in lineaire regressie met Stochastic Gradient Descent (SGD), een populair algoritme voor het trainen van een breed scala van andere machine learning-modellen, zoals geregulariseerde regressies of ondersteuning vector machines (SVM).

> [!NOTE]
> We hebben het LinearRegressionWithSGD-algoritme gevoelig gevonden voor feature scaling.
> 
> 

Hier is de code om variabelen te schalen voor gebruik met het geregulariseerde lineaire SGD-algoritme.

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

**Output:**

Tijd die nodig is om boven de cel uit te voeren: 13,17 seconden

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

**Output:** 

Tijd die nodig is om boven de cel uit te voeren: 0,15 seconde

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Voorspellen of een fooi wordt betaald met binaire classificatiemodellen
In dit gedeelte ziet u hoe het gebruik van drie modellen voor de binaire classificatietaak om te voorspellen of een fooi al dan niet wordt betaald voor een taxirit. De gepresenteerde modellen zijn:

* Geregulariseerde logistieke regressie 
* Willekeurig forestmodel
* Verloop stimuleren bomen

Elke sectie van de modelbouwcode wordt opgesplitst in stappen: 

1. **Trainingsgegevens modelleren** met één parameterset
2. **Evaluatie van het model** op een testgegevensset met statistieken
3. **Model opslaan** in blob voor toekomstig verbruik

### <a name="classification-using-logistic-regression"></a>Classificatie met behulp van logistieke regressie
De code in deze sectie laat zien hoe je een logistiek regressiemodel traint, evalueert en opslaat met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) dat voorspelt of er al dan niet een fooi wordt betaald voor een reis in de NYC-taxirit en tariefgegevensset.

**Train het logistieke regressiemodel met CV en hyperparameter vegen**

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


**Output:** 

Coëfficiënten: [0,0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0,111823113101, -0,32460912762, -0,204549780032, -1,36499216354, 0,591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Onderschepping: -0,0111216486893

Tijd die nodig is om boven de cel uit te voeren: 14,43 seconden

**Het binaire classificatiemodel evalueren met standaardstatistieken**

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

**Output:** 

Gebied onder PR = 0,985297691373

Gebied onder ROC = 0,983714670256

Overzichtsstatistieken

Precisie = 0,984304060189

Terugroepactie = 0,984304060189

F1 Score = 0,984304060189

Tijd die nodig is om boven de cel uit te voeren: 57,61 seconden

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


**Output:**

![Logistieke regressie ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Willekeurige bosclassificatie
De code in deze sectie laat zien hoe je een willekeurig bosmodel traint, evalueert en opslaat dat voorspelt of er al dan niet een fooi wordt betaald voor een reis in de NYC-taxirit en tariefgegevensset.

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

**Output:**

Gebied onder ROC = 0,985297691373

Tijd die nodig is om boven de cel uit te voeren: 31,09 seconden

### <a name="gradient-boosting-trees-classification"></a>Verloop stimuleren bomen classificatie
De code in deze sectie laat zien hoe je een verloop stimuleren bomen model dat voorspelt of een tip wordt betaald voor een reis in de NYC taxirit en tarief dataset op te slaan.

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


**Output:**

Gebied onder ROC = 0,985297691373

Tijd die nodig is om boven de cel uit te voeren: 19,76 seconden

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Tipbedragen voor taxiritten met regressiemodellen voorspellen
In dit gedeelte ziet u hoe het gebruik van drie modellen voor de regressietaak het voorspellen van het bedrag van de fooi die is betaald voor een taxirit op basis van andere tipfuncties. De gepresenteerde modellen zijn:

* Geregulariseerde lineaire regressie
* Willekeurig bos
* Verloop stimuleren bomen

Deze modellen werden beschreven in de inleiding. Elke sectie van de modelbouwcode wordt opgesplitst in stappen: 

1. **Trainingsgegevens modelleren** met één parameterset
2. **Evaluatie van het model** op een testgegevensset met statistieken
3. **Model opslaan** in blob voor toekomstig verbruik

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD
De code in deze sectie laat zien hoe geschaalde functies worden gebruikt om een lineaire regressie te trainen die stochastische gradiëntdescent (SGD) gebruikt voor optimalisatie en hoe u het model scoren, evalueren en opslaan in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kunnen er problemen zijn met de convergentie van LinearRegressionWithSGD-modellen en moeten parameters zorgvuldig worden gewijzigd/geoptimaliseerd voor het verkrijgen van een geldig model. Het schalen van variabelen helpt aanzienlijk bij convergentie. 
> 
> 

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

**Output:**

Coëfficiënten: [0,0045767509917, -0,0226314167349, -0,0191910355236, 0,246793409578, 0,312047890459, 0,359634405999, 0,0092869253981, -0,000987181489428, -0,0888306617845, 0,056937621553, 0,115519551711, 0,149250164995, - 0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Onderschepping: 0,853872718283

RMSE = 1,24190115863

R-sqr = 0,608017146081

Tijd die nodig is om boven de cel uit te voeren: 58.42 seconden

### <a name="random-forest-regression"></a>Willekeurige forestregressie
De code in deze sectie laat zien hoe te trainen, evalueren en opslaan van een willekeurige bos regressie die tip bedrag voorspelt voor de NYC taxi reis gegevens.

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

**Output:**

RMSE = 0,891209218139

R-sqr = 0,759661334921

Tijd die nodig is om boven de cel uit te voeren: 49,21 seconden

### <a name="gradient-boosting-trees-regression"></a>Verloop stimuleren bomen regressie
De code in deze sectie laat zien hoe te trainen, evalueren en opslaan van een gradiënt stimuleren bomen model dat tip bedrag voorspelt voor de NYC taxi reis gegevens.

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

**Output:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

Tijd die nodig is om boven de cel uit te voeren: 34,52 seconden

**Plot**

*tmp_results* is geregistreerd als hive-tabel in de vorige cel. Resultaten uit de tabel zijn uitvoer naar het *sqlResults-gegevensframe* voor plotten. Hier is de code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Hier is de code om de gegevens plot met behulp van de Jupyter server.

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


**Output:**

![Werkelijke-vs-voorspelde-tip-bedragen](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Objecten uit het geheugen opschonen
Hiermee `unpersist()` u objecten verwijderen die in het geheugen zijn opgeslagen.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Opslaglocaties van de modellen registreren voor verbruik en score
Als u een onafhankelijke gegevensset wilt gebruiken en scoren die in het onderwerp Score is beschreven [en spark-built machine learning-modellen](spark-model-consumption.md) wilt evalueren, moet u deze bestandsnamen kopiëren en plakken met de opgeslagen modellen die hier zijn gemaakt in het verbruiks-Jupyter-notitieblok. Hier is de code om de paden uit te printen om bestanden te modelleren die u daar nodig hebt.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Output**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Volgende stappen
Nu u regressie- en classificatiemodellen hebt gemaakt met de Spark MlLib, bent u klaar om te leren scoren en evalueren van deze modellen. De geavanceerde data-exploratie en modellering notebook duikt dieper in met inbegrip van cross-validatie, hyper-parameter vegen, en model evaluatie. 

**Modelverbruik:** Zie [Spark-built machine learning-modellen beoordelen voor](spark-model-consumption.md)meer informatie over het scoren en evalueren van de classificatie- en regressiemodellen die in dit onderwerp zijn gemaakt.

**Cross-validatie en hyperparameter vegen:** Zie [Geavanceerde gegevens exploratie en modellering met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met behulp van cross-validatie en hyper-parameter vegen

