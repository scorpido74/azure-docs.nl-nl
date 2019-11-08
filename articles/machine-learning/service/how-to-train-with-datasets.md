---
title: Trainen met azureml-gegevens sets
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van gegevens sets in training
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: f95798241084efdb91993a5add52f84a8a6dc1ec
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814807"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Train met gegevens sets in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over de twee manieren waarop u [Azure machine learning gegevens sets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) in de externe experiment-training kunt gebruiken zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

- Optie 1: als u gestructureerde gegevens hebt, maakt u een TabularDataset en gebruikt u deze rechtstreeks in uw trainings script.

- Optie 2: als u niet-gestructureerde gegevens hebt, maakt u een FileDataset en koppelt of downloadt u bestanden naar een externe Compute voor training.

Azure Machine Learning gegevens sets bieden een naadloze integratie met Azure Machine Learning trainings producten zoals [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) en [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py).

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en trainen, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

> [!Note]
> Voor sommige verzamelings klassen zijn afhankelijkheden van het pakket voor [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Optie 1: gegevens sets rechtstreeks in trainings scripts gebruiken

In dit voor beeld maakt u een [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) en gebruikt u deze als directe invoer voor uw `estimator`-object voor training. 

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Met de volgende code wordt een niet-geregistreerde TabularDataset gemaakt van een web-URL. U kunt ook gegevens sets maken op basis van lokale bestanden of paden in gegevens opslag. Meer informatie over [het maken van gegevens sets](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Toegang tot de invoer gegevensset in uw trainings script

TabularDataset-objecten bieden de mogelijkheid om de gegevens te laden in een Panda of Spark data frame, zodat u kunt werken met de vertrouwde gegevens voorbereiding en-trainings bibliotheken. Als u gebruik wilt maken van deze mogelijkheid, kunt u een TabularDataset door geven als de invoer in uw trainings configuratie en deze vervolgens ophalen in uw script.

Hiertoe opent u de invoer gegevensset via het [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) -object in uw trainings script en gebruikt u de methode [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>De Estimator configureren

Een [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) -object wordt gebruikt om de uitvoering van het experiment in te dienen. Azure Machine Learning heeft vooraf geconfigureerde schattingen voor algemene machine learning frameworks, evenals een algemene Estimator.

Deze code maakt een Gene riek Estimator-object, `est`, dat aangeeft

* Een script Directory voor uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het trainings script, *train_titanic. py*.
* De invoer gegevensset voor training, `titanic`.
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

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Optie 2: bestanden koppelen aan een extern Compute-doel

Als u uw gegevens bestanden beschikbaar wilt maken voor het reken doel voor training, gebruikt u [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) om bestanden te koppelen of te downloaden waarnaar wordt verwezen.

Wanneer u een bestands systeem koppelt, koppelt u dat bestands systeem aan een directory (koppel punt) en maakt u het beschikbaar op het berekenings doel. Koppelen is onmiddellijk omdat bestanden alleen worden geladen op het moment van verwerking. Koppelen wordt ondersteund en aanbevolen voor op Linux gebaseerde berekeningen, waaronder Azure Machine Learning compute, virtual machines en HDInsight. Voor niet op Linux gebaseerde Compute wordt alleen downloaden ondersteund.  

>[!WARNING]
> Als uw gegevens grootte de opslag limiet van het reken doel overschrijdt, mislukt het downloaden.

### <a name="create-a-filedataset"></a>Een FileDataset maken

In het volgende voor beeld wordt een niet-geregistreerde FileDataset gemaakt op basis van web-url's. Meer informatie over [het maken van gegevens sets](https://aka.ms/azureml/howto/createdatasets) van andere bronnen.

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

In plaats van de gegevensset via de para meter `inputs` in de Estimator door te geven, kunt u de gegevensset ook door geven via `script_params` en het gegevenspad (koppelings punt) ophalen in uw trainings script via argumenten. Op deze manier kunt u toegang krijgen tot uw gegevens en een bestaand trainings script gebruiken.

Een [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) Estimator-object wordt gebruikt om de run for scikit-experimenten te verzenden. Meer informatie over training met de [SKlearn Estimator](how-to-train-scikit-learn.md).

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

Nadat u de uitvoering hebt verzonden, worden gegevens bestanden waarnaar wordt verwezen door de `mnist` gegevensset gekoppeld aan het Compute-doel. De volgende code laat zien hoe u de gegevens in uw script kunt ophalen.

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

## <a name="notebook-examples"></a>Voor beelden van notebooks

De [notitie blokken](https://aka.ms/dataset-tutorial) van de gegevensset tonen en uitvouwen op concepten in dit artikel. 

## <a name="next-steps"></a>Volgende stappen

* [Machine learning modellen automatisch trainen](how-to-auto-train-remote.md) met TabularDatasets.

* [Train afbeeldings classificatie modellen](https://aka.ms/filedataset-samplenotebook) met FileDatasets.

