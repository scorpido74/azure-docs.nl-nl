---
title: Trainen met azureml-gegevens sets
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van gegevens sets in training
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7a3f839a676723942af2e669839457ed3246aabd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885896"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Train met gegevens sets in Azure Machine Learning


In dit artikel leert u hoe u kunt werken met [Azure machine learning gegevens sets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) in uw trainings experimenten.  U kunt gegevens sets gebruiken in uw lokale of externe Compute-doel zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

Azure Machine Learning gegevens sets bieden een naadloze integratie met Azure Machine Learning trainings producten zoals [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) en [Azure machine learning pijp lijnen](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en trainen, hebt u het volgende nodig:

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), waaronder het pakket met de azureml-gegevens sets.

> [!Note]
> Voor sommige verzamelings klassen zijn afhankelijkheden van het pakket voor [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) . Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="access-and-explore-input-datasets"></a>Invoer gegevens sets openen en verkennen

U hebt toegang tot een bestaande TabularDataset vanuit het trainings script van een experiment in uw werk ruimte en laadt die gegevensset in een Panda data frame voor verdere exploratie van uw lokale omgeving.

De volgende code gebruikt de [`get_context()`]() methode in de- [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) klasse om toegang te krijgen tot de bestaande invoer TabularDataset, `titanic` , in het trainings script. Gebruikt vervolgens de [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) methode om die gegevensset te laden in een Panda data frame voor verdere gegevens exploratie en voor bereiding voor de training.

