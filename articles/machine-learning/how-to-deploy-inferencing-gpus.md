---
title: Een model implementeren voor gevolgtrekking met GPU
titleSuffix: Azure Machine Learning
description: In dit artikel leert u hoe u Azure Machine Learning gebruiken om een Tenslearning-model met GPU-ondersteuning te implementeren als webservice.service en scoregevolgingsaanvragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398329"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Een deep learning-model implementeren voor gevolgtrekking met GPU
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning gebruiken om een GPU-model als webservice te implementeren. De informatie in dit artikel is gebaseerd op het implementeren van een model op Azure Kubernetes Service (AKS). Het AKS-cluster biedt een GPU-bron die door het model wordt gebruikt voor gevolgtrekking.

Gevolgtrekking, of modelscore, is de fase waarin het geïmplementeerde model wordt gebruikt om voorspellingen te doen. Het gebruik van GPU's in plaats van CPU's biedt prestatievoordelen op zeer parallelizable berekening.

> [!IMPORTANT]
> Voor webservice-implementaties wordt GPU-gevolgtrekking alleen ondersteund op Azure Kubernetes Service. Voor gevolgtrekking en gebruik van een __machine learning-pijplijn__worden GPU's alleen ondersteund op Azure Machine Learning Compute. Zie [Batchvoorspellingen uitvoeren](how-to-use-parallel-run-step.md)voor meer informatie over het gebruik van ML-pijplijnen. 

> [!TIP]
> Hoewel de codefragmenten in dit artikel een TensorFlow-model gebruiken, u de informatie toepassen op elk machine learning-framework dat GPU's ondersteunt.

> [!NOTE]
> De informatie in dit artikel bouwt voort op de informatie in het artikel [Hoe te implementeren in Azure Kubernetes Service.](how-to-deploy-azure-kubernetes-service.md) Wanneer dat artikel over het algemeen betrekking heeft op implementatie naar AKS, heeft dit artikel betrekking op GPU-specifieke implementatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)voor meer informatie.

* Een Python-ontwikkelomgeving met de Azure Machine Learning SDK geïnstalleerd. Zie [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)voor meer informatie.  

* Een geregistreerd model dat een GPU gebruikt.

    * Zie Modellen implementeren voor meer informatie over het registreren [van modellen.](how-to-deploy-and-where.md#registermodel)

    * Zie [Een TensorFlow-model](how-to-train-tensorflow.md)trainen als u het Tensorflow-model wilt maken en registreren dat wordt gebruikt om dit document te maken.

* Een algemeen inzicht [in hoe en waar modellen te implementeren.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Verbinding maken met uw werkruimte

Als u verbinding wilt maken met een bestaande werkruimte, gebruikt u de volgende code:

> [!IMPORTANT]
> In dit codefragment wordt verwacht dat de werkruimteconfiguratie wordt opgeslagen in de huidige map of de bovenliggende configuratie. Zie [Azure Machine Learning-werkruimten maken en beheren](how-to-manage-workspace.md)voor meer informatie over het maken van een werkruimte.   Zie [Een configuratiebestand voor een werkruimte maken](how-to-configure-environment.md#workspace)voor meer informatie over het opslaan van de configuratie in het bestand.

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Een Kubernetes-cluster maken met GPU's

Azure Kubernetes Service biedt veel verschillende GPU-opties. U elk van hen gebruiken voor modelgevolgtrekking. Zie [de lijst met VM's uit de N-serie](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) voor een volledige uitsplitsing van mogelijkheden en kosten.

In de volgende code wordt uitgelegd hoe u een nieuw AKS-cluster voor uw werkruimte maakt:

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
> Azure factureert u zolang het AKS-cluster bestaat. Zorg ervoor dat u uw AKS-cluster verwijdert wanneer u ermee klaar bent.

Zie [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)voor meer informatie over het gebruik van AKS met Azure Machine Learning.

## <a name="write-the-entry-script"></a>Het invoerscript schrijven

Het invoerscript ontvangt gegevens die zijn ingediend bij de webservice, geeft deze door aan het model en retourneert de scoreresultaten. Het volgende script laadt het Tensorflow-model bij het opstarten en gebruikt het model vervolgens om gegevens te scoren.

> [!TIP]
> Het invoerscript is specifiek voor uw model. Het script moet bijvoorbeeld het te gebruiken framework kennen met uw model, gegevensindelingen, enz.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Dit bestand `score.py`heeft de naam . Zie Hoe en waar [u](how-to-deploy-and-where.md)de implementatie implementeren voor meer informatie over invoerscripts.

## <a name="define-the-conda-environment"></a>De conda-omgeving definiëren

Het conda-omgevingsbestand geeft de afhankelijkheden voor de service op. Het omvat afhankelijkheden die vereist zijn door zowel het model als het invoerscript. Houd er rekening mee dat u azureml-defaults met verion >= 1.0.45 als een pip afhankelijkheid moet opgeven, omdat het de functionaliteit bevat die nodig is om het model als een webservice te hosten. De volgende YAML definieert de omgeving voor een Tensorflow-model. Het `tensorflow-gpu`geeft aan , die gebruik zal maken van de GPU die wordt gebruikt in deze implementatie:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

In dit voorbeeld wordt het `myenv.yml`bestand opgeslagen als .

## <a name="define-the-deployment-configuration"></a>De implementatieconfiguratie definiëren

De implementatieconfiguratie definieert de Azure Kubernetes Service-omgeving die wordt gebruikt om de webservice uit te voeren:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Zie voor meer informatie de referentiedocumentatie voor [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>De conclusieconfiguratie definiëren

De conclusieconfiguratie verwijst naar het invoerscript en een omgevingsobject, dat gebruik maakt van een dockerafbeelding met GPU-ondersteuning. Houd er rekening mee dat het YAML-bestand dat wordt gebruikt voor de definitie van omgeving azureml-standaards moet vermelden met versie >= 1.0,45 als een pip-afhankelijkheid, omdat het de functionaliteit bevat die nodig is om het model als een webservice te hosten.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over omgevingen.
Zie voor meer informatie de referentiedocumentatie voor [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Het model implementeren

Implementeer het model in uw AKS-cluster en wacht tot het uw service maakt.

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
> Als `InferenceConfig` het `enable_gpu=True`object dat `deployment_target` heeft, moet de parameter verwijzen naar een cluster dat een GPU biedt. Anders mislukt de implementatie.

Zie voor meer informatie de referentiedocumentatie voor [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Een voorbeeldquery aan uw service geven

Stuur een testquery naar het geïmplementeerde model. Wanneer u een jpeg-afbeelding naar het model stuurt, wordt de afbeelding weergegeven. In het volgende codevoorbeeld worden testgegevens gedownload en wordt vervolgens een willekeurige testafbeelding geselecteerd die naar de service moet worden verzonden.

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

Zie [Client maken om geïmplementeerde webservice te gebruiken voor](how-to-consume-web-service.md)meer informatie over het maken van een clienttoepassing.

## <a name="clean-up-the-resources"></a>De resources opschonen

Als u het AKS-cluster speciaal voor dit voorbeeld hebt gemaakt, verwijdert u uw resources nadat u klaar bent.

> [!IMPORTANT]
> Azure factureert u op basis van hoe lang het AKS-cluster wordt geïmplementeerd. Zorg ervoor dat het schoon te maken nadat u klaar bent met het.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Volgende stappen

* [Model implementeren op FPGA](how-to-deploy-fpga-web-service.md)
* [Model implementeren met ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Trein Tensorflow DNN Modellen](how-to-train-tensorflow.md)
