---
title: Log ML experimenten & metrische gegevens
titleSuffix: Azure Machine Learning
description: Bewaak uw Azure ML experimenten en bewaak metrische uitvoerings gegevens om het proces voor het maken van het model te verbeteren. Voeg logboek registratie toe aan uw trainings script en Bekijk de geregistreerde resultaten van een uitvoering.  Gebruik run. log, run. start_logging of ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.custom: seodec18
ms.openlocfilehash: c72de809dc5818cced95be2cbd6b47308bad4f22
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045211"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Uitvoeringen en metrische gegevens van Azure ML-experimenten bewaken

Verbeter het proces voor het maken van het model door de metrische gegevens voor experimenten en bewakings uitvoeringen bij te houden. In dit artikel leert u hoe u logboek registratie code kunt toevoegen aan uw trainings script, een experiment moet verzenden, de uitvoering ervan kunt controleren en de resultaten kunt controleren in Azure Machine Learning.

> [!NOTE]
> Azure Machine Learning kunnen ook gegevens van andere bronnen tijdens de training registreren, zoals geautomatiseerde machine learning uitvoeringen, of de docker-container die de trainings taak uitvoert. Deze logboeken worden niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

## <a name="available-metrics-to-track"></a>Beschik bare metrische gegevens om bij te houden

De volgende metrische gegevens kunnen worden toegevoegd aan een run tijdens het trainen van een experiment. Zie de [referentie documentatie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)voor het uitvoeren van klassen voor een gedetailleerde lijst met wat er kan worden gevolgd bij een uitvoering.

|Type| Python-functie | Opmerkingen|
|----|:----|:----|
|Scalaire waarden |Functieassembly<br>`run.log(name, value, description='')`<br><br>Voorbeeld:<br>run. log ("nauw keurig", 0,95) |Registreer een numerieke of teken reeks waarde voor de uitvoering met de opgegeven naam. Als u een metriek registreert voor een run, wordt de metrische waarde opgeslagen in het rapport uitvoeren in het experiment.  U kunt dezelfde metrische gegevens meerdere keren in een uitvoering registreren. het resultaat wordt gezien als een vector van die metriek.|
|Certificaatintrekkingslijst|Functieassembly<br>`run.log_list(name, value, description='')`<br><br>Voorbeeld:<br>run. log _list ("keurigheden", [0,6, 0,7, 0,87]) | Een lijst met waarden vastleggen voor de uitvoering met de opgegeven naam.|
|rijkoppen|Functieassembly<br>`run.log_row(name, description=None, **kwargs)`<br>Voorbeeld:<br>run. log _row ("Y over X", X = 1, Y = 0,4) | Met *log_row* maakt u een metriek met meerdere kolommen zoals beschreven in kwargs. Elke benoemde para meter genereert een kolom met de opgegeven waarde.  *log_row* kan eenmaal worden aangeroepen om een wille keurige tupel of meerdere keren in een lus te registreren om een volledige tabel te genereren.|
|Tabel|Functieassembly<br>`run.log_table(name, value, description='')`<br><br>Voorbeeld:<br>run. log _table ("Y over X", {"X": [1, 2, 3], "Y": [0,6, 0,7, 0,89]}) | Een Dictionary-object in een logboek vastleggen voor de uitvoering met de opgegeven naam. |
|Installatiekopieën|Functieassembly<br>`run.log_image(name, path=None, plot=None)`<br><br>Voorbeeld:<br>`run.log_image("ROC", plt)` | Een installatie kopie vastleggen in het rapport uitvoeren. Gebruik log_image om een afbeeldings bestand of een matplotlib-plot te registreren bij de uitvoering.  Deze installatie kopieën worden weer gegeven en vergeleken in de run-record.|
|Een uitvoering labelen|Functieassembly<br>`run.tag(key, value=None)`<br><br>Voorbeeld:<br>run. tag ("geselecteerd", "ja") | Label de run met een teken reeks sleutel en een optionele teken reeks waarde.|
|Bestand of map uploaden|Functieassembly<br>`run.upload_file(name, path_or_stream)`<br> <br> Voorbeeld:<br>run. upload_file ("best_model. PKL", "./model.PKL") | Upload een bestand naar het run-record. Voert automatisch een capture-bestand uit in de opgegeven uitvoermap, die standaard wordt ingesteld op './outputs ' voor de meeste typen uitvoering.  Gebruik upload_file alleen als er extra bestanden moeten worden geüpload of als er geen uitvoer Directory is opgegeven. We raden aan om `outputs` toe te voegen aan de naam, zodat deze wordt geüpload naar de uitvoer Directory. U kunt alle bestanden weer geven die zijn gekoppeld aan deze record voor uitvoeren door de naam `run.get_file_names()`|

