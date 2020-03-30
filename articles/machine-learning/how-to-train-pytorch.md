---
title: Train deep learning PyTorch modellen
titleSuffix: Azure Machine Learning
description: Lees hoe u uw PyTorch-trainingsscripts op bedrijfsschaal uitvoeren met de PyTorch-schatterklasse van Azure Machine Learning.  De voorbeeldscripts classificeren kip- en kalkoenafbeeldingen om een deep learning neuraal netwerk op te bouwen op basis van pytorch's transfer learning tutorial.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 136ee197271fc659497c169e27a6399c3940c19e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834864"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Train Pytorch deep learning-modellen op schaal met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw [PyTorch-trainingsscripts](https://pytorch.org/) op bedrijfsschaal uitvoeren met de [PyTorch-schatterklasse van](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) Azure Machine Learning.  

De voorbeeldscripts in dit artikel worden gebruikt om kip- en kalkoenafbeeldingen te classificeren [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)om een deep learning neuraal netwerk op te bouwen op basis van de overdracht-leertutorial van PyTorch. 

Of u nu een deep learning PyTorch-model vanaf de basis traint of een bestaand model in de cloud brengt, u Azure Machine Learning gebruiken om open-source trainingstaken uit te schalen met behulp van elastische cloudcomputeresources. U productiemodellen bouwen, implementeren, implementeren en bewaken met Azure Machine Learning. 

