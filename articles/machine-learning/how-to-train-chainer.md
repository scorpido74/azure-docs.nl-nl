---
title: Train deep learning Chainer modellen
titleSuffix: Azure Machine Learning
description: Lees hoe u uw PyTorch-trainingsscripts op bedrijfsschaal uitvoeren met behulp van de chatterklasse Azure Machine Learning Chainer.  Het voorbeeldscript classificert handgeschreven cijferafbeeldingen om een deep learning neuraal netwerk op te bouwen met behulp van de Chainer Python-bibliotheek die bovenop numpy wordt uitgevoerd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75536614"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Chainer-modellen op schaal trainen en registreren met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw Chainer-trainingsscripts op bedrijfsschaal uitvoeren met behulp van de klasse Azure Machine Learning Chainer.To be how to run your [Chainer](https://chainer.org/) training scripts at enterprise scale using the Azure Machine Learning [Chainer estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) class. Het voorbeeldtrainingsscript in dit artikel gebruikt de populaire [MNIST-gegevensset](http://yann.lecun.com/exdb/mnist/) om handgeschreven cijfers te classificeren met behulp van een diep neuraal netwerk (DNN) dat is gebouwd met behulp van de Chainer Python-bibliotheek die bovenop numpy wordt [uitgevoerd.](https://www.numpy.org/)

Of u nu een deep learning Chainer-model vanaf de basis traint of een bestaand model in de cloud brengt, u Azure Machine Learning gebruiken om open-source trainingstaken uit te schalen met behulp van elastische cloudcomputeresources. U productiemodellen bouwen, implementeren, implementeren en bewaken met Azure Machine Learning. 

Meer informatie over [deep learning versus machine learning](concept-deep-learning-vs-machine-learning.md).

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Vereisten

Voer deze code uit op een van deze omgevingen:

- Azure Machine Learning rekeninstantie - geen downloads of installatie nodig

    - De [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md) voltooien om een speciale notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.
    - Zoek in de map deep learning op de notebookserver een voltooid notitieblok en bestanden in de **how-to-use-azureml > ml-frameworks > chainer > implementatie > map trein-hyperparameter-tune-deploy-with-chainer.**  De notebook bevat uitgebreide secties over intelligente hyperparametertuning, modelimplementatie en notebookwidgets.