> [!NOTE]
> Metrische gegevens voor scalaire waarden, lijsten, rijen en tabellen kunnen type: float, integer of string hebben.

## <a name="choose-a-logging-option"></a>Kies een optie voor logboek registratie

Als u uw experiment wilt bijhouden of controleren, moet u code toevoegen om logboek registratie te starten wanneer u de uitvoering verzendt. Hier volgen enkele manieren om het indienen van de uitvoering te activeren:
* __Run. start_logging__ : Voeg logboek functies toe aan uw trainings script en start een interactieve logboek registratie sessie in het opgegeven experiment. **start_logging** maakt een interactieve uitvoering voor gebruik in scenario's zoals notebooks. Alle metrische gegevens die tijdens de sessie zijn geregistreerd, worden toegevoegd aan de run-record in het experiment.
* __ScriptRunConfig__ : Voeg logboek registratie functies toe aan uw trainings script en laad de volledige map met de uitvoering.  **ScriptRunConfig** is een klasse voor het instellen van configuraties voor script uitvoeringen. Met deze optie kunt u een bewakings code toevoegen om op de hoogte te worden gesteld, of om een visuele widget te laten bewaken.

## <a name="set-up-the-workspace"></a>De werk ruimte instellen
Voordat u logboek registratie toevoegt en een experiment verzendt, moet u de werk ruimte instellen.

1. Laad de werk ruimte. Zie [werkruimte configuratie bestand](how-to-configure-environment.md#workspace)voor meer informatie over het instellen van de configuratie van de werk ruimte.

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Optie 1: gebruik start_logging

**start_logging** maakt een interactieve uitvoering voor gebruik in scenario's zoals notebooks. Alle metrische gegevens die tijdens de sessie zijn geregistreerd, worden toegevoegd aan de run-record in het experiment.

In het volgende voor beeld wordt een eenvoudig gesklearne Ploois model lokaal in een lokale Jupyter-notebook getraind. Zie [Compute-doelen voor model training instellen met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets)voor meer informatie over het verzenden van experimenten voor verschillende omgevingen.

1. Een trainings script maken in een lokale Jupyter-notebook. 

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

2. Voeg experiment tracking toe met behulp van de Azure Machine Learning SDK en upload een persistent model in de record experiment run. Met de volgende code worden tags, logboeken en uploads van een model bestand naar de uitvoering van het experiment toegevoegd.

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

    Het script eindigt op ```run.complete()```, wat aangeeft dat de uitvoering is voltooid.  Deze functie wordt doorgaans gebruikt in scenario's met interactieve laptops.

## <a name="option-2-use-scriptrunconfig"></a>Optie 2: gebruik ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) is een klasse voor het instellen van configuraties voor script uitvoeringen. Met deze optie kunt u een bewakings code toevoegen om op de hoogte te worden gesteld, of om een visuele widget te laten bewaken.

In dit voor beeld wordt het sklearn-model van de bovenstaande basis uitgebreid. Het biedt een eenvoudige parameter opruiming voor het opruimen van de alfa waarden van het model voor het vastleggen van metrische gegevens en getrainde modellen in uitvoeringen onder het experiment. Het voor beeld wordt lokaal uitgevoerd op een door de gebruiker beheerde omgeving. 

1. Een trainings script maken `train.py`.

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

2. Het `train.py` script verwijst naar `mylib.py` waarmee u de lijst met alfa waarden kunt ophalen die in het ploois model kunnen worden gebruikt.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configureer een door de gebruiker beheerde lokale omgeving.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Dien het ```train.py```-script in om uit te voeren in de door de gebruiker beheerde omgeving. Deze hele script-map wordt verzonden voor training, inclusief het ```mylib.py```-bestand.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Een uitvoering beheren

In het [Start-, controle-en annulerings artikel over training worden](how-to-manage-runs.md) specifieke Azure machine learning werk stromen gemarkeerd voor het beheren van uw experimenten.

## <a name="view-run-details"></a>Details van uitvoering weer geven

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Bewaking uitvoeren met Jupyter notebook-widget
Wanneer u de methode **ScriptRunConfig** gebruikt om uitvoeringen te verzenden, kunt u de voortgang van de uitvoering met een [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)bekijken. Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10-15 seconden live updates totdat de taak is voltooid.

1. Bekijk de widget Jupyter tijdens het wachten tot de uitvoering is voltooid.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Scherm opname van Jupyter notebook-widget](./media/how-to-track-experiments/run-details-widget.png)

