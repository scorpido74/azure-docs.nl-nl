---
title: Experimenten uitvoeren met behulp van automatische ML van Azure
description: Voer machine learning experimenten uit met behulp van Apache Spark en Azure Automated ML
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 57830ae14c5d6653f6a2225924160fcb75646c12
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590653"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>Voer experimenten uit met behulp van automatische ML en Apache Spark van Azure

Azure Machine Learning is een omgeving in de cloud die u in staat stelt om machine learning modellen te trainen, te implementeren, te automatiseren, te beheren en bij te houden. 

In deze zelf studie gebruikt u [geautomatiseerde machine learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) in azure machine learning om een regressie model te maken om de prijzen van het NYCe taxi te voors pellen. Dit proces accepteert trainings gegevens en configuratie-instellingen en herhaalt automatisch combi Naties van verschillende methoden voor het normalisatie/standaardiseren van functies, modellen en afstemming-instellingen om het beste model te ontvangen.

In deze zelfstudie leert u het volgende:
- De gegevens downloaden met behulp van Apache Spark en Azure open gegevens sets
- Gegevens transformeren en opschonen met Apache Spark dataframes
- Een regressiemodel trainen met geautomatiseerde machine learning
- De nauwkeurigheid van een model berekenen

### <a name="before-you-begin"></a>Voordat u begint
- Maak een Apache Spark groep door de [zelf studie een Apache Spark groep maken](../quickstart-create-apache-spark-pool-studio.md)te volgen.
- Voltooi de [zelf studie](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) voor het instellen van de Azure machine learning-werk ruimte als u geen bestaande Azure machine learning-werk ruimte hebt. 

