---
title: Log ML experimenten & metrische gegevens
titleSuffix: Azure Machine Learning service
description: Bewaak uw Azure ML experimenten en bewaak metrische uitvoerings gegevens om het proces voor het maken van het model te verbeteren. Voeg logboek registratie toe aan uw trainings script en Bekijk de geregistreerde resultaten van een uitvoering.  Gebruik run. log, run. start_logging of ScriptRunConfig.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: 1c2747c8eaac0323ac32f67afb27ac854a3a5ce9
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959943"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Uitvoeringen en metrische gegevens van Azure ML-experimenten bewaken

Verbeter het proces voor het maken van het model door de metrische gegevens voor experimenten en bewakings uitvoeringen bij te houden. In dit artikel leert u hoe u logboek registratie code kunt toevoegen aan uw trainings script, een experiment moet verzenden, de uitvoering ervan kunt controleren en de resultaten kunt controleren in Azure Machine Learning-service.

> [!NOTE]
> Azure Machine Learning-service kan ook gegevens van andere bronnen tijdens de training registreren, zoals geautomatiseerde machine learning uitvoeringen, of de docker-container die de trainings taak uitvoert. Deze logboeken worden niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

## <a name="available-metrics-to-track"></a>Beschik bare metrische gegevens om bij te houden

