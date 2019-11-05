---
title: Model vertolking in automatische ML
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u het model voor automatische ML voorspellingen maakt met behulp van de Azure Machine Learning SDK. Het kan worden gebruikt tijdens training en demijnen om te begrijpen hoe het model van de functie belang rijk is en voor spellingen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515328"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Model interpreteren voor geautomatiseerde ML modellen

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze procedure leert u hoe u de functionaliteit voor het interpreteren kunt inschakelen in geautomatiseerde machine learning met behulp van Azure Machine Learning-service. Met geautomatiseerd ML kunt u zowel het belang van onbewerkte als ontworpen functies begrijpen. Als u de functie voor het interpreteren van modellen wilt gebruiken, stelt u `model_explainability=True` in het `AutoMLConfig`-object in.  

In dit artikel leert u de volgende taken:

* Interpretiteit tijdens de training voor het beste model of een model
* Visualisaties inschakelen om u te helpen bij het detecteren van patronen in gegevens en uitleg
* Interpretiteit tijdens deinterferentie of Score ring

## <a name="prerequisites"></a>Vereisten

* Voer `pip install azureml-interpret azureml-contrib-interpret` uit om de benodigde pakketten te verkrijgen voor de functies voor het interpreteren.
* In dit artikel wordt ervan uitgegaan dat u geautomatiseerde ML experimenten ontwikkelt. Raadpleeg de [zelf studie](tutorial-auto-train-models.md) of [procedures](how-to-configure-auto-train.md) voor meer informatie over het gebruik van automatische ml in de SDK.
 
## <a name="interpretability-during-training-for-the-best-model"></a>Interpretiteit tijdens de training voor het beste model 

Haal de uitleg op uit het `best_run`, dat uitleg bevat over de functies van ontworpen en onbewerkte functies. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Belang rijk onderdeel van de artefact opslag downloaden

U kunt `ExplanationClient` gebruiken om de toelichtingen van de functie te downloaden uit het artefact archief van de best_run. Als u de uitleg wilt weten voor de `raw=True`onbewerkte functies, stelt u deze in. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretiteit tijdens de training voor elk model 

In deze sectie leert u hoe u model uitleg kunt berekenen en hoe u de uitleg visualert. Naast het ophalen van een bestaande beschrijving van een model voor een geautomatiseerd ML-model, kunt u ook uw model uitleggen met verschillende test gegevens. Met de volgende stappen kunt u het urgentie-en onbewerkte onderdeel van de functie op basis van uw test gegevens berekenen en visualiseren.

### <a name="retrieve-any-other-automl-model-from-training"></a>Alle andere AutoML-modellen uit de training ophalen

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>De beschrijving van het model instellen

De fitted_model kan de volgende items genereren, die worden gebruikt voor het ophalen van de toelichtingen van de functie en onbewerkte functies met behulp van automl_setup_model_explanations:

* Featurized gegevens uit de voor beelden van Train/test
* Lijst met functies en onbewerkte functie naam lijsten verzamelen
* De klassen in uw gelabelde kolom zoeken in classificatie scenario's

De automl_explainer_setup_obj bevat alle structuren uit de bovenstaande lijst.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>De belichtings uitleg voor de functie prioriteit initialiseren

Gebruik de klasse `MimicWrapper` voor het uitleggen van de AutoML modellen. De MimicWrapper kan worden geïnitialiseerd met para meters voor het object van de uitleger installatie, uw werk ruimte en een LightGBM-model dat als surrogaat model fungeert om het Automated ML model (fitted_model hier) te verklaren. De MimicWrapper neemt ook het automl_run-object op waar de onbewerkte en geengineerde verklaringen worden geüpload.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>MimicExplainer gebruiken voor het berekenen en visualiseren van de belang rijke functie

De methode uitleggen () in MimicWrapper kan worden aangeroepen met de getransformeerde test voorbeelden om het belang van de functie voor de gegenereerde functies te verkrijgen. U kunt ExplanationDashboard ook gebruiken om de visualisatie van het dash board weer te geven van de belang rijke waarden van de functie van de gegenereerde functies met featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Geïmiteerde uitleg gebruiken voor het berekenen en visualiseren van de urgentie van RAW-onderdelen

De methode uitleggen () in MimicWrapper kan opnieuw worden aangeroepen met de getransformeerde test voorbeelden en het instellen van `get_raw` op waar om het belang van de functie voor de onbewerkte functies te verkrijgen. U kunt ExplanationDashboard ook gebruiken om de dashboard visualisatie van de belang rijke waarden van de functie van de onbewerkte functies weer te geven.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interpretiteit tijdens deinterferentie

In deze sectie leert u hoe u een geautomatiseerd ML-model kunt operationeel maken met de uitleger, die is gebruikt om de uitleg in de vorige sectie te berekenen.

### <a name="register-the-model-and-the-scoring-explainer"></a>Het model en de uitleg over scores registreren

Gebruik de `TreeScoringExplainer` om de Score uitleger te maken, die wordt gebruikt om de belang rijke waarden van de onbewerkte en gewerkte functie te berekenen op het tijdstip van de afleiding. Houd er rekening mee dat u de beoordelings uitleg initialiseert met de feature_map die eerder is berekend. De feature_map wordt gebruikt door de Score uitleger om het belang van de onbewerkte functie te retour neren.

In de onderstaande code slaat u de uitleg van de scoreer op en registreert u het model en de uitleg van de score met de Modelbeheer service.

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>De Conda-afhankelijkheden voor het instellen van de service maken

Vervolgens maakt u de omgevings afhankelijkheden die nodig zijn in de container voor het geïmplementeerde model.

```python
from azureml.core.conda_dependencies import CondaDependencies 

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Implementeer de service

Implementeer de service met het Conda-bestand en het Score bestand uit de vorige stappen.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Demijnren met behulp van test gegevens

U kunt bepaalde test gegevens gebruiken om de voorspelde waarde van het model Automated ML te bekijken en het belang van de functie te bekijken voor de voorspelde waarde en het belang van de onbewerkte functie voor de voorspelde waarde.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Visualisaties die u helpen bij het ontdekken van patronen in gegevens en uitleg tijdens de trainings tijd

U kunt ook het grafiek onderdeel urgentie in uw werk ruimte in [Azure machine learning Studio](https://ml.azure.com)visualiseren. Zodra de automatische ML-uitvoering is voltooid, moet u op model details weer geven klikken, waarmee u naar een specifieke uitvoering gaat. Hier klikt u op het tabblad uitleg om het visualisatie dashboard voor uitleg te bekijken. 

[Architectuur van Machine Learning interpretatie ![](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over hoe model toelichtingen en functie belang kunnen worden ingeschakeld op andere gebieden van de SDK buiten automatische machine learning, het [concept](how-to-machine-learning-interpretability.md) artikel over de interpretatie mogelijkheden.