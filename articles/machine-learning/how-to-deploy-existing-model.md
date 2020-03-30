---
title: Bestaande modellen gebruiken en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u Azure Machine Learning gebruiken met modellen die buiten de service zijn getraind. U modellen registreren die buiten Azure Machine Learning zijn gemaakt en deze vervolgens implementeren als webservice of Azure IoT Edge-module.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472372"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Een bestaand model gebruiken met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van een bestaand machine learning-model met Azure Machine Learning.

Als u een machine learning-model hebt dat buiten Azure Machine Learning is getraind, u de service nog steeds gebruiken om het model te implementeren als webservice of naar een IoT Edge-apparaat. 

> [!TIP]
> Dit artikel bevat basisinformatie over het registreren en implementeren van een bestaand model. Na implementatie biedt Azure Machine Learning bewaking voor uw model. Het stelt u ook in staat om invoergegevens op te slaan die naar de implementatie worden verzonden, die kunnen worden gebruikt voor gegevensdriftanalyse of het trainen van nieuwe versies van het model.
>
> Zie [Machine learning-modellen beheren, implementeren en monitoren](concept-model-management-and-deployment.md)voor meer informatie over de concepten en termen die hier worden gebruikt.
>
> Zie [Modellen implementeren met Azure Machine Learning voor](how-to-deploy-and-where.md)algemene informatie over het implementatieproces.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [Een werkruimte maken](how-to-manage-workspace.md)voor meer informatie.

    > [!TIP]
    > De Voorbeelden van Python in `ws` dit artikel gaan ervan uit dat de variabele is ingesteld op uw Azure Machine Learning-werkruimte.
    >
    > De CLI-voorbeelden maken gebruik `myworkspace` `myresourcegroup`van een tijdelijke aanduiding van en . Vervang deze door de naam van uw werkruimte en de resourcegroep die deze bevat.