> [!Note]
> Als de oorspronkelijke gegevens bron NaN, lege teken reeksen of lege waarden bevat, worden deze waarden vervangen als een *Null* -waarde als u de to_pandas_dataframe () gebruikt. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Als u de voor bereide gegevens wilt laden in een nieuwe gegevensset van een in geheugen Pandas data frame, schrijft u de gegevens naar een lokaal bestand, zoals een Parquet, en maakt u een nieuwe gegevensset van dat bestand. U kunt ook gegevens sets maken op basis van lokale bestanden of paden in gegevens opslag. Meer informatie over [het maken van gegevens sets](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Gegevens sets rechtstreeks in trainings scripts gebruiken

Als u gestructureerde gegevens nog niet hebt geregistreerd als een gegevensset, maakt u een TabularDataset en gebruikt u deze rechtstreeks in uw trainings script voor uw lokale of externe experiment.

In dit voor beeld maakt u een niet-geregistreerde [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) en gebruikt u deze als directe invoer voor uw `estimator` object voor training. Zie [gegevens sets registreren in uw werk ruimte](how-to-create-register-datasets.md#register-datasets)als u deze TabularDataset met andere experimenten in uw werk ruimte wilt gebruiken.

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Met de volgende code wordt een niet-geregistreerde TabularDataset gemaakt van een web-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-objecten bieden de mogelijkheid om de gegevens in uw TabularDataset te laden in een Panda of Spark data frame, zodat u kunt werken met de vertrouwde gegevens voorbereiding en-trainings bibliotheken zonder dat u uw notebook hoeft te verlaten. Zie [invoer gegevens sets openen en verkennen](#access-and-explore-input-datasets)voor meer informatie over het gebruik van deze mogelijkheid.

### <a name="configure-the-estimator"></a>De Estimator configureren

Een [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) -object wordt gebruikt om de uitvoering van het experiment in te dienen. Azure Machine Learning heeft vooraf geconfigureerde schattingen voor algemene machine learning frameworks, evenals een algemene Estimator.

Deze code maakt een Gene riek Estimator-object, `est` dat aangeeft

* Een script Directory voor uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het trainings script, *train_titanic. py*.
* De invoer gegevensset voor training, `titanic_ds` . `as_named_input()` is vereist zodat naar de gegevensset van de invoer kan worden verwezen door de toegewezen naam `titanic` in uw trainings script. 
* Het reken doel voor het experiment.
* De omgevings definitie voor het experiment.

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

## <a name="mount-files-to-remote-compute-targets"></a>Bestanden koppelen aan externe Compute-doelen

Als u ongestructureerde gegevens hebt, maakt u een [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) en koppelt of downloadt u uw gegevens bestanden om ze beschikbaar te maken voor uw externe Compute-doel voor training. Meer informatie over het gebruik van [Mount en down load](#mount-vs-download) voor uw externe trainings experimenten. 

In het volgende voor beeld wordt een FileDataset gemaakt en wordt de gegevensset aan het reken doel gekoppeld door deze als een argument door te geven in de Estimator voor training. 

> [!Note]
> Als u een aangepaste docker-basis installatie kopie gebruikt, moet u de installatie van zekering instellen op `apt-get install -y fuse` een afhankelijkheid van het koppelen van de gegevensset. Meer informatie over het [maken van een aangepaste build-installatie kopie](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Een FileDataset maken

In het volgende voor beeld wordt een niet-geregistreerde FileDataset gemaakt op basis van web-url's. Meer informatie over [het maken van gegevens sets](how-to-create-register-datasets.md) van andere bronnen.

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

### <a name="configure-the-estimator"></a>De Estimator configureren

U kunt de gegevensset het beste als argument door geven bij het koppelen. Naast het door geven van de gegevensset via de `inputs` para meter in de Estimator, kunt u de gegevensset ook door geven via `script_params` en het gegevenspad (koppel punt) ophalen in uw trainings script via argumenten. Op deze manier kunt u hetzelfde trainings script gebruiken voor lokale fout opsporing en externe trainingen op elk cloud platform.

Een [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py&preserve-view=true) Estimator-object wordt gebruikt om de run for scikit-experimenten te verzenden. Nadat u de uitvoering hebt verzonden, worden de gegevens bestanden waarnaar de gegevensset verwijst, `mnist` aan het reken doel gekoppeld. Meer informatie over training met de [SKlearn Estimator](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>De gegevens in uw trainings script ophalen

De volgende code laat zien hoe u de gegevens in uw script kunt ophalen.

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

## <a name="mount-vs-download"></a>Koppeling versus downloaden

Het koppelen of downloaden van bestanden van een indeling wordt ondersteund voor gegevens sets die zijn gemaakt op basis van Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. 

Wanneer u een gegevensset **koppelt** , koppelt u de bestanden waarnaar wordt verwezen door de gegevensset naar een directory (koppel punt) en maakt u deze beschikbaar op het berekenings doel. Koppelen wordt ondersteund voor op Linux gebaseerde berekeningen, waaronder Azure Machine Learning compute, virtual machines en HDInsight. 

Wanneer u een gegevensset **downloadt** , worden alle bestanden waarnaar wordt verwezen door de gegevensset gedownload naar het Compute-doel. Downloaden wordt ondersteund voor alle reken typen. 

Als uw script alle bestanden verwerkt waarnaar wordt verwezen door de gegevensset, en uw berekenings schijf kan overeenkomen met uw volledige gegevensset, wordt het downloaden aanbevolen om de overhead van streaming-gegevens van opslag services te voor komen. Als uw gegevens grootte de grootte van de berekenings schijf overschrijdt, is downloaden niet mogelijk. Voor dit scenario wordt u aangeraden te koppelen, omdat alleen de gegevens bestanden die door het script worden gebruikt, worden geladen op het moment van verwerking.

De volgende code wordt gekoppeld `dataset` aan de tijdelijke directory op `mounted_path`

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

## <a name="access-datasets-in-your-script"></a>Toegang tot gegevens sets in uw script

Geregistreerde gegevens sets zijn zowel lokaal als extern toegankelijk op reken clusters, zoals de Azure Machine Learning compute. Gebruik de volgende code om toegang te krijgen tot uw geregistreerde gegevensset voor experimenten en de geregistreerde gegevensset op naam te openen. De [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) methode voor de `Dataset` klasse retourneert standaard de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Toegang tot de bron code tijdens de training

Azure Blob-opslag heeft hogere doorvoer snelheden dan een Azure-bestands share en schaalt naar een groot aantal taken die parallel worden gestart. Daarom is het raadzaam om uw uitvoeringen te configureren voor het gebruik van Blob-opslag voor het overdragen van broncode bestanden.

In het volgende code voorbeeld wordt de uitvoerings configuratie opgegeven die BLOB-gegevens opslag moet gebruiken voor bron code overdrachten.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Voor beelden van notebooks

De [notitie blokken](https://aka.ms/dataset-tutorial) van de gegevensset tonen en uitvouwen op concepten in dit artikel.

## <a name="next-steps"></a>Volgende stappen

* [Machine learning modellen automatisch trainen](how-to-auto-train-remote.md) met TabularDatasets.

* [Train afbeeldings classificatie modellen](https://aka.ms/filedataset-samplenotebook) met FileDatasets.

* [Train met gegevens sets met behulp van pijp lijnen](how-to-create-your-first-pipeline.md).
