---
title: Gegevens verkennen en modelleren met Spark - Team Data Science Process
description: Brengt de mogelijkheden om gegevens verkennen en modelleren van de toolkit MLlib Spark op Azure.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718630"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Met Spark gegevens verkennen en modelleren

In dit scenario maakt gebruik van HDInsight Spark gegevens verkennen doen en binaire classificatie- en regressiemodellen taken modelleren van een steekproef van de NYC taxi reis en ritbedrag 2013-gegevensset.  Dit leidt u door de stappen van de [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, met behulp van een HDInsight Spark-cluster voor verwerkings- en Azure-blobs voor het opslaan van de gegevens en de modellen. Het proces worden verkend en visualiseert gegevens voorgelegd aan van een Azure Storage-Blob en vervolgens de gegevens voor het bouwen van voorspellende modellen voorbereid. Deze modellen zijn bouwen met behulp van de toolkit Spark MLlib binaire classificatie- en regressiemodellen modelleren om taken te voeren.

* De **binaire classificatie** taak is om te voorspellen of een tip voor de reis wordt betaald. 
* De **regressie** taak bestaat uit het voorspellen van het bedrag van de tip op basis van andere tip-functies. 

De modellen we gebruiken bevatten logistieke en lineaire regressie, willekeurige forests en kleurovergang boosted structuren:

* [Lineaire regressie met SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) is een lineair regressiemodel die gebruikmaakt van een methode stochastische kleurovergang afkomst (SGD) en optimalisatie en functie schalen om te voorspellen van de tip-bedragen betaald. 
* [Logistieke regressie met LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) of "logit" regressie, wordt een regressiemodel dat kan worden gebruikt wanneer de afhankelijke variabele categorische doen gegevensclassificatie is. LBFGS is een quasi toegepast optimalisatie-algoritme dat benadert de Broyden – Fletcher – Goldfarb – Shanno (BFGS)-algoritme met behulp van een beperkte hoeveelheid van het geheugen van de computer en die wordt veel gebruikt in machine learning.
* [Willekeurige forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) ensembles van beslissingsstructuren zijn.  Ze combineren veel beslissingsstructuren om het risico te beperken. Willekeurige forests worden gebruikt voor regressie en classificatie en categorische functies kunnen worden verwerkt en kunnen worden uitgebreid naar de instelling voor multiklassen classificatie. Ze vereisen geen functie schaalaanpassing en kunnen niet-mogelijkheid tot vastleggen en functie van interacties. Willekeurige forests vormen een van de meest succesvolle machine learning-modellen voor classificatie- en regressiemodellen.
* GBTS ( [Gradient, boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) zijn ensembles van beslissings structuren. GBTSer boom structuren van de trein om een verlies functie te minimaliseren. GBTS wordt gebruikt voor regressie en classificatie, en kan categorische-functies afhandelen, geen functie schaaling vereisen en niet-lineaire en functie-interacties kunnen vastleggen. Ze kunnen ook worden gebruikt in een multiklasse-classificatie.

De stappen modellering bevatten ook code waarin wordt getoond hoe om te trainen, evalueren en elk type model opslaan. Python is gebruikt om de oplossing en om de relevante grafieken weer te geven.   

> [!NOTE]
> Hoewel de toolkit Spark MLlib is ontworpen voor gebruik op grote gegevenssets, wordt hier een relatief klein aantal (ongeveer 30 Mb met behulp van 170K rijen, ongeveer 0,1% van de oorspronkelijke NYC gegevensset) gebruikt voor het gemak. De hier getoonde oefening efficiënt (in ongeveer 10 minuten) op een HDInsight-cluster met 2 worker-knooppunten wordt uitgevoerd. Dezelfde code, met kleine wijzigingen kan worden gebruikt voor het verwerken van grotere-gegevenssets, met de juiste wijzigingen voor gegevens in het geheugen cachen en de clustergrootte te wijzigen.
> 
> 

## <a name="prerequisites"></a>Vereisten
U moet een Azure-account en een Spark 1.6 (of Spark 2.0) HDInsight-cluster voor dit scenario. Zie de [overzicht van Gegevenswetenschap met Spark op Azure HDInsight](spark-overview.md) voor instructies over het voldoen aan deze vereisten. Dit onderwerp bevat ook een beschrijving van de hier gebruikte NYC 2013 Taxi-gegevens en instructies over hoe u code uitvoeren van een Jupyter-notebook in Spark-cluster. 

## <a name="spark-clusters-and-notebooks"></a>Spark-clusters en -laptops
Installatiestappen uit en code vindt u in dit scenario voor het gebruik van een HDInsight Spark 1.6. Maar Jupyter-notebooks voor HDInsight Spark 1.6- en Spark 2.0-clusters worden geleverd. Een beschrijving van de laptops en koppelingen naar deze vindt u in de [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) voor de GitHub-opslagplaats die ze bevatten. Bovendien is de code hier en in de gekoppelde notebooks is algemeen en werkt op een Spark-cluster. Als u niet met behulp van HDInsight Spark, configureren van het cluster en beheertaken uit kunnen enigszins afwijken van wat wordt hier weergegeven. Hier volgen de koppelingen naar de Jupyter-notebooks voor Spark 1.6 (om te worden uitgevoerd in de pySpark-kernel van de Jupyter-Notebook-server) en Spark 2.0 (om te worden uitgevoerd in de kernel pySpark3 van de Jupyter-Notebook-server) voor het gemak:

### <a name="spark-16-notebooks"></a>Spark 1.6-laptops

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): bevat informatie over het uitvoeren van de gegevens verkennen, modelleren en scoren met diverse verschillende algoritmes.

### <a name="spark-20-notebooks"></a>Spark 2.0-laptops
De regressie en classificatie van taken die worden geïmplementeerd met behulp van een cluster van Spark 2.0 in afzonderlijke laptops zijn en de classificatie-notitieblok maakt gebruik van een andere gegevensset:

- [Spark2.0-pySpark3-machine-Learning-Data-Science-Spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): dit bestand bevat informatie over het uitvoeren van de gegevens verkennen, modellerings- en scoring in Spark 2.0-clusters met behulp van de fietstocht NYC Taxi en fare gegevensset-beschreven [hier](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Dit notitieblok mogelijk een goed uitgangspunt voor het snel verkennen van de code die we voor Spark 2.0 hebt opgegeven. Voor een meer gedetailleerde notebook de gegevens over taxi's NYC analyseert, Zie de volgende notebook in deze lijst. Zie de opmerkingen na deze lijst waarin deze notitie blokken worden vergeleken. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): dit bestand ziet u hoe u data wrangling (Spark SQL- en dataframe bewerkingen), gegevensonderzoek, modelleren en scoren met behulp van de NYC Taxi reis en fare set gegevens die worden beschreven uitvoeren [hier ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): dit bestand ziet u hoe u data wrangling (Spark SQL- en dataframe bewerkingen), gegevensonderzoek, modelleren en scoren met behulp van de bekende luchtvaartmaatschappij op tijd vertrek uitvoeren de gegevensset in 2011 en 2012. We hebben de gegevensset van de luchtvaart maatschappij geïntegreerd met de weers gegevens van de lucht haven (bijvoorbeeld windspeed, Tempe ratuur, hoogte enz.) voordat ze worden gemodelleerd, zodat deze weers functies kunnen worden opgenomen in het model.

<!-- -->

> [!NOTE]
> De gegevensset luchtvaartmaatschappij is toegevoegd aan de notebooks Spark 2.0 ter illustratie van het gebruik van bestandsclassificatie-algoritmen beter. Zie de volgende koppelingen voor meer informatie over luchtvaartmaatschappij op tijd vertrek gegevensset en de gegevensset weer:
> 
> - Luchtvaartmaatschappij op tijd vertrek gegevens: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Weergegevens luchthaven: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> De notebooks Spark 2.0 op de NYC taxi en luchtvaartmaatschappij flight vertraging-gegevenssets kunnen 10 minuten of langer om uit te voeren (afhankelijk van de grootte van het HDI-cluster) duren. Het eerste notitieblok in de bovenstaande lijst ziet u veel aspecten van de gegevens verkennen, training in een notitieblok dat minder tijd om uit te voeren naar beneden steekproef NYC gegevens is ingesteld kost, waarin de bestanden over taxi's en fare zijn vooraf is toegevoegd aan het model met visualisatie en ML: [ Spark2.0-pySpark3-machine-Learning-Data-Science-Spark-Advanced-Data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) dit notitieblok neemt een veel kortere tijd om te voltooien (2-3 minuten) en kan worden een goed startpunt om snel de die we hebben opgegeven te verkennen voor Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> De onderstaande beschrijvingen zijn gerelateerd aan met behulp van Spark 1.6. Gebruik de notebooks beschreven en bovenstaande voor Spark 2.0-versies. 

<!-- -->

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Import-bibliotheken
Instellen is ook vereist benodigde bibliotheken importeren. Spark-context instellen en importeren van de vereiste bibliotheken door de volgende code:

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
De PySpark-kernels die worden geleverd met Jupyter-notebooks hebben een vooraf ingestelde context. Zo hoeft u niet om in te stellen van de Spark of Hive-contexten expliciet voordat u begint met het werken met de toepassing die u ontwikkelt. Deze contexten zijn standaard voor u beschikbaar. Deze context zijn:

* SC - voor Spark 
* sqlContext - voor Hive

De PySpark-kernel biedt enkele vooraf gedefinieerde "kunt", die zijn speciale opdrachten die u met aanroepen kunt %%. Er zijn twee dergelijke opdrachten die worden gebruikt in deze voorbeelden van code.

* **%% lokale** geeft aan dat de code in de volgende regels is moet lokaal worden uitgevoerd. Code moet geldige Python-code.
* **%% SQL-o \<naam van variabele >** Voert een Hive-query uit op basis van de sqlContext. Als de parameter -o wordt doorgegeven, het resultaat van de query worden bewaard de %% lokale Python-context als een Pandas DataFrame.

Zie [kernels die beschikbaar zijn voor Jupyter-notebooks met Hdinsight Spark Linux-clusters in hdinsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md)voor meer informatie over Jupyter notebook-kernels en de vooraf gedefinieerde ' magics '.

## <a name="data-ingestion-from-public-blob"></a>Opname van gegevens van een openbare blob
De eerste stap in het data science process is om op te nemen van de gegevens kunnen worden geanalyseerd van bronnen waar is zich bevindt in de omgeving van uw gegevens verkennen en modelleren. De omgeving is Spark in dit scenario. Deze sectie bevat de code voor het voltooien van een reeks taken:

* het voorbeeld van gegevens moet worden gemodelleerd opnemen
* Lees in de invoergegevensset (opgeslagen als een bestand .tsv)
* indeling en alleen de gegevens opgeschoond
* maken en objecten (rdd's of gegevensframes) in het geheugen in de cache
* registreert u dit als een tijdelijk bestand-tabel in SQL-context.

Hier volgt de code voor opname van gegevens.

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

**DE UITVOER:**

Gebruikte tijd voor het uitvoeren van boven cel: 51.72 seconden

## <a name="data-exploration--visualization"></a>Gegevens verkennen en visualisatie
Zodra de gegevens in Spark is ingesteld, is de volgende stap in het data science process dieper inzicht te krijgen van de gegevens via het verkennen en visualiseren. In deze sectie we de gegevens over taxi's is met behulp van SQL-query's controleren en tekenen van de doelvariabelen en toekomstige functies voor visueel inspecteren. We tekenen met name de frequentie van de passagiers telt het aantal in de gegevens over taxi's, de frequentie van de tip bedragen en hoe tips variëren per te betalingsbedrag en het type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Een histogram met aantal-frequenties passagier in het voorbeeld van gegevens over taxi's tekenen
Deze code en de volgende fragmenten gebruiken SQL magic om op te vragen van de voorbeeld- en lokale Magic-pakket voor de gegevens worden uitgezet.

* **SQL Magic-pakket (`%%sql`)** eenvoudig inline HiveQL query's op de sqlContext biedt ondersteuning voor het HDInsight-PySpark-kernel. De (-o naam_variabele) argument als een Pandas DataFrame op de Jupyter-server de uitvoer van de SQL-query zich blijft voordoen. Met deze instelling wordt de uitvoer beschikbaar in de lokale modus.
* De  **`%%local` magic** code lokaal uitvoeren op de Jupyter-server, waarop het hoofdknooppunt van het HDInsight-cluster wordt gebruikt. Meestal gebruikt u `%%local` magic in combinatie met de `%%sql` magic met -o-parameter. De uitvoer van de SQL-query lokaal wilt behouden door de parameter -o en vervolgens %% lokale Magic-pakket voor de volgende set codefragment lokaal uitvoeren op basis van de uitvoer van de SQL-query's die lokaal is opgeslagen, zouden activeren

De uitvoer wordt automatisch weergegeven nadat u de code uitvoeren.

Deze query worden de gegevens op telling van de passagiers opgehaald. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Deze code maakt een lokale gegevensframe van de query-uitvoer en de gegevens die zichtbaar zijn. De `%%local` Magic-pakket wordt gemaakt van een lokale gegevensframe, `sqlResults`, die kan worden gebruikt voor het uitzetten met matplotlib. 

> [!NOTE]
> Deze magic PySpark wordt meerdere malen gebruikt in dit scenario. Als de hoeveelheid gegevens te groot is, moet u een steekproef voor het maken van een gegevensframe die passen in het lokale geheugen.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Hier volgt de code voor het tekenen van de trips door passagier tellingen

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

**DE UITVOER:**

![Frequentie van de fietstocht op telling van de passagiers](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

U kunt kiezen uit verschillende soorten visualisaties (tabel, Pie, regel, gebied of balk) met behulp van de **Type** menuknoppen in de notebook. De balk plot wordt hier weergegeven.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Een histogram van de tip bedragen en hoe tip bedrag is afhankelijk van de passagiers aantal en de fare bedragen tekenen.
Gebruik een SQL-query om de voorbeeldgegevens.

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


Deze codecel gebruikt de SQL-query om de gegevens voor drie grafieken maken.

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


**DE UITVOER:** 

![Tip bedragdistributie](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tip bedrag per passagier tellen](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip bedrag per fare bedrag](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Functie-engineering, transformatie en gegevens voorbereiden voor modellering
In deze sectie wordt beschreven en vindt de code voor procedures voor het voorbereiden van gegevens voor gebruik in ML-modellen. U leert hoe u de volgende taken uitvoeren:

* Maakt een nieuwe functie door binning uur in verkeer tijd buckets
* Index- en categorische functies coderen
* Gelabelde point-objecten voor invoer in ML-functies maken
* Een wille keurige subsampling van de gegevens maken en deze splitsen in trainings-en test sets
* Functie schalen
* Cacheobjecten in het geheugen

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Maakt een nieuwe functie door binning uur in verkeer tijd buckets
Deze code geeft over het maken van een nieuwe functie door binning uur in verkeer tijd buckets en vervolgens hoe u de resulterende gegevensframe in het geheugen in de cache. Indien Resilient Distributed Datasets (rdd's) en gegevensframes herhaaldelijk worden gebruikt, leidt opslaan in cache tot verbeterde uitvoertijd. Dus cache we rdd's en gegevensframes in verschillende fasen in het overzicht. 

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

**DE UITVOER:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Index- en coderen categorische functies voor de invoer voor het modelleren van functies
Deze sectie wordt beschreven hoe om te indexeren of categorische functies voor invoer in de modelfuncties coderen. Het maken van modellering en functies van MLlib functies met categorische invoergegevens worden geïndexeerd of gecodeerde vóór gebruik nodig hebben te voorspellen. Afhankelijk van het model moet u indexeren of ze coderen op verschillende manieren:  

* **Modellen op basis van een structuur** categorieën op die moeten worden gecodeerd als numerieke waarden vereist (bijvoorbeeld, een functie met drie categorieën kan worden gecodeerd met 0, 1, 2). Dit algoritme wordt verschaft door de [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) -functie van MLlib. Deze functie codeert een kolom met tekenreeksen van labels aan een kolom van het label indexen die zijn geordend op label frequenties. Hoewel geïndexeerd met numerieke waarden voor de invoer- en verwerking van gegevens, worden de structuur gebaseerde algoritmen voor ze op de juiste manier te behandelen als categorieën opgegeven. 
* **Logistieke en lineaire regressie modellen** vereisen een hot-codering, wanneer bijvoorbeeld een functie met drie categorieën kan worden uitgebreid naar drie kolommen van de functie, met elke met 0 of 1, afhankelijk van de categorie van een opmerking. MLlib biedt [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) functie een hot-codering. Een kolom van het label indexen dit coderingsprogramma toegewezen aan een kolom van de binaire vectoren, met maximaal één één-waarde. Deze codering kunt algoritmen die verwacht dat de numerieke waarden functies, zoals logistieke regressie, moet worden toegepast op categorische functies.

Dit is de code om te indexeren en coderen categorische functies:

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

**DE UITVOER:**

Gebruikte tijd voor het uitvoeren van boven cel: 1.28 seconden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Gelabelde point-objecten voor invoer in ML-functies maken
In deze sectie bevat de code die laat hoe u categorische gegevens als een gegevenstype gelabelde punt te indexeren en het coderen zien, zodat deze kan worden gebruikt om te trainen en testen MLlib logistieke regressie en andere modellen voor classificatie. Gelabelde point-objecten zijn Resilient Distributed Datasets (RDD) geformatteerd op een manier die is nodig als invoer voor de meeste van ML-algoritmes in MLlib. Een [met het label punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) is een lokale vector, compacte of sparse, die zijn gekoppeld aan een label/antwoord.  

In deze sectie bevat de code die laat zien hoe u index categorische gegevens als een [met het label punt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) gegevenstype en het coderen, zodat deze kan worden gebruikt om te trainen en testen MLlib logistieke regressie en andere modellen voor classificatie. Gelabelde point-objecten zijn Resilient Distributed Datasets (RDD) die bestaan uit een label (doel/reactie-variabele) en de functie vector. Deze indeling wordt door veel ML-algoritmes in MLlib nodig als invoer.

Dit is de code om te indexeren en het coderen van tekstfuncties voor de binaire indeling.

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
Deze code maakt een willekeurige steekproef van de gegevens (25% wordt hier gebruikt). Maar dit niet vereist voor dit voorbeeld vanwege de grootte van de gegevensset is, laten we zien hoe u kunt een steekproef hier, zodat u hoe u deze gebruiken voor uw eigen probleem weet wanneer dat nodig is. Wanneer steek proeven groot zijn, kan de steek proef aanzienlijke tijd besparen bij het trainen van modellen. Volgende splitsen we het voorbeeld in een training-onderdeel (hier 75%) en testen deel (25% hier) in classificatie en modellering regressie te gebruiken.

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

**DE UITVOER:**

Benodigde tijd voor het uitvoeren van de cel: 0,24 seconde

### <a name="feature-scaling"></a>Functie schalen
Functie schalen, ook wel bekend als de gegevens normaliseren, weet u zeker dat functies met veel betaald waarden zijn niet opgegeven overmatige wegen de servicedoelstelling functie. De code voor functie schalen maakt gebruik van de [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) voor het schalen van de functies verschillen eenheid. Wordt geleverd door MLlib voor gebruik in de lineaire regressie met stochastische kleurovergang afkomst (SGD), een populaire algoritme voor het trainen van een breed scala aan andere machine learning-modellen, zoals overgegaan regressie of support vector machines (SVM).

> [!NOTE]
> We hebben het algoritme LinearRegressionWithSGD gevoelig zijn voor de functie schalen worden gevonden.
> 
> 

Hier volgt de code op schaal variabelen voor gebruik met de regularized lineaire SGD algoritme.

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

**DE UITVOER:**

Gebruikte tijd voor het uitvoeren van boven cel: 13.17 seconden

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

**DE UITVOER:** 

Benodigde tijd voor het uitvoeren van de cel: 0,15 seconde

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Te voorspellen of een tip wordt betaald met binaire classificatie-modellen
Deze sectie wordt beschreven hoe gebruiken drie modellen voor de taak binaire classificatie van het voorspellen van al dan niet een tip voor een reis taxi wordt betaald. De modellen die zijn gepresenteerd zijn:

* Logistieke regressie overgegaan 
* Willekeurige forestmodel
* Kleurovergang Gradient Boosting structuren

Elke sectie met voorbeeldcode bouwen model is opgesplitst in stappen: 

1. **Training model** gegevens met één parameterset
2. **Model voor evaluatie** op een verzameling test met metrische gegevens
3. **Model opslaan** in blob voor toekomstig gebruik

### <a name="classification-using-logistic-regression"></a>Classificatie met logistieke regressie
De code in deze sectie toont hoe u trainen, evalueren en opslaan van een model voor logistieke regressie met [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) die al dan niet een tip wordt betaald voor een reis in de gegevensset NYC taxi reis- en fare worden voorspeld.

**Het model voor logistieke regressie met behulp van CV en hyperparameter sweeping trainen**

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


**DE UITVOER:** 

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept:-0.0111216486893

Gebruikte tijd voor het uitvoeren van boven cel: 14.43 seconden

**Evalueren van de binaire classificeringsmodel met standaard metrische gegevens**

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

**DE UITVOER:** 

Gebied onder de pull-aanvraag 0.985297691373 =

Gebied onder ROC 0.983714670256 =

Samenvattende statistieken

Precisie 0.984304060189 =

Intrekken 0.984304060189 =

F1 Score = 0.984304060189

Gebruikte tijd voor het uitvoeren van boven cel: 57.61 seconden

**De ROC-curve tekenen.**

De *predictionAndLabelsDF* is geregistreerd als een tabel, *tmp_results*, in de vorige cel. *tmp_results* query's en resultaten van de uitvoer kan worden gebruikt in de sqlResults gegevensframe voor het plotten. Dit is de code.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Hier is de code voor voorspellingen en de ROC-curve tekenen.

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


**DE UITVOER:**

![Logistieke regressie ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Willekeurige forest classificatie
De code in deze sectie wordt beschreven hoe trainen, evalueren en opslaan van een willekeurige forestmodel die al dan niet een tip wordt betaald voor een reis in de gegevensset NYC taxi reis- en fare worden voorspeld.

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

**DE UITVOER:**

Gebied onder ROC 0.985297691373 =

Gebruikte tijd voor het uitvoeren van boven cel: 31.09 seconden

### <a name="gradient-boosting-trees-classification"></a>Kleurovergang Gradient boosting structuren classificatie
De code in deze sectie wordt beschreven hoe trainen, evalueren, en slaat u een kleurovergang Gradient boosting structuren model waarmee voorspeld of een tip wordt betaald voor een reis in de NYC taxi reis en ritbedrag gegevensset.

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


**DE UITVOER:**

Gebied onder ROC 0.985297691373 =

Gebruikte tijd voor het uitvoeren van boven cel: 19.76 seconden

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Tip bedragen voor gegevens over taxi's met regressiemodellen voorspellen
Deze sectie wordt beschreven hoe de drie modellen gebruiken voor de taak regressie van het voorspellen van het bedrag van de tip betaald voor de reis van een over taxi's op basis van andere functies tip. De modellen die zijn gepresenteerd zijn:

* Lineaire regressie overgegaan
* Willekeurige forest
* Kleurovergang Gradient Boosting structuren

Deze modellen zijn beschreven in de inleiding. Elke sectie met voorbeeldcode bouwen model is opgesplitst in stappen: 

1. **Training model** gegevens met één parameterset
2. **Model voor evaluatie** op een verzameling test met metrische gegevens
3. **Model opslaan** in blob voor toekomstig gebruik

### <a name="linear-regression-with-sgd"></a>Lineaire regressie met SGD
De code in deze sectie leest over het gebruik van uitgebreide functies met het trainen van een lineaire regressie die gebruikmaakt van de toolkit leren met stochastische gradiëntdaling (SGD) om te optimaliseren en om te beoordelen, evalueren en slaat u het model in Azure Blob Storage (WASB).

> [!TIP]
> In onze ervaring kunnen er problemen met de convergentie van LinearRegressionWithSGD modellen en parameters moeten worden gewijzigd/geoptimaliseerd zorgvuldig voor het verkrijgen van een geldig model. Schalen van variabelen aanzienlijk helpt bij de convergentie. 
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

**DE UITVOER:**

Coefficients: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Intercept: 0.853872718283

RMSE = 1.24190115863

R-sqr 0.608017146081 =

Gebruikte tijd voor het uitvoeren van boven cel: 58,42 seconden

### <a name="random-forest-regression"></a>Willekeurige Forest regressie
De code in deze sectie wordt beschreven hoe trainen, evalueren en opslaan van een willekeurige forest regressie waarmee tip bedrag voor de reisgegevens NYC taxi worden voorspeld.

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

**DE UITVOER:**

RMSE 0.891209218139 =

R-sqr 0.759661334921 =

Gebruikte tijd voor het uitvoeren van boven cel: 49.21 seconden

### <a name="gradient-boosting-trees-regression"></a>Kleurovergang Gradient boosting structuren regressie
De code in deze sectie wordt beschreven hoe trainen, evalueren en slaat u een kleurovergang Gradient boosting structuren model waarmee tip bedrag voor de reisgegevens NYC taxi worden voorspeld.

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

**DE UITVOER:**

RMSE 0.908473148639 =

R-sqr 0.753835096681 =

Gebruikte tijd voor het uitvoeren van boven cel: 34.52 seconden

**Tekengebied**

*tmp_results* is geregistreerd als een Hive-tabel in de vorige cel. Resultaten van de tabel worden uitgevoerd in de *sqlResults* gegevensframe voor het plotten. Dit is de code

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Hier volgt de code voor het tekenen van de gegevens met behulp van de Jupyter-server.

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


**DE UITVOER:**

![Werkelijke-vs-voorspeld-tip-bedragen](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Opschonen van de objecten uit het geheugen
Gebruik `unpersist()` het verwijderen van objecten in de cache opgeslagen in het geheugen.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>De opslaglocaties record van de modellen voor verbruik en scoren
Om te gebruiken en beoordelen van een onafhankelijke gegevensset wordt beschreven in de [Score met Spark gebouwde machine learning-modellen en evalueren](spark-model-consumption.md) onderwerp, moet u kopieert en plakt u deze met de opgeslagen modellen die hier zijn gemaakt in het gebruik van bestandsnamen Jupyter-notebook. Dit is de code naar het tekstvenster de paden naar modelbestanden u er moet.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**UITVOER**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>En verder?
Nu u een regressie en classificatie-modellen met de MlLib Spark hebt gemaakt, bent u klaar om te leren hoe u kunt beoordelen en evalueren van deze modellen. De geavanceerde gegevens verkennen en modelleren notebook dives dieper in met inbegrip van kruisvalidatie, hyper-parameter verstrekkende, en het model evaluatie. 

**Model voor verbruik:** voor informatie over het beoordelen en evalueren van de classificatie- en regressiemodellen modellen die in dit onderwerp hebt gemaakt, Zie [Score en evalueren met Spark gebouwde machine learning-modellen](spark-model-consumption.md).

**Kruisvalidatie en hyperparameter sweeping**: Zie [geavanceerde met Spark gegevens verkennen en modelleren](spark-advanced-data-exploration-modeling.md) op hoe de modellen kunnen worden getraind met behulp van kruisvalidatie en hyper-parameter sweeping

