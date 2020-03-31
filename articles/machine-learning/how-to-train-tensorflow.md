---
title: Een TensorFlow-model trainen en implementeren
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van TensorFlow-trainingsscripts op schaal met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228302"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Bouw een TensorFlow deep learning-model op schaal met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel ziet u hoe u uw [TensorFlow-trainingsscripts](https://www.tensorflow.org/overview) op schaal uitvoeren met de [tensorflow-schatterklasse van](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Azure Machine Learning. In dit voorbeeld wordt een TensorFlow-model traint en registreert om handgeschreven cijfers te classificeren met behulp van een diep neuraal netwerk (DNN).

Of u nu een TensorFlow-model vanaf de basis ontwikkelt of een [bestaand model](how-to-deploy-existing-model.md) in de cloud brengt, u Azure Machine Learning gebruiken om open-source trainingstaken uit te schalen om productiemodellen te bouwen, implementeren, implementeren en monitoren.

Meer informatie over [deep learning versus machine learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Vereisten

Voer deze code uit op een van deze omgevingen:

 - Azure Machine Learning rekeninstantie - geen downloads of installatie nodig

     - De [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md) voltooien om een speciale notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map deep learning op de notebookserver een voltooid en uitgebreid notitieblok door naar deze map te navigeren: **how-to-use-azureml > ml-frameworks > tensorflow >-implementatie > map trein-hyperparameter-tune-deploy-with-tensorflow.** 
 
 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Een configuratiebestand voor werkruimtes maken](how-to-configure-environment.md#workspace).
    - [Download de voorbeeldscriptbestanden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` en`utils.py`
     
    U ook een voltooide [Jupyter Notebook-versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) van deze handleiding vinden op de pagina GitHub-voorbeelden. De notebook bevat uitgebreide secties over intelligente hyperparametertuning, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainingsexperiment ingesteld door de vereiste Python-pakketten te laden, een werkruimte te initialiseren, een experiment te maken en de trainingsgegevens en trainingsscripts te uploaden.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning-werkruimte](concept-workspace.md) is de bron op het hoogste niveau voor de service. Het biedt u een centrale plek om te werken met alle artefacten die u maakt. In de Python SDK hebt u toegang tot [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) de artefacten van de werkruimte door een object te maken.

Maak een werkruimteobject `config.json` uit het bestand dat is gemaakt in de [sectie Vereistevoorwaarden](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Een deep learning-experiment maken

Maak een experiment en een map om uw trainingsscripts vast te houden. Maak in dit voorbeeld een experiment met de naam "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Een bestandsgegevensset maken

Een `FileDataset` object verwijst naar een of meer bestanden in uw werkruimtegegevensarchief of openbare url's. De bestanden kunnen van elk formaat zijn en de klasse biedt u de mogelijkheid om de bestanden te downloaden of te monteren op uw berekening. Door een `FileDataset`, maakt u een verwijzing naar de locatie van de gegevensbron. Als u transformaties toepast op de gegevensset, worden deze ook opgeslagen in de gegevensset. De gegevens blijven op de bestaande locatie, dus er worden geen extra opslagkosten gemaakt. Zie de [handleiding](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) over `Dataset` het pakket voor meer informatie.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Gebruik `register()` de methode om de gegevensset te registreren in uw werkruimte, zodat deze met anderen kunnen worden gedeeld, opnieuw kan worden gebruikt in verschillende experimenten en met naam kan worden aangeduid in uw trainingsscript.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een rekendoel voor uw TensorFlow-taak waarop u uitvoeren. Maak in dit voorbeeld een Azure Machine Learning-computecluster met GPU.To this example, create a GPU-enabled Azure Machine Learning compute cluster.

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

Zie het [artikel wat een compute target is](concept-compute-target.md) voor meer informatie over rekendoelen.

## <a name="create-a-tensorflow-estimator"></a>Een TensorFlow-schatter maken

De [TensorFlow-schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) biedt een eenvoudige manier om een TensorFlow-trainingstaak te starten op een compute target.

De TensorFlow-schatter wordt geïmplementeerd [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) via de generieke klasse, die kan worden gebruikt om elk framework te ondersteunen. Zie [treinmodellen met Azure Machine Learning met behulp van schatter](how-to-train-ml-models.md) voor meer informatie over trainingsmodellen met behulp van de generieke schatter

Als uw trainingsscript extra pip- of conda-pakketten nodig heeft om uit te voeren, kunt `pip_packages` `conda_packages` u de pakketten op de resulterende Docker-afbeelding laten installeren door hun namen door te geven via de en argumenten.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Ondersteuning voor **Tensorflow 2.0** is toegevoegd aan de Tensorflow schatter klasse. Zie de [blogpost](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) voor meer informatie.

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over het aanpassen van uw Python-omgeving. 

## <a name="submit-a-run"></a>Een run verzenden

Het [object Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface naar de rungeschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Als de run wordt uitgevoerd, gaat deze door de volgende fasen:

- **Voorbereiden**: Er wordt een Docker-afbeelding gemaakt volgens de TensorFlow-schatter. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere uitvoeringen. Logboeken worden ook gestreamd naar de rungeschiedenis en kunnen worden bekeken om de voortgang te controleren.

- **Schalen:** het cluster probeert op te schalen als het AI-cluster batch meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar is.

- **Actief:** Alle scripts in de scriptmap worden geüpload naar het compute-doel, gegevensarchieven worden gemonteerd of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map ./logs worden gestreamd naar de rungeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Nabewerking:** de map ./uitvoer van de run wordt gekopieerd naar de rungeschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, u het registreren op uw werkruimte. Met modelregistratie u uw modellen opslaan en in uw werkruimte gebruiken om [het modelbeheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen. Door de parameters `model_framework` `model_framework_version`op `resource_configuration`te geven en wordt de implementatie van no-code-modellen beschikbaar. Hiermee u uw model rechtstreeks implementeren als een webservice vanuit het geregistreerde model en definieert het object de `ResourceConfiguration` compute resource voor de webservice.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

U ook een lokale kopie van het model downloaden met het object Uitvoeren. In het `mnist-tf.py`trainingsscript blijft een TensorFlow-saver-object het model aanhouden in een lokale map (lokaal naar het rekendoel). U het object Uitvoeren gebruiken om een kopie te downloaden.

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

De [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) schatter ondersteunt ook gedistribueerde training in CPU- en GPU-clusters. U eenvoudig gedistribueerde TensorFlow-taken uitvoeren en Azure Machine Learning beheert de orchestration voor u.

Azure Machine Learning ondersteunt twee methoden voor gedistribueerde training in TensorFlow:

- [MPI-gebaseerde](https://www.open-mpi.org/) gedistribueerde training met behulp van het [Horovod-kader](https://github.com/uber/horovod)
- Native [distributed TensorFlow](https://www.tensorflow.org/deploy/distributed) met behulp van de parameterservermethode

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) is een open-source framework voor gedistribueerde opleidingen ontwikkeld door Uber. Het biedt een eenvoudig pad naar gedistribueerde GPU TensorFlow-taken.

Als u Horovod [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) wilt gebruiken, geeft u een object op voor de `distributed_training` parameter in de TensorFlow-constructor. Deze parameter zorgt ervoor dat de Horovod-bibliotheek is geïnstalleerd voor gebruik in uw trainingsscript.

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
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Parameterserver

U ook [native distributed TensorFlow](https://www.tensorflow.org/deploy/distributed)uitvoeren, waarbij het parameterservermodel wordt gebruikt. Met deze methode traint u in een cluster van parameterservers en -werknemers. De werknemers berekenen de verlopen tijdens de training, terwijl de parameterservers de verlopen samenvoegen.

Als u de parameterservermethode [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) wilt `distributed_training` gebruiken, geeft u een object op voor de parameter in de tensorflowconstructor.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Clusterspecificaties definiëren in 'TF_CONFIG'

U hebt ook de netwerkadressen en poorten [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)van het cluster `TF_CONFIG` nodig voor de , zodat Azure Machine Learning de omgevingsvariabele voor u instelt.

De `TF_CONFIG` omgevingsvariabele is een JSON-tekenreeks. Hier is een voorbeeld van de variabele voor een parameterserver:

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

Voor de API op [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) hoog niveau van TensorFlow `TF_CONFIG` pareert TensorFlow de variabele en bouwt de clusterspecificaties voor u.

Voor tensorFlow's lagere niveau core API's voor `TF_CONFIG` training, `tf.train.ClusterSpec` ontlijn de variabele en bouw de in uw trainingscode.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Een TensorFlow-model implementeren

Het model dat u zojuist hebt geregistreerd, kan op exact dezelfde manier worden geïmplementeerd als elk ander geregistreerd model in Azure Machine Learning, ongeacht welke schatter u hebt gebruikt voor training. De implementatiehow-to bevat een sectie over het registreren van modellen, maar u rechtstreeks overgaan naar [het maken van een compute target](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

## <a name="preview-no-code-model-deployment"></a>(Preview) Implementatie van no-code-model

In plaats van de traditionele implementatieroute u ook de functie voor implementatie zonder code (voorbeeld) gebruiken voor Tensorflow. Door uw model te registreren `model_framework` `model_framework_version`zoals `resource_configuration` hierboven weergegeven met de `deploy()` ,, en parameters, u gewoon gebruik maken van de statische functie om uw model te implementeren.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

De volledige [how-to](how-to-deploy-and-where.md) dekt implementatie in Azure Machine Learning in grotere diepte.

## <a name="next-steps"></a>Volgende stappen

In dit artikel trainde en registreerde u een TensorFlow-model en leerde u over opties voor implementatie. Bekijk deze andere artikelen voor meer informatie over Azure Machine Learning.

* [Hardloopstatistieken bijhouden tijdens de training](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