De volgende metrische gegevens kunnen worden toegevoegd aan een run tijdens het trainen van een experiment. Voor een meer gedetailleerd overzicht van wat er op een uitvoering kunnen worden bijgehouden, raadpleegt u de [uitvoeren van de klasse referentiedocumentatie voor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Funkce Pythonu | Opmerkingen|
|----|:----|:----|
|Scalaire waarden |Functie:<br>`run.log(name, value, description='')`<br><br>Voorbeeld:<br>Run.log ("nauwkeurigheid", 0.95) |Meld u een numerieke of tekenreekswaarde voor de uitvoering met de opgegeven naam. Logboekregistratie van een metrische waarde aan een run zorgt ervoor dat deze metrische gegevens worden opgeslagen in de record uitvoeren in het experiment.  U kunt de dezelfde metrische gegevens meerdere keren binnen een uitvoering, het resultaat wordt beschouwd als een vector van deze metrische gegevens vastleggen.|
|Lijsten|Functie:<br>`run.log_list(name, value, description='')`<br><br>Voorbeeld:<br>Run.log_list ("nauwkeurigheden," [0,6, 0,7, 0.87]) | Meld u een lijst met waarden voor de uitvoering met de opgegeven naam.|
|Rij|Functie:<br>`run.log_row(name, description=None, **kwargs)`<br>Voorbeeld:<br>Run.log_row ("Y via X", x = 1, y = 0.4) | Met behulp van *log_row* maakt u een metrische waarde met meerdere kolommen zoals beschreven in kwargs. Elke benoemde parameter genereert een kolom met de opgegeven waarde.  *log_row* kan eenmaal worden aangeroepen voor het melden van een willekeurige tuple of meerdere keren in een lus voor het genereren van een volledige tabel.|
|Tabel|Functie:<br>`run.log_table(name, value, description='')`<br><br>Voorbeeld:<br>Run.log_table ("Y via X", {"x": [1, 2, 3], "y": [0,6, 0,7, 0,89]}) | Meld u aan een dictionary-object de uitvoeren met de opgegeven naam. |
|Installatiekopieën|Functie:<br>`run.log_image(name, path=None, plot=None)`<br><br>Voorbeeld:<br>`run.log_image("ROC", plt)` | Meld u aan een installatiekopie van de record uitvoeren. Log_image gebruiken om aan te melden voor een afbeelding of een matplotlib getekend met het uitvoeren.  Deze installatiekopieën worden zichtbaar en vergelijkbare in de record uitvoeren.|
|Een uitvoering taggen|Functie:<br>`run.tag(key, value=None)`<br><br>Voorbeeld:<br>Run.Tag ("ingeschakeld", "Ja") | Tag de uitvoering met een tekenreekssleutel en een optionele tekenreeks-waarde.|
|Bestand of map uploaden|Functie:<br>`run.upload_file(name, path_or_stream)`<br> <br> Voorbeeld:<br>Run.upload_file ("best_model.pkl", ". / model.pkl") | Upload een bestand naar de record uitvoeren. Wordt uitgevoerd automatisch vastleggen bestand in de map met de opgegeven uitvoer, die standaard ". / levert ' voor de meeste typen die worden uitgevoerd.  Gebruik upload_file alleen als aanvullende bestanden moeten worden geüpload of een map met de uitvoer is niet opgegeven. Het is raadzaam toe te voegen `outputs` op de naam, zodat deze wordt het geüpload naar de map voor uitvoer. U kunt alle bestanden die gekoppeld zijn een lijst met deze record door te voeren met de naam `run.get_file_names()`|

> [!NOTE]
> Metrische gegevens voor hoeken, een lijst met rijen en tabellen kunt type hebben: float, geheel getal of tekenreeks.

## <a name="choose-a-logging-option"></a>Kies een optie voor logboek registratie

Als u wilt bijhouden of bewaken van uw experiment, moet u code om te starten wanneer u de uitvoering verzendt logboekregistratie toevoegen. De volgende zijn manieren voor het activeren van het verzenden van de uitvoering:
* __Run.start_logging__ - logboekregistratiefuncties toevoegen aan uw trainingsscript en een interactieve sessie te starten in de opgegeven experiment. **start_logging** maakt u een interactieve uitvoeren voor gebruik in scenario's zoals laptops. Alle metrische gegevens die zijn vastgelegd tijdens de sessie worden toegevoegd aan de uitvoerregistratie in het experiment.
* __ScriptRunConfig__ - logboekregistratiefuncties toevoegen aan uw trainingsscript en laden van de gehele script-map met de uitvoering.  **ScriptRunConfig** is een klasse voor het instellen van configuraties voor het script wordt uitgevoerd. Met deze optie kunt u de code voor bewaking om te worden geïnformeerd over voltooiing of het opvragen van een visual widget voor het bewaken van toevoegen.

## <a name="set-up-the-workspace"></a>Instellen van de werkruimte
Voordat u logboekregistratie en het verzenden van een experiment toevoegt, moet u de werkruimte instellen.

1. Laden van de werkruimte. Zie [werkruimte configuratie bestand](how-to-configure-environment.md#workspace)voor meer informatie over het instellen van de configuratie van de werk ruimte.

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Optie 1: Start_logging gebruiken

**start_logging** maakt u een interactieve uitvoeren voor gebruik in scenario's zoals laptops. Alle metrische gegevens die zijn vastgelegd tijdens de sessie worden toegevoegd aan de uitvoerregistratie in het experiment.

Het volgende voorbeeld traint een eenvoudig model sklearn Ridge lokaal in een lokaal Jupyter-notitieblok. Zie voor meer informatie over het verzenden van experimenten in verschillende omgevingen, [compute-doelen voor modeltraining met Azure Machine Learning-service instellen](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Maak een trainingsscript in een lokaal Jupyter-notitieblok. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Experiment bijhouden met behulp van de Azure Machine Learning-service-SDK toevoegen en een persistente model uploaden naar het experiment record worden uitgevoerd. De volgende code wordt toegevoegd tags, Logboeken, en wordt een modelbestand geüpload naar het experiment uitvoeren.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Het script wordt beëindigd met ```run.complete()```, die de uitvoering wordt gemarkeerd als voltooid.  Deze functie wordt meestal gebruikt in interactieve scenario's.

## <a name="option-2-use-scriptrunconfig"></a>Optie 2: ScriptRunConfig gebruiken

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) is een klasse voor het instellen van configuraties voor script uitvoeringen. Met deze optie kunt u de code voor bewaking om te worden geïnformeerd over voltooiing of het opvragen van een visual widget voor het bewaken van toevoegen.

In dit voorbeeld is een vervolg op het basismodel sklearn Ridge van boven. Hiervoor wordt een eenvoudige parameter vegen te vegen via alpha-waarden van het model om vast te leggen metrische gegevens en getrainde modellen in wordt uitgevoerd onder het experimentcanvas. Het voorbeeld lokaal wordt uitgevoerd op basis van een gebruiker beheerde omgeving. 

1. Een trainings script `train.py`maken.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. De `train.py` script verwijzingen `mylib.py` waarmee u de lijst met alfa waarden kunt ophalen voor gebruik in het ploois model.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configureer een beheerde gebruiker lokale omgeving.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Dien de ```train.py``` script uit te voeren in de gebruiker beheerde omgeving. Deze scriptmap gehele wordt ingediend voor de training, met inbegrip van de ```mylib.py``` bestand.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Een uitvoering beheren

In het [Start-, controle-en annulerings artikel over training worden](how-to-manage-runs.md) specifieke Azure machine learning werk stromen gemarkeerd voor het beheren van uw experimenten.

## <a name="view-run-details"></a>Details van de uitvoering weergeven

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Bewaking uitvoeren met Jupyter notebook-widget
Wanneer u de methode **ScriptRunConfig** gebruikt om uitvoeringen te verzenden, kunt u de voortgang van de uitvoering met een [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)bekijken. Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10-15 seconden live updates totdat de taak is voltooid.

1. Bekijk de Jupyter-widget tijdens het wachten op voor het uitvoeren om te voltooien.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Schermafbeelding van de Jupyter-notebook widget](./media/how-to-track-experiments/run-details-widget.png)

U kunt ook een koppeling naar dezelfde weer gave in uw werk ruimte ophalen.

```python
print(run.get_portal_url())
```

2. **[Voor geautomatiseerde machine learning-uitvoeringen]**  Voor toegang tot de grafieken van een vorige uitvoering. Vervang `<<experiment_name>>` door de juiste naam van het experiment:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget voor geautomatiseerde Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Om verdere details van een pijplijn-Klik op de pijplijn weer te geven u graag zou willen verkennen in de tabel en de grafieken worden weergegeven in een pop-upvenster vanuit Azure portal.

### <a name="get-log-results-upon-completion"></a>Resultaten van logboeken weergeven bij voltooiing

Model trainen en bewaking optreden op de achtergrond, zodat u andere taken uitvoeren kunt terwijl u wacht. U kunt ook wachten totdat het model opleiding vóór het uitvoeren van meer code is voltooid. Bij het gebruik **ScriptRunConfig**, kunt u ```run.wait_for_completion(show_output = True)``` om weer te geven wanneer het trainen van het model is voltooid. De ```show_output``` vlag biedt uitgebreide uitvoer. 


### <a name="query-run-metrics"></a>Uitvoering van metrische gegevens van de query

U kunt de metrische gegevens van het gebruik van een getraind model weergeven ```run.get_metrics()```. U kunt nu alle van de metrische gegevens die zijn geregistreerd in het voorbeeld hierboven om te bepalen van het beste model krijgen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal-or-your-workspace-landing-page-previewhttpsmlazurecom"></a>Bekijk het experiment in de Azure Portal of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com)

Wanneer een experiment is voltooid, kunt u bladeren naar de vastgelegde experiment record worden uitgevoerd. U kunt de geschiedenis op twee manieren openen:

* Ophalen van de URL van de uitvoering rechtstreeks ```print(run.get_portal_url())```
* Details van de uitvoering weergeven door in te dienen de naam van de uitvoering (in dit geval ```run```). Op deze manier verwijst u naar de naam van het experiment, -ID, type, status, de pagina met details, een koppeling naar de Azure-portal en een koppeling naar de documentatie.

De koppeling voor de uitvoering biedt u rechtstreeks naar de pagina de details van uitvoering in Azure portal. Hier ziet u alle eigenschappen, bijgehouden metrische gegevens, afbeeldingen en grafieken die zijn vastgelegd in het experiment. In dit geval geregistreerd we MSE en het alpha-waarden.

  ![Details van de uitvoering in Azure portal](./media/how-to-track-experiments/run-details-page.png)

U kunt ook eventuele uitvoer of de logboeken voor de uitvoering weergeven of downloaden van de momentopname van het experiment dat u hebt verzonden, zodat u de map experiment met anderen kunt delen.

### <a name="viewing-charts-in-run-details"></a>Grafieken weergeven in de details van uitvoering

Er zijn verschillende manieren voor het gebruik van de logboekregistratie van API's gebruiken voor andere recordtypen van metrische gegevens tijdens een uitvoering en deze weergeven als grafieken in Azure portal. 

|Geregistreerde waarde|Voorbeeldcode| Weergeven in portal|
|----|----|----|
|Meld u een matrix met numerieke waarden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|één variabele lijndiagram weer te geven|
|Meld u één numerieke waarde met dezelfde metrische naam herhaaldelijk gebruikt (zoals vanuit een for-lus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| één variabele lijndiagram weer te geven|
|Meld u een rij met 2 numerieke kolommen herhaaldelijk|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Twee variabelen lijndiagram weer te geven|
|Logboektabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Twee variabelen lijndiagram weer te geven|


## <a name="example-notebooks"></a>Voorbeeld-laptops
De volgende notebooks illustratie van concepten in dit artikel:
* [How-to-use-azureml/training/Train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [How-to-use-azureml/training/Train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/training/Logging-API/Logging-API.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Probeer de volgende stappen voor informatie over het gebruik van de Azure Machine Learning-SDK voor Python:

* Bekijk een voorbeeld van hoe u het beste model registreren en deze implementeren in de zelfstudie [een model van de installatiekopie classificatie met Azure Machine Learning te trainen](tutorial-train-models-with-aml.md).

* Meer informatie over het [PyTorch-modellen met Azure Machine Learning te trainen](how-to-train-pytorch.md).
