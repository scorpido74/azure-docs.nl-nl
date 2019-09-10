---
title: Data sets maken voor toegang tot gegevens met azureml-gegevens sets
titleSuffix: Azure Machine Learning service
description: Meer informatie over het maken van gegevens sets van verschillende bronnen en het registreren van gegevens sets met uw werk ruimte
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: 215660b0f0b8748461849f20e65a3585f939085e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858794"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Gegevens sets (preview) maken en openen in Azure Machine Learning

In dit artikel leert u hoe u Azure Machine Learning gegevens sets (preview) maakt en hoe u toegang krijgt tot gegevens uit lokale of externe experimenten.

Met Azure Machine Learning gegevens sets kunt u het volgende doen: 

* **Bewaar één kopie van de gegevens in uw opslag** waarnaar wordt verwezen door gegevens sets. 

* **Eenvoudig toegang krijgen tot gegevens tijdens model training** zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

* **Gegevens delen & samen werken** met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer nog vandaag de [gratis of betaalde versie van de Azure Machine Learning Service](https://aka.ms/AMLFree).

* Een [Azure Machine Learning Services-werk ruimte](how-to-manage-workspace.md)

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

> [!Note]
> Voor sommige klassen van gegevensset (preview) gelden afhankelijkheden voor het pakket [met de azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies:  Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="dataset-types"></a>Typen gegevensset

Gegevens sets worden in verschillende typen ingedeeld op basis van de manier waarop gebruikers ze in de training gebruiken. Lijst met typen gegevensset:
* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens te realiseren in een Panda data frame. U `TabularDataset` kunt een object maken op basis van CSV-, tsv-, Parquet-bestanden, SQL-query resultaten, enzovoort. Raadpleeg onze [documentatie](https://aka.ms/tabulardataset-api-reference)voor een volledige lijst.
* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Dit biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw compute. De bestanden kunnen uit elke indeling bestaan, waardoor een breder scala aan machine learning scenario's mogelijk is, waaronder diep gaande lessen.

Meer informatie over aanstaande API-wijzigingen vindt u [hier](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Gegevenssets maken 

Door een gegevensset te maken, maakt u een verwijzing naar de locatie van de gegevens bron, samen met een kopie van de meta gegevens ervan. De gegevens blijven op de bestaande locatie, waardoor er geen extra opslag kosten in rekening worden gebracht.

Azure Machine Learning gegevens sets moeten worden gemaakt op basis van paden in [Azure data stores](how-to-access-data.md) of open bare Web-url's.

Gegevens sets maken op basis van een [Azure-gegevens opslag](how-to-access-data.md):

* Controleer of `contributor` `owner` u toegang hebt tot het geregistreerde Azure-gegevens archief.

* Maak de gegevensset door te verwijzen naar een pad in het gegevens archief.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>TabularDatasets maken

Gebruik de `from_delimited_files()` methode voor `TabularDatasetFactory` de klasse om bestanden te lezen in de CSV-of TSV-indeling en maak een niet-geregistreerde TabularDataset. Als u leest uit meerdere bestanden, worden de resultaten samengevoegd in één tabel weergave.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Dummy tekst|Pclass|Name|Seks|Leeftijd|SibSp|Parch|Kaart|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||Z
1|2|1|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, missen. Laina|vrouwelijk|26,0|0|0|STON/O2. 3101282|7,9250||Z

### <a name="create-filedatasets"></a>FileDatasets maken
Gebruik de `from_files()` methode voor `FileDatasetFactory` de klasse om bestanden in elke indeling te laden en een niet-geregistreerde FileDataset te maken.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```
## <a name="register-datasets"></a>Gegevens sets registreren

Als u het aanmaak proces wilt volt ooien, registreert u uw gegevens sets met de werk ruimte:

Gebruik de `register()` methode om gegevens sets te registreren in uw werk ruimte, zodat deze kunnen worden gedeeld met anderen en opnieuw worden gebruikt in verschillende experimenten.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>Versie gegevens sets

U kunt een nieuwe gegevensset onder dezelfde naam registreren door een nieuwe versie te maken. De gegevensset-versie is een manier om de status van uw gegevens te bookmarkren, zodat u een specifieke versie van de gegevensset kunt Toep assen voor experimenten of toekomstige reproductie. Typische scenario's om versie beheer te overwegen: 
* Wanneer nieuwe gegevens beschikbaar zijn voor retraining.
* Wanneer u verschillende benaderingen voor gegevens voorbereiding of functie techniek toepast.

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>Toegang tot uw gegevens tijdens de training

Geregistreerde gegevens sets zijn lokaal en extern toegankelijk op reken clusters, zoals de Azure Machine Learning compute. Als u toegang wilt krijgen tot uw geregistreerde gegevensset voor experimenten, gebruikt u de volgende code om uw werk ruimte en geregistreerde gegevensset op naam op te halen. De [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) methode voor de `Dataset` klasse retourneert standaard de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte.

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

## <a name="next-steps"></a>Volgende stappen

* Gebruik automatische machine learning om [met TabularDatasets te trainen](https://aka.ms/automl-dataset).
* Voor meer voor beelden van training met gegevens sets raadpleegt u de [voorbeeld notitieblokken](https://aka.ms/dataset-tutorial).
