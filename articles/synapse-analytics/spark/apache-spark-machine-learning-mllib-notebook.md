---
title: Een machine learning-app bouwen met Apache Spark MLlib en Azure Synapse Analytics
description: Meer informatie over het gebruik van Apache Spark MLlib om een machine learning-app te maken die een gegevensset analyseert met behulp van classificatie via logistieke regressie.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430004"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Een machine learning-app bouwen met Apache Spark MLlib en Azure Synapse Analytics

In dit artikel leert u hoe u Apache Spark [MLlib](https://spark.apache.org/mllib/) gebruiken om een machine learning-toepassing te maken die eenvoudige voorspellende analyses doet op een open Azure-gegevensset. Spark biedt ingebouwde machine learning-bibliotheken. In dit voorbeeld wordt gebruik gemaakt van *classificatie* via logistieke regressie.

MLlib is een kernsparkbibliotheek die veel hulpprogramma's biedt die nuttig zijn voor machine learning-taken, waaronder hulpprogramma's die geschikt zijn voor:

- Classificatie
- Regressie
- Clustering
- Onderwerpmodellering
- Enkelvoudwaardedecompositie (SVD) en hoofdcomponentanalyse (PCA)
- Hypothesetesten en het berekenen van steekproefstatistieken

## <a name="understand-classification-and-logistic-regression"></a>Begrijp classificatie en logistieke regressie

*Classificatie*, een populaire machine learning taak, is het proces van het sorteren van invoergegevens in categorieën. Het is de taak van een classificatiealgoritme om erachter te komen hoe *u labels* toewijzen aan invoergegevens die u verstrekt. U bijvoorbeeld denken aan een machine learning-algoritme dat voorraadinformatie accepteert als invoer en de voorraad opdeelt in twee categorieën: aandelen die u moet verkopen en aandelen die u moet houden.

*Logistieke regressie* is een algoritme dat u gebruiken voor classificatie. Spark's logistieke regressie API is handig voor *binaire classificatie,* of classificeren van invoergegevens in een van de twee groepen. Zie [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)voor meer informatie over logistieke regressies.

Samengevat, het proces van logistieke regressie produceert een *logistieke functie* die kan worden gebruikt om de waarschijnlijkheid te voorspellen dat een invoervector in de ene groep of de andere thuishoort.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Voorbeeld van voorspellende analyse op nyc taxigegevens

In dit voorbeeld gebruikt u Spark om een aantal voorspellende analyses uit te voeren op tipgegevens van taxiritten uit New York. De gegevens zijn beschikbaar via [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Deze subset van de gegevensset bevat informatie over gele taxiritten, inclusief informatie over elke rit, de begin- en eindtijd en locaties, de kosten en andere interessante kenmerken.

> [!IMPORTANT]
> Er kunnen extra kosten in rekening worden gebracht voor het ophalen van deze gegevens van de opslaglocatie.

In de volgende stappen ontwikkelt u een model om te voorspellen of een bepaalde reis een tip bevat of niet.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Een Apache Spark MLlib machine learning-app maken

1. Maak een notitieblok met de PySpark-kernel. Zie [Een notitieblok maken](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)voor de instructies.
2. Importeer de typen die nodig zijn voor deze toepassing. Kopieer en plak de volgende code in een lege cel en druk op **SHIFT + ENTER**of voer de cel uit met het blauwe afspeelpictogram links van de code.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Vanwege de PySpark-kernel hoeft u geen contexten expliciet te maken. De Spark-context wordt automatisch voor u gemaakt wanneer u de eerste codecel uitvoert.

## <a name="construct-the-input-dataframe"></a>Het invoergegevensframe construeren

Omdat de ruwe gegevens een parketindeling hebben, u de Spark-context gebruiken om het bestand rechtstreeks als gegevensframe in het geheugen te halen. Terwijl de onderstaande code de standaardopties gebruikt, is het mogelijk om het toewijzen van gegevenstypen en andere schemakenmerken te forceren indien nodig.

1. Voer de volgende regels uit om een Spark-gegevensframe te maken door de code in een nieuwe cel te plakken. In de eerste sectie worden azure-opslagtoegangsgegevens toebedeeld aan variabelen. In het tweede deel kan Spark op afstand uitlezen vanuit blobopslag. De laatste regel code leest parket, maar er worden op dit moment geen gegevens geladen.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Het trekken van al deze gegevens genereert ongeveer 1,5 miljard rijen. Afhankelijk van de grootte van uw Spark-pool (voorbeeld) kunnen de ruwe gegevens te groot zijn of te veel tijd in beslag nemen om te werken. U deze gegevens filteren op iets kleiners. Voeg indien nodig de volgende regels toe om de gegevens tot ongeveer 2 miljoen rijen te filteren voor een meer responsieve ervaring. Gebruik deze parameters om een week aan gegevens op te halen.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Het nadeel van eenvoudige filtering is dat, vanuit een statistisch perspectief, het kan bias in de gegevens te introduceren. Een andere benadering is het gebruik van de bemonstering ingebouwd in Spark. De volgende code reduceert de gegevensset tot ongeveer 2000 rijen, indien toegepast na de bovenstaande code. Deze bemonsteringsstap kan worden gebruikt in plaats van het eenvoudige filter of in combinatie met het eenvoudige filter.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Het is nu mogelijk om te kijken naar de gegevens om te zien wat er werd gelezen. Het is normaal gesproken beter om gegevens te bekijken met een subset in plaats van de volledige set, afhankelijk van de grootte van de gegevensset. De volgende code biedt twee manieren om de gegevens te bekijken: de eerste is basic en de laatste biedt een veel rijkere rasterervaring, evenals de mogelijkheid om de gegevens grafisch te visualiseren.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. Afhankelijk van de grootte van de gegevenssetgrootte die is gegenereerd en de noodzaak om het notitieblok vele malen te experimenteren of uit te voeren, kan het raadzaam zijn om de gegevensset lokaal in de werkruimte in de cache op te nemen. Er zijn drie manieren om expliciete caching uit te voeren:

   - Het gegevensframe lokaal opslaan als bestand
   - Het gegevensframe opslaan als tijdelijke tabel of weergave
   - Het gegevensframe opslaan als permanente tabel

De eerste 2 van deze benaderingen zijn opgenomen in de volgende codevoorbeelden.

Het maken van een tijdelijke tabel of weergave biedt verschillende toegangspaden tot de gegevens, maar duurt alleen voor de duur van de Spark-instantiesessie.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>De gegevens begrijpen

Normaal gesproken zou je gaan door middel van een fase van *verkennende data-analyse* (EDA) op dit punt om een goed begrip van de gegevens te ontwikkelen. De volgende code toont drie verschillende visualisaties van de gegevens met betrekking tot tips die leiden tot conclusies over de status en kwaliteit van de gegevens.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Histogram](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Box Whisker Plot](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Scatter Plot](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>De gegevens voorbereiden

De gegevens in zijn ruwe vorm zijn vaak niet geschikt om rechtstreeks naar een model over te gaan. Een reeks acties moet worden uitgevoerd op de gegevens om het in een staat te krijgen waar het model het kan verbruiken.

In de onderstaande code worden vier bewerkingen uitgevoerd:

- Het verwijderen van uitschieters/onjuiste waarden door middel van filtering.
- Het verwijderen van kolommen, die niet nodig zijn.
- Het maken van nieuwe kolommen afgeleid van de ruwe gegevens om het model effectiever te laten werken, ook wel featurization genoemd.
- Etikettering, als u bezig bent binaire classificatie (zal er een tip of niet op een bepaalde reis) is er een noodzaak om de tip bedrag om te zetten in een 0 of 1 waarde.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Een tweede pas wordt vervolgens gemaakt over de gegevens om de uiteindelijke functies toe te voegen.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Een logistiek regressiemodel maken

De uiteindelijke taak is om de gelabelde gegevens om te zetten in een formaat dat kan worden geanalyseerd door logistieke regressie. De invoer naar een logistiek regressiealgoritme moet een set vectorparen van *labelfuncties*zijn, waarbij de *functievector* een vector is van getallen die het invoerpunt vertegenwoordigen. We moeten de categorische kolommen omzetten in getallen. De `trafficTimeBins` `weekdayString` kolommen en kolommen moeten worden omgezet in gehele representaties. Er zijn meerdere benaderingen voor het uitvoeren van de conversie, maar de aanpak in dit voorbeeld is *OneHotEncoding*, een gemeenschappelijke aanpak.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Dit resulteert in een nieuw gegevensframe met alle kolommen in de juiste indeling om een model te trainen.

## <a name="train-a-logistic-regression-model"></a>Train een logistiek regressiemodel

De eerste taak is het splitsen van de gegevensset in een trainingsset en een test- of validatieset. De splitsing hier is willekeurig en je moet spelen met verschillende split instellingen om te zien of ze invloed hebben op het model.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nu er twee DataFrames zijn, is de volgende taak om de modelformule te maken en deze uit te voeren tegen het trainingsdataframe en vervolgens te valideren tegen het testgegevensframe. U moet experimenteren met verschillende versies van de modelformule om de impact van verschillende combinaties te zien.

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

De uitvoer van deze cel is

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Een visuele weergave van de voorspelling maken

U nu een definitieve visualisatie maken om u te helpen redeneren over de resultaten van deze test. Een [ROC Curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is een manier om het resultaat te bekijken.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![ROC Curve voor Logistieke Regressie tipmodel](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC Curve voor Logistieke Regressie tipmodel")

## <a name="shut-down-the-spark-instance"></a>De spark-instantie afsluiten

Nadat u klaar bent met het uitvoeren van de toepassing, sluit u het notitieblok af om de bronnen vrij te geven door het tabblad te sluiten of selecteer **de sessie beëindigen** in het statuspaneel onder aan het notitieblok.

## <a name="see-also"></a>Zie ook

- [Overzicht: Apache Spark op Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Volgende stappen

- [.NET voor Apache Spark-documentatie](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark officiële documentatie](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Sommige van de officiële Apache Spark-documentatie is gebaseerd op het gebruik van de Spark-console, die niet beschikbaar is op Azure Synapse Spark. Gebruik in plaats daarvan de [ervaringen van het notitieblok](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) of [IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