- Uw eigen Jupyter Notebook-server

    - [Installeer de Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Een configuratiebestand voor werkruimtes maken](how-to-configure-environment.md#workspace).
    - Download het voorbeeldscriptbestand [chainer_mnist.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - U ook een voltooide [Jupyter Notebook-versie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) van deze handleiding vinden op de pagina Met GitHub-voorbeelden. De notebook bevat uitgebreide secties over intelligente hyperparametertuning, modelimplementatie en notebookwidgets.

## <a name="set-up-the-experiment"></a>Het experiment instellen

In deze sectie wordt het trainingsexperiment ingesteld door de vereiste python-pakketten te laden, een werkruimte te initialiseren, een experiment te maken en de trainingsgegevens en trainingsscripts te uploaden.

### <a name="import-packages"></a>Pakketten importeren

Importeer eerst de Azureml.core Python-bibliotheek en geef het versienummer weer.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Een werkruimte initialiseren

De [Azure Machine Learning-werkruimte](concept-workspace.md) is de bron op het hoogste niveau voor de service. Het biedt u een centrale plek om te werken met alle artefacten die u maakt. In de Python SDK hebt u toegang tot [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) de artefacten van de werkruimte door een object te maken.

Maak een werkruimteobject `config.json` door het bestand te lezen dat is gemaakt in de [sectie Vereisten:](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Een projectmap maken
Maak een map met alle benodigde code van uw lokale machine waartoe u toegang nodig hebt op de externe bron. Dit omvat het trainingsscript en eventuele extra bestanden waarvan uw trainingsscript afhankelijk is.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Trainingsscript voorbereiden

In deze zelfstudie is het trainingsscript **chainer_mnist.py** al voor u beschikbaar. In de praktijk moet u elk aangepast trainingsscript kunnen nemen zoals het is en het kunnen uitvoeren met Azure ML zonder dat u uw code hoeft te wijzigen.

Als u de azure ML-tracking- en metrische mogelijkheden wilt gebruiken, voegt u een kleine hoeveelheid Azure ML-code toe in uw trainingsscript.  Het trainingsscript **chainer_mnist.py** laat zien hoe u bepaalde `Run` statistieken registreren bij uw Azure ML-run met behulp van het object in het script.

Het meegeleverde trainingsscript maakt gebruik `datasets.mnist.get_mnist` van voorbeeldgegevens van de chainerfunctie.  Voor uw eigen gegevens moet u mogelijk stappen zoals [Gegevensset uploaden en scripts](how-to-train-keras.md#data-upload) gebruiken om gegevens beschikbaar te maken tijdens de training.

Kopieer het trainingsscript **chainer_mnist.py** naar je projectmap.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Een deep learning-experiment maken

Een experiment maken. Maak in dit voorbeeld een experiment genaamd "chainer-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Een rekendoel maken of krijgen

Je hebt een [rekendoel](concept-compute-target.md) nodig om je model te trainen. In dit voorbeeld gebruikt u Azure ML managed compute (AmlCompute) voor uw compute resource voor externe training.

**Het maken van AmlCompute duurt ongeveer 5 minuten.** Als de AmlCompute met die naam zich al in uw werkruimte bevindt, wordt het creatieproces overgeslagen.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Zie het [artikel wat een compute target is](concept-compute-target.md) voor meer informatie over rekendoelen.

## <a name="create-a-chainer-estimator"></a>Een Chainer-schatter maken

De [Chainer-schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) biedt een eenvoudige manier om Chainer-trainingstaken op uw rekendoel te lanceren.

De Chainer-schatter wordt geïmplementeerd [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) via de generieke klasse, die kan worden gebruikt om elk framework te ondersteunen. Zie [treinmodellen met Azure Machine Learning met behulp van schatter](how-to-train-ml-models.md) voor meer informatie over trainingsmodellen met behulp van de generieke schatter

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Een run verzenden

Het [object Uitvoeren](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) biedt de interface naar de rungeschiedenis terwijl de taak wordt uitgevoerd en nadat deze is voltooid.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Als de run wordt uitgevoerd, gaat deze door de volgende fasen:

- **Voorbereiden**: Een docker beeld wordt gemaakt volgens de Chainer schatter. De afbeelding wordt geüpload naar het containerregister van de werkruimte en in de cache opgeslagen voor latere uitvoeringen. Logboeken worden ook gestreamd naar de rungeschiedenis en kunnen worden bekeken om de voortgang te controleren.

- **Schalen:** het cluster probeert op te schalen als het AI-cluster batch meer knooppunten nodig heeft om de uitvoering uit te voeren dan momenteel beschikbaar is.

- **Actief:** Alle scripts in de scriptmap worden geüpload naar het compute-doel, gegevensarchieven worden gemonteerd of gekopieerd en de entry_script wordt uitgevoerd. Uitvoer van stdout en de map ./logs worden gestreamd naar de rungeschiedenis en kunnen worden gebruikt om de run te controleren.

- **Nabewerking:** de map ./uitvoer van de run wordt gekopieerd naar de rungeschiedenis.

## <a name="save-and-register-the-model"></a>Het model opslaan en registreren

Zodra u het model hebt getraind, u het opslaan en registreren op uw werkruimte. Met modelregistratie u uw modellen opslaan en in uw werkruimte gebruiken om [het modelbeheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.


Nadat de modeltraining is voltooid, registreert u het model in uw werkruimte met de volgende code.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Het model dat u zojuist hebt geregistreerd, wordt op exact dezelfde manier geïmplementeerd als elk ander geregistreerd model in Azure Machine Learning, ongeacht welke chatter u hebt gebruikt voor training. De implementatiehow-to bevat een sectie over het registreren van modellen, maar u rechtstreeks overgaan naar [het maken van een compute target](how-to-deploy-and-where.md#choose-a-compute-target) voor implementatie, omdat u al een geregistreerd model hebt.

U ook een lokale kopie van het model downloaden. Dit kan handig zijn voor het lokaal uitvoeren van extra modelvalidatiewerk. In het trainingsscript `chainer_mnist.py`blijft een saver-object het model in een lokale map (lokaal naar het rekendoel). U het object Uitvoeren gebruiken om een kopie uit de datastore te downloaden.

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

In dit artikel trainde en registreerde u een deep learning, neuraal netwerk met Chainer op Azure Machine Learning. Ga verder naar ons [modelimplementatieartikel](how-to-deploy-and-where.md) om te leren hoe u een model implementeert.

* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)

* [Hardloopstatistieken bijhouden tijdens de training](how-to-track-experiments.md)

* [Bekijk onze referentiearchitectuur voor gedistribueerde deep learning-training in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
