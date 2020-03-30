---
title: Hoe en waar modellen kunnen worden geïmplementeerd
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen, waaronder Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge en veldprogrammeerbare gatearrays.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 96d9a0722ae04dc150b639dced34fa290da93630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159406"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Modellen implementeren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het implementeren van uw machine learning-model als webservice in de Azure-cloud of op Azure IoT Edge-apparaten.

De workflow is [vergelijkbaar, ongeacht waar u](#target) uw model implementeert:

1. Registreer het model.
1. Bereid de implementatie voor. (Geef de assets, het gebruik en het rekendoel op.)
1. Implementeer het model op het rekendoel.
1. Test het geïmplementeerde model, ook wel een webservice genoemd.

Zie [Modellen beheren, implementeren en bewaken met Azure Machine Learning](concept-model-management-and-deployment.md)voor meer informatie over de concepten die betrokken zijn bij de implementatieworkflow.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een model. Als u geen getraind model hebt, u het model en de afhankelijkheidsbestanden gebruiken die in [deze zelfstudie zijn](https://aka.ms/azml-deploy-cloud)opgenomen.

- De [Azure CLI-extensie voor de Machine Learning-service](reference-azure-machine-learning-cli.md), de [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of de Azure Machine Learning Visual Studio [Code-extensie](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

In de volgende code ziet u hoe u verbinding maakt met een Azure Machine Learning-werkruimte met behulp van informatie die is opgeslagen in de lokale ontwikkelomgeving:

+ **De SDK gebruiken**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Zie de [Azure Machine Learning SDK voor Python-documentatie voor](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) meer informatie over het gebruik van de SDK om verbinding te maken met een werkruimte.

+ **Met behulp van de CLI**

   Gebruik bij het gebruik `-w` `--workspace-name` van de CLI de parameter of om de werkruimte voor de opdracht op te geven.

+ **Visual Studio Code gebruiken**

   Wanneer u Visual Studio Code gebruikt, selecteert u de werkruimte met behulp van een grafische interface. Zie [Modellen implementeren en beheren](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) in de documentatie van de Visual Studio Code-extensie voor meer informatie.

## <a name="register-your-model"></a><a id="registermodel"></a>Uw model registreren

Een geregistreerd model is een logische container voor een of meer bestanden die deel uitmaken van uw model. Als u bijvoorbeeld een model hebt dat in meerdere bestanden is opgeslagen, u deze als één model in de werkruimte registreren. Nadat u de bestanden hebt geregistreerd, u het geregistreerde model downloaden of implementeren en alle bestanden ontvangen die u hebt geregistreerd.

> [!TIP]
> Wanneer u een model registreert, geeft u het pad op van een cloudlocatie (vanaf een trainingsrun) of een lokale map. Dit pad is alleen maar om de bestanden te vinden voor het uploaden als onderdeel van het registratieproces. Het hoeft niet overeen te komen met het pad dat in het invoerscript wordt gebruikt. Zie [Modelbestanden zoeken in uw invoerscript](#load-model-files-in-your-entry-script)voor meer informatie.

Machine learning-modellen worden geregistreerd in uw Azure Machine Learning-werkruimte. Het model kan afkomstig zijn van Azure Machine Learning of ergens anders. Bij het registreren van een model u optioneel metagegevens over het model verstrekken. De `tags` `properties` woordenboeken die u toepast op een modelregistratie, kunnen vervolgens worden gebruikt om modellen te filteren.

De volgende voorbeelden laten zien hoe je een model registreert.

### <a name="register-a-model-from-an-experiment-run"></a>Een model registreren vanuit een experimentrun

De codefragmenten in deze sectie laten zien hoe u een model registreert vanaf een trainingsrun:

> [!IMPORTANT]
> Als u deze fragmenten wilt gebruiken, moet u eerder een trainingsrun `Run` hebben uitgevoerd en moet u toegang hebben tot het object (SDK-voorbeeld) of de run ID-waarde (bijvoorbeeld CLI). Zie [Rekendoelen instellen voor modeltraining voor](how-to-set-up-training-targets.md)meer informatie over trainingsmodellen.

+ **De SDK gebruiken**

  Wanneer u de SDK gebruikt om een model te trainen, u een [Run-object](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) of een [AutoMLRun-object](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) ontvangen, afhankelijk van hoe u het model hebt getraind. Elk object kan worden gebruikt om een model te registreren dat is gemaakt door een experimentrun.

  + Een model van `azureml.core.Run` een object registreren:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    De `model_path` parameter verwijst naar de cloudlocatie van het model. In dit voorbeeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt `model_path` opnemen in de modelregistratie, stelt u het pad in van een map die de bestanden bevat. Zie de documentatie [Run.register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) voor meer informatie.

  + Een model van `azureml.train.automl.run.AutoMLRun` een object registreren:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In dit voorbeeld `metric` `iteration` worden de parameters en parameters niet opgegeven, zodat de iteratie met de beste primaire statistiek wordt geregistreerd. De `model_id` waarde die van de run wordt geretourneerd, wordt gebruikt in plaats van een modelnaam.

    Zie voor meer informatie de documentatie [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Met behulp van de CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  De `--asset-path` parameter verwijst naar de cloudlocatie van het model. In dit voorbeeld wordt het pad van één bestand gebruikt. Als u meerdere bestanden wilt `--asset-path` opnemen in de modelregistratie, stelt u het pad in van een map die de bestanden bevat.

+ **Visual Studio Code gebruiken**

  Registreer modellen met behulp van modelbestanden of mappen met behulp van de visual [studiocode-extensie.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Een model registreren uit een lokaal bestand

U een model registreren door het lokale pad van het model op te geven. U het pad van een map of één bestand opgeven. U deze methode gebruiken om modellen te registreren die zijn getraind met Azure Machine Learning en vervolgens kunnen worden gedownload. U deze methode ook gebruiken om modellen te registreren die buiten Azure Machine Learning zijn getraind.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

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

  Als u meerdere bestanden wilt `model_path` opnemen in de modelregistratie, stelt u het pad in van een map die de bestanden bevat.

+ **Met behulp van de CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Als u meerdere bestanden wilt `-p` opnemen in de modelregistratie, stelt u het pad in van een map die de bestanden bevat.

**Tijdsschatting**: Ongeveer 10 seconden.

Zie voor meer informatie de documentatie voor de [klasse Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Zie [Een bestaand model implementeren](how-to-deploy-existing-model.md)voor meer informatie over het werken met modellen die buiten Azure Machine Learning zijn opgeleid.

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Eindpunten met één versus meerdere modellen
Azure ML ondersteunt het implementeren van enkele of meerdere modellen achter één eindpunt.

Multi-model eindpunten gebruiken een gedeelde container om meerdere modellen te hosten. Dit helpt om overheadkosten te verlagen, verbetert het gebruik en stelt u in staat om modules aan elkaar te ketenen in ensembles. Modellen die u opgeeft in uw implementatiescript worden gemonteerd en beschikbaar gesteld op de schijf van de serveercontainer - u ze laden in het geheugen op aanvraag en scoren op basis van het specifieke model dat wordt aangevraagd bij het scoren.

Zie [dit voorbeeld](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model) voor een E2E-voorbeeld waarin wordt weergegeven hoe u meerdere modellen achter één containerpunt gebruiken

## <a name="prepare-to-deploy"></a>Implementatie voorbereiden

Als u het model als service wilt implementeren, hebt u de volgende onderdelen nodig:

* **Definieer de gevolgtrekkingsomgeving**. Deze omgeving bevat de afhankelijkheden die nodig zijn om uw model uit te voeren voor gevolgtrekking.
* **Scorecode definiëren**. Dit script accepteert aanvragen, scoort de aanvragen met behulp van het model en retourneert de resultaten.
* **Definieer de conclusieconfiguratie**. De gevolgtrekkingsconfiguratie geeft de omgevingsconfiguratie, het invoerscript en andere componenten op die nodig zijn om het model als service uit te voeren.

Zodra u over de benodigde onderdelen beschikt, u de service die wordt gemaakt als gevolg van het implementeren van uw model, profileren om inzicht te krijgen in de CPU- en geheugenvereisten.

### <a name="1-define-inference-environment"></a>1. Definieer de gevolgtrekkingsomgeving

In een gevolgtrekkingsconfiguratie wordt beschreven hoe u de webservice met uw model instelt. Het wordt later gebruikt, wanneer u het model implementeert.

Inference-configuratie maakt gebruik van Azure Machine Learning-omgevingen om de softwareafhankelijkheden te definiëren die nodig zijn voor uw implementatie. Met omgevingen u de softwareafhankelijkheden die nodig zijn voor training en implementatie, maken, beheren en hergebruiken. U een omgeving maken op basis van aangepaste afhankelijkheidsbestanden of een van de samengestelde Azure Machine Learning-omgevingen gebruiken. De volgende YAML is een voorbeeld van een Conda-afhankelijkheidsbestand voor gevolgtrekking. Houd er rekening mee dat u azureml-defaults met verion >= 1,0,45 als een pip-afhankelijkheid moet opgeven, omdat het de functionaliteit bevat die nodig is om het model als webservice te hosten. Als u automatische schemageneratie wilt gebruiken, moet `inference-schema` uw invoerscript ook de pakketten importeren.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Als uw afhankelijkheid beschikbaar is via zowel Conda als pip (van PyPi), raadt Microsoft aan om de Conda-versie te gebruiken, omdat Conda-pakketten meestal worden geleverd met vooraf gebouwde binaire bestanden die de installatie betrouwbaarder maken.
>
> Zie [Conda en Pip begrijpen](https://www.anaconda.com/understanding-conda-and-pip/)voor meer informatie.
>
> Als u wilt controleren of uw afhankelijkheid beschikbaar `conda search <package-name>` is via Conda, [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)gebruikt u de opdracht of gebruikt u de pakketindexen op en .

U het afhankelijkheidsbestand gebruiken om een omgevingsobject te maken en op te slaan in uw werkruimte voor toekomstig gebruik:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Scorecode definiëren

Het invoerscript ontvangt de gegevens die bij een geïmplementeerde webservice zijn ingediend en stuurt ze door naar het model. Vervolgens wordt de reactie die door het model is geretourneerd naar de client geretourneerd. *Het script is specifiek voor uw model.* Het moet de gegevens begrijpen die het model verwacht en retourneert.

Het script bevat twee functies voor het laden en uitvoeren van het model:

* `init()`: Deze functie laadt het model meestal in een globaal object. Deze functie wordt slechts één keer uitgevoerd wanneer de Docker-container voor uw webservice wordt gestart.

* `run(input_data)`: Deze functie gebruikt het model om een waarde te voorspellen op basis van de invoergegevens. De in- en uitvoer van de uitvoerbewerking maken doorgaans gebruik van JSON voor serialisatie en deserialisatie. U kunt ook werken met onbewerkte binaire gegevens. U kunt de gegevens transformeren voordat u deze naar het model verzendt of voordat u deze naar de client retourneert.

#### <a name="load-model-files-in-your-entry-script"></a>Modelbestanden laden in uw invoerscript

Er zijn twee manieren om modellen in uw invoerscript te vinden:
* `AZUREML_MODEL_DIR`: Een omgevingsvariabele die het pad naar de modellocatie bevat.
* `Model.get_model_path`: een API die het pad retourneert naar modelbestand met de geregistreerde modelnaam.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR is een omgevingsvariabele die is gemaakt tijdens de implementatie van de service. U deze omgevingsvariabele gebruiken om de locatie van het geïmplementeerde model(en) te vinden.

In de volgende tabel wordt de waarde van AZUREML_MODEL_DIR beschreven, afhankelijk van het aantal geïmplementeerde modellen:

| Implementatie | Omgevingsvariabele waarde |
| ----- | ----- |
| Eén model | Het pad naar de map met het model. |
| Meerdere modellen | Het pad naar de map met alle modellen. Modellen bevinden zich op naam en`$MODEL_NAME/$VERSION`versie in deze map ( ) |

Tijdens modelregistratie en -implementatie worden modellen in het AZUREML_MODEL_DIR pad geplaatst en blijven de oorspronkelijke bestandsnamen behouden.

Als u het pad naar een modelbestand in uw invoerscript wilt downloaden, combineert u de omgevingsvariabele met het bestandspad dat u zoekt.

**Voorbeeld van één model**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Voorbeeld van meerdere modellen**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Wanneer u een model registreert, geeft u een modelnaam op die wordt gebruikt voor het beheren van het model in het register. U gebruikt deze naam met de methode [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) om het pad van het modelbestand of de bestanden op het lokale bestandssysteem op te halen. Als u een map of een verzameling bestanden registreert, retourneert deze API het pad van de map dat deze bestanden bevat.

Wanneer u een model registreert, geeft u het een naam. De naam komt overeen met waar het model wordt geplaatst, lokaal of tijdens de service-implementatie.

#### <a name="optional-define-model-web-service-schema"></a>(Optioneel) Modelwebserviceschema definiëren

Als u automatisch een schema voor uw webservice wilt genereren, geeft u een voorbeeld van de invoer en/of uitvoer in de constructor voor een van de gedefinieerde tekstobjecten. Het type en het voorbeeld worden gebruikt om automatisch het schema te maken. Azure Machine Learning maakt vervolgens een [OpenAPI-specificatie](https://swagger.io/docs/specification/about/) (Swagger) voor de webservice tijdens de implementatie.

Deze typen worden momenteel ondersteund:

* `pandas`
* `numpy`
* `pyspark`
* Standaard Python-object

Als u het genereren van `inference-schema` schema's wilt gebruiken, neemt u het open-sourcepakket op in uw afhankelijkheidsbestand. Zie voor meer informatie [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)over dit pakket. Definieer de invoer- en `input_sample` uitvoervoorbeeldnotaties in de en `output_sample` variabelen, die de aanvraag- en antwoordnotaties voor de webservice vertegenwoordigen. Gebruik deze voorbeelden in de input en `run()` output functie decorateurs op de functie. In het volgende voorbeeld van scikit-learn wordt gebruik gebruikt voor het genereren van schema's.

##### <a name="example-entry-script"></a>Voorbeelditemscript

In het volgende voorbeeld wordt uitgelegd hoe JSON-gegevens kunnen worden geaccepteerd en retourneren:

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

In het volgende voorbeeld wordt uitgelegd hoe `<key: value>` u de invoergegevens als een woordenboek definieert met behulp van een Gegevensframe. Deze methode wordt ondersteund voor het gebruik van de geïmplementeerde webservice van Power BI. ([Meer informatie over het gebruik van de webservice van Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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

Zie de volgende scripts voor meer voorbeelden:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow (TensorFlow)](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binaire gegevens](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Definitie van inference configuratie
    
In het volgende voorbeeld wordt het laden van een omgeving vanuit uw werkruimte en vervolgens gebruikt met de gevolgtrekkingsconfiguratie:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over omgevingen.

Zie de documentatie van de [klasse InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) voor meer informatie over de configuratie van de gevolgtrekking.

Zie [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste Docker-afbeelding met een gevolgtrekkingsconfiguratie.

#### <a name="cli-example-of-inferenceconfig"></a>CLI-voorbeeld van InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Met de volgende opdracht wordt uitgelegd hoe u een model implementeert met de CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

In dit voorbeeld geeft de configuratie de volgende instellingen op:

* Dat het model Python nodig heeft.
* Het [invoerscript](#script)dat wordt gebruikt om webaanvragen te verwerken die naar de geïmplementeerde service zijn verzonden.
* Het Conda-bestand dat de Python-pakketten beschrijft die nodig zijn voor gevolgtrekking.

Zie [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)voor informatie over het gebruik van een aangepaste Docker-afbeelding met een gevolgtrekkingsconfiguratie.

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Optioneel) Profiel van uw model om het gebruik van resources te bepalen

Zodra u uw model hebt geregistreerd en de andere onderdelen hebt voorbereid die nodig zijn voor de implementatie ervan, u de CPU en het geheugen bepalen die de geïmplementeerde service nodig heeft. Profilering test de service die uw model uitvoert en retourneert informatie zoals het CPU-gebruik, geheugengebruik en reactielatentie. Het biedt ook een aanbeveling voor de CPU en het geheugen op basis van resourcegebruik.

Om uw model te profileren, heeft u het gewenste aantal nodig:
* Een geregistreerd model.
* Een gevolgtrekkingsconfiguratie op basis van uw invoerscript en de definitie van de inferentieomgeving.
* Een tabelgegevensset met één kolomtabel, waarbij elke rij een tekenreeks bevat die voorbeeldaanvraaggegevens vertegenwoordigt.

> [!IMPORTANT]
> Op dit moment ondersteunen we alleen profilering van services die verwachten dat hun aanvraaggegevens een tekenreeks zijn, bijvoorbeeld: tekenreeks geserialiseerde json, tekst, geserialiseerde afbeelding, enz. De inhoud van elke rij van de gegevensset (tekenreeks) wordt in de hoofdtekst van de HTTP-aanvraag geplaatst en naar de service gestuurd waarin het model wordt ingekapseld om te scoren.

Hieronder vindt u een voorbeeld van hoe u een invoergegevensset maken om een service te profileren waarvan wordt verwacht dat de binnenkomende aanvraaggegevens geserialiseerde json bevatten. In dit geval hebben we een gegevensset gemaakt op basis van honderd exemplaren van dezelfde inhoud van aanvraaggegevens. In scenario's in de echte wereld raden we u aan grotere gegevenssets te gebruiken die verschillende ingangen bevatten, vooral als uw modelresourcegebruik/-gedrag invoerafhankelijk is.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Zodra u de gegevensset met voorbeeldaanvraaggegevens gereed hebt, maakt u een conclusieconfiguratie. De conclusieconfiguratie is gebaseerd op de score.py en de omgevingsdefinitie. In het volgende voorbeeld wordt uitgelegd hoe u de conclusieconfiguratie maken en profilering uitvoeren:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Met de volgende opdracht laat u zien hoe u een model profileren met de CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Als u de informatie wilt blijven behouden die door profilering wordt geretourneerd, gebruikt u tags of eigenschappen voor het model. Met behulp van tags of eigenschappen worden de gegevens opgeslagen met het model in het modelregister. In de volgende voorbeelden wordt het `requestedCpu` toevoegen `requestedMemoryInGb` van een nieuwe tag met de informatie en informatie aangetoond:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Implementeren op doel

Implementatie maakt gebruik van de configuratie-implementatie van de inferentenaar om de modellen te implementeren. Het implementatieproces is vergelijkbaar, ongeacht het rekendoel. Implementeren op AKS is iets anders omdat u een verwijzing naar het AKS-cluster moet opgeven.

### <a name="choose-a-compute-target"></a>Een rekendoel kiezen

U de volgende rekendoelen of rekenbronnen gebruiken om uw webservice-implementatie te hosten:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Uw implementatieconfiguratie definiëren

Voordat u uw model implementeert, moet u de implementatieconfiguratie definiëren. *De implementatieconfiguratie is specifiek voor het rekendoel dat de webservice host.* Wanneer u bijvoorbeeld een model lokaal implementeert, moet u de poort opgeven waar de service aanvragen accepteert. De implementatieconfiguratie maakt geen deel uit van uw invoerscript. Het wordt gebruikt om de kenmerken van het rekendoel te definiëren dat het model en het invoerscript host.

Mogelijk moet u ook de compute resource maken, als u bijvoorbeeld nog geen AKS-exemplaar (Azure Kubernetes Service) aan uw werkruimte hebt gekoppeld.

In de volgende tabel vindt u een voorbeeld van het maken van een implementatieconfiguratie voor elk rekendoel:

| Rekendoel | Voorbeeld van implementatieconfiguratie |
| ----- | ----- |
| Lokaal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

De klassen voor lokale, Azure Container Instances en AKS-webservices kunnen worden geïmporteerd uit: `azureml.core.webservice`

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-ssl"></a>Implementaties beveiligen met SSL

Zie [SSL gebruiken om een webservice te beveiligen voor](how-to-secure-web-service.md#enable)meer informatie over het beveiligen van een webservice.

### <a name="local-deployment"></a><a id="local"></a>Lokale implementatie

Als u een model lokaal wilt implementeren, moet Docker op uw lokale machine zijn geïnstalleerd.

#### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Zie voor meer informatie de documentatie voor [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)en [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Met behulp van de CLI

Als u een model wilt implementeren met de CLI, gebruikt u de volgende opdracht. Vervang `mymodel:1` door de naam en versie van het geregistreerde model:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Zie voor meer informatie de implementatiedocumentatie van het [AZ ML-model.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="understanding-service-state"></a>Servicestatus begrijpen

Tijdens de modelimplementatie u de status van de service zien veranderen terwijl deze volledig wordt geïmplementeerd.

In de volgende tabel worden de verschillende servicestatussen beschreven:

| Webservicestatus | Beschrijving | Definitieve staat?
| ----- | ----- | ----- |
| Overgang | De service is in het proces van implementatie. | Nee |
| Slechte status | De service is geïmplementeerd, maar is momenteel onbereikbaar.  | Nee |
| Niet te plannen | De service kan op dit moment niet worden geïmplementeerd vanwege een gebrek aan resources. | Nee |
| Mislukt | De service kan niet worden geïmplementeerd vanwege een fout of crash. | Ja |
| Goed | De service is gezond en het eindpunt is beschikbaar. | Ja |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Compute instance webservice (dev/test)

Zie [Een model implementeren voor de rekeninstantie Azure Machine Learning.](how-to-deploy-local-container-notebook-vm.md)

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure Container Instances (dev/test)

Zie [Implementeren naar Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Azure Kubernetes-service (dev/test en productie)

Zie [Implementeren naar Azure Kubernetes-service](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>A/B-tests (gecontroleerde uitrol)
Zie [Gecontroleerde uitrol van ML-modellen](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) voor meer informatie.

## <a name="consume-web-services"></a>Webservices gebruiken

Elke geïmplementeerde webservice biedt een REST-eindpunt, zodat u clienttoepassingen maken in elke programmeertaal.
Als u verificatie op basis van sleutels voor uw service hebt ingeschakeld, moet u een servicesleutel als token opgeven in de koptekst van uw aanvraag.
Als u tokengebaseerde verificatie voor uw service hebt ingeschakeld, moet u een Azure Machine Learning JSON-webtoken (JWT) opgeven als een token aan toonder in uw aanvraagheader. 

Het belangrijkste verschil is dat **sleutels statisch zijn en handmatig kunnen worden geregenereerd**en tokens moeten worden vernieuwd na **afloop**. Key-based auth wordt ondersteund voor Azure Container Instance en Azure Kubernetes Service geïmplementeerd web-services, en token-based auth is **alleen** beschikbaar voor Azure Kubernetes Service implementaties. Zie de [how-to](how-to-setup-authentication.md#web-service-authentication) on-verificatie voor meer informatie en specifieke codevoorbeelden.

> [!TIP]
> U het JSON-document van het schema ophalen nadat u de service hebt geïmplementeerd. Gebruik de [eigenschap swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) van de geïmplementeerde `service.swagger_uri`webservice (bijvoorbeeld) om de URI naar het Swagger-bestand van de lokale webservice te krijgen.

### <a name="request-response-consumption"></a>Verbruik van antwoord op verzoek

Hier is een voorbeeld van hoe u uw service in Python aanroepen:
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

Zie [Clienttoepassingen maken om webservices te gebruiken voor](how-to-consume-web-service.md)meer informatie.

### <a name="web-service-schema-openapi-specification"></a>Webserviceschema (OpenAPI-specificatie)

Als u automatische schemageneratie hebt gebruikt bij uw implementatie, u het adres van de OpenAPI-specificatie voor de service opvragen met behulp van de [eigenschap swagger_uri.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) (Bijvoorbeeld .) `print(service.swagger_uri)` Gebruik een GET-aanvraag of open de URI in een browser om de specificatie op te halen.

Het volgende JSON-document is een voorbeeld van een schema (OpenAPI-specificatie) dat is gegenereerd voor een implementatie:

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

Zie [OpenAPI-specificatie](https://swagger.io/specification/)voor meer informatie.

Zie [branie-codegen](https://github.com/swagger-api/swagger-codegen)voor een hulpprogramma dat clientbibliotheken kan maken op basis van de specificatie.

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Partijgevolgtrekking
Azure Machine Learning Compute-doelen worden gemaakt en beheerd door Azure Machine Learning. Ze kunnen worden gebruikt voor batchvoorspelling van Azure Machine Learning-pijplijnen.

Zie [Batchvoorspellingen uitvoeren](tutorial-pipeline-batch-scoring-classification.md)voor een walkthrough van batch-inference met Azure Machine Learning Compute.

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge-gevolgtrekking
Ondersteuning voor het implementeren naar de rand is in preview. Zie [Azure Machine Learning implementeren als een IoT Edge-module voor](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)meer informatie.


## <a name="update-web-services"></a><a id="update"></a>Webservices bijwerken

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Modellen continu implementeren

U modellen continu implementeren met de Machine Learning-extensie voor [Azure DevOps.](https://azure.microsoft.com/services/devops/) U de machine learning-extensie voor Azure DevOps gebruiken om een implementatiepijplijn te activeren wanneer een nieuw machine learning-model is geregistreerd in een Azure Machine Learning-werkruimte.

1. Meld u aan voor [Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)waardoor continue integratie en levering van uw toepassing naar elk platform of cloud mogelijk is. (Houd er rekening mee dat Azure Pipelines niet hetzelfde is als [Machine Learning-pijplijnen](concept-ml-pipelines.md#compare).)

1. [Maak een Azure DevOps-project.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installeer de [Machine Learning-extensie voor Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Gebruik serviceverbindingen om een hoofdverbinding met uw Azure Machine Learning-werkruimte in te stellen, zodat u toegang hebt tot uw artefacten. Ga naar projectinstellingen, selecteer **Serviceverbindingen**en selecteer **Azure Resource Manager:**

    [![Azure Resource Manager selecteren](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Selecteer **AzureMLWorkspace**in de lijst **Bereikniveau** en voer de rest van de waarden in:

    ![AzureMLWorkspace selecteren](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Als u uw machine learning-model continu wilt implementeren met Azure Pipelines, selecteert u onder pijplijnen **de optie Release**. Voeg een nieuw artefact toe en selecteer vervolgens het **AzureML-modelartefact** en de serviceverbinding die u eerder hebt gemaakt. Selecteer het model en de versie om een implementatie te activeren:

    [![AzureML-model selecteren](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Schakel de modeltrigger in op het artefact van uw model. Wanneer u de trigger inschakelt, wordt elke keer dat de opgegeven versie (dat wil zeggen de nieuwste versie) van dat model in uw werkruimte wordt geregistreerd, een Azure DevOps-releasepijplijn geactiveerd.

    [![De modeltrigger inschakelen](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Zie deze voorbeeldrepo's in GitHub voor meer voorbeeldprojecten en voorbeelden:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Een model downloaden
Als u uw model wilt downloaden om het in uw eigen uitvoeringsomgeving te gebruiken, u dit doen met de volgende SDK/ CLI-opdrachten:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Cli:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Preview) Implementatie van no-code-model

No-code model implementatie is momenteel in preview en ondersteunt de volgende machine learning frameworks:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel-indeling
Tensorflow-modellen moeten worden geregistreerd in **de Indeling SavedModel** om te werken met implementatie van no-code-modellen.

Zie [deze link](https://www.tensorflow.org/guide/saved_model) voor informatie over het maken van een SavedModel.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>ONNX-modellen

Onnx-modelregistratie en -implementatie wordt ondersteund voor elke ONNX-inferencegrafiek. Preprocess- en postprocesstappen worden momenteel niet ondersteund.

Hier vindt u een voorbeeld van het registreren en implementeren van een MNIST ONNX-model:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Als u Pytorch gebruikt, heeft [het exporteren van modellen van PyTorch naar ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) de details over conversie en beperkingen. 

### <a name="scikit-learn-models"></a>Scikit-learn modellen

Er wordt geen codemodelimplementatie ondersteund voor alle ingebouwde scikit-learn-modeltypen.

Hier is een voorbeeld van hoe u een sklearn-model registreren en implementeren zonder extra code:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

OPMERKING: Deze afhankelijkheden zijn opgenomen in de vooraf gebouwde sklearn-inferentiecontainer:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Pakketmodellen

In sommige gevallen wilt u mogelijk een Docker-afbeelding maken zonder het model te implementeren (als u bijvoorbeeld van plan bent [te implementeren in Azure App Service).](how-to-deploy-app-service.md) Of u wilt de afbeelding downloaden en uitvoeren op een lokale Docker-installatie. Misschien wilt u zelfs de bestanden downloaden die worden gebruikt om de afbeelding te bouwen, ze te inspecteren, ze aan te passen en de afbeelding handmatig te bouwen.

Modelverpakking stelt u in staat om deze dingen te doen. Het verpakt alle activa die nodig zijn om een model te hosten als een webservice en stelt u in staat om een volledig gebouwde Docker-afbeelding te downloaden of de bestanden die nodig zijn om er een te bouwen. Er zijn twee manieren om modelverpakkingen te gebruiken:

**Download een verpakt model:** Download een Docker-afbeelding die het model en andere bestanden bevat dat nodig is om deze als webservice te hosten.

**Een Dockerfile genereren:** Download het Dockerfile, model, invoerscript en andere elementen die nodig zijn om een Docker-afbeelding te bouwen. U de bestanden vervolgens inspecteren of wijzigingen aanbrengen voordat u de afbeelding lokaal bouwt.

Beide pakketten kunnen worden gebruikt om een lokale Docker-afbeelding te krijgen.

> [!TIP]
> Het maken van een pakket is vergelijkbaar met het implementeren van een model. U gebruikt een geregistreerd model en een gevolgtrekkingsconfiguratie.

> [!IMPORTANT]
> Als u een volledig opgebouwde afbeelding wilt downloaden of lokaal een afbeelding wilt maken, moet [Docker](https://www.docker.com) in uw ontwikkelomgeving zijn geïnstalleerd.

### <a name="download-a-packaged-model"></a>Een verpakt model downloaden

In het volgende voorbeeld wordt een afbeelding gemaakt die is geregistreerd in het Azure-containerregister voor uw werkruimte:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Nadat u een pakket hebt `package.pull()` gemaakt, u de afbeelding naar uw lokale Docker-omgeving trekken. De uitvoer van deze opdracht geeft de naam van de afbeelding weer. Bijvoorbeeld: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Nadat u het model `docker images` hebt gedownload, gebruikt u de opdracht om de lokale afbeeldingen weer te geven:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Als u een lokale container wilt starten op basis van deze afbeelding, gebruikt u de volgende opdracht om een benoemde container te starten vanaf de shell- of opdrachtregel. Vervang `<imageid>` de waarde door de `docker images` afbeeldings-id die door de opdracht wordt geretourneerd.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Met deze opdracht wordt de `myimage`nieuwste versie van de afbeelding met de naam . Het brengt lokale poort 6789 in kaart aan de poort in de container waarop de webdienst luistert (5001). Het wijst ook `mycontainer` de naam aan de container toe, die de container gemakkelijker maakt te stoppen. Nadat de container is gestart, `http://localhost:6789/score`kunt u aanvragen indienen bij .

### <a name="generate-a-dockerfile-and-dependencies"></a>Een Dockerbestand en afhankelijkheden genereren

In het volgende voorbeeld ziet u hoe u het Dockerfile, model en andere elementen downloaden die nodig zijn om een afbeelding lokaal te bouwen. De `generate_dockerfile=True` parameter geeft aan dat u de bestanden wilt, niet een volledig opgebouwde afbeelding.

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

Deze code downloadt de bestanden die `imagefiles` nodig zijn om de afbeelding naar de map te bouwen. Het Dockerbestand dat in de opgeslagen bestanden is opgenomen, verwijst naar een basisafbeelding die is opgeslagen in een Azure-containerregister. Wanneer u de afbeelding op uw lokale Docker-installatie bouwt, moet u het adres, de gebruikersnaam en het wachtwoord gebruiken om te verifiëren bij het register. Gebruik de volgende stappen om de afbeelding te maken met een lokale Docker-installatie:

1. Gebruik vanuit een shell- of opdrachtregelsessie de volgende opdracht om Docker te verifiëren met het Azure-containerregister. Vervangen `<address>` `<username>`, `<password>` en door de `package.get_container_registry()`waarden die worden opgehaald door .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Als u de afbeelding wilt maken, gebruikt u de volgende opdracht. Vervang `<imagefiles>` door het pad `package.save()` van de map waar de bestanden zijn opgeslagen.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Met deze opdracht stelt `myimage`u de naam van de afbeelding in op .

Als u wilt controleren of `docker images` de afbeelding is gemaakt, gebruikt u de opdracht. U ziet `myimage` de afbeelding in de lijst:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Als u een nieuwe container wilt starten op basis van deze afbeelding, gebruikt u de volgende opdracht:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Met deze opdracht wordt de `myimage`nieuwste versie van de afbeelding met de naam . Het brengt lokale poort 6789 in kaart aan de poort in de container waarop de webdienst luistert (5001). Het wijst ook `mycontainer` de naam aan de container toe, die de container gemakkelijker maakt te stoppen. Nadat de container is gestart, `http://localhost:6789/score`kunt u aanvragen indienen bij .

### <a name="example-client-to-test-the-local-container"></a>Voorbeeldclient om de lokale container te testen

De volgende code is een voorbeeld van een Python-client die met de container kan worden gebruikt:

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

Zie Bijvoorbeeld clients in andere programmeertalen modellen [consumeren die zijn geïmplementeerd als webservices.](how-to-consume-web-service.md)

### <a name="stop-the-docker-container"></a>De Docker-container stoppen

Als u de container wilt stoppen, gebruikt u de volgende opdracht van een andere shell- of opdrachtregel:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een geïmplementeerde `service.delete()`webservice wilt verwijderen, gebruikt u .
Als u een geregistreerd `model.delete()`model wilt verwijderen, gebruikt u .

Zie de documentatie voor [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) en [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)voor meer informatie.

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Ontwerpvan geavanceerde invoerscript

<a id="binary"></a>

### <a name="binary-data"></a>Binaire gegevens

Als uw model binaire gegevens accepteert, zoals een `score.py` afbeelding, moet u het bestand wijzigen dat wordt gebruikt voor uw implementatie om ruwe HTTP-aanvragen te accepteren. Als u ruwe gegevens `AMLRequest` wilt accepteren, gebruikt `@rawhttp` u de `run()` klasse in uw invoerscript en voegt u de decorateur toe aan de functie.

Hier is een voorbeeld `score.py` van een die binaire gegevens accepteert:

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
> De `AMLRequest` klasse bevindt zich in de `azureml.contrib` naamruimte. Entiteiten in deze naamruimte veranderen regelmatig terwijl we werken aan het verbeteren van de service. Alles in deze naamruimte moet worden beschouwd als een voorbeeld dat niet volledig wordt ondersteund door Microsoft.
>
> Als u dit moet testen in uw lokale ontwikkelomgeving, u de componenten installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Cross-origin resource sharing (CORS)

Cross-origin resource sharing is een manier om resources op een webpagina te vragen vanuit een ander domein. CORS werkt via HTTP-headers die met de clientaanvraag worden verzonden en geretourneerd met het serviceantwoord. Zie [Cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in Wikipedia voor meer informatie over CORS en geldige headers.

Als u de implementatie van uw `AMLResponse` model wilt configureren om CORS te ondersteunen, gebruikt u de klasse in uw invoerscript. Met deze klasse u de kopteksten op het antwoordobject instellen.

In het volgende `Access-Control-Allow-Origin` voorbeeld wordt de koptekst ingesteld voor het antwoord uit het invoerscript:

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
> De `AMLResponse` klasse bevindt zich in de `azureml.contrib` naamruimte. Entiteiten in deze naamruimte veranderen regelmatig terwijl we werken aan het verbeteren van de service. Alles in deze naamruimte moet worden beschouwd als een voorbeeld dat niet volledig wordt ondersteund door Microsoft.
>
> Als u dit moet testen in uw lokale ontwikkelomgeving, u de componenten installeren met behulp van de volgende opdracht:
>
> ```shell
> pip install azureml-contrib-services
> ```

## <a name="next-steps"></a>Volgende stappen

* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie](how-to-troubleshoot-deployment.md)
* [Azure Machine Learning-webservices beveiligen met SSL](how-to-secure-web-service.md)
* [Een Azure Machine Learning-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning-modellen controleren met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurteniswaarschuwingen en -triggers maken voor modelimplementaties](how-to-use-event-grid.md)

