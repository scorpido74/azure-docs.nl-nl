---
title: Data sets maken voor toegang tot gegevens met azureml-gegevens sets
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van gegevens sets van verschillende bronnen en het registreren van gegevens sets met uw werk ruimte
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 10/10/2019
ms.openlocfilehash: a558658d7c853560f0939c99dc5dce739d985944
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900703"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Gegevens sets (preview) maken en openen in Azure Machine Learning

In dit artikel leert u hoe u Azure Machine Learning gegevens sets (preview) maakt en hoe u toegang krijgt tot gegevens uit lokale of externe experimenten.

Met Azure Machine Learning gegevens sets kunt u het volgende doen:

* **Bewaar één kopie van de gegevens in uw opslag** waarnaar wordt verwezen door gegevens sets.

* **Eenvoudig toegang krijgen tot gegevens tijdens model training** zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

* **Gegevens delen & samen werken** met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md)

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

> [!Note]
> Voor sommige klassen van gegevensset (preview) gelden afhankelijkheden voor het pakket [met de azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="dataset-types"></a>Typen gegevensset

Gegevens sets worden onderverdeeld in twee typen, op basis van hoe gebruikers ze in de training gebruiken.

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens te realiseren in een Panda of Spark data frame. Een `TabularDataset`-object kan worden gemaakt op basis van CSV-, tsv-, Parquet-, SQL-query resultaten enzovoort. Raadpleeg onze [documentatie](https://aka.ms/tabulardataset-api-reference)voor een volledige lijst.

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Dit biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw compute. De bestanden kunnen uit elke indeling bestaan, waardoor een breder scala aan machine learning scenario's mogelijk is, waaronder diep gaande lessen.

Meer informatie over aanstaande API-wijzigingen vindt u [hier](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Gegevenssets maken

Door een gegevensset te maken, maakt u een verwijzing naar de locatie van de gegevens bron, samen met een kopie van de meta gegevens ervan. De gegevens blijven op de bestaande locatie, waardoor er geen extra opslag kosten in rekening worden gebracht. Zowel TabularDatasets als FileDatasets kunnen worden gemaakt met behulp van de python-SDK of de landings pagina van de werk ruimte (preview). 

Gegevens sets moeten worden gemaakt op basis van paden in [Azure-data stores](how-to-access-data.md) of open bare Web-url's, zodat deze door Azure machine learning toegankelijk zijn.

### <a name="using-the-sdk"></a>De SDK gebruiken

Gegevens sets maken op basis van een [Azure-gegevens archief](how-to-access-data.md) met behulp van de PYTHON-SDK:

* Controleer of u `contributor` of `owner` toegang hebt tot het geregistreerde Azure-gegevens archief.

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
#### <a name="create-tabulardatasets"></a>TabularDatasets maken

Gebruik de methode [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) op `TabularDatasetFactory` klasse om bestanden te lezen in de CSV-of TSV-indeling en een niet-geregistreerde TabularDataset te maken. Als u leest uit meerdere bestanden, worden de resultaten samengevoegd in één tabel weergave.

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

| |PassengerId|Dummy tekst|Pclass|Naam|Seks|Leeftijd|SibSp|Parch|Kaart|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|1|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|1|3|Heikkinen, missen. Laina|vrouwelijk|26,0|0|0|STON/O2. 3101282|7,9250||S

Gebruik de methode [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) op `TabularDatasetFactory` klasse om te lezen van Azure SQL database.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In TabularDatasets kan een tijds tempel worden opgegeven vanuit een kolom in de gegevens of de patroon gegevens van het pad worden opgeslagen in om een time series-eigenschappen in te scha kelen, waarmee u eenvoudig en efficiënt filteren op tijd kunt.

Gebruik de methode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) op `TabularDataset` klasse om de kolom tijds tempel op te geven en filteren op tijd in te scha kelen. [Hier](https://aka.ms/azureml-tsd-notebook)vindt u meer voor beelden en informatie.

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-filedatasets"></a>FileDatasets maken

Gebruik de methode [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) op `FileDatasetFactory` klasse om bestanden in een wille keurige indeling te laden en een niet-geregistreerde FileDataset te maken.

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

### <a name="using-the-workspace-landing-page"></a>De landings pagina van de werk ruimte gebruiken

Meld u aan bij de pagina voor het land van de [werk ruimte](https://ml.azure.com) om een gegevensset te maken via de webervaring. De landings pagina voor de werk ruimte ondersteunt het maken van zowel TabularDatasets als FileDatasets.

De volgende animatie laat zien hoe u een gegevensset maakt op de landings pagina van de werk ruimte.

Selecteer eerst **gegevens sets** in het gedeelte **assets** van het linkerdeel venster. Selecteer vervolgens **+ gegevensset maken** om de bron van uw gegevensset te kiezen. Dit kan een van de volgende zijn: lokale bestanden, gegevens opslag of open bare Web-url's. Selecteer het **type gegevensset**: * in tabel vorm of in het bestand. De **instellingen en het voor beeld** en de **schema** formulieren worden op intelligente wijze gevuld op basis van het bestands type. Selecteer **volgende** om deze te bekijken of uw gegevensset verder te configureren voordat u deze hebt gemaakt. Selecteer **gereed** om het maken van de gegevensset te volt ooien.

![Een gegevensset maken met de gebruikers interface](media/how-to-create-register-datasets/create-dataset-ui.gif)

## <a name="register-datasets"></a>Gegevens sets registreren

Als u het aanmaak proces wilt volt ooien, registreert u uw gegevens sets met een werk ruimte.

Gebruik de methode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) om gegevens sets met uw werk ruimte te registreren zodat deze met anderen kunnen worden gedeeld en opnieuw worden gebruikt in verschillende experimenten.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> Gegevens sets die zijn gemaakt via de werk ruimte landings pagina worden automatisch geregistreerd bij de werk ruimte.

## <a name="version-datasets"></a>Versie gegevens sets

U kunt een nieuwe gegevensset onder dezelfde naam registreren door een nieuwe versie te maken. De gegevensset-versie is een manier om de status van uw gegevens te bookmarkren, zodat u een specifieke versie van de gegevensset kunt Toep assen voor experimenten of toekomstige reproductie. Meer informatie over de versies van de [gegevensset](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
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


## <a name="access-datasets-in-your-script"></a>Toegang tot gegevens sets in uw script

Geregistreerde gegevens sets zijn lokaal en extern toegankelijk op reken clusters, zoals de Azure Machine Learning compute. Als u toegang wilt krijgen tot uw geregistreerde gegevensset voor experimenten, gebruikt u de volgende code om uw werk ruimte en geregistreerde gegevensset op naam op te halen. De methode [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) op de klasse `Dataset` retourneert standaard de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte.

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

* Meer informatie [over het trainen van gegevens sets](how-to-train-with-datasets.md).
* Gebruik automatische machine learning om [met TabularDatasets te trainen](https://aka.ms/automl-dataset).
* Voor meer voor beelden van training met gegevens sets raadpleegt u de [voorbeeld notitieblokken](https://aka.ms/dataset-tutorial).
