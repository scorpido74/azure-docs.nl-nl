---
title: Azure Machine Learning-gegevenssets maken om toegang te krijgen tot gegevens
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van Azure Machine Learning-gegevenssets om toegang te krijgen tot uw gegevens voor machine learning-experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: feaa0c22ec98d170a65e5c9bee119ba3904a95cf
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673727"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-gegevenssets maken

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning-gegevenssets maakt om toegang te krijgen tot gegevens voor uw lokale of externe experimenten.

Met Azure Machine Learning-gegevenssets u het als nog niet doen:

* Bewaar één kopie van gegevens in uw opslag, waarnaar wordt verwezen door gegevenssets.

* Krijg tijdens de modeltraining naadloos toegang tot gegevens zonder u zorgen te maken over verbindingstekenreeksen of gegevenspaden.

* Gegevens delen en samenwerken met andere gebruikers.

## <a name="prerequisites"></a>Vereisten
' Als u gegevenssets wilt maken en ermee wilt werken, hebt u het:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md).

* De [Azure Machine Learning SDK voor Python geïnstalleerd,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)die de azureml-datasets pakket omvat.

> [!NOTE]
> Sommige gegevenssetklassen hebben afhankelijkheden van het [azureml-dataprep-pakket.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Voor Linux-gebruikers worden deze klassen alleen ondersteund op de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="compute-size-guidance"></a>Richtlijnen voor de berekeningsgrootte

Wanneer u een gegevensset maakt, controleert u uw rekenverwerkingskracht en de grootte van uw gegevens in het geheugen. De grootte van uw gegevens in de opslag is niet dezelfde als de grootte van gegevens in een gegevensframe. Gegevens in CSV-bestanden kunnen bijvoorbeeld tot 10x worden uitgebreid in een gegevensframe, zodat een CSV-bestand van 1 GB 10 GB kan worden in een gegevensframe. 

De belangrijkste factor is hoe groot de gegevensset is in het geheugen, d.w.z. als een dataframe. We raden je rekengrootte aan en de rekenkracht bevat 2x de grootte van RAM. Dus als uw dataframe 10 GB is, wilt u een rekendoel met meer dan 20+ GB RAM om ervoor te zorgen dat het dataframe comfortabel in het geheugen past en wordt verwerkt. Als uw gegevens worden gecomprimeerd, kan deze verder worden uitgebreid. 20 GB van relatief schaarse gegevens opgeslagen in gecomprimeerd parket formaat kan uitbreiden tot ~ 800 GB in het geheugen. Aangezien Parketbestanden gegevens opslaan in een kolomindeling, als u slechts de helft van de kolommen nodig hebt, hoeft u slechts ~ 400 GB in het geheugen te laden.
 
Als u panda's gebruikt, is er geen reden om meer dan 1 vCPU te hebben, want dat is alles wat het zal gebruiken. U eenvoudig parallel lopen met veel vCPU's op één Azure Machine Learning-rekeninstantie/knooppunt via Modin en Dask/Ray, en indien nodig uitbreiden naar een groot cluster door simpelweg over te schakelen `import pandas as pd` naar `import modin.pandas as pd`. 
 
Als u niet krijgen een groot genoeg virtuele voor de gegevens, heb je twee opties: gebruik een framework zoals Spark of Dask om de verwerking uit te voeren op de gegevens 'out of memory', dat wil zeggen het dataframe wordt geladen in RAM-partitie door partitie en verwerkt, met het uiteindelijke resultaat wordt verzameld aan het einde. Als dit te traag is, u met Spark of Dask uitschalen naar een cluster dat nog steeds interactief kan worden gebruikt. 

## <a name="dataset-types"></a>Typen gegevenssets

Er zijn twee gegevenssettypen, gebaseerd op hoe gebruikers ze gebruiken in training:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in een tabelindeling door het opgegeven bestand of de lijst met bestanden te ontleden. Dit biedt u de mogelijkheid om de gegevens te materialiseren in een Pandas of Spark DataFrame. U `TabularDataset` een object maken van .csv, .tsv, .parquet, .jsonl-bestanden en sql-queryresultaten. Zie [TabelvormigeDatasetFactory- klasse](https://aka.ms/tabulardataset-api-reference)voor een volledige lijst.

* De klasse [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar enkele of meerdere bestanden in uw gegevensopslag of openbare URL's. Met deze methode u de bestanden downloaden of monteren op uw compute als een FileDataset-object. De bestanden kunnen in elk formaat zijn, waardoor een breder scala aan machine learning-scenario's mogelijk is, waaronder deep learning.

Zie [Wijzigingsbericht gegevensset API voor](https://aka.ms/tabular-dataset)meer informatie over aankomende API-wijzigingen.

## <a name="create-datasets"></a>Gegevenssets maken

Door een gegevensset te maken, maakt u een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de metagegevens. Omdat de gegevens op de bestaande locatie blijven, hoeft u geen extra opslagkosten te maken. U `TabularDataset` beide `FileDataset` en gegevenssets maken met https://ml.azure.combehulp van de Python SDK of op.

Als de gegevens toegankelijk moeten zijn voor Azure Machine Learning, moeten gegevenssets worden gemaakt op paden in [Azure-gegevensopslag](how-to-access-data.md) of openbare web-URL's. 

### <a name="use-the-sdk"></a>De SDK gebruiken

Ga als een overzicht van gegevenssets uit een [Azure-gegevensarchief](how-to-access-data.md) met de Python SDK:

1. Controleer of `contributor` u `owner` de geregistreerde Azure-gegevensarchief hebt of toegang hebt tot de geregistreerde Azure-gegevensarchief.

2. Maak de gegevensset door te verwijzen naar paden in het gegevensarchief.
> [!Note]
> U een gegevensset maken op basis van meerdere paden in meerdere gegevensarchieven. Er is geen harde limiet voor het aantal bestanden of gegevensgrootte waarvan u een gegevensset maken. Voor elk gegevenspad worden echter een paar aanvragen naar de opslagservice verzonden om te controleren of het naar een bestand of een map wijst. Deze overhead kan leiden tot verminderde prestaties of uitval. Een gegevensset die verwijst naar één map met 1000 bestanden binnen, wordt beschouwd als een verwijzing naar één gegevenspad. We raden u aan gegevenssets te maken die verwijzen naar minder dan 100 paden in datastores voor optimale prestaties.

#### <a name="create-a-tabulardataset"></a>Een tabeltabelsetmaken

Gebruik [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) de methode `TabularDatasetFactory` in de klasse om bestanden in de CSV- of .tsv-indeling te lezen en een niet-geregistreerde TabularDataset te maken. Als u uit meerdere bestanden leest, worden de resultaten samengevoegd tot één tabelvormige weergave. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Wanneer u een TabularDataset maakt, worden kolomgegevenstypen standaard automatisch afgeleid. Als de afgeleide typen niet aan uw verwachtingen voldoen, u kolomtypen opgeven met behulp van de volgende code. De `infer_column_type` parameter is alleen van toepassing op gegevenssets die zijn gemaakt op afhankelijke bestanden. U ook [meer te weten komen over ondersteunde gegevenstypen.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)

> [!IMPORTANT] 
> Als uw opslag zich achter een virtueel netwerk of firewall bevindt, wordt alleen het maken van een gegevensset via de SDK ondersteund. Als u uw gegevensset wilt `validate=False` maken, moet u de parameters en `infer_column_types=False` in uw `from_delimited_files()` methode opnemen. Hiermee omzeilt u de eerste validatiecontrole en zorgt u ervoor dat u uw gegevensset maken op deze beveiligde bestanden. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Overleefde|Pclass Pclass|Name|Sex|Leeftijd|SibSp Sibsp|Parch|Ticket|Tarief|Cabine|Begonnen
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|man|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|vrouwelijk|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen. Laina (Laina)|vrouwelijk|26.0|0|0|STON/O2. 3101282|7.9250||S


Als u een gegevensset wilt maken uit een in-memory pandas-gegevensframe, schrijft u de gegevens naar een lokaal bestand, zoals een csv, en maakt u uw gegevensset uit dat bestand. De volgende code toont deze werkstroom aan.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Gebruik [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) de methode `TabularDatasetFactory` in de klasse om te lezen uit Azure SQL Database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In Tabeltabelgegevenssets u een tijdstempel opgeven vanuit een kolom in de gegevens of van waar de padpatroongegevens worden opgeslagen om een tijdreekskenmerk in te schakelen. Deze specificatie zorgt voor eenvoudige en efficiënte filtering door de tijd.

Gebruik [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) de methode`TabularDataset` in de klasse om uw tijdstempelkolom op te geven en filteren op tijd in te schakelen. Zie [Api-demo met API-demo met NOAA-weersinformatie voor](https://aka.ms/azureml-tsd-notebook)meer informatie.

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

#### <a name="create-a-filedataset"></a>Een Bestandssetgegevensmaken

Gebruik [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) de methode `FileDatasetFactory` in de klasse om bestanden in elke indeling te laden en een niet-geregistreerde FileDataset te maken. Als uw opslag zich achter een virtueel netwerk `validate =False` of `from_files()` firewall bevindt, stelt u de parameter in uw methode in. Hiermee wordt de eerste validatiestap omzeild en wordt ervoor zorgen dat u uw gegevensset maken op deze beveiligde bestanden.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Op het web 
In de volgende stappen en animaties ziet u hoe https://ml.azure.comu een gegevensset maakt in Azure Machine Learning-studio.

![Een gegevensset maken met de gebruikersinterface](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Ga als lid van het werk om een gegevensset in de studio te maken:
1. Log hier https://ml.azure.comin
1. Selecteer **Gegevenssets** in de sectie **Activa** van het linkerdeelvenster. 
1. Selecteer **Gegevensset maken** om de bron van uw gegevensset te kiezen. Deze bron kan lokale bestanden, een gegevensarchief of openbare URL's zijn.
1. Selecteer **Tabelvormig** of **Bestand** voor gegevenssettype.
1. Selecteer **Volgende** om het **gegevensarchief en het formulier bestandsselectie te** openen. In dit formulier selecteert u waar u uw gegevensset na het maken wilt bewaren en selecteert u welke gegevensbestanden u voor uw gegevensset wilt gebruiken. 
1. Selecteer **Volgende** om de **formulieren Instellingen en voorbeeld-** en **schema's** in te vullen. ze worden intelligent ingevuld op basis van bestandstype en u uw gegevensset verder configureren voordat u deze formulieren maakt. 
1. Selecteer **Volgende** om het formulier **Details bevestigen** te controleren. Controleer uw selecties en maak een optioneel gegevensprofiel voor uw gegevensset. Meer informatie over [gegevensprofilering](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Selecteer **Maken** om het maken van gegevenssets te voltooien.

## <a name="register-datasets"></a>Gegevenssets registreren

Als u het creatieproces wilt voltooien, registreert u uw gegevenssets bij een werkruimte. Gebruik [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) de methode om gegevenssets te registreren met uw werkruimte om ze met anderen te delen en opnieuw te gebruiken in verschillende experimenten:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Gegevenssets die zijn gemaakt via Azure Machine Learning-studio, worden automatisch geregistreerd bij de werkruimte.

## <a name="create-datasets-with-azure-open-datasets"></a>Gegevenssets maken met Azure Open-gegevenssets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Gegevenssets bevatten gegevens uit het publieke domein voor weer, telling, feestdagen, openbare veiligheid en locatie waarmee u machine learning-modellen trainen en voorspellende oplossingen verrijken. Open datasets bevinden zich in de cloud op Microsoft Azure en zijn opgenomen in zowel de SDK als de gebruikersinterface van de werkruimte.

### <a name="use-the-sdk"></a>De SDK gebruiken

Als u gegevenssets wilt maken met Azure Open Datasets van `pip install azureml-opendatasets`de SDK, controleert u of u het pakket hebt geïnstalleerd met . Elke afzonderlijke gegevensset wordt vertegenwoordigd door een eigen klasse in de SDK `TabularDataset` `FileDataset`en bepaalde klassen zijn beschikbaar als een , of beide. Zie de [referentiedocumentatie](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) voor een volledige lijst met klassen.

U bepaalde klassen ophalen `TabularDataset` `FileDataset`als een of, waarmee u de bestanden rechtstreeks manipuleren en/of downloaden. Andere klassen kunnen een **only** gegevensset alleen `get_tabular_dataset()` `get_file_dataset()` krijgen met behulp van een van of functies. In het volgende codevoorbeeld worden enkele voorbeelden van dit soort klassen weergegeven.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Wanneer u een gegevensset registreert die is gemaakt met Open Datasets, worden er geen gegevens onmiddellijk gedownload, maar worden de gegevens later geopend wanneer deze (bijvoorbeeld tijdens de training) wordt aangevraagd vanaf een centrale opslaglocatie.

### <a name="use-the-ui"></a>De gebruikersinterface gebruiken

U ook gegevenssets maken van klassen met open gegevenssets via de gebruikersinterface. Selecteer in uw werkruimte het tabblad **Gegevenssets** onder **Activa**. Selecteer in de vervolgkeuzelijst **Gegevensset maken** de optie **Uit Gegevenssets openen**.

![Gegevensset openen met de gebruikersinterface](./media/how-to-create-register-datasets/open-datasets-1.png)

Selecteer een gegevensset door de tegel te selecteren. (U filteren met de zoekbalk.) Selecteer **Volgende**.

![Gegevensset kiezen](./media/how-to-create-register-datasets/open-datasets-2.png)

Kies een naam waaronder u de gegevensset wilt registreren en filter de gegevens optioneel met behulp van de beschikbare filters. In dit geval filtert u voor de gegevensset feestdagen de periode tot één jaar en de landcode alleen naar de VS. Selecteer **Maken**.

![Gegevenssetparams instellen en gegevensset maken](./media/how-to-create-register-datasets/open-datasets-3.png)

De gegevensset is nu beschikbaar in uw werkruimte onder **Gegevenssets**. U het op dezelfde manier gebruiken als andere gegevenssets die u hebt gemaakt.

## <a name="version-datasets"></a>Versiegegevenssets

U een nieuwe gegevensset onder dezelfde naam registreren door een nieuwe versie te maken. Een gegevenssetversie is een manier om de status van uw gegevens een bladwijzer te geven, zodat u een specifieke versie van de gegevensset toepassen voor experimenten of toekomstige reproductie. Meer informatie over [gegevenssetversies](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Toegang tot gegevenssets in uw script

Geregistreerde gegevenssets zijn zowel lokaal als op afstand toegankelijk op compute clusters zoals de Azure Machine Learning compute. Als u toegang wilt krijgen tot uw geregistreerde gegevensset voor experimenten, gebruikt u de volgende code om toegang te krijgen tot uw werkruimte en geregistreerde gegevensset op naam. Standaard retourneert [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) de `Dataset` methode in de klasse de nieuwste versie van de gegevensset die bij de werkruimte is geregistreerd.

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

* Meer informatie over [trainen met gegevenssets.](how-to-train-with-datasets.md)
* Gebruik geautomatiseerde machine learning om te [trainen met TabularDatasets.](https://aka.ms/automl-dataset)
* Zie de [voorbeeldnotitieblokken voor](https://aka.ms/dataset-tutorial)meer voorbeelden van gegevenssets.