* De [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* De [AZURE CLI-](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en [Machine Learning CLI-extensie](reference-azure-machine-learning-cli.md).

* Een getraind model. Het model moet worden volgehouden tot een of meer bestanden op uw ontwikkelomgeving.

    > [!NOTE]
    > Om aan te tonen dat een model is geregistreerd dat buiten Azure Machine Learning is getraind, gebruiken de [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)voorbeeldcodefragmenten in dit artikel de modellen die zijn gemaakt door het Twitter-sentimentanalyseproject van Paolo Ripamonti: .

## <a name="register-the-models"></a>Het model(en) registreren

Als u een model registreert, u metagegevens over modellen in uw werkruimte opslaan, gebruiken en bijhouden. In de volgende Voorbeelden van `models` Python en `model.h5` `model.w2v`CLI `encoder.pkl`bevat `tokenizer.pkl` de map de , , en bestanden. In dit voorbeeld worden de `models` bestanden in de map `sentiment`geüpload als een nieuwe modelregistratie met de naam:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Zie de referentie [Model.register() voor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) meer informatie.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> U ook `tags` `properties` add- en woordenboekobjecten instellen op het geregistreerde model. Deze waarden kunnen later worden gebruikt om een specifiek model te identificeren. Bijvoorbeeld het gebruikte kader, trainingsparameters, enz.

Zie voor meer informatie de referentie van het [AZ ML-modelregister.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Zie [Machine learning-modellen beheren, implementeren en controleren](concept-model-management-and-deployment.md)voor meer informatie over modelregistratie in het algemeen.

## <a name="define-inference-configuration"></a>Inferenceconfiguratie definiëren

De conclusieconfiguratie definieert de omgeving die wordt gebruikt om het geïmplementeerde model uit te voeren. De gevolgtrekkingsconfiguratie verwijst naar de volgende entiteiten, die worden gebruikt om het model uit te voeren wanneer het wordt geïmplementeerd:

* Een invoerscript. Dit bestand `score.py`(met de naam ) laadt het model wanneer de geïmplementeerde service wordt gestart. Het is ook verantwoordelijk voor het ontvangen van gegevens, het doorgeven aan het model, en vervolgens het retourneren van een reactie.
* Een Azure Machine [Learning-omgeving](how-to-use-environments.md). Een omgeving definieert de softwareafhankelijkheden die nodig zijn om het model- en invoerscript uit te voeren.

In het volgende voorbeeld ziet u hoe u de SDK gebruikt om een omgeving te maken en deze vervolgens te gebruiken met een gevolgtrekkingsconfiguratie:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Raadpleeg voor meer informatie de volgende artikelen:

+ [Omgevingen gebruiken.](how-to-use-environments.md)
+ [GevolgtrekkingConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referentie.


De CLI laadt de gevolgtrekkingsconfiguratie uit een YAML-bestand:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Met de CLI wordt de conda-omgeving gedefinieerd in het `myenv.yml` bestand waarnaar wordt verwezen door de gevolgtrekkingsconfiguratie. De volgende YAML is de inhoud van dit bestand:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Zie Modellen implementeren met Azure [Machine Learning voor](how-to-deploy-and-where.md)meer informatie over de configuratie van gevolgtrekkingen.

### <a name="entry-script"></a>Invoerscript

Het invoerscript heeft slechts `init()` twee `run(data)`vereiste functies en . Deze functies worden gebruikt om de service te initialiseren bij het opstarten en het model uit te voeren met behulp van aanvraaggegevens die door een client zijn doorgegeven. De rest van het script verwerkt het laden en uitvoeren van het model(en).

> [!IMPORTANT]
> Er is geen algemeen invoerscript dat voor alle modellen werkt. Het is altijd specifiek voor het model dat wordt gebruikt. Het moet begrijpen hoe het model moet worden geladen, de gegevensindeling die het model verwacht en hoe gegevens te scoren met behulp van het model.

De volgende Python-code is`score.py`een voorbeelditemscript ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Zie Modellen implementeren met [Azure Machine Learning voor](how-to-deploy-and-where.md)meer informatie over invoerscripts.

## <a name="define-deployment"></a>Implementatie definiëren

Het [Webservice-pakket](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) bevat de klassen die worden gebruikt voor implementatie. De klasse die u gebruikt, bepaalt waar het model is geïmplementeerd. Als u bijvoorbeeld als webservice op Azure Kubernetes Service wilt implementeren, gebruikt u [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) om de implementatieconfiguratie te maken.

De volgende Python-code definieert een implementatieconfiguratie voor een lokale implementatie. Met deze configuratie wordt het model geïmplementeerd als een webservice op uw lokale computer.

> [!IMPORTANT]
> Een lokale implementatie vereist een werkende installatie van [Docker](https://www.docker.com/) op uw lokale computer:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Zie de referentie [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) voor meer informatie.

De CLI laadt de implementatieconfiguratie uit een YAML-bestand:

```YAML
{
    "computeType": "LOCAL"
}
```

Implementeren naar een ander rekendoel, zoals Azure Kubernetes Service in de Azure-cloud, is net zo eenvoudig als het wijzigen van de implementatieconfiguratie. Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie.

## <a name="deploy-the-model"></a>Het model implementeren

In het volgende voorbeeld wordt `sentiment`informatie over het geregistreerde model `sentiment`met de naam geladen en wordt deze vervolgens geïmplementeerd als een service met de naam . Tijdens de implementatie worden de inferenceconfiguratie en implementatieconfiguratie gebruikt om de serviceomgeving te maken en te configureren:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Zie de referentie [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) voor meer informatie.

Als u het model vanuit de CLI wilt implementeren, gebruikt u de volgende opdracht. Met deze opdracht wordt versie 1`sentiment:1`van het geregistreerde model ( ) `inferenceConfig.json` `deploymentConfig.json` geïmplementeerd met behulp van de gevolgtrekking en implementatieconfiguratie die is opgeslagen in de bestanden en bestanden:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Zie voor meer informatie de referentie voor het [implementeren van het AZ ML-model.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie over implementatie.

## <a name="request-response-consumption"></a>Verbruik van antwoord op verzoek

Na implementatie wordt de scorende URI weergegeven. Deze URI kan door clients worden gebruikt om aanvragen bij de service in te dienen. Het volgende voorbeeld is een basis-Python-client die gegevens naar de service verzendt en het antwoord weergeeft:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Zie [Een client maken](how-to-consume-web-service.md)voor meer informatie over het gebruik van de geïmplementeerde service.

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Machine Learning-modellen controleren met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Hoe en waar modellen kunnen worden geïmplementeerd](how-to-deploy-and-where.md)
* [Een client maken voor een geïmplementeerd model](how-to-consume-web-service.md)
