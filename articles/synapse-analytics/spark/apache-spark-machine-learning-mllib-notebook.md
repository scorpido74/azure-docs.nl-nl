---
title: Een machine learning-app bouwen met Apache Spark MLlib en Azure Synapse Analytics
description: Meer informatie over het gebruik van Apache Spark MLlib voor het maken van een machine learning-app die een gegevensset analyseert met behulp van logistiek regressie.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: c2e1dbba61399ee3a4435f4f287b47f4bfd6f872
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774450"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Een machine learning-app bouwen met Apache Spark MLlib en Azure Synapse Analytics

In dit artikel leert u hoe u Apache Spark [MLlib](https://spark.apache.org/mllib/) kunt gebruiken om een machine learning toepassing te maken die eenvoudige voorspellende analyse op een open bare gegevensset van Azure biedt. Spark biedt ingebouwde machine learning bibliotheken. In dit voor beeld wordt *classificatie* gebruikt via logistiek regressie.

MLlib is een belang rijke Spark-bibliotheek die veel hulpprogram ma's biedt die nuttig zijn voor het machine learning taken, waaronder hulpprogram ma's die geschikt zijn voor:

- Classificatie
- Regressie
- Clustering
- Onderwerpen model leren
- Enkelvouds waarde (SVD) en Principal component analyse (PCA)
- Hypo Thesen testen en voorbeeld statistieken berekenen

## <a name="understand-classification-and-logistic-regression"></a>Meer informatie over classificatie en logistiek regressie

*Classificatie*, een populaire machine learning taak, is het proces waarbij invoer gegevens in categorieën worden gesorteerd. Het is de taak van een classificatie algoritme om te bepalen hoe *labels* moeten worden toegewezen aan invoer gegevens die u opgeeft. U kunt bijvoorbeeld zien wat een machine learning-algoritme is dat aandelen informatie als invoer accepteert en het aandeel in twee categorieën opsplitsen: aandelen die u moet verkopen en aandelen die u moet houden.

*Logistiek regressie* is een algoritme dat u voor classificatie kunt gebruiken. De logistiek regressie-API van Spark is handig voor *binaire classificatie*of het classificeren van invoer gegevens in een van twee groepen. Zie [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)voor meer informatie over logistiek regressies.

In samen vatting produceert het proces van logistiek regressie een *logistiek functie* die kan worden gebruikt om de kans te voors pellen dat een invoer vector deel uitmaakt van de ene groep of de andere.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Voor beeld van voorspellende analyse op NYC taxi-gegevens

In dit voor beeld gebruikt u Spark voor het uitvoeren van een bepaalde voorspellende analyse op basis van gegevens over de taxi-fooi van New York. De gegevens zijn beschikbaar via [data sets van Azure open](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Deze subset van de gegevensset bevat informatie over gele taxi reizen, inclusief informatie over elke reis, de begin-en eind tijd en locaties, de kosten en andere interessante kenmerken.

> [!IMPORTANT]
> Er zijn mogelijk extra kosten voor het ophalen van deze gegevens uit de opslag locatie.

In de volgende stappen ontwikkelt u een model om te voors pellen of een bepaalde reis een tip bevat of niet.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Een Apache Spark MLlib machine learning-app maken

1. Een notitie blok maken met behulp van de PySpark-kernel. Zie [een notitie blok maken](../quickstart-apache-spark-notebook.md#create-a-notebook)voor instructies.
2. Importeer de typen die vereist zijn voor deze toepassing. Kopieer en plak de volgende code in een lege cel en druk op **SHIFT + ENTER**of voer de cel uit met behulp van het blauwe afspeel pictogram links van de code.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Vanwege de PySpark-kernel hoeft u geen contexten expliciet te maken. De Spark-context wordt automatisch voor u gemaakt wanneer u de eerste code-cel uitvoert.

## <a name="construct-the-input-dataframe"></a>De invoer-data frame maken

Omdat de onbewerkte gegevens zich in een Parquet-indeling bevindt, kunt u de Spark-context gebruiken om het bestand rechtstreeks in het geheugen te halen als een data frame. Hoewel in de volgende code de standaard opties worden gebruikt, is het mogelijk om zo nodig de toewijzing van gegevens typen en andere schema kenmerken te forceren.

1. Voer de volgende regels uit om een Spark-data frame te maken door de code in een nieuwe cel te plakken. In de eerste sectie worden Azure Storage-toegangs gegevens aan variabelen toegewezen. In het tweede gedeelte kan Spark van de Blob-opslag op afstand worden gelezen. De laatste regel code leest Parquet, maar er worden op dit moment geen gegevens geladen.

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

2. Als u al deze gegevens ophaalt, worden er meer dan 1.500.000.000 rijen gegenereerd. Afhankelijk van de grootte van uw Spark-groep (preview) kunnen de onbewerkte gegevens te groot zijn of te veel tijd in beslag nemen. U kunt deze gegevens naar een kleiner filter filteren. Voeg, indien nodig, de volgende regels toe om de gegevens te filteren op ongeveer 2.000.000 rijen voor een snellere ervaring. Gebruik deze para meters om één week van gegevens te verzamelen.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Het nadeel van eenvoudige filtering is dat, vanuit een statistisch perspectief, het kan leiden tot een afwijking in de gegevens. Een andere benadering is het gebruik van de in Spark ingebouwde steek proeven. Met de volgende code wordt de gegevensset in ongeveer 2000 rijen kleiner als deze na de bovenstaande code wordt toegepast. Deze steekproef stap kan worden gebruikt in plaats van het eenvoudige filter of in combi natie met het eenvoudige filter.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Het is nu mogelijk om de gegevens te bekijken om te zien wat er is gelezen. Normaal gesp roken is het beter om gegevens met een subset te bekijken in plaats van de volledige set, afhankelijk van de grootte van de gegevensset. De volgende code biedt twee manieren om de gegevens weer te geven: de eerste basis en de laatste die een veel rijkere raster ervaring bieden, evenals de mogelijkheid om de gegevens grafisch te visualiseren.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. Afhankelijk van de grootte van de gegenereerde gegevensset en de nood zaak om het notitie blok meermaals te experimenteren of uit te voeren, kan het raadzaam zijn om de gegevensset lokaal in de cache op te slaan in de werk ruimte. Er zijn drie manieren om expliciet caching uit te voeren:

   - Sla het data frame lokaal op als een bestand
   - De data frame opslaan als een tijdelijke tabel of weer gave
   - De data frame opslaan als een permanente tabel

De eerste twee van deze benaderingen zijn opgenomen in de volgende code voorbeelden.

Het maken van een tijdelijke tabel of weer gave biedt verschillende toegangs paden naar de gegevens, maar het duurt alleen voor de duur van de Spark-sessie.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Informatie over de gegevens

Normaal gesp roken gaat u op dit punt een fase van *experimentele gegevens analyse* (EDA) door lopen om een goed idee van de gegevens te ontwikkelen. De volgende code toont drie verschillende visualisaties van de gegevens met betrekking tot tips die leiden tot conclusies over de status en kwaliteit van de gegevens.

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

![](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
 ![ Het ](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
 ![ spreidings diagram van het histogram Box whisker tekenen](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>De gegevens voorbereiden

De gegevens in de onbewerkte vorm zijn vaak niet geschikt om rechtstreeks aan een model door te geven. Er moet een reeks acties worden uitgevoerd op de gegevens om deze op te halen in een staat waarin het model deze kan gebruiken.

In de code onder vier bewerkings categorieën worden de volgende bewerkingen uitgevoerd:

- Het verwijderen van uitschieters/onjuiste waarden via filteren.
- Het verwijderen van kolommen is niet nodig.
- Het maken van nieuwe kolommen die zijn afgeleid van de onbewerkte gegevens om het model effectiever te maken, ook wel parametrisatie genoemd.
- Labelen: als u een binaire classificatie hebt (er is een tip of niet op een bepaalde reis), moet u het fooie bedrag converteren naar een waarde van 0 of 1.

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

Vervolgens wordt er een tweede fase van de gegevens gemaakt om de laatste functies toe te voegen.

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

## <a name="create-a-logistic-regression-model"></a>Een logistiek regressie model maken

De laatste taak is het converteren van de gelabelde gegevens in een indeling die door logistiek regressie kan worden geanalyseerd. De invoer van een algoritme voor logistiek regressie moet een set van *Vector paren voor label functies*zijn, waarbij de *functie Vector* een vector is van getallen die het invoer punt vertegenwoordigen. Daarom moeten de categorische-kolommen worden geconverteerd naar getallen. De `trafficTimeBins` `weekdayString` kolommen en moeten worden geconverteerd naar gehele getallen. Er zijn meerdere benaderingen voor het uitvoeren van de conversie, maar de benadering die in dit voor beeld wordt gegeven, is *OneHotEncoding*, een gemeen schappelijke aanpak.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Dit resulteert in een nieuwe data frame met alle kolommen in de juiste indeling voor het trainen van een model.

## <a name="train-a-logistic-regression-model"></a>Een logistiek regressie model trainen

De eerste taak bestaat uit het splitsen van de gegevensset in een Trainingsset en een test-of validatieset. De Splits hier is wille keurig en u moet met andere Splits instellingen spelen om te zien of ze van invloed zijn op het model.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nu er twee DataFrames zijn, bestaat de volgende taak uit het maken van de model formule en voert u deze uit met de training data frame en valideert u vervolgens op basis van de test data frame. Experimenteer met verschillende versies van de model formule om de impact van verschillende combi Naties te bekijken.

> [!Note]
> Als u het model wilt opslaan, hebt u de Azure Storage Blob de RBAC-rol data contributor nodig. Navigeer onder uw opslag account naar Access Control (IAM) en selecteer roltoewijzing toevoegen. Wijs de RBAC-rol Storage BLOB data Inzender toe aan uw SQL Database-Server. Alleen leden met de bevoegdheid eigenaar kunnen deze stap uitvoeren. Raadpleeg deze [hand leiding](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor verschillende ingebouwde rollen voor Azure-resources.

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

## <a name="create-a-visual-representation-of-the-prediction"></a>Een visuele weer gave van de voor spelling maken

U kunt nu een definitieve visualisatie maken om u te helpen de resultaten van deze test te controleren. Een [ROC-curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) is een manier om het resultaat te bekijken.

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

![ROC curve voor logistiek regressie model](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC curve voor logistiek regressie model")

## <a name="shut-down-the-spark-instance"></a>Het Spark-exemplaar afsluiten

Wanneer u klaar bent met het uitvoeren van de toepassing, sluit u het notitie blok om de resources vrij te geven door het tabblad te sluiten of door **sessie beëindigen** te selecteren in het status paneel onder aan het notitie blok.

## <a name="see-also"></a>Zie ook

- [Overzicht: Apache Spark in azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor .NET for Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark officiële documentatie](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Sommige van de officiële Apache Spark documentatie is gebaseerd op het gebruik van de Spark-console, die niet beschikbaar is in azure Synapse Spark. Gebruik in plaats daarvan de ervaring [notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) of [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .
