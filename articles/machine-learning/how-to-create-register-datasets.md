---
title: Azure Machine Learning data sets maken voor toegang tot gegevens
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van Azure Machine Learning gegevens sets voor het openen van de machine learning-experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 06/29/2020
ms.openlocfilehash: a220a7279cbb5ba75c8aa803cb4bd709442a52fe
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326389"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning gegevens sets maken

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning gegevens sets maakt om toegang te krijgen tot gegevens voor uw lokale of externe experimenten. Zie het artikel over [beveiligde toegang](concept-data.md#data-workflow) als u wilt weten waar gegevens sets passen in de algehele werk stroom van Azure machine learning Data Access.

Met Azure Machine Learning gegevens sets kunt u het volgende doen:

* Bewaar één kopie van de gegevens in uw opslag, waarnaar wordt verwezen door gegevens sets.

* Naadloos toegang krijgen tot gegevens tijdens model training zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

* Gegevens delen en samen werken met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

> [!NOTE]
> Sommige dataset-klassen hebben afhankelijkheden van het pakket voor [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Dit is alleen compatibel met 64-bits python. Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) en CentOS (7).

## <a name="compute-size-guidance"></a>Richt lijn voor reken capaciteit

Wanneer u een gegevensset maakt, controleert u de verwerkings kracht van de berekening en de grootte van uw gegevens in het geheugen. De grootte van uw gegevens in de opslag is niet hetzelfde als de grootte van de gegevens in een data frame. Gegevens in CSV-bestanden kunnen bijvoorbeeld tot 10x worden uitgebreid in een data frame, zodat een CSV-bestand van 1 GB 10 GB kan worden in een data frame. 

De belangrijkste factor is hoe groot de gegevensset in het geheugen is, d.w.z. als een data frame. Het is raadzaam om de reken grootte en de verwerkings capaciteit twee maal zo groot te hebben als RAM-geheugen. Dus als uw data frame 10 GB is, wilt u een compute target met 20 GB RAM-geheugen om ervoor te zorgen dat de data frame in het geheugen past en kunnen worden verwerkt. Als uw gegevens zijn gecomprimeerd, kunnen ze verder worden uitgebreid. 20 GB aan relatief sparse gegevens die zijn opgeslagen in de gecomprimeerde Parquet-indeling kunnen worden uitgebreid naar ~ 800 GB in het geheugen. Aangezien Parquet-bestanden gegevens in een kolom indeling opslaan en u alleen de helft van de kolommen nodig hebt, hoeft u niet meer dan ~ 400 GB in het geheugen te laden.
 
Als u gebruikmaakt van Pandas, is er geen reden om meer dan 1 vCPU te hebben, want dat is alles. U kunt eenvoudig parallelliseren aan veel Vcpu's op één Azure Machine Learning Reken instantie/knoop punt via modi en Dask/Ray, en zo nodig uitschalen naar een groot cluster door eenvoudigweg `import pandas as pd` te wijzigen in `import modin.pandas as pd` . 
 
Als u een groot aantal virtuele machines niet kunt verkrijgen voor de gegevens, hebt u twee opties: gebruik een framework zoals Spark of Dask om de verwerking van de gegevens uit het geheugen te vervolledigen, d.w.z. de data frame wordt in de RAM-partitie geladen door de partitie en verwerkt, waarbij het uiteindelijke resultaat aan het einde wordt verzameld. Als dit te langzaam is, kunt u met Spark of Dask uitschalen naar een cluster dat nog steeds interactief kan worden gebruikt. 

## <a name="dataset-types"></a>Typen gegevenssets

Er zijn twee typen gegevensset, op basis van hoe gebruikers ze in de training gebruiken:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens te realiseren in een Panda of Spark data frame. U kunt een `TabularDataset` object maken van. CSV-,. tsv-,. Parquet-,. json-bestanden en uit SQL-query resultaten. Zie [TabularDatasetFactory-klasse](https://aka.ms/tabulardataset-api-reference)voor een volledige lijst.

* De klasse [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Met deze methode kunt u de bestanden downloaden of koppelen aan uw Compute als een FileDataset-object. De bestanden kunnen een wille keurige indeling hebben, waardoor een breder scala aan machine learning scenario's mogelijk is, met inbegrip van diep gaande lessen. 

## <a name="create-datasets"></a>Gegevenssets maken

Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de bijbehorende metagegevens. Omdat de gegevens op de bestaande locatie bewaard blijven, maakt u geen extra opslagkosten. U kunt zowel- `TabularDataset` als- `FileDataset` gegevens sets maken met behulp van de PYTHON-SDK of de Azure machine learning Studio op https://ml.azure.com .

Gegevens sets moeten worden gemaakt op basis van paden in [Azure-data stores](how-to-access-data.md) of open bare Web-url's, zodat deze door Azure machine learning toegankelijk zijn. 

### <a name="use-the-sdk"></a>De SDK gebruiken

Gegevens sets maken op basis van een [Azure-gegevens opslag](how-to-access-data.md) met behulp van de PYTHON-SDK:

1. Controleer of u toegang hebt tot `contributor` `owner` het geregistreerde Azure-gegevens archief.

2. Maak de gegevensset door te verwijzen naar de paden in het gegevens archief.

> [!Note]
> U kunt een gegevensset maken op basis van meerdere paden in meerdere gegevens opslag. Er is geen vaste limiet voor het aantal bestanden of gegevens grootte waaruit u een gegevensset kunt maken. Voor elk gegevenspad worden echter enkele aanvragen verzonden naar de opslag service om te controleren of deze naar een bestand of map verwijst. Deze overhead kan leiden tot slechtere prestaties of storingen. Een gegevensset die verwijst naar één map met 1000 bestanden in, wordt beschouwd als verwijzingen naar één gegevenspad. U kunt het beste een gegevensset maken die verwijst naar minder dan 100 paden in gegevens opslag voor optimale prestaties.

#### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Gebruik de [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) methode in de `TabularDatasetFactory` klasse om bestanden te lezen in de CSV-of. TSV-indeling en om een niet-geregistreerde TabularDataset te maken. Als u een lees bewerking uitvoert van meerdere bestanden, worden de resultaten samengevoegd in één tabel weergave. 

Met de volgende code wordt de bestaande werk ruimte en de gewenste gegevens opslag op naam opgehaald. En vervolgens worden de gegevens opslag en bestands locaties door gegeven aan de `path` para meter om een nieuwe TabularDataset te maken `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Wanneer u een TabularDataset maakt, worden de kolom gegevens typen standaard automatisch afgeleid. Als de instelde typen niet overeenkomen met uw verwachtingen, kunt u kolom typen opgeven door de volgende code te gebruiken. De para meter `infer_column_type` is alleen van toepassing op gegevens sets die zijn gemaakt van bestanden met scheidings tekens. U kunt ook [meer te weten komen over ondersteunde gegevens typen](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Als uw opslag zich achter een virtueel netwerk of een firewall bevindt, wordt alleen het maken van een gegevensset via de SDK ondersteund. Als u uw gegevensset wilt maken, moet u de para meters `validate=False` en `infer_column_types=False` in uw `from_delimited_files()` methode toevoegen. Dit omzeilt de initiële validatie controle en zorgt ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|TabIndex|PassengerId|Dummy tekst|Pclass|Naam|Seks|Ouderdom|SibSp|Parch|Ticket|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Niet waar|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Waar|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Waar|3|Heikkinen, missen. Laina|vrouwelijk|26,0|0|0|STON/O2. 3101282|7,9250||S

Als u een gegevensset wilt maken op basis van een in geheugen-Panda data frame, schrijft u de gegevens naar een lokaal bestand, zoals een CSV, en maakt u uw gegevensset vanuit dat bestand. De volgende code toont deze werk stroom.

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

Gebruik de [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) methode op de `TabularDatasetFactory` klasse om te lezen van Azure SQL database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore. Take note of double parenthesis.
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In TabularDatasets kunt u een tijds tempel opgeven van een kolom in de gegevens of van de locatie waar de patroon gegevens voor het pad zijn opgeslagen om een time series-eigenschappen in te scha kelen. Met deze specificatie kunt u eenvoudig en efficiënt filteren op tijd.

Gebruik de [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) methode in de `TabularDataset` klasse om de kolom tijds tempel op te geven en filteren op tijd in te scha kelen. Zie voor meer informatie [TABELLAIRE API-demo met een time-out met NOAA-weer gegevens](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

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

#### <a name="create-a-filedataset"></a>Een FileDataset maken

Gebruik de [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) methode in de- `FileDatasetFactory` klasse om bestanden in een wille keurige indeling te laden en een niet-geregistreerde FileDataset te maken. Als uw opslag zich achter een virtueel netwerk of een firewall bevindt, stelt u de para meter `validate=False` in uw `from_files()` methode in. Hiermee wordt de eerste validatie stap omzeild en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden.

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
In de volgende stappen en animatie ziet u hoe u een gegevensset maakt in Azure Machine Learning Studio, https://ml.azure.com .

![Een gegevensset maken met de gebruikers interface](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Een gegevensset maken in Studio:
1. Meld u aan bij https://ml.azure.com .
1. Selecteer **gegevens sets** in het gedeelte **assets** van het linkerdeel venster. 
1. Selecteer **gegevensset maken** om de bron van uw gegevensset te kiezen. Deze bron kan lokale bestanden, een gegevens opslag of een open bare URL zijn.
1. Selecteer een **tabel** of **bestand** voor het type gegevensset.
1. Selecteer **volgende** om het formulier **gegevens opslag en bestand selecteren** te openen. Op dit formulier selecteert u waar u uw gegevensset na het maken moet blijven en selecteert u welke gegevens bestanden u voor uw gegevensset wilt gebruiken. 
    1. Schakel overs laan van validatie in als uw gegevens zich in een virtueel netwerk bevindt. Meer informatie over het [isoleren van virtuele netwerken en privacy](how-to-enable-virtual-network.md#machine-learning-studio).
1. Selecteer **volgende** om de **instellingen en de voor beeld** -en **schema** formulieren in te vullen. ze worden op intelligente wijze ingevuld op basis van het bestands type en u kunt uw gegevensset verder configureren voordat deze formulieren worden gemaakt. 
1. Selecteer **volgende** om het formulier **Details bevestigen** weer te geven. Controleer uw selecties en maak een optioneel gegevens profiel voor uw gegevensset. Meer informatie over [gegevensprofilering](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Selecteer **maken** om het maken van de gegevensset te volt ooien.

## <a name="register-datasets"></a>Gegevens sets registreren

Als u het aanmaak proces wilt volt ooien, registreert u uw gegevens sets met een werk ruimte. Gebruik de [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) methode voor het registreren van gegevens sets met uw werk ruimte om ze te delen met anderen en ze opnieuw te gebruiken voor experimenten in uw werk ruimte:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Gegevens sets die zijn gemaakt via Azure Machine Learning Studio, worden automatisch geregistreerd bij de werk ruimte.

## <a name="create-datasets-with-azure-open-datasets"></a>Gegevens sets maken met Azure open gegevens sets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Gegevens sets bevatten informatie over openbaar domein voor weer, telling, feest dagen, open bare veiligheid en locatie die u helpen bij het trainen van machine learning modellen en verrijkende voorspellende oplossingen. Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn opgenomen in de SDK en de gebruikers interface van de werk ruimte.

### <a name="use-the-sdk"></a>De SDK gebruiken

Als u gegevens sets met Azure open gegevens sets wilt maken vanuit de SDK, moet u ervoor zorgen dat u het pakket met hebt geïnstalleerd `pip install azureml-opendatasets` . Elke afzonderlijke gegevensset wordt vertegenwoordigd door een eigen klasse in de SDK en bepaalde klassen zijn beschikbaar als een `TabularDataset` , `FileDataset` of beide. Zie de [referentie documentatie](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) voor een volledige lijst met klassen.

U kunt bepaalde klassen ophalen als ofwel een `TabularDataset` of `FileDataset` , waarmee u de bestanden rechtstreeks kunt bewerken en/of downloaden. Andere klassen kunnen een gegevensset **alleen** ophalen met behulp van een van `get_tabular_dataset()` of- `get_file_dataset()` functies. In het volgende code voorbeeld ziet u enkele voor beelden van deze typen klassen.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Wanneer u een gegevensset registreert die is gemaakt op basis van geopende gegevens sets, worden er geen gegevens direct gedownload, maar worden de gegevens later weer gegeven wanneer u hierom wordt gevraagd (bijvoorbeeld tijdens training) vanuit een centrale opslag locatie.

### <a name="use-the-ui"></a>De gebruikers interface gebruiken

U kunt ook gegevens sets maken van de klassen Open gegevens sets via de gebruikers interface. Selecteer in uw werk ruimte het tabblad **gegevens sets** onder **assets**. Selecteer in de vervolg keuzelijst **gegevensset maken** de optie **uit geopende gegevens sets**.

![Gegevensset openen met de gebruikers interface](./media/how-to-create-register-datasets/open-datasets-1.png)

Selecteer een gegevensset door de tegel ervan te selecteren. (U hebt de mogelijkheid om te filteren met behulp van de zoek balk.) Selecteer **volgende**.

![Gegevensset kiezen](./media/how-to-create-register-datasets/open-datasets-2.png)

Kies een naam waaronder u de gegevensset wilt registreren en de gegevens eventueel kunt filteren met behulp van de beschik bare filters. In dit geval filtert u voor de gegevensset van de open bare feest dagen de periode tot één jaar en de land code naar alleen de Verenigde Staten. Selecteer **Maken**.

![Gegevensset-para meters instellen en gegevensset maken](./media/how-to-create-register-datasets/open-datasets-3.png)

De gegevensset is nu beschikbaar in uw werk ruimte onder **gegevens sets**. U kunt deze op dezelfde manier gebruiken als andere gegevens sets die u hebt gemaakt.

## <a name="version-datasets"></a>Versie gegevens sets

U kunt een nieuwe gegevensset onder dezelfde naam registreren door een nieuwe versie te maken. Een gegevensset-versie is een manier om de status van uw gegevens te bookmarkren, zodat u een specifieke versie van de gegevensset kunt Toep assen voor experimenten of toekomstige reproductie. Meer informatie over de versies van de [gegevensset](how-to-version-track-datasets.md).
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

## <a name="access-datasets-in-your-script"></a>Toegang tot gegevens sets in uw script

Geregistreerde gegevens sets zijn zowel lokaal als extern toegankelijk op reken clusters, zoals de Azure Machine Learning compute. Gebruik de volgende code om toegang te krijgen tot uw geregistreerde gegevensset voor experimenten en de geregistreerde gegevensset op naam te openen. De [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) methode voor de `Dataset` klasse retourneert standaard de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte.

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

## <a name="access-datasets-in-a-virtual-network"></a>Toegang tot gegevens sets in een virtueel netwerk

Als uw werk ruimte zich in een virtueel netwerk bevindt, moet u de gegevensset configureren om validatie over te slaan. Voor meer informatie over het gebruik van data stores en gegevens sets in een virtueel netwerk, raadpleegt u [netwerk isolatie tijdens de training & afwijzen met persoonlijke virtuele netwerken](how-to-enable-virtual-network.md#use-datastores-and-datasets).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het trainen van gegevens sets](how-to-train-with-datasets.md).
* Gebruik automatische machine learning om [met TabularDatasets te trainen](https://aka.ms/automl-dataset).
* Zie de voor beelden van [notitie blokken](https://aka.ms/dataset-tutorial)voor meer informatie over het trainen van gegevensset.
