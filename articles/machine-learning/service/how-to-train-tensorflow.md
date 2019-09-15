---
title: Een diepe Learning Neural-netwerk trainen met tensor flow
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van tensor flow-trainings scripts op schaal met behulp van Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 52c675369fa70d1b1113f34b9b0dda2126547e0a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002508"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Bouw een tensor flow-Learning model op schaal met Azure Machine Learning

Dit artikel laat u zien hoe u uw [tensor flow](https://www.tensorflow.org/overview) -trainings scripts op schaal kunt uitvoeren met behulp van de [tensor flow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) -klasse van Azure machine learning. In dit voor beeld wordt een tensor flow-model getraind en geregistreerd om handgeschreven cijfers te classificeren met behulp van een diepe Neural Network (DNN).

Of u nu een tensor flow-model ontwikkelt of een [bestaand model](how-to-deploy-existing-model.md) in de Cloud brengt, u kunt Azure machine learning gebruiken voor het uitschalen van open-source trainings taken om productie-kwaliteits modellen te bouwen, te implementeren, te maken en te bewaken .

Meer informatie over [uitgebreide kennis en machine learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:

 - Azure Machine Learning-notebook-VM-geen down loads of installatie vereist

     - U moet de [Zelfstudie: Omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) instellen voor het maken van een toegewezen notebook server vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met uitgebreide trainingen op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **instructies-to-use-azureml > training-met-learning > Train-afstemming-Tune-Deploy-with-tensor flow** map. 
 
 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - [De voorbeeld script bestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` en`utils.py`
     
    U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. Het notitie blok bevat uitgebreide secties die betrekking hebben op intelligent afstemming tuning, model implementatie en notebook widgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van een experiment en het uploaden van de trainings gegevens en trainings scripts.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) object te maken.

Maak een werkruimte object op basis `config.json` van het bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Een diep leer experiment maken

Maak een experiment en een map om uw trainings scripts te bewaren. In dit voor beeld maakt u een experiment met de naam ' TF-mnist '.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Gegevensset en scripts uploaden

Het gegevens [Archief](how-to-access-data.md) is een plek waar de informatie kan worden opgeslagen en geopend door het koppelen of kopiëren van de gegevens naar het reken doel. Elke werk ruimte biedt een standaard gegevens opslag. Upload de gegevens-en trainings scripts naar het gegevens archief zodat ze eenvoudig kunnen worden geopend tijdens de training.

1. Down load de MNIST-gegevensset lokaal.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Upload de MNIST-gegevensset naar de standaard gegevens opslag.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Upload het tensor flow-trainings script `tf_mnist.py`, en het helperbestand, `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Een compute-doel maken

Maak een compute-doel voor uw tensor flow-taak om uit te voeren. In dit voor beeld maakt u een Azure Machine Learning Compute-cluster waarvoor GPU is ingeschakeld.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Zie voor meer informatie over Compute-doelen het artikel [Wat is een reken doel](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Een tensor flow-Estimator maken

De [tensor flow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) biedt een eenvoudige manier om een tensor flow-trainings taak te starten op een compute-doel.

De tensor flow Estimator wordt geïmplementeerd via de algemene [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasse, die kan worden gebruikt ter ondersteuning van een Framework. Voor meer informatie over trainings modellen die gebruikmaken van de algemene Estimator, raadpleegt [u modellen met Azure machine learning met behulp van Estimator](how-to-train-ml-models.md)

Als uw trainings script extra PIP-of Conda-pakketten nodig heeft om uit te voeren, kunt u de pakketten op de resulterende docker-installatie kopie installeren `pip_packages` door `conda_packages` hun namen door te geven via de argumenten en.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Een run verzenden

Het [object run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: Een docker-installatie kopie wordt gemaakt op basis van de tensor flow-Estimator. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken.

- **Schalen**: Het cluster probeert omhoog te schalen als het Batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: Alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map./logs worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Nabewerken**: De map./outputs van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, kunt u het registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

U kunt ook een lokale kopie van het model downloaden met behulp van het object run. In het trainings script `mnist-tf.py`wordt het model door een tensor flow-beveiligings object naar een lokale map (lokaal naar het Compute-doel) bewaard. U kunt het object run gebruiken om een kopie te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Gedistribueerde training

De [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Estimator biedt ook ondersteuning voor gedistribueerde training over CPU-en GPU-clusters. U kunt eenvoudig gedistribueerde tensor flow-taken uitvoeren en de indeling wordt door Azure Machine Learning beheerd.

Azure Machine Learning ondersteunt twee methoden van gedistribueerde training in TensorFlow:

- [Mpi](https://www.open-mpi.org/) gedistribueerde training met behulp van het [Horovod](https://github.com/uber/horovod) -Framework
- Systeem eigen [gedistribueerde tensor flow](https://www.tensorflow.org/deploy/distributed) met de para meter server methode

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) is een open-source framework voor gedistribueerde training ontwikkeld door uber. Het biedt een eenvoudig pad naar gedistribueerde GPU tensor flow-taken.

Als u Horovod wilt gebruiken, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) geeft u een `distributed_training` object op voor de para meter in de tensor flow-constructor. Deze para meter zorgt ervoor dat de Horovod-bibliotheek wordt geïnstalleerd zodat u deze kunt gebruiken in uw trainings script.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Parameterserver

U kunt ook uitvoeren [native gedistribueerde TensorFlow](https://www.tensorflow.org/deploy/distributed), waarbij het servermodel parameter worden gebruikt. Bij deze methode hoeft trainen u in een cluster van de parameter-servers en werknemers. De werknemers Bereken de verlopen tijdens de training, terwijl de parameter-servers de verlopen aggregeren.

Als u de methode van de parameter server wilt [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) gebruiken, geeft `distributed_training` u een object op voor de para meter in de tensor flow-constructor.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Cluster specificaties definiëren in ' TF_CONFIG '

U hebt ook de netwerk adressen en poorten van het cluster nodig voor [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)de, zodat Azure machine learning de `TF_CONFIG` omgevings variabele voor u instelt.

De `TF_CONFIG` omgevingsvariabele is een JSON-tekenreeks. Hier volgt een voorbeeld van de variabele voor de parameterserver:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Voor de API op hoog [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) niveau van tensor flow parseert tensor flow `TF_CONFIG` de variabele en bouwt de cluster specificatie voor u.

Voor de kern-api's van tensor flow voor training moet u de `TF_CONFIG` variabele parseren en de `tf.train.ClusterSpec` in uw trainings code bouwen.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een tensor flow-model getraind en geregistreerd. Voor meer informatie over het implementeren van een model op een GPU-cluster gaat u verder met het artikel over het implementeren van het GPU-model.

> [!div class="nextstepaction"]
> [Hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md)
* [Metrische gegevens over uitvoeren tijdens de training bijhouden](how-to-track-experiments.md)
* [Afstemmen van hyperparameters](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