### <a name="understand-regression-models"></a>Regressie modellen begrijpen
*Regressie modellen* voor spelden numerieke uitvoer waarden op basis van onafhankelijke voor spellingen. In regressie is het doel om te helpen bij het vaststellen van de relatie tussen deze onafhankelijke Voorspellings variabelen door te schatten hoe de ene variabele invloed heeft op de andere.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Voor beeld van regressie analyse op de NYC taxi-gegevens
In dit voor beeld gebruikt u Spark voor het uitvoeren van een analyse van gegevens uit de taxi-fooi van New York. De gegevens zijn beschikbaar via [data sets van Azure open](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Deze subset van de gegevensset bevat informatie over gele taxi reizen, inclusief informatie over elke reis, de begin-en eind tijd en locaties, de kosten en andere interessante kenmerken.

> [!IMPORTANT]
> 
> Er zijn mogelijk extra kosten voor het ophalen van deze gegevens uit de opslag locatie. In de volgende stappen gaat u een model ontwikkelen om de prijzen van het NYCe taxi te voors pellen. 
> 

##  <a name="download-and-prepare-the-data"></a>De gegevens downloaden en voorbereiden

1. Een notitie blok maken met behulp van de PySpark-kernel. Zie [een notitie blok maken](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.) voor instructies
   
   > [!Note]
   > 
   > Vanwege de PySpark-kernel hoeft u geen contexten expliciet te maken. De Spark-context wordt automatisch voor u gemaakt wanneer u de eerste code-cel uitvoert.
   >

2. Omdat de onbewerkte gegevens zich in een Parquet-indeling bevindt, kunt u de Spark-context gebruiken om het bestand rechtstreeks in het geheugen te halen als een data frame. Een Spark-data frame maken door de gegevens op te halen via de API open data sets. Hier gebruiken we het Spark data frame- *schema op Lees* eigenschappen om de gegevens typen en het schema af te leiden. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. Afhankelijk van de grootte van uw Spark-groep (preview) kunnen de onbewerkte gegevens te groot zijn of te veel tijd in beslag nemen. U kunt deze gegevens met behulp van de-en-filters naar een kleiner filter filteren ```start_date``` ```end_date``` . Hiermee wordt een filter toegepast dat een maand aan gegevens retourneert. Zodra we de gefilterde data frame hebben, wordt de functie ook uitgevoerd ```describe()``` op de nieuwe data frame om samenvattings statistieken voor elk veld weer te geven. 

   Op basis van de samenvattings statistieken kunnen we zien dat er sprake is van onregelmatigheden en uitschieters in de gegevens. De statistieken laten bijvoorbeeld zien dat de minimale reis afstand kleiner is dan 0. Deze onregelmatige gegevens punten moeten worden gefilterd.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Nu genereren we functies uit de gegevensset door een set kolommen te selecteren en verschillende op tijd gebaseerde functies te maken op basis van het veld datum van ophalen. Er worden ook uitbijters gefilterd die in de vorige stap zijn geïdentificeerd en vervolgens de laatste paar kolommen verwijderd die niet nodig zijn voor de training.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
Zoals u ziet, wordt er een nieuwe data frame gemaakt met aanvullende kolommen voor de dag van de maand, het tijdstip van de ophaal periode, de weekdag en de totale retour tijd. 

![Afbeelding van de taxi-data frame.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Gegevens sets voor testen en validatie genereren

Zodra we de laatste gegevensset hebben, kunnen we de gegevens in trainings-en test sets splitsen met behulp van de Spark- ```random_ split ``` functie. Met de meegeleverde gewichten wordt met deze functie de gegevens wille keurig gesplitst in de trainings gegevensset voor model training en de validatie gegevensset voor testen.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Met deze stap zorgt u ervoor dat de gegevens punten het voltooide model moeten testen dat niet is gebruikt om het model te trainen. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Verbinding maken met een Azure Machine Learning-werkruimte
In Azure Machine Learning is een  **werk ruimte** een klasse die uw Azure-abonnement en resource gegevens accepteert. Hier wordt ook een cloudresource gemaakt om de uitvoeringen van uw model te controleren en bij te houden. In deze stap maakt u een werkruimte object op basis van de bestaande Azure Machine Learning-werk ruimte.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Een data frame converteren naar een Azure Machine Learning-gegevensset
Als u een extern experiment wilt verzenden, moet u onze gegevensset omzetten in een Azure Machine Learning ```TabularDatset``` . Een [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door de meegeleverde bestanden te parseren.

Met de volgende code worden de bestaande werk ruimte en de standaard Azure Machine Learning standaard gegevens opslag opgehaald. Vervolgens worden de gegevens opslag en bestands locaties door gegeven aan de para meter Path om een nieuwe te maken ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![Afbeelding van geüploade gegevensset.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Een auto ML-experiment verzenden

#### <a name="define-training-settings"></a>Trainingsinstellingen definiëren

1. Voor het verzenden van een experiment moet u de para meter en de model instellingen voor de training definiëren. U kunt [hier](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)de volledige lijst met instellingen weer geven.

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

2. Nu worden de gedefinieerde opleidings instellingen door gegeven als een \* \* kwargs-para meter voor een AutoMLConfig-object. Omdat we oefenen in Spark, moeten we ook de Spark-context door geven die automatisch toegankelijk is voor de ```sc``` variabele. Daarnaast zullen we de trainings gegevens en het type model opgeven, die in dit geval regressie is.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
> Geautomatiseerde machine learning-voorverwerkingsstappen (kenmerknormalisatie, het verwerken van ontbrekende gegevens, het converteren van tekst naar numerieke waarden, enzovoort) worden onderdeel van het onderliggende model. Wanneer u het model voor voorspellingen gebruikt, worden dezelfde voorverwerkingsstappen die tijdens de training zijn toegepast, automatisch toegepast op uw invoergegevens.

#### <a name="train-the-automatic-regression-model"></a>Het automatische regressiemodel trainen 
Nu gaan we een experiment object maken in uw Azure Machine Learning-werk ruimte. Een experiment fungeert als een container voor uw afzonderlijke uitvoeringen. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

Zodra het experiment is voltooid, wordt door de uitvoer informatie over de voltooide iteraties geretourneerd. Voor elke iteratie ziet u het modeltype, de uitvoeringsduur en de nauwkeurigheid van de training. In het veld wordt de best uitgevoerde trainings Score getraceerd op basis van uw metrische type.

![Scherm opname van model uitvoer.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> Nadat het AutoML-experiment is verzonden, worden verschillende iteraties en model typen uitgevoerd. Deze uitvoering duurt doorgaans 1 tot 1,5 uur. 

#### <a name="retrieve-the-best-model"></a>Het beste model ophalen
Om het beste model van uw iteraties te selecteren, zullen we de ```get_output``` functie gebruiken om het beste uitvoeren en het model te krijgen. Met de onderstaande code wordt het beste uitvoeren en het model voor de vastgelegde metrische gegevens of een bepaalde herhaling opgehaald.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Nauw keurigheid van test model

1. Als u de nauw keurigheid van het model wilt testen, gebruiken we het beste model voor het uitvoeren van de voor spellingen van taxi-ritbedrag op de test gegevensset. De ```predict``` functie gebruikt het beste model en voor spelt de waarden van y (ritbedrag amount) uit de validatie gegevensset. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. De hoofd-Mean-Square-fout (RMSE) is een veelgebruikte meting van de verschillen tussen de voorbeeld waarden die worden voor speld door een model en de waarden die zijn waargenomen. We berekenen de vierkantswortel fout van de resultaten door de y_test data frame te vergelijken met de waarden die voor speld zijn door het model. 

   De functie ```mean_squared_error``` neemt twee matrices in beslag en berekent het gemiddelde kwadraat ertussen. Vervolgens nemen we de vierkantswortel van het resultaat. Deze metrische gegevens duiden ongeveer op hoe ver de voor spellingen van de taxi-ritbedrag van de werkelijke tarieven waarden zijn.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   De belangrijkste-kwadraat fout is een goede meting van hoe nauw keurig het model het antwoord voorspelt. Vanuit de resultaten ziet u dat het model redelijk goed is bij het voors pellen van de taxi tarieven van de functies van de gegevensset, meestal binnen +-$2,00

3. Voer de volgende code uit om de gemiddelde absolute percentage fout (MAPE) te berekenen. Deze meet waarde is nauw keurig als een percentage van de fout. Dit doet u door een absoluut verschil tussen elke voorspelde en werkelijke waarde te berekenen en vervolgens alle verschillen op te tellen. Vervolgens wordt deze som als een percentage van het totaal van de werkelijke waarden.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Uit de twee nauw keurige meet waarden voor de voor spellingen ziet u dat het model redelijk goed is bij het voors pellen van de taxi tarieven van de functies van de gegevensset. 

4. Na het aanpassen van een lineair regressie model moeten we nu bepalen hoe goed het model past bij de gegevens. Om dit te doen, zullen we de werkelijke ritbedrag waarden voor de voorspelde uitvoer uitzetten. Daarnaast berekenen we ook de R-kwadraat meting om te begrijpen hoe dicht de gegevens zich op de toegevoegde regressie lijn bevindt.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![Scherm afbeelding van een regressie tekening.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Vanuit de resultaten kunnen we zien dat de R-kwadraat maat accounts 95% van onze variantie. Dit wordt ook gevalideerd door de werkelijke tekst die in de grafiek is waargenomen. Hoe meer verschillen er door het regressie model worden verwerkt, des te nauw keuriger de gegevens punten naar de ingrijpende regressie lijn vallen.  

## <a name="register-model-to-azure-machine-learning"></a>Model registreren bij Azure Machine Learning
Zodra we ons beste model hebben gevalideerd, kunnen we het model registreren bij Azure Machine Learning. Nadat u het model hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Resultaten in Azure Machine Learning weer geven
U kunt ook de resultaten van de iteraties openen door te navigeren naar het experiment in uw Azure Machine Learning-werkruimte. Hier kunt u meer informatie vinden over de status van de uitvoering, de gebruikte modellen en de metrische gegevens van het model. 

![Scherm afbeelding van de AML-werk ruimte.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Volgende stappen
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark zelf studie voor MLlib](./apache-spark-machine-learning-mllib-notebook.md)
