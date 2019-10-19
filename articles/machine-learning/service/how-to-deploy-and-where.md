---
title: Hoe en waar modellen moeten worden geïmplementeerd
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe en waar u uw Azure Machine Learning-modellen kunt implementeren, waaronder Azure Container Instances, de Azure Kubernetes-service, Azure IoT Edge en Programmeer bare poort matrices voor velden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 03fb21197b2e71603b8078945e16ccd69f151577
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555775"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellen met Azure Machine Learning implementeren

Meer informatie over het implementeren van uw machine learning model als een webservice in de Azure-Cloud of het Azure IoT Edge van apparaten.

De werk stroom is vergelijkbaar, [waar u uw model implementeert](#target) :

1. Registreer het model.
1. Voorbereiden op implementeren. (Assets opgeven, gebruik, reken doel.)
1. Implementeer het model op het Compute-doel.
1. Test het geïmplementeerde model, ook wel webservice genoemd.

Zie [modellen beheren, implementeren en bewaken met Azure machine learning](concept-model-management-and-deployment.md)voor meer informatie over de concepten die zijn betrokken bij de implementatie werk stroom.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een model. Als u geen getraind model hebt, kunt u het model en de afhankelijkheids bestanden van [deze zelf studie](https://aka.ms/azml-deploy-cloud)gebruiken.

- De [Azure cli-extensie voor de machine learning-service](reference-azure-machine-learning-cli.md), de [Azure machine learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of de [Azure machine learning Visual Studio code extension](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werk ruimte

De volgende code laat zien hoe u verbinding maakt met een Azure Machine Learning-werk ruimte met behulp van informatie in cache naar de lokale ontwikkel omgeving:

+ **De SDK gebruiken**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Zie de documentatie van [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) voor meer informatie over het gebruik van de SDK om verbinding te maken met een werk ruimte.

+ **De CLI gebruiken**

   Bij gebruik van de CLI gebruikt u de para meter `-w` of `--workspace-name` om de werk ruimte voor de opdracht op te geven.

+ **VS code gebruiken**

   Wanneer u VS code gebruikt, selecteert u de werk ruimte met behulp van een grafische interface. Zie [modellen implementeren en beheren](how-to-vscode-tools.md#deploy-and-manage-models) in de documentatie over de VS code-extensie voor meer informatie.

## <a id="registermodel"></a>Uw model registreren

Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werk ruimte. Nadat u de bestanden hebt geregistreerd, kunt u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op naar een Cloud locatie (van een trainings uitvoering) of van een lokale map. Dit pad is alleen bedoeld voor het vinden van de bestanden die worden geüpload als onderdeel van het registratie proces. Het hoeft niet overeen te komen met het pad dat wordt gebruikt in het vermeldings script. Zie voor meer informatie [model bestanden zoeken in uw invoer script](#locate-model-files-in-your-entry-script).

Machine learning-modellen worden geregistreerd in uw Azure Machine Learning-werk ruimte. Het model kan afkomstig zijn van Azure Machine Learning of van een andere locatie. De volgende voor beelden laten zien hoe u een model kunt registreren.

### <a name="register-a-model-from-an-experiment-run"></a>Een model registreren vanuit een experiment

De code fragmenten in deze sectie laten zien hoe u een model kunt registreren bij een trainings uitvoering:

> [!IMPORTANT]
> Als u deze fragmenten wilt gebruiken, moet u eerder een training hebben uitgevoerd en moet u toegang hebben tot het `Run` object (SDK-voor beeld) of de waarde run ID (CLI-voor beeld). Zie [Compute-doelen voor model training instellen](how-to-set-up-training-targets.md)voor meer informatie over trainings modellen.

+ **De SDK gebruiken**

  Wanneer u de SDK gebruikt voor het trainen van een model, kunt u een [uitvoerings](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) object of een [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) -object ontvangen, afhankelijk van hoe u het model hebt getraind. Elk object kan worden gebruikt voor het registreren van een model dat is gemaakt door een experiment.

  + Een model registreren vanuit een `azureml.core.Run`-object:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    De para meter `model_path` verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt u `model_path` in op het pad van een map die de bestanden bevat. Zie de documentatie van [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) voor meer informatie.

  + Een model registreren vanuit een `azureml.train.automl.run.AutoMLRun`-object:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    In dit voor beeld worden de para meters `metric` en `iteration` niet opgegeven, zodat de iteratie met de beste primaire metriek wordt geregistreerd. De `model_id` waarde die wordt geretourneerd door de uitvoering, wordt gebruikt in plaats van een model naam.

    Zie de documentatie van [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) voor meer informatie.

+ **De CLI gebruiken**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  De para meter `--asset-path` verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt u `--asset-path` in op het pad van een map die de bestanden bevat.

+ **VS code gebruiken**

  Registreer modellen met behulp van een of meer model bestanden of mappen met de [VS code](how-to-vscode-tools.md#deploy-and-manage-models) -extensie.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

U kunt een model registreren door het lokale pad van het model op te geven. U kunt het pad naar een map of één bestand opgeven. U kunt deze methode gebruiken om modellen te registreren die zijn getraind met Azure Machine Learning en vervolgens te downloaden. U kunt deze methode ook gebruiken voor het registreren van modellen die buiten Azure Machine Learning worden getraind.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **De SDK en ONNX gebruiken**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt u `model_path` in op het pad van een map die de bestanden bevat.

+ **De CLI gebruiken**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Als u meerdere bestanden wilt toevoegen aan de model registratie, stelt u `-p` in op het pad van een map die de bestanden bevat.

**Geschatte tijd**: ongeveer 10 seconden.

Zie de documentatie voor de [model klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)voor meer informatie.

Zie [een bestaand model implementeren](how-to-deploy-existing-model.md)voor meer informatie over het werken met modellen die buiten Azure machine learning zijn getraind.

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Een reken doel kiezen

U kunt de volgende Compute-doelen of reken resources gebruiken om de implementatie van de webservice te hosten:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Implementatie voorbereiden

Voor het implementeren van het model hebt u de volgende items nodig:

* **Een invoer script**. Met dit script worden aanvragen geaccepteerd, worden de aanvragen met behulp van het model gescoord en worden de resultaten geretourneerd.

    > [!IMPORTANT]
    > * Het invoer script is specifiek voor uw model. Het moet inzicht krijgen in de indeling van de gegevens van de inkomende aanvraag, de indeling van de gegevens die worden verwacht door uw model en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    >   Als de gegevens van de aanvraag een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook de reactie transformeren voordat deze wordt geretourneerd naar de client.
    >
    > * De SDK van Azure Machine Learning biedt geen manier voor webservices of IoT Edge implementaties om toegang te krijgen tot uw gegevens archief of data sets. Als uw geïmplementeerde model toegang moet hebben tot gegevens die buiten de implementatie zijn opgeslagen, zoals gegevens in een Azure Storage-account, moet u een aangepaste code oplossing ontwikkelen met behulp van de relevante SDK. Bijvoorbeeld de [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python).
    >
    >   Een alternatief dat kan worden gebruikt voor uw scenario is [batch voorspelling](how-to-run-batch-predictions.md), waarmee tijdens de Score toegang wordt geboden tot gegevens archieven.

* **Afhankelijkheden**, zoals helper-scripts of python/Conda-pakketten die zijn vereist voor het uitvoeren van het script of model van de vermelding.

* **De implementatie configuratie** voor het reken doel dat als host fungeert voor het geïmplementeerde model. In deze configuratie worden de vereisten voor geheugen en CPU beschreven die nodig zijn om het model uit te voeren.

Deze items worden ingekapseld in een Afleidings *configuratie* en een *implementatie configuratie*. De configuratie voor afwijzen verwijst naar het script voor de vermelding en andere afhankelijkheden. U definieert deze configuraties programmatisch wanneer u de SDK gebruikt om de implementatie uit te voeren. U definieert ze in JSON-bestanden wanneer u de CLI gebruikt.

### <a id="script"></a>1. uw invoer script en afhankelijkheden definiëren

Het item script ontvangt gegevens die zijn verzonden naar een geïmplementeerde webservice en door gegeven aan het model. Vervolgens wordt het antwoord opgehaald dat door het model is geretourneerd en wordt dat naar de client geretourneerd. *Het script is specifiek voor uw model*. Het moet inzicht hebben in de gegevens die het model verwacht en retourneert.

Het script bevat twee functies die het model laden en uitvoeren:

* `init()`: deze functie laadt meestal het model in een globaal object. Deze functie wordt slechts één keer uitgevoerd wanneer de docker-container voor uw webservice wordt gestart.

* `run(input_data)`: deze functie maakt gebruik van het model voor het voors pellen van een waarde op basis van de invoer gegevens. Invoer en uitvoer van de run worden meestal JSON gebruikt voor serialisatie en deserialisatie. U kunt ook werken met onbewerkte binaire gegevens. U kunt de gegevens transformeren voordat u deze naar het model verzendt of voordat u deze naar de client stuurt.

#### <a name="locate-model-files-in-your-entry-script"></a>Model bestanden zoeken in uw invoer script

Er zijn twee manieren om modellen te vinden in het involgings script:
* `AZUREML_MODEL_DIR`: een omgevings variabele die het pad naar de model locatie bevat.
* `Model.get_model_path`: een API die het pad naar het model bestand retourneert met de geregistreerde model naam.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR is een omgevings variabele die tijdens de implementatie van de service wordt gemaakt. U kunt deze omgevings variabele gebruiken om de locatie van de geïmplementeerde model (en) te vinden.

De volgende tabel beschrijft de waarde van AZUREML_MODEL_DIR, afhankelijk van het aantal geïmplementeerde modellen:

| Implementatie | Waarde van omgevings variabele |
| ----- | ----- |
| Eén model | Het pad naar de map die het model bevat. |
| Meerdere modellen | Het pad naar de map met alle modellen. Modellen bevinden zich op naam en versie in deze map (`$MODEL_NAME/$VERSION`) |

Als u het pad naar een bestand in een model wilt ophalen, combineert u de omgevings variabele met de bestands naam waarnaar u op zoek bent.
De bestands namen van de model bestanden blijven behouden tijdens registratie en implementatie. 

**Voor beeld van één model**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Voor beeld van meerdere modellen**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Wanneer u een model registreert, geeft u een model naam op die wordt gebruikt voor het beheren van het model in het REGI ster. U gebruikt deze naam met de methode [model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) om het pad van het model bestand of de bestanden op het lokale bestands systeem op te halen. Als u een map of een verzameling bestanden registreert, retourneert deze API het pad van de map die de bestanden bevat.

Wanneer u een model registreert, geeft u het een naam. De naam komt overeen met de locatie waar het model lokaal of tijdens de service-implementatie wordt geplaatst.

> [!IMPORTANT]
> Als u automatische machine learning hebt gebruikt om een model te trainen, wordt een `model_id` waarde gebruikt als de naam van het model. Zie [Azure-MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment) op github voor een voor beeld van het registreren en implementeren van een model dat is getraind met automatische machine learning.

In het volgende voor beeld wordt een pad geretourneerd naar één bestand met de naam `sklearn_mnist_model.pkl` (dat is geregistreerd met de name `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Beschrijving Automatisch schema genereren

Als u automatisch een schema voor uw webservice wilt genereren, geeft u een voor beeld van de invoer en/of uitvoer in de constructor voor een van de gedefinieerde type-objecten. Het type en het voor beeld worden gebruikt voor het automatisch maken van het schema. Azure Machine Learning maakt vervolgens tijdens de implementatie een [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger)-specificatie voor de webservice.

Deze typen worden momenteel ondersteund:

* `pandas`
* `numpy`
* `pyspark`
* Standard python-object

Als u schema generatie wilt gebruiken, neemt u het `inference-schema`-pakket op in uw Conda-omgevings bestand.

##### <a name="example-dependencies-file"></a>Voor beeld van afhankelijkheids bestand

De volgende YAML is een voor beeld van een Conda-afhankelijkheids bestand voor ingrijpen:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Als u automatische schema generatie wilt gebruiken, moet uw invoer script de `inference-schema`-pakketten importeren.

Definieer de voorbeeld indelingen voor invoer en uitvoer in de `input_sample` en `output_sample` variabelen, die de aanvraag-en antwoord indelingen voor de webservice vertegenwoordigen. Gebruik deze voor beelden in de functie voor invoer en uitvoer op de functie `run()`. Het volgende voor beeld van scikit maakt gebruik van schema generatie.

##### <a name="example-entry-script"></a>Voorbeeld script

In het volgende voor beeld ziet u hoe u JSON-gegevens kunt accepteren en retour neren:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

In het volgende voor beeld ziet u hoe u de invoer gegevens als een `<key: value>` Dictionary definieert met behulp van een data frame. Deze methode wordt ondersteund voor het gebruik van de geïmplementeerde webservice van Power BI. (Meer[informatie over het gebruik van de webservice van Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Zie de volgende scripts voor meer voor beelden:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Binaire gegevens

Als uw model binaire gegevens accepteert, zoals een afbeelding, moet u het `score.py` bestand wijzigen dat voor uw implementatie wordt gebruikt om onbewerkte HTTP-aanvragen te accepteren. Als u onbewerkte gegevens wilt accepteren, gebruikt u de klasse `AMLRequest` in uw invoer script en voegt u de `@rawhttp` decorator toe aan de functie `run()`.

Hier volgt een voor beeld van een `score.py` waarin binaire gegevens worden geaccepteerd:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLRequest` klasse bevindt zich in de `azureml.contrib` naam ruimte. Entiteiten in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Alles in deze naam ruimte moet worden beschouwd als een preview-versie die niet volledig wordt ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Cross-Origin-resource delen (CORS)

Cross-Origin-resource delen is een manier om te voor komen dat resources op een webpagina worden aangevraagd vanuit een ander domein. CORS werkt via HTTP-headers die worden verzonden met de aanvraag van de client en worden geretourneerd met de service reactie. Zie [Cross-Origin-resource delen](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia voor meer informatie over CORS en geldige headers.

Als u uw model implementatie wilt configureren voor de ondersteuning van CORS, gebruikt u de `AMLResponse`-klasse in uw invoer script. Met deze klasse kunt u de headers op het antwoord object instellen.

In het volgende voor beeld wordt de `Access-Control-Allow-Origin` koptekst ingesteld voor het antwoord van het entry-script:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLResponse` klasse bevindt zich in de `azureml.contrib` naam ruimte. Entiteiten in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Alles in deze naam ruimte moet worden beschouwd als een preview-versie die niet volledig wordt ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definieer uw InferenceConfig

De configuratie voor afnemen beschrijft hoe u het model configureert om voor spellingen te maken. Deze configuratie maakt geen deel uit van uw invoer script. Het verwijst naar uw invoer script en wordt gebruikt voor het zoeken van alle resources die vereist zijn voor de implementatie. Het wordt later gebruikt wanneer u het model implementeert.

De configuratie voor het afwijzen van een Dede ring kan gebruikmaken van Azure Machine Learning omgevingen om de vereiste software afhankelijkheden voor uw implementatie te definiëren. Met omgevingen kunt u de software-afhankelijkheden maken, beheren en opnieuw gebruiken die zijn vereist voor training en implementatie. In het volgende voor beeld ziet u hoe u een omgeving laadt vanuit uw werk ruimte en deze vervolgens gebruikt met de configuratie voor inschakeling:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Zie [omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)voor meer informatie over omgevingen.

U kunt ook rechtstreeks de afhankelijkheden opgeven zonder een omgeving te gebruiken. In het volgende voor beeld ziet u hoe u een configuratie voor het afwijzen van een interferentie maakt waarmee software-afhankelijkheden worden geladen vanuit een Conda-bestand:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Zie de documentatie van de [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -klasse voor meer informatie.

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met een afnemende configuratie.

### <a name="cli-example-of-inferenceconfig"></a>CLI-voor beeld van InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

De volgende opdracht laat zien hoe u een model implementeert met behulp van de CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In dit voor beeld geeft de configuratie de volgende instellingen aan:

* Dat het model python vereist.
* Het [invoer script](#script), dat wordt gebruikt voor het afhandelen van webaanvragen die worden verzonden naar de geïmplementeerde service.
* Het Conda-bestand waarin de Python-pakketten worden beschreven die nodig zijn voor de.

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met een afnemende configuratie.

### <a name="3-define-your-deployment-configuration"></a>3. de implementatie configuratie definiëren

Voordat u uw model implementeert, moet u de implementatie configuratie definiëren. *De implementatie configuratie is specifiek voor het reken doel dat als host fungeert voor de webservice.* Wanneer u bijvoorbeeld een model lokaal implementeert, moet u de poort opgeven waar de service aanvragen accepteert. De implementatie configuratie maakt geen deel uit van het invoer script. Het wordt gebruikt voor het definiëren van de kenmerken van het Compute-doel die als host dienen voor het model en het script.

Mogelijk moet u ook de reken resource maken, als u bijvoorbeeld nog geen AKS-instantie (Azure Kubernetes service) hebt die aan uw werk ruimte is gekoppeld.

De volgende tabel bevat een voor beeld van het maken van een implementatie configuratie voor elk reken doel:

| Rekendoel | Implementatie configuratie-voor beeld |
| ----- | ----- |
| Lokaal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

De klassen voor lokale, Azure Container Instances-en AKS-webservices kunnen vanuit `azureml.core.webservice` worden geïmporteerd:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profileren

Voordat u uw model als een service implementeert, kunt u het beste een profiel voor het bepalen van de optimale CPU-en geheugen vereisten gebruiken. U kunt de SDK of de CLI gebruiken om uw model te profileren. In de volgende voor beelden ziet u hoe u een model kunt profielen met behulp van de SDK.

> [!IMPORTANT]
> Wanneer u profileren gebruikt, kan de configuratie voor het afwijzen van de deinterferentie niet verwijzen naar een Azure Machine Learning omgeving. Definieer in plaats daarvan de software afhankelijkheden met behulp van de para meter `conda_file` van het object `InferenceConfig`.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Met deze code wordt een resultaat weer gegeven dat vergelijkbaar is met de volgende uitvoer:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Model profilerings resultaten worden verzonden als een `Run`-object.

Zie [AZ ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)(Engelstalig) voor meer informatie over het gebruik van profile ring van de cli.

Zie de volgende documenten voor meer informatie:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [Profiel ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Schema voor de configuratie van het Afleidings bestand](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Implementeren naar doel

Implementatie maakt gebruik van de configuratie-implementatie configuratie voor innemen om de modellen te implementeren. Het implementatie proces is vergelijkbaar, ongeacht het berekenings doel. Implementeren naar AKS is iets anders omdat u een verwijzing naar het AKS-cluster moet opgeven.

### <a id="local"></a>Lokale implementatie

Als u een model lokaal wilt implementeren, moet docker op uw lokale computer zijn geïnstalleerd.

#### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de documentatie voor [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)en [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)voor meer informatie.

#### <a name="using-the-cli"></a>De CLI gebruiken

Als u een model wilt implementeren met behulp van de CLI, gebruikt u de volgende opdracht. Vervang `mymodel:1` door de naam en versie van het geregistreerde model:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Zie voor meer informatie de documentatie van [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>VM van notebook-webservice (dev/test)

Zie [een model implementeren op laptop-vm's](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (dev/test)

Zie [implementeren naar Azure container instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes-service (ontwikkelen/testen en productie)

Zie [implementeren naar Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Webservices gebruiken

Elke geïmplementeerde webservice biedt een REST API, zodat u client toepassingen kunt maken in verschillende programmeer talen.
Als u de sleutel verificatie voor uw service hebt ingeschakeld, moet u een service sleutel opgeven als een token in de aanvraag header.
Als u token verificatie voor uw service hebt ingeschakeld, moet u een Azure Machine Learning JWT-token opgeven als Bearer-token in de aanvraag header.

> [!TIP]
> U kunt het schema-JSON-document ophalen nadat u de service hebt geïmplementeerd. Gebruik de [eigenschap swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) van de geïmplementeerde webservice (bijvoorbeeld `service.swagger_uri`) om de URI naar het Swagger-bestand van de lokale webservice op te halen.

### <a name="request-response-consumption"></a>Aanvraag-antwoord verbruik

Hier volgt een voor beeld van het aanroepen van uw service in Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Zie [client toepassingen maken om webservices te gebruiken](how-to-consume-web-service.md)voor meer informatie.

### <a name="web-service-schema-openapi-specification"></a>Web Service-schema (OpenAPI-specificatie)

Als u automatische schema generatie hebt gebruikt met uw implementatie, kunt u het adres van de OpenAPI-specificatie voor de service ophalen met behulp van de [eigenschap swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Bijvoorbeeld `print(service.swagger_uri)`.) Gebruik een GET-aanvraag of open de URI in een browser om de specificatie op te halen.

Het volgende JSON-document is een voor beeld van een schema (OpenAPI-specificatie) dat is gegenereerd voor een implementatie:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Zie [OpenAPI Specification](https://swagger.io/specification/)(Engelstalig) voor meer informatie.

Zie [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen)voor een hulp programma waarmee client bibliotheken kunnen worden gemaakt op basis van de specificatie.

### <a id="azuremlcompute"></a>Batch-deinterferentie
Azure Machine Learning Compute-doelen worden gemaakt en beheerd door Azure Machine Learning. Ze kunnen worden gebruikt voor batch voorspelling van Azure Machine Learning pijp lijnen.

Zie [batch voorspellingen uitvoeren](tutorial-pipeline-batch-scoring-classification.md)voor een overzicht van batch deinterferentie met Azure machine learning compute.

### <a id="iotedge"></a>IoT Edge afleiding
Ondersteuning voor het implementeren naar de rand is in preview. Zie [Deploy Azure machine learning als een IOT Edge-module](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)voor meer informatie.


## <a id="update"></a>Webservices bijwerken

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Continu implementeren van modellen

U kunt modellen voortdurend implementeren met behulp van de Machine Learning-extensie voor [Azure DevOps](https://azure.microsoft.com/services/devops/). U kunt de Machine Learning extensie voor Azure DevOps gebruiken om een implementatie pijplijn te activeren wanneer een nieuw machine learning model wordt geregistreerd in een Azure Machine Learning-werk ruimte.

1. Meld u aan voor [Azure-pijp lijnen](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), wat een continue integratie en levering van uw toepassing naar elk platform of Cloud mogelijk maakt. (Houd er rekening mee dat Azure-pijp lijnen niet hetzelfde zijn als [machine learning pijp lijnen](concept-ml-pipelines.md#compare).)

1. [Een Azure DevOps-project maken.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installeer de [machine learning extensie voor Azure-pijp lijnen](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Gebruik service verbindingen voor het instellen van een Service-Principal-verbinding met uw Azure Machine Learning-werk ruimte, zodat u toegang hebt tot uw artefacten. Ga naar project instellingen, selecteer **service verbindingen**en selecteer vervolgens **Azure Resource Manager**:

    [![Select Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Selecteer **AzureMLWorkspace**in de lijst **bereik niveau** en voer de rest van de waarden in:

    ![AzureMLWorkspace selecteren](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Als u uw machine learning model continu wilt implementeren met behulp van Azure-pijp lijnen, selecteert u onder pijp lijnen de optie **release**. Voeg een nieuw artefact toe en selecteer vervolgens het **model** artefact voor AzureML en de service verbinding die u eerder hebt gemaakt. Selecteer het model en de versie om een implementatie te activeren:

    [AzureML-model ![Select](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Schakel de model trigger in voor uw model artefact. Wanneer u de trigger inschakelt, wordt elke keer dat de opgegeven versie (dat wil zeggen, de nieuwste versie) van het model geregistreerd in uw werk ruimte, een Azure DevOps release-pijp lijn geactiveerd.

    [de model trigger ![Enable](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Voor meer voorbeeld projecten en voor beelden raadpleegt u deze voor beeld-opslag plaatsen in GitHub:

* [Micro soft-MLOps](https://github.com/Microsoft/MLOps)
* [Micro soft-MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Een model downloaden
Als u uw model wilt downloaden om dit in uw eigen uitvoerings omgeving te gebruiken, kunt u dit doen met de volgende SDK/CLI-opdrachten:

SDK
```python
model_path = Model(ws,'mymodel').download()
```

CLI
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="package-models"></a>Pakket modellen

In sommige gevallen wilt u mogelijk een docker-installatie kopie maken zonder het model te implementeren (als u bijvoorbeeld van plan bent [om te implementeren naar Azure app service](how-to-deploy-app-service.md)). Of misschien wilt u de installatie kopie downloaden en uitvoeren op een lokale docker-installatie. Mogelijk wilt u de bestanden die worden gebruikt om de installatie kopie te maken, ook downloaden, bekijken, wijzigen en de installatie kopie hand matig bouwen.

Met model pakketten kunt u deze dingen doen. Alle assets die nodig zijn om een model als een webservice te hosten, worden verpakt en u kunt een volledig gebouwde docker-installatie kopie of de bestanden die nodig zijn om er een te maken. Er zijn twee manieren om model pakketten te gebruiken:

**Een verpakt model downloaden:** Down load een docker-installatie kopie die het model en andere bestanden bevat die nodig zijn om deze als webservice te hosten.

**Een Dockerfile genereren:** Down load het Dockerfile, model, het invoer script en andere assets die nodig zijn om een docker-installatie kopie te bouwen. U kunt de bestanden vervolgens controleren of wijzigingen aanbrengen voordat u de installatie kopie lokaal bouwt.

Beide pakketten kunnen worden gebruikt voor het ophalen van een lokale docker-installatie kopie.

> [!TIP]
> Het maken van een pakket is vergelijkbaar met het implementeren van een model. U gebruikt een geregistreerd model en een interferentie configuratie.

> [!IMPORTANT]
> Als u een volledig gemaakte installatie kopie wilt downloaden of een installatie kopie wilt bouwen, moet u [docker](https://www.docker.com) hebben geïnstalleerd in uw ontwikkel omgeving.

### <a name="download-a-packaged-model"></a>Een verpakt model downloaden

In het volgende voor beeld wordt een installatie kopie gebouwd die is geregistreerd in het Azure container Registry voor uw werk ruimte:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Nadat u een pakket hebt gemaakt, kunt u `package.pull()` gebruiken om de installatie kopie naar uw lokale docker-omgeving te halen. Met de uitvoer van deze opdracht wordt de naam van de afbeelding weer gegeven. Bijvoorbeeld: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Nadat u het model hebt gedownload, gebruikt u de opdracht `docker images` om de lokale installatie kopieën weer te geven:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Als u een lokale container wilt starten op basis van deze installatie kopie, gebruikt u de volgende opdracht om een benoemde container te starten vanuit de shell of de opdracht regel. Vervang de `<imageid>` waarde door de afbeeldings-ID die wordt geretourneerd door de `docker images` opdracht.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Met deze opdracht start u de nieuwste versie van de installatie kopie met de naam `myimage`. De lokale poort 6789 wordt toegewezen aan de poort in de container waarop de webservice luistert (5001). Hiermee wordt ook de naam `mycontainer` toegewezen aan de container, waardoor de container eenvoudiger kan worden gestopt. Nadat de container is gestart, kunt u aanvragen verzenden naar `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Een Dockerfile en afhankelijkheden genereren

In het volgende voor beeld ziet u hoe u het Dockerfile, model en andere assets downloadt die nodig zijn om een installatie kopie lokaal te maken. De para meter `generate_dockerfile=True` geeft aan dat u de bestanden, niet een volledig samengestelde afbeelding, wilt.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Met deze code worden de bestanden gedownload die nodig zijn om de installatie kopie te bouwen in de `imagefiles` Directory. De Dockerfile die in de opgeslagen bestanden zijn opgenomen, verwijst naar een basis installatie kopie die is opgeslagen in een Azure container Registry. Wanneer u de installatie kopie op uw lokale docker-installatie bouwt, moet u het adres, de gebruikers naam en het wacht woord gebruiken om u te verifiëren bij het REGI ster. Gebruik de volgende stappen om de installatie kopie met behulp van een lokale docker-installatie te maken:

1. Gebruik vanuit een shell of opdracht regel sessie de volgende opdracht om docker te verifiëren met het Azure container Registry. Vervang `<address>`, `<username>` en `<password>` door de waarden die zijn opgehaald door `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Gebruik de volgende opdracht om de installatie kopie te maken. Vervang `<imagefiles>` door het pad naar de map waarin `package.save()` de bestanden hebt opgeslagen.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Met deze opdracht wordt de naam van de installatie kopie ingesteld op `myimage`.

Gebruik de opdracht `docker images` om te controleren of de installatie kopie is gemaakt. Als het goed is, ziet u de `myimage` installatie kopie in de lijst:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Als u een nieuwe container wilt starten op basis van deze installatie kopie, gebruikt u de volgende opdracht:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Met deze opdracht start u de nieuwste versie van de installatie kopie met de naam `myimage`. De lokale poort 6789 wordt toegewezen aan de poort in de container waarop de webservice luistert (5001). Hiermee wordt ook de naam `mycontainer` toegewezen aan de container, waardoor de container eenvoudiger kan worden gestopt. Nadat de container is gestart, kunt u aanvragen verzenden naar `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Voor beeld-client om de lokale container te testen

De volgende code is een voor beeld van een python-client die met de container kan worden gebruikt:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Zie voor beelden van clients in andere programmeer talen [modellen gebruiken die zijn geïmplementeerd als webservices](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>De docker-container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht uit een andere shell of opdracht regel:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `service.delete()`.
Als u een geregistreerd model wilt verwijderen, gebruikt u `model.delete()`.

Zie de documentatie voor [webservice. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) en [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning webservices beveiligen met SSL](how-to-secure-web-service.md)
* [Een Azure Machine Learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)

