---
title: Een model implementeren voor demijnen met GPU
titleSuffix: Azure Machine Learning service
description: In dit artikel leert u hoe u de Azure Machine Learning-service kunt gebruiken voor het implementeren van een met GPU ingeschakeld tensor flow-Learning model als een webservice. aanvragen voor het instellen van de service en de score.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 07/24/2019
ms.openlocfilehash: 08ceb5d795465a5759d0130618eafdccdc8c3c91
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011523"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Een diep leer model implementeren voor demijnen met GPU

In dit artikel leert u hoe u de Azure Machine Learning-service kunt gebruiken om een model met GPU-functionaliteit te implementeren als een webservice. De informatie in dit artikel is gebaseerd op het implementeren van een model in azure Kubernetes service (AKS). Het AKS-cluster biedt een GPU-resource die wordt gebruikt door het model voor demijnen.

Detrainer of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor het maken van voor spellingen. Het gebruik van Gpu's in plaats van Cpu's biedt prestatie voordelen voor een zeer kan worden opgestart berekening.

> [!IMPORTANT]
> GPU-demijnen worden alleen ondersteund in de Azure Kubernetes-service.

> [!TIP]
> Hoewel de code fragmenten in dit artikel een tensor flow-model usee, kunt u de gegevens Toep assen op elk machine learning Framework dat Gpu's ondersteunt.

> [!NOTE]
> De informatie in dit artikel is gebaseerd op de informatie in het artikel [Deploy to Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md) . Als dat artikel in het algemeen betrekking heeft op de implementatie van AKS, wordt in dit artikel een specifieke GPU-implementatie besproken.

## <a name="prerequisites"></a>Vereisten

* Een werkruimte van Azure Machine Learning-service. Zie [een Azure machine learning service-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

* Een python-ontwikkel omgeving met de Azure Machine Learning SDK geïnstalleerd. Zie [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)voor meer informatie.  

* Een geregistreerd model dat gebruikmaakt van een GPU.

    * Zie [modellen implementeren](../service/how-to-deploy-and-where.md#registermodel)voor meer informatie over het registreren van modellen.

    * Zie [een tensor flow-model trainen](how-to-train-tensorflow.md)voor het maken en registreren van het tensor flow-model dat wordt gebruikt om dit document te maken.

* Algemene informatie [over hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werk ruimte

Gebruik de volgende code om verbinding te maken met een bestaande werk ruimte:

> [!IMPORTANT]
> Dit code fragment verwacht dat de werkruimte configuratie wordt opgeslagen in de huidige map of het bovenliggende item. Zie [Azure machine learning service-werk ruimten maken en beheren](how-to-manage-workspace.md)voor meer informatie over het maken van een werk ruimte.   Zie [een configuratie bestand voor een werk ruimte maken](how-to-configure-environment.md#workspace)voor meer informatie over het opslaan van de configuratie in een bestand.

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Een Kubernetes-cluster maken met Gpu's

De Azure Kubernetes-service biedt veel verschillende GPU-opties. U kunt elk van deze gebruiken voor model deinterferentie. Bekijk [de lijst met virtuele machines uit de N-serie](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) voor een volledige uitsplitsing van de mogelijkheden en kosten.

De volgende code laat zien hoe u een nieuw AKS-cluster maakt voor uw werk ruimte:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> In azure wordt u gefactureerd zolang het AKS-cluster bestaat. Zorg ervoor dat u uw AKS-cluster verwijdert wanneer u klaar bent.

Zie [How to Deploy to Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md)(Engelstalig) voor meer informatie over het gebruik van AKS met Azure machine learning service.

## <a name="write-the-entry-script"></a>Het invoer script schrijven

Het invoer script ontvangt gegevens die zijn verzonden naar de webservice, geeft deze door aan het model en retourneert de Score resultaten. Met het volgende script wordt het tensor flow-model bij het opstarten geladen en wordt het model vervolgens gebruikt voor het beoordelen van gegevens.

> [!TIP]
> Het invoer script is specifiek voor uw model. Het script moet bijvoorbeeld weten dat het Framework kan worden gebruikt met uw model, gegevens indelingen, enzovoort.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Dit bestand heeft de `score.py`naam. Voor meer informatie over invoer scripts, Zie [hoe en waar u wilt implementeren](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>De Conda-omgeving definiëren

Het Conda-omgevings bestand bevat de afhankelijkheden voor de service. Het bevat afhankelijkheden die worden vereist door het model en het script. De volgende YAML definieert de omgeving voor een tensor flow-model. Hiermee wordt `tensorflow-gpu`opgegeven, waarmee gebruik wordt gemaakt van de GPU die in deze implementatie wordt gebruikt:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

In dit voor beeld wordt het bestand opgeslagen als `myenv.yml`.

## <a name="define-the-deployment-configuration"></a>De implementatie configuratie definiëren

De implementatie configuratie definieert de Azure Kubernetes-service omgeving die wordt gebruikt om de webservice uit te voeren:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Zie de referentie documentatie voor [AksService. deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)voor meer informatie.

## <a name="define-the-inference-configuration"></a>De configuratie voor het afstellen van interferentie definiëren

De configuratie punten voor het afwijzen van de inzending naar het Conda-script bestand en de omgeving. Het schakelt ook GPU-ondersteuning in, die CUDA installeert in de docker-installatie kopie die is gemaakt voor de webservice:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml",
                                   enable_gpu=True)
```

Zie de referentie documentatie voor [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)voor meer informatie.

## <a name="deploy-the-model"></a>Het model implementeren

Implementeer het model op uw AKS-cluster en wacht tot het uw service heeft gemaakt.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Als het `InferenceConfig` object heeft `enable_gpu=True`, moet de `deployment_target` para meter verwijzen naar een cluster dat een GPU biedt. Anders mislukt de implementatie.

Zie de referentie documentatie voor [model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)voor meer informatie.

## <a name="issue-a-sample-query-to-your-service"></a>Een voorbeeld query voor uw service uitgeven

Verzend een test query naar het geïmplementeerde model. Wanneer u een JPEG-afbeelding naar het model verzendt, wordt de afbeelding gescoord. In het volgende code voorbeeld worden test gegevens gedownload en wordt vervolgens een wille keurige test afbeelding geselecteerd die naar de service wordt verzonden. 

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Zie [Create client to consumed web service](how-to-consume-web-service.md)(Engelstalig) voor meer informatie over het maken van een client toepassing.

## <a name="clean-up-the-resources"></a>De resources opschonen

Als u het AKS-cluster specifiek voor dit voor beeld hebt gemaakt, verwijdert u uw resources Nadat u klaar bent.

> [!IMPORTANT]
> Azure factureert u op basis van hoe lang het AKS-cluster wordt geïmplementeerd. Zorg ervoor dat u het opschoont nadat u klaar bent.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Volgende stappen

* [Model implementeren op FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Model implementeren met ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensor flow DNN-modellen trainen](../service/how-to-train-tensorflow.md)