U kunt ook een koppeling naar dezelfde weer gave in uw werk ruimte ophalen.

```python
print(run.get_portal_url())
```

2. **[Voor automatische machine learning uitvoeringen]** Om toegang te krijgen tot de grafieken van een vorige uitvoering. Vervang `<<experiment_name>>` door de juiste naam van het experiment:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook-widget voor automatische Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Als u meer details van een pijp lijn wilt weer geven, klikt u op de pijp lijn die u wilt verkennen in de tabel. de grafieken worden weer gegeven in een pop-up van de Azure Portal.

### <a name="get-log-results-upon-completion"></a>Resultaten van logboeken weergeven bij voltooiing

Model training en-bewaking vindt plaats op de achtergrond, zodat u andere taken kunt uitvoeren terwijl u wacht. U kunt ook wachten tot het model training heeft voltooid voordat u meer code uitvoert. Wanneer u **ScriptRunConfig**gebruikt, kunt u ```run.wait_for_completion(show_output = True)``` gebruiken om te laten zien wanneer de model training is voltooid. De vlag ```show_output``` biedt uitgebreide uitvoer. 


### <a name="query-run-metrics"></a>Metrische gegevens van query uitvoeren

U kunt de metrische gegevens van een getraind model weer geven met behulp van ```run.get_metrics()```. U kunt nu alle metrische gegevens ophalen die zijn vastgelegd in bovenstaand voor beeld om het beste model te bepalen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal-or-your-workspace-landing-page-previewhttpsmlazurecom"></a>Bekijk het experiment in de Azure Portal of de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com)

Wanneer een experiment is uitgevoerd, kunt u bladeren naar de record voor het uitvoeren van opgenomen experimenten. U kunt de geschiedenis op twee manieren openen:

* De URL naar de uitvoering rechtstreeks ophalen ```print(run.get_portal_url())```
* Bekijk de details van de uitvoering door de naam van de uitvoeringsrun (in dit geval ```run```) in te dienen. Op deze manier wijst u naar de naam van het experiment, de ID, het type, de status, de detail pagina, een koppeling naar de Azure Portal en een koppeling naar de documentatie.

Met de koppeling voor de uitvoering brengt u rechtstreeks naar de pagina Details uitvoeren in de Azure Portal. Hier ziet u alle eigenschappen, bijgehouden metrische gegevens, afbeeldingen en grafieken die zijn geregistreerd in het experiment. In dit geval hebben we de MSE en de alpha-waarden geregistreerd.

  ![Details uitvoeren in de Azure Portal](./media/how-to-track-experiments/run-details-page.png)

U kunt ook uitvoer of logboeken bekijken voor de uitvoering, of de moment opname van het experiment downloaden dat u hebt verzonden, zodat u de map experiment met anderen kunt delen.

### <a name="viewing-charts-in-run-details"></a>Grafieken weer geven in details van uitvoering

Er zijn verschillende manieren om de logboek registratie-Api's te gebruiken voor het vastleggen van verschillende soorten metrische gegevens tijdens een uitvoering en om ze weer te geven als grafieken in de Azure Portal. 

|Geregistreerde waarde|Voorbeeld code| Weer geven in de portal|
|----|----|----|
|Een matrix met numerieke waarden vastleggen in een logboek| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|lijn diagram met één variabele|
|Een enkele numerieke waarde met dezelfde metrische naam in het logboek vastleggen, herhaaldelijk gebruikt (bijvoorbeeld van binnen een for-lus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Lijn diagram met één variabele|
|Een rij met 2 numerieke kolommen herhaaldelijk vastleggen|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Lijn diagram met twee variabelen|
|Logboek tabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Lijn diagram met twee variabelen|


## <a name="example-notebooks"></a>Voorbeeld notitieblokken
In de volgende notitie blokken worden concepten in dit artikel gedemonstreerd:
* [instructies-to-use-azureml/training/training-binnen-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [instructies-to-use-azureml/training/trein-on-Local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [How-to-use-azureml/training/logging-API/logging-API. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Voer de volgende stappen uit om te leren hoe u de Azure Machine Learning SDK voor python kunt gebruiken:

* Bekijk een voor beeld van hoe u het beste model registreert en implementeert in de zelf studie, [een classificatie model voor installatie kopieën traint met Azure machine learning](tutorial-train-models-with-aml.md).

* Meer informatie over het [trainen van PyTorch-modellen met Azure machine learning](how-to-train-pytorch.md).
