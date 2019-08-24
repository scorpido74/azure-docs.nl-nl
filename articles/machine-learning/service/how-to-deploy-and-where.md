---
title: Hoe en waar modellen moeten worden geïmplementeerd
titleSuffix: Azure Machine Learning service
description: 'Meer informatie over hoe en waar u uw Azure Machine Learning service modellen kunt implementeren, met inbegrip van: Azure Container Instances, Azure Kubernetes-service, Azure IoT Edge en veld-Programmeer bare poort matrices.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: a4146e20efae87287b77687e4a1d3b0196cb1c95
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997942"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implementeer modellen met de Azure Machine Learning-service

Meer informatie over het implementeren van uw machine learning model als een webservice in de Azure-Cloud of voor het IoT Edge van apparaten.

De werk stroom is vergelijkbaar, ongeacht [waar u](#target) uw model implementeert:

1. Registreer het model.
1. Voorbereiden op implementeren (assets opgeven, gebruik, reken doel).
1. Implementeer het model op het Compute-doel.
1. Test het geïmplementeerde model, ook wel webservice genoemd.

Zie [modellen beheren, implementeren en bewaken met Azure machine learning-service](concept-model-management-and-deployment.md)voor meer informatie over de concepten die bij de implementatie werk stroom zijn betrokken.

## <a name="prerequisites"></a>Vereisten

- Een werkruimte van Azure Machine Learning-service. Zie [een Azure machine learning service-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een model. Als u geen getraind model hebt, kunt u gebruikmaken van het model & afhankelijkheids bestanden in [deze zelf studie](https://aka.ms/azml-deploy-cloud).

- De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](https://aka.ms/aml-sdk)of de [Azure machine learning Visual Studio code extension](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werk ruimte

De volgende code laat zien hoe u verbinding maakt met een Azure Machine Learning service-werk ruimte met behulp van informatie in cache naar de lokale ontwikkel omgeving:

**De SDK gebruiken**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Zie de [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)voor meer informatie over het gebruik van de SDK om verbinding te maken met een werk ruimte.

**De CLI gebruiken**

Gebruik `-w` de para meter of `--workspace-name` voor het opgeven van de werk ruimte voor de opdracht wanneer u de CLI gebruikt.

**VS code gebruiken**

Wanneer u VS code gebruikt, wordt de werk ruimte geselecteerd met een grafische interface. Zie [modellen implementeren en beheren](how-to-vscode-tools.md#deploy-and-manage-models) in de documentatie over de VS code-extensie voor meer informatie.

## <a id="registermodel"></a>Uw model registreren

Een geregistreerd model is een logische container voor een of meer bestanden die het model vormen. Als u bijvoorbeeld een model hebt dat is opgeslagen in meerdere bestanden, kunt u ze registreren als één model in de werk ruimte. Na de registratie kunt u het geregistreerde model downloaden of implementeren en alle geregistreerde bestanden ontvangen.

> [!TIP]
> Bij het registreren van een model geeft u een pad op naar een Cloud locatie (vanuit een training-uitvoering) of een lokale map. Dit pad is alleen bedoeld voor het vinden van de bestanden die worden geüpload als onderdeel van het registratie proces. het hoeft niet overeen te komen met het pad dat wordt gebruikt in het vermeldings script. Zie [Wat is get_model_path](#what-is-get_model_path)voor meer informatie.

Machine learning-modellen worden geregistreerd in uw Azure Machine Learning-werk ruimte. Het model kan afkomstig zijn van Azure Machine Learning of van ergens anders afkomstig zijn. De volgende voor beelden laten zien hoe u een model kunt registreren:

### <a name="register-a-model-from-an-experiment-run"></a>Een model registreren vanuit een experiment

De code fragmenten in deze sectie demonstreren het registreren van een model van een trainings uitvoering:

> [!IMPORTANT]
> Deze fragmenten nemen aan dat u eerder een trainings uitvoering hebt uitgevoerd en toegang hebt tot het `run` object (SDK-voor beeld) of een run-id-waarde (CLI-voor beeld). Zie voor meer informatie over trainings modellen COMPUTE- [doelen maken en gebruiken voor model training](how-to-set-up-training-targets.md).

+ **De SDK gebruiken**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  De `model_path` verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad naar één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie `model_path` , stelt u deze in op de map die de bestanden bevat.

+ **De CLI gebruiken**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  De `--asset-path` verwijst naar de locatie van de cloud van het model. In dit voor beeld wordt het pad naar één bestand gebruikt. Als u meerdere bestanden wilt toevoegen aan de model registratie `--asset-path` , stelt u deze in op de map die de bestanden bevat.

+ **VS code gebruiken**

  Registreer modellen met behulp van model bestanden of-mappen met de [VS code](how-to-vscode-tools.md#deploy-and-manage-models) -extensie.

### <a name="register-a-model-from-a-local-file"></a>Een model registreren vanuit een lokaal bestand

U kunt een model registreren door een **lokaal pad** naar het model op te geven. U kunt een map of één bestand opgeven. U kunt deze methode gebruiken om beide modellen te registreren die zijn getraind met Azure Machine Learning service en vervolgens te downloaden, of modellen die buiten Azure Machine Learning zijn getraind.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **ONNX-voor beeld met de python-SDK:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
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

  Als u meerdere bestanden wilt toevoegen aan de model registratie `model_path` , stelt u deze in op de map die de bestanden bevat.

+ **De CLI gebruiken**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Als u meerdere bestanden wilt toevoegen aan de model registratie `-p` , stelt u deze in op de map die de bestanden bevat.

**Geschatte tijd**: Ongeveer 10 seconden.

Zie voor meer informatie de documentatie bij de [Modelklasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Zie [een bestaand model implementeren](how-to-deploy-existing-model.md)voor meer informatie over het werken met modellen die zijn getraind buiten Azure machine learning service.

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Een reken doel kiezen

De volgende Compute-doelen of reken bronnen kunnen worden gebruikt voor het hosten van de implementatie van de webservice. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Implementatie voorbereiden

Het implementeren van het model vereist verschillende dingen:

* Een __invoer script__. Met dit script worden aanvragen geaccepteerd, wordt de aanvraag met het model gescoord en worden de resultaten geretourneerd.

    > [!IMPORTANT]
    > Het invoer script is specifiek voor uw model. het moet inzicht krijgen in de indeling van de gegevens van de inkomende aanvraag, de indeling van de gegevens die worden verwacht door uw model en de indeling van de gegevens die aan clients worden geretourneerd.
    >
    > Als de gegevens van de aanvraag een indeling hebben die niet kan worden gebruikt door uw model, kan het script deze omzetten in een acceptabele indeling. Het kan ook de reactie transformeren voordat deze naar de client wordt teruggekeerd.

    > [!IMPORTANT]
    > De Azure Machine Learning SDK biedt geen manier voor het uitvoeren van de webservice of IoT Edge implementaties om toegang te krijgen tot uw Data Store-of gegevens sets. Als u het geïmplementeerde model nodig hebt om toegang te krijgen tot gegevens die buiten de implementatie zijn opgeslagen, zoals in een Azure Storage-account, moet u een oplossing voor aangepaste code ontwikkelen met behulp van de relevante SDK. Bijvoorbeeld de [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python).
    >
    > Een ander alternatief dat kan worden gebruikt voor uw scenario is [batch](how-to-run-batch-predictions.md)voorspellingen. Dit biedt ook toegang tot gegevens opslag in de score.

* **Afhankelijkheden**, zoals hulp scripts of python/Conda-pakketten die zijn vereist voor het uitvoeren van het script of model van de vermelding

* De __implementatie configuratie__ voor het reken doel dat als host fungeert voor het geïmplementeerde model. In deze configuratie worden de vereisten voor geheugen en CPU beschreven die nodig zijn om het model uit te voeren.

Deze entiteiten worden ingekapseld in een Afleidings __configuratie__en een __implementatie configuratie__. De configuratie voor afwijzen verwijst naar het script voor de vermelding en andere afhankelijkheden. Deze configuraties worden programmatisch gedefinieerd wanneer u de SDK gebruikt en als JSON-bestanden wanneer u de CLI gebruikt om de implementatie uit te voeren.

### <a id="script"></a> 1. & Afhankelijkheden van uw invoer script definiëren

Het item script ontvangt gegevens die zijn verzonden naar een geïmplementeerde webservice en geeft deze door aan het model. Vervolgens wordt het antwoord opgehaald dat door het model is geretourneerd en wordt dat naar de client geretourneerd. **Het script is specifiek voor uw model**. het moet inzicht hebben in de gegevens die het model verwacht en retourneert.

Het script bevat twee functies die het model laden en uitvoeren:

* `init()`: Deze functie laadt meestal het model in een globaal object. Deze functie wordt slechts één keer uitgevoerd wanneer de docker-container voor uw web service wordt gestart.

* `run(input_data)`: Deze functie maakt gebruik van het model voor het voors pellen van een waarde op basis van de invoer gegevens. Invoer en uitvoer van de run worden meestal JSON gebruikt voor serialisatie en deserialisatie. U kunt ook werken met onbewerkte binaire gegevens. U kunt de gegevens transformeren voordat u deze naar het model verzendt, of voordat u terugkeert naar de client.

#### <a name="what-is-get_model_path"></a>Wat is get_model_path?

Wanneer u een model registreert, geeft u een model naam op die wordt gebruikt voor het beheren van het model in het REGI ster. U gebruikt deze naam voor het [model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) om het pad van het model bestand (en) op het lokale bestands systeem op te halen. Als u een map of een verzameling bestanden registreert, retourneert deze API het pad naar de map waarin zich de bestanden bevinden.

Wanneer u een model registreert, geeft u het een naam die overeenkomt met waar het model lokaal of tijdens de service-implementatie wordt geplaatst.

In het onderstaande voor beeld wordt een pad geretourneerd naar één bestand `sklearn_mnist_model.pkl` (dat is geregistreerd met de naam `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Beschrijving Automatisch schema genereren

Als u automatisch een schema voor uw webservice wilt genereren, geeft u een voor beeld van de invoer en/of uitvoer in de constructor voor een van de gedefinieerde type-objecten. het type en het voor beeld wordt gebruikt om automatisch het schema te maken. Azure Machine Learning-service maakt vervolgens een [OpenAPI](https://swagger.io/docs/specification/about/) -specificatie (Swagger) voor de webservice tijdens de implementatie.

De volgende typen worden momenteel ondersteund:

* `pandas`
* `numpy`
* `pyspark`
* Standard python-object

Als u schema generatie wilt gebruiken, `inference-schema` neemt u het pakket op in uw Conda-omgevings bestand.

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

Als u automatische schema generatie wilt gebruiken, **moet** uw invoer script de `inference-schema` pakketten importeren.

Definieer de voorbeeld indelingen voor invoer en uitvoer in `input_sample` de `output_sample` variabelen en, die de aanvraag-en antwoord indelingen voor de webservice vertegenwoordigen. Gebruik deze voor beelden in de functie voor invoer en uitvoer op de `run()` functie. Het scikit-voor beeld hieronder maakt gebruik van schema generatie.

##### <a name="example-entry-script"></a>Voorbeeld script

In het volgende voor beeld ziet u hoe u JSON-gegevens kunt accepteren en retour neren:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

In het volgende voor beeld ziet u hoe u de invoer gegevens definieert `<key: value>` als een woorden lijst, met behulp van een data frame. Deze methode wordt ondersteund voor het gebruiken van de geïmplementeerde webservice van Power BI (meer[informatie over het gebruik van de webservice van Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Zie de volgende voor beelden voor meer voorbeeld scripts:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* Tensor flow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Binaire gegevens

Als uw model binaire gegevens, zoals een afbeelding accepteert, moet u wijzigen de `score.py` bestand dat wordt gebruikt voor uw implementatie om onbewerkte HTTP-aanvragen te accepteren. Als u onbewerkte gegevens wilt `AMLRequest` accepteren, gebruikt u de klasse in uw `@rawhttp` invoer script en `run()` voegt u de decorator toe aan de functie.

Hier volgt een voor beeld van `score.py` een waarmee binaire gegevens worden geaccepteerd:

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
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLRequest` klasse bevindt `azureml.contrib` zich in de naam ruimte. Items in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Als zodanig moeten alle in deze naam ruimte worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Cross-Origin-resource delen (CORS)

Cross-Origin-resource delen is een manier om te voor komen dat resources op een webpagina worden aangevraagd vanuit een ander domein. CORS werkt op basis van HTTP-headers die worden verzonden met de client aanvraag en geretourneerd met de service reactie. Zie [Cross-Origin-resource delen](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) op Wikipedia voor meer informatie over CORS en geldige headers.

Als u uw model implementatie wilt configureren voor de ondersteuning van `AMLResponse` CORS, gebruikt u de-klasse in uw invoer script. Met deze klasse kunt u de headers op het antwoord object instellen.

In het volgende voor beeld `Access-Control-Allow-Origin` wordt de koptekst ingesteld voor het antwoord van het entry-script:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> De `AMLResponse` klasse bevindt `azureml.contrib` zich in de naam ruimte. Items in deze naam ruimte worden regel matig gewijzigd, terwijl we werken om de service te verbeteren. Als zodanig moeten alle in deze naam ruimte worden beschouwd als een preview en niet volledig worden ondersteund door micro soft.
>
> Als u dit in uw lokale ontwikkel omgeving moet testen, kunt u de onderdelen installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Uw InferenceConfig definiëren

De configuratie voor afnemen beschrijft hoe u het model configureert om voor spellingen te maken. Deze configuratie maakt geen deel uit van het script voor de invoer. het verwijst naar uw invoer script en wordt gebruikt voor het zoeken van alle resources die vereist zijn voor de implementatie. Dit wordt later gebruikt bij het implementeren van het model.

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

Zie voor meer informatie de [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -klasse verwijzing.

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met behulp van de configuratie voor inhalen.

### <a name="cli-example-of-inferenceconfig"></a>CLI-voor beeld van InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

De volgende opdracht laat zien hoe u een model implementeert met behulp van de CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In dit voor beeld bevat de configuratie de volgende items:

* Dat dit model python vereist
* Het [invoer script](#script), dat wordt gebruikt voor het afhandelen van webaanvragen die worden verzonden naar de geïmplementeerde service
* Het Conda-bestand met een beschrijving van de Python-pakketten die nodig zijn om aan te doen

Zie [een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste docker-installatie kopie met behulp van de configuratie voor inhalen.

### <a name="3-define-your-deployment-configuration"></a>3. Uw implementatie configuratie definiëren

Voordat u implementeert, moet u de implementatie configuratie definiëren. __De implementatie configuratie is specifiek voor het reken doel dat als host fungeert voor de webservice__. Wanneer u bijvoorbeeld lokaal implementeert, moet u de poort opgeven waar de service aanvragen accepteert. De implementatie configuratie maakt geen deel uit van het invoer script. Het wordt gebruikt voor het definiëren van de kenmerken van het reken doel dat als host fungeert voor het model en het invoer script.

Mogelijk moet u ook de reken resource maken. Bijvoorbeeld, als u nog geen Azure Kubernetes-service hebt die aan uw werk ruimte is gekoppeld.

De volgende tabel bevat een voor beeld van het maken van een implementatie configuratie voor elk reken doel:

| COMPUTE-doel | Implementatie configuratie-voor beeld |
| ----- | ----- |
| Lokaal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Elk van deze klassen voor lokale, ACI-en AKS-webservices kan worden `azureml.core.webservice`geïmporteerd vanuit:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profileren

Voordat u uw model als een service implementeert, kunt u het beste een profiel gebruiken om de optimale CPU-en geheugen vereisten te bepalen. U kunt uw model profileren met behulp van de SDK of CLI. In de volgende voor beelden ziet u hoe u profileren kunt gebruiken vanuit de SDK:

> [!IMPORTANT]
> Bij het gebruik van profileren, kan de configuratie van de deinterferentie die u opgeeft niet verwijzen naar een Azure Machine Learning omgeving. Definieer in plaats daarvan de software afhankelijkheden `conda_file` met behulp `InferenceConfig` van de para meter van het object.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(true)
profiling_results = profile.get_results()
print(profiling_results)
```

Met deze code wordt een resultaat weer gegeven dat vergelijkbaar is met de volgende tekst:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Model profilerings resultaten worden verzonden als een `Run` object.

Zie [AZ ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)(Engelstalig) voor meer informatie over het gebruik van profile ring van de cli.

Zie de volgende naslag documenten voor meer informatie:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [Profiel ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--model~s--inference-config--input-data-)
* [Schema voor de configuratie van het Afleidings bestand](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Implementeren naar doel

Implementatie maakt gebruik van de configuratie-implementatie configuratie voor innemen om de modellen te implementeren. Het implementatie proces is vergelijkbaar, ongeacht het berekenings doel. Implementeren naar AKS is iets anders, omdat u een verwijzing naar het AKS-cluster moet opgeven.

### <a id="local"></a>Lokale implementatie

Als u lokaal wilt implementeren, moet docker op uw lokale computer zijn geïnstalleerd.

#### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie de referentie documentatie voor [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)en [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)voor meer informatie.

#### <a name="using-the-cli"></a>De CLI gebruiken

Als u wilt implementeren met behulp van de CLI, gebruikt u de volgende opdracht. Vervang `mymodel:1` door de naam en versie van het geregistreerde model:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Zie voor meer informatie de referentie [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>NotebookVM-webservice (DEVTEST)

Zie [een model implementeren op laptop-vm's](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Azure Container Instances (DEVTEST)

Zie [implementeren naar Azure container instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes-service (DEVTEST & productie)

Zie [implementeren naar Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Webservices gebruiken

Elke geïmplementeerde webservice biedt een REST API, zodat u client toepassingen kunt maken in verschillende programmeer talen. Als u sleutel verificatie voor uw service hebt ingeschakeld, moet u een service sleutel opgeven als een token in de aanvraag header.
Als u token verificatie voor uw service hebt ingeschakeld, moet u een Azure Machine Learning JWT-token opgeven als Bearer-token in de aanvraag header.

> [!TIP]
> Nadat de service is geïmplementeerd, kunt u het JSON-document van het schema ophalen. Gebruik de [eigenschap swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) van de geïmplementeerde webservice, zoals `service.swagger_uri`, om de URI naar het Swagger-bestand van de lokale webservice op te halen.

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

Als u het automatisch genereren van schema's met de implementatie hebt gebruikt, kunt u het adres van de OpenAPI-specificatie voor de service ophalen met behulp van de [eigenschap swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Bijvoorbeeld `print(service.swagger_uri)`. Gebruik een GET-aanvraag (of open de URI in een browser) om de specificatie op te halen.

Het volgende JSON-document is een voor beeld van een schema (OpenAPI-specificatie) dat is gegenereerd voor een implementatie:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
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

Zie voor meer informatie over de specificatie de [open API-specificatie](https://swagger.io/specification/).

Zie [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen)voor een hulp programma waarmee client bibliotheken kunnen worden gemaakt op basis van de specificatie.

### <a id="azuremlcompute"></a>Batch-deinterferentie
Azure Machine Learning Compute-doelen worden gemaakt en beheerd door de Azure Machine Learning-service. Ze kunnen worden gebruikt voor batch voorspelling van Azure Machine Learning pijp lijnen.

Lees voor een overzicht van batch deinterferentie met Azure Machine Learning Compute het artikel [batch voorspellingen uitvoeren](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>IoT Edge afleiding
Ondersteuning voor het implementeren naar de rand is in preview. Zie voor meer informatie het artikel [Deploy Azure machine learning als IOT Edge module](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Webservices bijwerken

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Continue model implementatie 

U kunt met behulp van de Machine Learning-extensie voor [Azure DevOps](https://azure.microsoft.com/services/devops/)continu modellen implementeren. Door de Machine Learning-extensie voor Azure DevOps te gebruiken, kunt u een implementatie pijplijn activeren wanneer een nieuw machine learning model wordt geregistreerd in Azure Machine Learning service-werk ruimte. 

1. Meld u aan voor [Azure-pijp lijnen](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), wat een continue integratie en levering van uw toepassing naar elk platform/elke Cloud mogelijk maakt. Azure-pijp lijnen [wijken af van ml-pijp lijnen](concept-ml-pipelines.md#compare). 

1. [Een Azure DevOps-project maken.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. De [uitbrei ding voor de machine learning voor Azure-pijp lijnen](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) installeren 

1. Gebruik __service verbindingen__ om een Service-Principal-verbinding met uw Azure machine learning service-werk ruimte in te stellen voor toegang tot al uw artefacten. Ga naar project instellingen, klik op service verbindingen en selecteer Azure Resource Manager.

    [![weer gave-service-verbinding](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png) 

1. Definieer AzureMLWorkspace als __Scope niveau__ en vul de volgende para meters in.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Ga vervolgens als u uw machine learning model continu wilt implementeren met behulp van de Azure-pijp lijnen, onder pijp lijnen de optie __release__. Voeg een nieuw artefact toe, selecteer een model artefact voor AzureML en de service verbinding die in de vorige stap is gemaakt. Selecteer het model en de versie om een implementatie te activeren. 

    [![Select-AzureMLmodel-artefact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Schakel de model trigger in voor uw model artefact. Door de trigger in te scha kelen, elke keer dat de opgegeven versie (bijvoorbeeld de nieuwste versie) van het model is geregistreerd in uw werk ruimte, een Azure DevOps release-pijp lijn wordt geactiveerd. 

    [![Enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Voor meer voorbeeld projecten en voor beelden raadpleegt u de volgende voorbeeld opslag plaatsen:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="package-models"></a>Pakket modellen

In sommige gevallen wilt u mogelijk een docker-installatie kopie maken zonder het model te implementeren. Bijvoorbeeld wanneer u van plan bent [om te implementeren in azure app service](how-to-deploy-app-service.md). Of u wilt de installatie kopie downloaden en uitvoeren op een lokale docker-installatie. Mogelijk wilt u de bestanden die worden gebruikt om de installatie kopie te maken, ook downloaden, bekijken, wijzigen en hand matig bouwen.

Met model pakketten kunt u beide doen. Alle assets die nodig zijn om een model als een webservice te hosten, worden verpakt en u kunt een volledig gebouwde docker-installatie kopie of de bestanden die nodig zijn om er een te maken. Er zijn twee manieren om model pakketten te gebruiken:

* __Pakket model downloaden__: U downloadt een docker-installatie kopie die het model en andere bestanden bevat die nodig zijn om het als een webservice te hosten.
* __Dockerfile genereren__: U downloadt het dockerfile, model, het invoer script en andere assets die nodig zijn om een docker-installatie kopie te bouwen. U kunt de bestanden vervolgens controleren of wijzigingen aanbrengen voordat u de installatie kopie lokaal bouwt.

Beide pakketten kunnen worden gebruikt voor het ophalen van een lokale docker-installatie kopie. 

> [!TIP]
> Het maken van een pakket is vergelijkbaar met het implementeren van een model, omdat het een geregistreerd model en een Afleidings configuratie gebruikt.

> [!IMPORTANT]
> De functionaliteit, zoals het downloaden van een volledig samengestelde installatie kopie of het maken van [](https://www.docker.com) een installatie kopie, vereist een werkende docker-installatie in uw ontwikkel omgeving.

### <a name="download-a-packaged-model"></a>Een verpakt model downloaden

In het volgende voor beeld ziet u hoe u een installatie kopie bouwt die is geregistreerd in de Azure Container Registry voor uw werk ruimte:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Nadat u een pakket hebt gemaakt, kunt `package.pull()` u gebruiken om de installatie kopie naar uw lokale docker-omgeving te halen. Met de uitvoer van deze opdracht wordt de naam van de afbeelding weer gegeven. Bijvoorbeeld `Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. Na het downloaden gebruikt u `docker images` de opdracht om de lokale installatie kopieën weer te geven:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Als u een lokale container wilt starten met behulp van deze installatie kopie, gebruikt u de volgende opdracht om een benoemde container te starten vanuit de shell of de opdracht regel. Vervang de `<imageid>` waarde door de afbeeldings-id die `docker images` wordt geretourneerd door de opdracht:

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Met deze opdracht start u de nieuwste versie van de `myimage`installatie kopie met de naam. De lokale poort 6789 wordt toegewezen aan de poort in de container waarnaar de webservice luistert (5001). De naam `mycontainer` wordt ook toegewezen aan de container, waardoor het eenvoudiger wordt om te stoppen. Zodra het is gestart, kunt u aanvragen `http://localhost:6789/score`verzenden naar.

### <a name="generate-dockerfile-and-dependencies"></a>Dockerfile en afhankelijkheden genereren

In het volgende voor beeld ziet u hoe u het dockerfile, model en andere assets downloadt die nodig zijn om de installatie kopie lokaal te bouwen. De `generate_dockerfile=True` para meter geeft aan dat de bestanden, niet een volledig gebouwde installatie kopie, moeten worden gemaakt:

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package
package.save("./imagefiles")
# Get the Azure Container Registry that the model/dockerfile uses
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Met deze code worden de bestanden gedownload die nodig zijn om de `imagefiles` installatie kopie te bouwen in de Directory. De dockerfile die zijn opgenomen in de opslag bestanden verwijzen naar een basis installatie kopie die is opgeslagen in een Azure Container Registry. Wanneer u de installatie kopie op uw lokale docker-installatie bouwt, moet u het adres, de gebruikers naam en het wacht woord voor verificatie bij dit REGI ster gebruiken. Gebruik de volgende stappen om de installatie kopie te bouwen met een lokale docker-installatie:

1. Gebruik vanuit een shell of opdracht regel sessie de volgende opdracht om docker te verifiëren met de Azure Container Registry. Vervang `<address>`, `<username>`, `package.get_container_registry()`en `<password>` door de waarden die zijn opgehaald met:

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Gebruik de volgende opdracht om de installatie kopie te maken. Vervang `<imagefiles>` door het pad naar de map waarin `package.save()` de bestanden zijn opgeslagen:

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Met deze opdracht wordt de naam van `myimage`de installatie kopie ingesteld op.

Gebruik de `docker images` opdracht om te controleren of de installatie kopie is gemaakt. U ziet de `myimage` afbeelding in de lijst:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Als u een nieuwe container wilt starten op basis van deze installatie kopie, gebruikt u de volgende opdracht:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Met deze opdracht start u de nieuwste versie van de `myimage`installatie kopie met de naam. De lokale poort 6789 wordt toegewezen aan de poort in de container waarnaar de webservice luistert (5001). De naam `mycontainer` wordt ook toegewezen aan de container, waardoor het eenvoudiger wordt om te stoppen. Zodra het is gestart, kunt u aanvragen `http://localhost:6789/score`verzenden naar.

### <a name="example-client-to-test-the-local-container"></a>Voor beeld-client om de lokale container te testen

De volgende code is een voor beeld van een python-client die met de container kan worden gebruikt:

```python
import requests
import json

# URL for the web service
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Zie [modellen gebruiken die zijn geïmplementeerd als](how-to-consume-web-service.md)webservices voor meer voor beelden van clients in andere programmeer talen.

### <a name="stop-the-docker-container"></a>De docker-container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht uit een andere shell of opdracht regel:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt verwijderen van een geïmplementeerde webservice, gebruikt u `service.delete()`.
Als u wilt een geregistreerde model verwijderen, gebruikt u `model.delete()`.

Zie de referentie documentatie voor [webservice. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)en [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning-webservices met SSL beveiligde](how-to-secure-web-service.md)
* [Een ML-Model dat is geïmplementeerd als een webservice gebruiken](how-to-consume-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)

