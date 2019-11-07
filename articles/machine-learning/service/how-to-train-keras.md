---
title: Een diepe Learning Neural-netwerk trainen met Keras
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen en registreren van een Keras diepe Neural-netwerk classificatie model dat wordt uitgevoerd op tensor flow met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 42a824863766b5d4d91d9eaa3dad1012f5019f63
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584502"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Een Keras-classificatie model trainen en registreren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leest u hoe u een Keras-classificatie model kunt trainen en registreren dat is gebaseerd op tensor flow met behulp van Azure Machine Learning. Er wordt gebruikgemaakt van de populaire [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/) om handgeschreven cijfers te classificeren met behulp van een diepe Neural Network (DNN) die is gebouwd met behulp van de [Keras python-bibliotheek](https://keras.io) op [tensor flow](https://www.tensorflow.org/overview).

Keras is een Neural-netwerk-API op hoog niveau die de ontwikkeling van andere populaire DNN-Frameworks kan vereenvoudigen. Met Azure Machine Learning kunt u trainings taken snel uitschalen met behulp van elastische Cloud Compute-resources. U kunt ook uw trainings uitvoeringen, versie modellen, implementatie modellen en nog veel meer volgen.

Of u nu een Keras-model ontwikkelt of een bestaand model in de Cloud brengt, Azure Machine Learning u kunt helpen bij het bouwen van productie klare modellen.

Zie het [conceptuele artikel](concept-deep-learning-vs-machine-learning.md) voor informatie over de verschillen tussen machine learning en diep gaande lessen.

## <a name="prerequisites"></a>Vereisten

Voer deze code uit in een van de volgende omgevingen:

 - Azure Machine Learning-notebook-VM-geen down loads of installatie vereist

     - Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.
    - Zoek in de map met voor beelden op de notebook server een volledig en uitgebreid notitie blok door naar deze map te navigeren: **instructies-to-use-azureml > training-met-learning > Train-afstemming-Tune-Deploy-with-Keras** -map.

 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
    - [Down load de voorbeeld script bestanden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` en `utils.py`

    U kunt ook een voltooide [Jupyter notebook versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) van deze hand leiding vinden op de pagina met voor beelden van github. Het notitie blok bevat uitgebreide secties die betrekking hebben op intelligent afstemming tuning, model implementatie en notebook widgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainings experiment opgesteld door het laden van de vereiste Python-pakketten, het initialiseren van een werk ruimte, het maken van een experiment en het uploaden van de trainings gegevens en trainings scripts.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde python-bibliotheken.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Een werk ruimte initialiseren

De [Azure machine learning werk ruimte](concept-workspace.md) is de resource op het hoogste niveau voor de service. Het biedt u een centrale locatie voor het werken met alle artefacten die u maakt. In de python-SDK hebt u toegang tot de werkruimte artefacten door een [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) -object te maken.

Maak een werkruimte object vanuit het `config.json` bestand dat in de [sectie vereisten](#prerequisites)is gemaakt.

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment met de naam ' Keras-mnist ' in uw werk ruimte.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Een bestands gegevensset maken

Een `FileDataset`-object verwijst naar een of meer bestanden in uw werk ruimte of open bare url's. De bestanden hebben een wille keurige indeling en de-klasse biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw computer. Door een `FileDataset`te maken, maakt u een verwijzing naar de locatie van de gegevens bron. Als u trans formaties hebt toegepast op de gegevensset, worden deze ook opgeslagen in de gegevens verzameling. De gegevens blijven op de bestaande locatie, waardoor er geen extra opslag kosten in rekening worden gebracht. Raadpleeg de hand leiding voor het `Dataset` [-](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) pakket voor meer informatie.

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

Gebruik de `register()` methode om de gegevensset te registreren bij uw werk ruimte, zodat deze kan worden gedeeld met anderen, opnieuw worden gebruikt voor verschillende experimenten en waarnaar wordt verwezen met de naam in uw trainings script.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Een reken doel maken

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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Een tensor flow-Estimator maken en Keras importeren

De [tensor flow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) biedt een eenvoudige manier om tensor flow-trainings taken op reken doel te starten. Aangezien Keras boven op tensor flow wordt uitgevoerd, kunt u de tensor flow Estimator gebruiken en de Keras-bibliotheek importeren met behulp van het argument `pip_packages`.

Haal eerst de gegevens op uit de werk ruimte Data Store met behulp van de klasse `Dataset`.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

De tensor flow Estimator wordt geïmplementeerd via de algemene [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasse, die kan worden gebruikt ter ondersteuning van een Framework. Maak bovendien een woorden lijst `script_params` die de afstemming-instellingen voor DNN bevat. Voor meer informatie over trainings modellen die gebruikmaken van de algemene Estimator, raadpleegt [u modellen met Azure machine learning met behulp van Estimator](how-to-train-ml-models.md)

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

Het [object run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface voor de uitvoerings geschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Wanneer de uitvoering wordt uitgevoerd, worden de volgende fasen door lopen:

- **Voorbereiden**: een docker-installatie kopie wordt gemaakt op basis van de tensor flow-Estimator. De afbeelding wordt geüpload naar het container register van de werk ruimte en opgeslagen in de cache voor latere uitvoeringen. Logboeken worden ook gestreamd naar de uitvoerings geschiedenis en kunnen worden weer gegeven om de voortgang te bewaken.

- **Schalen**: het cluster probeert omhoog te schalen als het batch AI-cluster meer knoop punten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar zijn.

- **Uitvoeren**: alle scripts in de map script worden geüpload naar het Compute-doel, gegevens archieven worden gekoppeld of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map./logs worden gestreamd naar de uitvoerings geschiedenis en kunnen worden gebruikt om de uitvoering te bewaken.

- **Na de verwerking**: de map./outputs van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis.

## <a name="register-the-model"></a>Het model registreren

Nadat u het DNN-model hebt getraind, kunt u dit registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

U kunt ook een lokale kopie van het model downloaden. Dit kan handig zijn om een extra model validatie lokaal te kunnen uitvoeren. In het trainings script `mnist-keras.py`een tensor flow-beveiligings object het model persistent maken naar een lokale map (lokaal naar het Compute-doel). U kunt het object run gebruiken om een kopie te downloaden uit de gegevens opslag.

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

In dit artikel hebt u een Keras-model getraind en geregistreerd op Azure Machine Learning. Ga verder met ons model implementatie artikel voor meer informatie over het implementeren van een model.

> [!div class="nextstepaction"]
> [Hoe en waar modellen moeten worden geïmplementeerd](how-to-deploy-and-where.md)
* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [Afstemmen van hyperparameters](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentie architectuur voor gedistribueerde training van diep gaande lessen in azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