Meer informatie over [deep learning versus machine learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Vereisten

Voer deze code uit op een van deze omgevingen:

- Azure Machine Learning rekeninstantie - geen downloads of installatie nodig

    - De [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md) voltooien om een speciale notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map deep learning op de notebookserver een voltooid en uitgebreid notitieblok door naar deze map te navigeren: **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-pytorch** map. 
 
 - Uw eigen Jupyter Notebook-server

    - [Installeer de Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Een configuratiebestand voor werkruimtes maken](how-to-configure-environment.md#workspace).
    - [De voorbeeldscriptbestanden downloaden](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    U ook een voltooide [Jupyter Notebook-versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) van deze handleiding vinden op de pagina GitHub-voorbeelden. De notebook bevat uitgebreide secties over intelligente hyperparametertuning, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainingsexperiment ingesteld door de vereiste python-pakketten te laden, een werkruimte te initialiseren, een experiment te maken en de trainingsgegevens en trainingsscripts te uploaden.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de benodigde Python-bibliotheken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning-werkruimte](concept-workspace.md) is de bron op het hoogste niveau voor de service. Het biedt u een centrale plek om te werken met alle artefacten die u maakt. In de Python SDK hebt u toegang tot [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) de artefacten van de werkruimte door een object te maken.

Maak een werkruimteobject `config.json` uit het bestand dat is gemaakt in de [sectie Vereistevoorwaarden](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Een deep learning-experiment maken

Maak een experiment en een map om uw trainingsscripts vast te houden. Maak in dit voorbeeld een experiment genaamd "pytorch-birds".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>De gegevens ophalen

De dataset bestaat uit ongeveer 120 trainingsafbeeldingen voor kalkoenen en kippen, met 100 validatieafbeeldingen voor elke klasse. We zullen de dataset downloaden en extraheren `pytorch_train.py`als onderdeel van ons trainingsscript. De afbeeldingen zijn een subset van de [gegevensset Open Afbeeldingen v5](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Trainingsscripts voorbereiden

In deze tutorial, de `pytorch_train.py`opleiding script, , is al voorzien. In de praktijk u elk aangepast trainingsscript nemen, zoals het is, en het uitvoeren met Azure Machine Learning.

Upload het Pytorch `pytorch_train.py`trainingsscript.

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Als u echter azure machine learning-tracking- en metrische mogelijkheden wilt gebruiken, moet u een kleine hoeveelheid code toevoegen aan uw trainingsscript. Voorbeelden van het bijhouden `pytorch_train.py`van statistieken zijn te vinden in .

## <a name="create-a-compute-target"></a>Een rekendoel maken

Maak een rekendoel voor uw PyTorch-taak om op te draaien. Maak in dit voorbeeld een Azure Machine Learning-computecluster met GPU.To this example, create a GPU-enabled Azure Machine Learning compute cluster.

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

## <a name="create-a-pytorch-estimator"></a>Maak een PyTorch schatter

De [PyTorch schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) biedt een eenvoudige manier om een PyTorch-trainingstaak te starten op een compute target.

De PyTorch schatter wordt geïmplementeerd [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) via de generieke klasse, die kan worden gebruikt om elk kader te ondersteunen. Zie [treinmodellen met Azure Machine Learning met behulp van schatter](how-to-train-ml-models.md) voor meer informatie over trainingsmodellen met behulp van de generieke schatter

Als uw trainingsscript extra pip- of conda-pakketten nodig heeft om uit te voeren, kunt `pip_packages` `conda_packages` u de pakketten op de resulterende dockerafbeelding laten installeren door hun namen door te geven via de en argumenten.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over het aanpassen van uw Python-omgeving.

## <a name="submit-a-run"></a>Een run verzenden

Het [object Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface naar de rungeschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Als de run wordt uitgevoerd, gaat deze door de volgende fasen:

- **Voorbereiden**: Een docker beeld wordt gemaakt volgens de PyTorch schatter. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere uitvoeringen. Logboeken worden ook gestreamd naar de rungeschiedenis en kunnen worden bekeken om de voortgang te controleren.

- **Schalen:** het cluster probeert op te schalen als het AI-cluster batch meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar is.

- **Actief:** Alle scripts in de scriptmap worden geüpload naar het compute-doel, gegevensarchieven worden gemonteerd of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map ./logs worden gestreamd naar de rungeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Nabewerking:** de map ./uitvoer van de run wordt gekopieerd naar de rungeschiedenis.

## <a name="register-or-download-a-model"></a>Een model registreren of downloaden

Zodra u het model hebt getraind, u het registreren op uw werkruimte. Met modelregistratie u uw modellen opslaan en in uw werkruimte gebruiken om [het modelbeheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Het model dat u zojuist hebt geregistreerd, wordt op exact dezelfde manier geïmplementeerd als elk ander geregistreerd model in Azure Machine Learning, ongeacht welke chatter u hebt gebruikt voor training. De implementatiehow-to bevat een sectie over het registreren van modellen, maar u rechtstreeks overgaan naar [het maken van een compute target](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

U ook een lokale kopie van het model downloaden met het object Uitvoeren. In het `pytorch_train.py`trainingsscript blijft een PyTorch-saveobject het model aanhouden in een lokale map (lokaal naar het rekendoel). U het object Uitvoeren gebruiken om een kopie te downloaden.

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

De [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) schatter ondersteunt ook gedistribueerde training in CPU- en GPU-clusters. U eenvoudig gedistribueerde PyTorch-taken uitvoeren en Azure Machine Learning beheert de orkestratie voor u.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) is een open-source, alle verminderen kader voor gedistribueerde opleiding ontwikkeld door Uber. Het biedt een eenvoudig pad naar gedistribueerde GPU PyTorch-taken.

Als u Horovod [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) wilt gebruiken, geeft u een object op voor de `distributed_training` parameter in de PyTorch-constructor. Deze parameter zorgt ervoor dat de Horovod-bibliotheek is geïnstalleerd voor gebruik in uw trainingsscript.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```
Horovod en zijn afhankelijkheden worden voor u geïnstalleerd, zodat `train.py` u het als volgt in uw trainingsscript importeren:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exporteren naar ONNX

Om de conclusie te optimaliseren met de [ONNX Runtime,](concept-onnx.md)zet u uw getrainde PyTorch-model om naar het ONNX-formaat. Gevolgtrekking, of modelscore, is de fase waarin het geïmplementeerde model wordt gebruikt voor voorspelling, meestal op productiegegevens. Zie de [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) voor een voorbeeld.

## <a name="next-steps"></a>Volgende stappen

In dit artikel trainde en registreerde u een deep learning, neuraal netwerk met PyTorch op Azure Machine Learning. Ga verder naar ons modelimplementatieartikel om te leren hoe u een model implementeert.

> [!div class="nextstepaction"]
> [Hoe en waar modellen kunnen worden geïmplementeerd](how-to-deploy-and-where.md)
* [Hardloopstatistieken bijhouden tijdens de training](how-to-track-experiments.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

