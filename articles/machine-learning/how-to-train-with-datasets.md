---
title: Trainen met azureml-datasets
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van gegevenssets in training
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283496"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Trainen met gegevenssets in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u de twee manieren om [Azure Machine Learning-gegevenssets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) te gebruiken in een externe experimenttraining zonder u zorgen te maken over verbindingstekenreeksen of gegevenspaden.

- Optie 1: Als u gestructureerde gegevens hebt, maakt u een TabularDataset en gebruikt u deze rechtstreeks in uw trainingsscript.

- Optie 2: Als u ongestructureerde gegevens hebt, maakt u een FileDataset en monteert of downloadt u bestanden naar een externe computervoor training.

Azure Machine Learning-gegevenssets bieden een naadloze integratie met Azure Machine Learning-trainingsproducten zoals [ScriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [Estimator,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) en [Azure Machine Learning-pijplijnen.](how-to-create-your-first-pipeline.md)

## <a name="prerequisites"></a>Vereisten

Als u gegevenssets wilt maken en trainen, hebt u het:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md).

* De [Azure Machine Learning SDK voor Python geïnstalleerd,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)die de azureml-datasets pakket omvat.

> [!Note]
> Sommige gegevenssetklassen hebben afhankelijkheden van het [azureml-dataprep-pakket.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Voor Linux-gebruikers worden deze klassen alleen ondersteund op de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Optie 1: Gegevenssets rechtstreeks gebruiken in trainingsscripts

In dit voorbeeld maakt u een [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) en gebruikt `estimator` u deze als directe invoer voor uw object voor training. 

### <a name="create-a-tabulardataset"></a>Een tabeltabelsetmaken

Met de volgende code wordt een niet-geregistreerde TabularDataset van een web-url. U ook gegevenssets maken van lokale bestanden of paden in datastores. Meer informatie over [het maken van gegevenssets](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Toegang tot de invoergegevensset in uw trainingsscript

Tabelmapdataset-objecten bieden de mogelijkheid om de gegevens in een panda's of spark DataFrame te laden, zodat u werken met bekende gegevensvoorbereidings- en trainingsbibliotheken. Als u deze mogelijkheid wilt benutten, u een TabularDataset als invoer in uw trainingsconfiguratie doorgeven en deze ophalen in uw script.

Om dit te doen, toegang [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) tot de invoerset [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) via het object in uw trainingsscript en gebruik de methode. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>De schatter configureren

Een [schatterobject](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) wordt gebruikt om de experimentrun in te dienen. Azure Machine Learning heeft vooraf geconfigureerde schatters voor veelgebruikte machine learning-frameworks en een generieke schatter.

Met deze code wordt een `est`algemeen schatterobject gemaakt, dat

* Een scriptmap voor uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het trainingsscript, *train_titanic.py*.
* De invoergegevensset `titanic`voor training, . `as_named_input()`is vereist, zodat de invoergegevensset kan worden verwezen met de toegewezen naam in uw trainingsscript. 
* Het rekendoel voor het experiment.
* De omgevingsdefinitie voor het experiment.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Optie 2: Bestanden monteren naar een extern rekendoel

Als u uw gegevensbestanden beschikbaar wilt maken op het rekendoel voor training, gebruikt u [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) om bestanden te monteren of te downloaden die door het apparaat worden verwezen.

### <a name="mount-vs-download"></a>Mount vs. Downloaden

Het monteren of downloaden van bestanden van elke indeling wordt ondersteund voor gegevenssets die zijn gemaakt met Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database voor PostgreSQL. 

Wanneer u een gegevensset monteert, koppelt u de bestanden waarnaar de gegevensset verwijst toe aan een map (bevestigingspunt) en maakt u deze beschikbaar op het rekendoel. Montage wordt ondersteund voor op Linux gebaseerde computes, waaronder Azure Machine Learning Compute, virtuele machines en HDInsight. Wanneer u een gegevensset downloadt, worden alle bestanden waarnaar de gegevensset verwijst, gedownload naar het rekendoel. Downloaden wordt ondersteund voor alle compute types. 

Als uw script alle bestanden verwerkt waarnaar wordt verwezen door de gegevensset en uw compute disk in uw volledige gegevensset past, wordt downloaden aanbevolen om de overhead van streaminggegevens van opslagservices te voorkomen. Als de grootte van uw gegevens groter is dan de grootte van de rekenschijf, is downloaden niet mogelijk. Voor dit scenario raden we aan om te worden gemonteerd, omdat alleen de gegevensbestanden die door uw script worden gebruikt, worden geladen op het moment van verwerking.

De volgende code `dataset` wordt gemonteerd op de tijdelijke map op`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>Een Bestandssetgegevensmaken

In het volgende voorbeeld wordt een niet-geregistreerde FileDataset van weburls gemaakt. Meer informatie over het maken van [gegevenssets](https://aka.ms/azureml/howto/createdatasets) uit andere bronnen.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>De schatter configureren

Naast het doorgeven `inputs` van de dataset via de parameter in `script_params` de schatter, u de gegevensset ook doorgeven en het gegevenspad (montagepunt) in uw trainingsscript via argumenten krijgen. Op deze manier u uw trainingsscript onafhankelijk houden van azureml-sdk. Met andere woorden, u hetzelfde trainingsscript gebruiken voor lokale debuggen en externe training op elk cloudplatform.

Een [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) SKLearn-schatterobject wordt gebruikt om de run in te dienen voor scikit-learn-experimenten. Meer informatie over trainen met de [SKlearn-schatter](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>De gegevens ophalen in uw trainingsscript

Nadat u de run hebt verzonden, worden gegevensbestanden die door de `mnist` gegevensset worden verwezen, gemonteerd op het rekendoel. In de volgende code ziet u hoe u de gegevens in uw script ophaalt.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Voorbeelden van notitieblokken

De [gegevenssetnotitieblokken](https://aka.ms/dataset-tutorial) tonen en breiden concepten uit in dit artikel.

## <a name="next-steps"></a>Volgende stappen

* [Train machine learning-modellen](how-to-auto-train-remote.md) automatisch met TabularDatasets

* [Classificatiemodellen voor schermafbeeldingen](https://aka.ms/filedataset-samplenotebook) met Bestandsdatasets

* [Trainen met gegevenssets met behulp van pijplijnen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
