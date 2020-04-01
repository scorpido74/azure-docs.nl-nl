---
title: De interpretatiemogelijkheden van modellen bij geautomatiseerde machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uitleg krijgt over hoe uw geautomatiseerde ML-model het belang van de functie bepaalt en voorspellingen doet bij het gebruik van de Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 03/11/2020
ms.openlocfilehash: e2465a2df3fab736c8f118911da14ef23c8aec86
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437284"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>De interpretatiemogelijkheden van modellen bij geautomatiseerde machine learning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de functies voor interpreteerbaarheid voor geautomatiseerde machine learning (ML) inAzure Machine Learning inschakelen. Geautomatiseerde ML helpt u inzicht te krijgen in het belang van de engineered feature. 

Alle SDK-versies na standaard ingesteld `model_explainability=True` op 1.0.85. In SDK-versie 1.0.85 en eerdere `model_explainability=True` versies `AutoMLConfig` moeten gebruikers het object instellen om modelinterpreteerbaarheid te gebruiken. 

In dit artikel leert u het volgende:

- Voer interpreteerbaarheid uit tijdens de training voor het beste model of een model.
- Schakel visualisaties in om u te helpen patronen in gegevens en uitleg te zien.
- Implementeer interpreteerbaarheid tijdens gevolgtrekking of score.

## <a name="prerequisites"></a>Vereisten

- Eigenschappen van interpreteerbaarheid. Ren `pip install azureml-interpret azureml-contrib-interpret` om de nodige pakketten te krijgen.
- Kennis van het bouwen van geautomatiseerde ML experimenten. Voer deze zelfstudie voor [het regressiemodel in](tutorial-auto-train-models.md) voor meer informatie over het gebruik van de Azure Machine Learning SDK of het configureren van [geautomatiseerde ML-experimenten.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Interpreteerbaarheid tijdens de training voor het beste model

Haal de uitleg `best_run`uit de , die uitleg voor engineered functies bevat.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Download engineered feature belang van artefact store

U kunt `ExplanationClient` gebruiken om de engineered functie uitleg `best_run`te downloaden van de artefact winkel van de . 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpreteerbaarheid tijdens de training voor elk model 

Wanneer u modeluitleg berekent en visualiseert, bent u niet beperkt tot een bestaande modeluitleg voor een geautomatiseerd ML-model. U ook een verklaring krijgen voor uw model met verschillende testgegevens. De stappen in deze sectie laten u zien hoe u het belang van de ontworpen functie berekenen en visualiseren op basis van uw testgegevens.

### <a name="retrieve-any-other-automl-model-from-training"></a>Elk ander AutoML-model ophalen uit de training

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>De modeluitleg instellen

Gebruik `automl_setup_model_explanations` om de gemanipuleerde uitleg te krijgen. Het `fitted_model` kan de volgende items genereren:

- Aanbevolen gegevens van getrainde of testvoorbeelden
- Lijsten met door de kundige functie
- Vindbare klassen in de kolom met label in classificatiescenario's

De `automl_explainer_setup_obj` bevat alle structuren van bovenstaande lijst.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initialiseer de Mimic Explainer voor functiebelang

Gebruik de `MimicWrapper` klasse om een verklaring voor AutoML-modellen te genereren. U de MimicWrapper initialiseren met de volgende parameters:

- Het installatieobject explainer
- Uw werkruimte
- Een LightGBM-model, dat fungeert `fitted_model` als surrogaat voor het geautomatiseerde ML-model

De MimicWrapper neemt `automl_run` ook het object waar de engineered uitleg zal worden geüpload.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>MimicExplainer gebruiken voor het berekenen en visualiseren van het belang van de engineered feature

U `explain()` de methode in MimicWrapper aanroepen met de getransformeerde testmonsters om het functiebelang voor de gegenereerde engineered functies te krijgen. U `ExplanationDashboard` ook de dashboardvisualisatie van de functiebelangwaarden van de gegenereerde engineered-functies bekijken door geautomatiseerde ML-featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Interpreteerbaarheid tijdens gevolgtrekking

In deze sectie leert u hoe u een geautomatiseerd ML-model operationaliseren met de uitleg die is gebruikt om de uitleg in de vorige sectie te berekenen.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registreer het model en de scoreexplainer

Gebruik `TreeScoringExplainer` de scoreexplainer die de ontworpen functiebelangwaarden op gevolgtrekkingstijd berekent. U initialiseert de scoreexplainer `feature_map` met de scoredie eerder is berekend. 

Sla de scoreexplainer op en registreer vervolgens het model en de scoreuitleg bij de Model Management Service. Voer de volgende code uit:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>De afhankelijkheden van conda maken voor het instellen van de service

Maak vervolgens de benodigde omgevingsafhankelijkheden in de container voor het geïmplementeerde model. Houd er rekening mee dat azureml-standaardinstellingen met versie >= 1.0,45 moeten worden vermeld als een pip-afhankelijkheid, omdat het de functionaliteit bevat die nodig is om het model als webservice te hosten.

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

Implementeer de service met behulp van het conda-bestand en het scorebestand uit de vorige stappen.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Gevolgtrekking met testgegevens

Gevolgtrekking met een aantal testgegevens om de voorspelde waarde van geautomatiseerd ML-model te zien. Bekijk het belang van de ontworpen functie voor de voorspelde waarde.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualiseer om patronen in gegevens en uitleg te ontdekken tijdens de training

U de functiebelangrijkgrafiek in uw werkruimte visualiseren in [Azure Machine Learning-studio.](https://ml.azure.com) Nadat de geautomatiseerde ML-run is voltooid, selecteert u **Modeldetails weergeven** om een specifieke run weer te geven. Selecteer het tabblad **Uitleg** om het dashboard voor uitlegvisualisatie weer te geven.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

Zie het [conceptartikel over interpreteerbaarheid](how-to-machine-learning-interpretability.md)voor meer informatie over hoe u modeluitleg en functiebelang inschakelen op andere gebieden van de Azure Machine Learning SDK dan geautomatiseerde machine learning.
