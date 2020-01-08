---
title: Bestaande modellen gebruiken en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u Azure Machine Learning kunt gebruiken met modellen die buiten de service zijn getraind. U kunt modellen registreren die buiten Azure Machine Learning zijn gemaakt en deze vervolgens implementeren als webservice of Azure IoT Edge module.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: d783751137510febf9fb2e31d9d44728a049d88d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611813"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Een bestaand model gebruiken met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van een bestaand machine learning model met Azure Machine Learning.

Als u een machine learning model hebt dat is getraind buiten Azure Machine Learning, kunt u de service nog steeds gebruiken om het model te implementeren als een webservice of op een IoT Edge apparaat. 

> [!TIP]
> Dit artikel bevat basis informatie over het registreren en implementeren van een bestaand model. Na de implementatie biedt Azure Machine Learning bewaking voor uw model. U kunt hiermee ook invoer gegevens opslaan die naar de implementatie worden verzonden, die kunnen worden gebruikt voor gegevens verplaatsing of voor het trainen van nieuwe versies van het model.
>
> Zie [machine learning modellen beheren, implementeren en controleren](concept-model-management-and-deployment.md)voor meer informatie over de concepten en termen die hier worden gebruikt.
>
> Zie [modellen implementeren met Azure machine learning](service/how-to-deploy-and-where.md)voor algemene informatie over het implementatie proces.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [een werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

    > [!TIP]
    > In de python-voor beelden in dit artikel wordt ervan uitgegaan dat de variabele `ws` is ingesteld op uw Azure Machine Learning-werk ruimte.
    >
    > De CLI-voor beelden gebruiken een tijdelijke aanduiding van `myworkspace` en `myresourcegroup`. Vervang deze door de naam van uw werk ruimte en de resource groep waarin deze zich bevindt.

* De [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* De [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -en [machine learning cli-extensie](reference-azure-machine-learning-cli.md).

* Een getraind model. Het model moet persistent zijn gemaakt voor een of meer bestanden in uw ontwikkel omgeving.

    > [!NOTE]
    > Voor het registreren van een model dat is getraind buiten Azure Machine Learning, gebruiken de voorbeeld code fragmenten in dit artikel de modellen die zijn gemaakt met het Twitter sentiment Analysis-project van Paolo Ripamonti: [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Model (len) registreren

Als u een model registreert, kunt u meta gegevens over modellen in uw werk ruimte opslaan, versie en bijhouden. In de volgende python-en CLI-voor beelden bevat de `models` Directory de bestanden `model.h5`, `model.w2v`, `encoder.pkl`en `tokenizer.pkl`. In dit voor beeld worden de bestanden in de `models` Directory geüpload als een nieuwe model registratie met de naam `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Zie voor meer informatie de referentie [model. register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Zie voor meer informatie de verwijzing [AZ ml model REGI ster](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) .


Zie [machine learning modellen beheren, implementeren en bewaken](concept-model-management-and-deployment.md)voor meer informatie over model registratie in het algemeen.


## <a name="define-inference-configuration"></a>Configuratie voor het afstellen van interferenties definiëren

De configuratie voor afwijzen bepaalt de omgeving die wordt gebruikt om het geïmplementeerde model uit te voeren. De configuratie voor het afmaken van een deservering verwijst naar de volgende entiteiten die worden gebruikt om het model uit te voeren wanneer het wordt geïmplementeerd:

* Een invoer script. Dit bestand (met de naam `score.py`) laadt het model wanneer de geïmplementeerde service wordt gestart. Het is ook verantwoordelijk voor het ontvangen van gegevens, het door geven aan het model en het retour neren van een antwoord.
* Een Azure Machine Learning [omgeving](how-to-use-environments.md). Een omgeving definieert de software afhankelijkheden die nodig zijn voor het uitvoeren van het model en het vermeldings script.

In het volgende voor beeld ziet u hoe u de SDK gebruikt om een omgeving te maken en deze vervolgens te gebruiken met een Afleidings configuratie:

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

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Raadpleeg voor meer informatie de volgende artikelen:

+ [Het gebruik van omgevingen](how-to-use-environments.md).
+ [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -verwijzing.


De CLI laadt de configuratie van de deinterferentie van een YAML-bestand:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Met de CLI wordt de Conda-omgeving gedefinieerd in het `myenv.yml` bestand waarnaar wordt verwezen door de configuratie voor afwijzen. De volgende YAML is de inhoud van dit bestand:

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
```

Zie [modellen implementeren met Azure machine learning](service/how-to-deploy-and-where.md)voor meer informatie over het afnemen van de configuratie.

### <a name="entry-script"></a>Invoer script

Het invoer script heeft slechts twee vereiste functies: `init()` en `run(data)`. Deze functies worden gebruikt voor het initialiseren van de service bij het opstarten en het uitvoeren van het model met behulp van aanvraag gegevens die door een client worden door gegeven. De rest van het script zorgt voor het laden en uitvoeren van de model (len).

> [!IMPORTANT]
> Er is geen algemeen invoer script dat voor alle modellen werkt. Het is altijd specifiek voor het model dat wordt gebruikt. Het moet inzicht krijgen in het laden van het model, de gegevens indeling die het model verwacht en hoe u gegevens met behulp van het model kunt scoren.

De volgende python-code is een voorbeeld script (`score.py`):

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

Zie [modellen implementeren met Azure machine learning](service/how-to-deploy-and-where.md)voor meer informatie over invoer scripts.

## <a name="define-deployment"></a>Implementatie definiëren

Het [webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) -pakket bevat de klassen die worden gebruikt voor de implementatie. De klasse die u gebruikt, bepaalt waar het model wordt geïmplementeerd. Als u bijvoorbeeld wilt implementeren als webservice op de Azure Kubernetes-service, gebruikt u [AksWebService. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) om de implementatie configuratie te maken.

De volgende python-code definieert een implementatie configuratie voor een lokale implementatie. Met deze configuratie wordt het model als een webservice geïmplementeerd op uw lokale computer.

> [!IMPORTANT]
> Een lokale implementatie vereist een werkende installatie van [docker](https://www.docker.com/) op uw lokale computer:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Zie de referentie [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) voor meer informatie.

De CLI laadt de implementatie configuratie van een YAML-bestand:

```YAML
{
    "computeType": "LOCAL"
}
```

Implementatie op een ander reken doel, zoals de Azure Kubernetes-service in de Azure-Cloud, is net zo eenvoudig als het wijzigen van de implementatie configuratie. Zie [hoe en waar modellen worden geïmplementeerd](service/how-to-deploy-and-where.md)voor meer informatie.

## <a name="deploy-the-model"></a>Het model implementeren

In het volgende voor beeld wordt informatie over het geregistreerde model met de naam `sentiment`geladen en vervolgens geïmplementeerd als een service met de naam `sentiment`. Tijdens de implementatie worden de configuratie-en implementatie configuratie voor deinterferenties gebruikt om de service omgeving te maken en te configureren:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Zie de naslag gids voor [model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) voor meer informatie.

Gebruik de volgende opdracht om het model te implementeren vanuit de CLI. Met deze opdracht wordt versie 1 van het geregistreerde model (`sentiment:1`) geïmplementeerd met de configuratie voor inschakeling en implementatie die is opgeslagen in de `inferenceConfig.json` en `deploymentConfig.json` bestanden:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Zie voor meer informatie de referentie [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

Zie [hoe en wanneer u modellen implementeert](service/how-to-deploy-and-where.md)voor meer informatie over de implementatie.

## <a name="request-response-consumption"></a>Aanvraag-antwoord verbruik

Na de implementatie wordt de Score-URI weer gegeven. Deze URI kan worden gebruikt door clients voor het verzenden van aanvragen naar de service. Het volgende voor beeld is een eenvoudige python-client waarmee gegevens naar de service worden verzonden en het antwoord wordt weer gegeven:

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

Zie [een client maken](how-to-consume-web-service.md)voor meer informatie over het gebruiken van de geïmplementeerde service.

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Hoe en waar modellen moeten worden geïmplementeerd](service/how-to-deploy-and-where.md)
* [Een client voor een geïmplementeerd model maken](how-to-consume-web-service.md)
