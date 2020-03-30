---
title: Train deep learning Keras modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en registreren van een Keras deep neural network classification model dat draait op TensorFlow met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269963"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Een Keras-classificatiemodel trainen en registreren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel ziet u hoe u een Keras-classificatiemodel traint en registreert dat is gebaseerd op TensorFlow met Azure Machine Learning. Het maakt gebruik van de populaire [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/) om handgeschreven cijfers te classificeren met behulp van een diep neuraal netwerk (DNN) dat is gebouwd met behulp van de [Keras Python-bibliotheek](https://keras.io) die bovenop [TensorFlow](https://www.tensorflow.org/overview)wordt uitgevoerd.

Keras is een high-level neurale netwerk API in staat om de top van andere populaire DNN-frameworks uit te voeren om de ontwikkeling te vereenvoudigen. Met Azure Machine Learning u trainingstaken snel uitschalen met elastische cloudcomputeresources. U ook uw trainingsruns, versiemodellen, implementatiemodellen en nog veel meer bijhouden.

Of u nu een Keras-model vanaf de basis ontwikkelt of een bestaand model in de cloud brengt, Azure Machine Learning kan u helpen bij het bouwen van productieklare modellen.

Zie het [conceptuele artikel](concept-deep-learning-vs-machine-learning.md) voor informatie over de verschillen tussen machine learning en deep learning.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit op een van deze omgevingen:

- Azure Machine Learning rekeninstantie - geen downloads of installatie nodig

     - De [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md) voltooien om een speciale notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de voorbeeldmap op de notebookserver een voltooid en uitgebreid notitieblok door naar deze map te navigeren: **how-to-use-azureml > training-with-deep-learning > trein-hyperparameter-tune-deploy-with-keras** map.

 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Een configuratiebestand voor werkruimtes maken](how-to-configure-environment.md#workspace).
    - [Download de voorbeeldscriptbestanden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` en`utils.py`

    U ook een voltooide [Jupyter Notebook-versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) van deze handleiding vinden op de pagina GitHub-voorbeelden. De notebook bevat uitgebreide secties over intelligente hyperparametertuning, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainingsexperiment ingesteld door de vereiste python-pakketten te laden, een werkruimte te initialiseren, een experiment te maken en de trainingsgegevens en trainingsscripts te uploaden.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning-werkruimte](concept-workspace.md) is de bron op het hoogste niveau voor de service. Het biedt u een centrale plek om te werken met alle artefacten die u maakt. In de Python SDK hebt u toegang tot [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) de artefacten van de werkruimte door een object te maken.

Maak een werkruimteobject `config.json` uit het bestand dat is gemaakt in de [sectie Vereistevoorwaarden](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment genaamd "keras-mnist" in uw werkruimte.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Maak een TensorFlow-schatter en importeer Keras

De [TensorFlow-schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) biedt een eenvoudige manier om TensorFlow-trainingstaken op compute target te lanceren. Aangezien Keras bovenop TensorFlow draait, u de TensorFlow-schatter gebruiken `pip_packages` en de Keras-bibliotheek importeren met behulp van het argument.

Haal eerst de gegevens uit het `Dataset` werkruimtegegevensarchief met behulp van de klasse.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

De TensorFlow-schatter wordt geïmplementeerd [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) via de generieke klasse, die kan worden gebruikt om elk framework te ondersteunen. Maak bovendien een `script_params` woordenboek met de hyperparameterinstellingen van DNN. Zie [treinmodellen met Azure Machine Learning met behulp van schatter](how-to-train-ml-models.md) voor meer informatie over trainingsmodellen met behulp van de generieke schatter

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Een run verzenden

Het [object Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface naar de rungeschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Als de run wordt uitgevoerd, gaat deze door de volgende fasen:

- **Voorbereiden**: Er wordt een dockerafbeelding gemaakt volgens de TensorFlow-schatter. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere uitvoeringen. Logboeken worden ook gestreamd naar de rungeschiedenis en kunnen worden bekeken om de voortgang te controleren.

- **Schalen:** het cluster probeert op te schalen als het AI-cluster batch meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar is.

- **Actief:** Alle scripts in de scriptmap worden geüpload naar het compute-doel, gegevensarchieven worden gemonteerd of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map ./logs worden gestreamd naar de rungeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Nabewerking:** de map ./uitvoer van de run wordt gekopieerd naar de rungeschiedenis.

## <a name="register-the-model"></a>Het model registreren

Zodra u het DNN-model hebt getraind, u het registreren op uw werkruimte. Met modelregistratie u uw modellen opslaan en in uw werkruimte gebruiken om [het modelbeheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Het model dat u zojuist hebt geregistreerd, wordt op exact dezelfde manier geïmplementeerd als elk ander geregistreerd model in Azure Machine Learning, ongeacht welke chatter u hebt gebruikt voor training. De implementatiehow-to bevat een sectie over het registreren van modellen, maar u rechtstreeks overgaan naar [het maken van een compute target](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

U ook een lokale kopie van het model downloaden. Dit kan handig zijn voor het lokaal uitvoeren van extra modelvalidatiewerk. In het trainingsscript `mnist-keras.py`blijft een TensorFlow-saver-object het model aanhouden in een lokale map (lokaal naar het rekendoel). U het object Uitvoeren gebruiken om een kopie uit de datastore te downloaden.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Keras-model getraind en geregistreerd op Azure Machine Learning. Ga verder naar ons modelimplementatieartikel om te leren hoe u een model implementeert.

> [!div class="nextstepaction"]
> [Hoe en waar modellen kunnen worden geïmplementeerd](how-to-deploy-and-where.md)
* [Hardloopstatistieken bijhouden tijdens de training](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
