---
title: Azure Machine Learning data sets maken voor toegang tot gegevens
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van Azure Machine Learning gegevens sets voor het openen van de machine learning-experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 775c6016acbcd0f87f368852a68eaea706c79898
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945712"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning gegevens sets maken

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning gegevens sets maakt om toegang te krijgen tot gegevens voor uw lokale of externe experimenten.

Met Azure Machine Learning gegevens sets kunt u het volgende doen:

* Bewaar één kopie van de gegevens in uw opslag, waarnaar wordt verwezen door gegevens sets.

* Naadloos toegang krijgen tot gegevens tijdens model training zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden.

* Gegevens delen en samen werken met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u er nog geen hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

> [!NOTE]
> Voor sommige verzamelings klassen zijn afhankelijkheden van het pakket voor [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux, Ubuntu, Fedora en CentOS.

## <a name="dataset-types"></a>Typen gegevenssets

Er zijn twee typen gegevensset, op basis van hoe gebruikers ze in de training gebruiken:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens te realiseren in een Panda of Spark data frame. U kunt een `TabularDataset`-object maken van CSV-,. TSV-en Parquet-bestanden en uit SQL-query resultaten. Zie [TabularDatasetFactory-klasse](https://aka.ms/tabulardataset-api-reference)voor een volledige lijst.

* De klasse [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Met deze methode kunt u de bestanden downloaden of koppelen aan uw Compute als een FileDataset-object. De bestanden kunnen een wille keurige indeling hebben, waardoor een breder scala aan machine learning scenario's mogelijk is, met inbegrip van diep gaande lessen.

Zie voor meer informatie over aanstaande wijzigingen in de API de API- [wijzigings waarschuwing](https://aka.ms/tabular-dataset)voor de gegevensset.

## <a name="create-datasets"></a>Gegevenssets maken

Door een gegevensset te maken, maakt u een verwijzing naar de locatie van de gegevens bron, samen met een kopie van de meta gegevens ervan. Omdat de gegevens zich op de bestaande locatie blijven, ontstaan er geen extra opslag kosten. U kunt `TabularDataset` en `FileDataset` gegevens sets maken met behulp van de python-SDK of de landings pagina voor de werk ruimte (preview).

Gegevens sets moeten worden gemaakt op basis van paden in [Azure-data stores](how-to-access-data.md) of open bare Web-url's, zodat deze door Azure machine learning toegankelijk zijn.

### <a name="use-the-sdk"></a>De SDK gebruiken

Gegevens sets maken op basis van een [Azure-gegevens opslag](how-to-access-data.md) met behulp van de PYTHON-SDK:

1. Controleer of u `contributor` of `owner` toegang hebt tot het geregistreerde Azure-gegevens archief.

2. Maak de gegevensset door te verwijzen naar de paden in het gegevens archief.
> [!Note]
> U kunt een gegevensset maken op basis van meerdere paden in meerdere gegevens opslag. Er is geen vaste limiet voor het aantal bestanden of gegevens grootte waaruit u een gegevensset kunt maken. Voor elk gegevenspad worden echter enkele aanvragen verzonden naar de opslag service om te controleren of deze naar een bestand of map verwijst. Deze overhead kan leiden tot slechtere prestaties of storingen. Een gegevensset die verwijst naar één map met 1000 bestanden in, wordt beschouwd als verwijzingen naar één gegevenspad. We raden u aan om een gegevensset te maken die verwijst naar minder dan 100 paden in gegevens opslag voor optimale prestaties.


#### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

U kunt TabularDatasets maken via de SDK of met behulp van Azure Machine Learning Studio. U kunt een tijds tempel opgeven van een kolom in de gegevens of van het pad-patroon waarin de gegevens worden opgeslagen om een time series-eigenschappen in te scha kelen. Met deze specificatie kunt u eenvoudig en efficiënt filteren op tijd.

Gebruik de methode [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) op de klasse `TabularDatasetFactory` om bestanden te lezen in de CSV-of. TSV-indeling en om een niet-geregistreerde TabularDataset te maken. Als u een lees bewerking uitvoert van meerdere bestanden, worden de resultaten samengevoegd in één tabel weergave.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Wanneer u een TabularDataset maakt, worden de kolom gegevens typen standaard automatisch afgeleid. Als de instelde typen niet overeenkomen met uw verwachtingen, kunt u kolom typen opgeven door de volgende code te gebruiken. U kunt ook [meer te weten komen over ondersteunde gegevens typen](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Dummy tekst|Pclass|Name|Seks|Leeftijd|SibSp|Parch|Ticket|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Onwaar|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Waar|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Waar|3|Heikkinen, missen. Laina|vrouwelijk|26,0|0|0|STON/O2. 3101282|7,9250||S

Gebruik de methode [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) op de `TabularDatasetFactory` klasse om te lezen uit Azure SQL database:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

In TabularDatasets kunt u een tijds tempel opgeven van een kolom in de gegevens of van de locatie waar de patroon gegevens voor het pad zijn opgeslagen om een time series-eigenschappen in te scha kelen. Met deze specificatie kunt u eenvoudig en efficiënt filteren op tijd.

Gebruik de methode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) op de klasse`TabularDataset` om de kolom tijds tempel op te geven en filteren op tijd in te scha kelen. Zie voor meer informatie [TABELLAIRE API-demo met een time-out met NOAA-weer gegevens](https://aka.ms/azureml-tsd-notebook).

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

Gebruik de methode [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) op de `FileDatasetFactory`-klasse om bestanden in een wille keurige indeling te laden en een niet-geregistreerde FileDataset te maken:

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Op het Web 
In de volgende stappen en animatie ziet u hoe u een gegevensset maakt in Azure Machine Learning Studio, https://ml.azure.com.

![Een gegevensset maken met de gebruikers interface](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Een gegevensset maken in Studio:
1. Meld u aan bij https://ml.azure.com.
1. Selecteer **gegevens sets** in het gedeelte **assets** van het linkerdeel venster. 
1. Selecteer **gegevensset maken** om de bron van uw gegevensset te kiezen. Deze bron kan lokale bestanden, een gegevens opslag of een open bare URL zijn.
1. Selecteer een **tabel** of **bestand** voor het type gegevensset.
1. Selecteer **volgende** om de **instellingen en de voor beeld**-, **schema** -en **gegevens** formulieren te controleren. ze worden op intelligente wijze ingevuld op basis van het bestands type. Gebruik deze formulieren om uw selecties te controleren en uw gegevensset verder te configureren voordat u deze hebt gemaakt.  
1. Selecteer **maken** om het maken van de gegevensset te volt ooien.

## <a name="register-datasets"></a>Gegevens sets registreren

Als u het aanmaak proces wilt volt ooien, registreert u uw gegevens sets met een werk ruimte. Gebruik de methode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) om gegevens sets te registreren bij uw werk ruimte om ze te delen met anderen en ze opnieuw te gebruiken in verschillende experimenten:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Gegevens sets die zijn gemaakt via Azure Machine Learning Studio worden automatisch geregistreerd in de werk ruimte.

## <a name="create-datasets-with-azure-open-datasets"></a>Gegevens sets maken met Azure open gegevens sets

[Azure open gegevens sets](https://azure.microsoft.com/services/open-datasets/) zijn alle open bare gegevens sets die u kunt gebruiken om scenario's met specifieke functies toe te voegen aan Machine Learning oplossingen voor nauw keurigere modellen. Gegevens sets bevatten informatie over openbaar domein voor weer, telling, feest dagen, open bare veiligheid en locatie die u helpen bij het trainen van machine learning modellen en verrijkende voorspellende oplossingen. Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn opgenomen in de SDK en de gebruikers interface van de werk ruimte.

### <a name="use-the-sdk"></a>De SDK gebruiken

Als u gegevens sets met Azure open gegevens sets wilt maken vanuit de SDK, moet u ervoor zorgen dat u het pakket hebt geïnstalleerd met `pip install azureml-opendatasets`. Elke afzonderlijke gegevensset wordt vertegenwoordigd door een eigen klasse in de SDK en bepaalde klassen zijn beschikbaar als een `TabularDataset`, `FileDataset`of beide. Zie de [referentie documentatie](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) voor een volledige lijst met klassen.

De meeste klassen nemen van en retour neren een exemplaar van `TabularDataset`. Voor beelden van deze klassen zijn `PublicHolidays`, `BostonSafety`en `UsPopulationZip`. Als u een `TabularDataset` van deze typen klassen wilt maken, gebruikt u de constructor zonder argumenten. Wanneer u een gegevensset registreert die is gemaakt op basis van geopende gegevens sets, worden er geen gegevens direct gedownload, maar worden de gegevens later weer gegeven wanneer u hierom wordt gevraagd (bijvoorbeeld tijdens training) vanuit een centrale opslag locatie. 

```python
from azureml.opendatasets import UsPopulationZip

tabular_dataset = UsPopulationZip()
tabular_dataset = tabular_dataset.register(workspace=workspace, name="pop data", description="US population data by zip code")
```

U kunt bepaalde klassen ophalen als een `TabularDataset` of `FileDataset`, zodat u de bestanden rechtstreeks kunt bewerken en/of downloaden. Andere klassen kunnen een gegevensset alleen ophalen met behulp van de functies `get_tabular_dataset()` of `get_file_dataset()`. In het volgende code voorbeeld ziet u enkele voor beelden van deze typen klassen:

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

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

Geregistreerde gegevens sets zijn zowel lokaal als extern toegankelijk op reken clusters, zoals de Azure Machine Learning compute. Gebruik de volgende code om toegang te krijgen tot uw geregistreerde gegevensset voor experimenten en de geregistreerde gegevensset op naam te openen. Standaard retourneert de methode [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) op de klasse `Dataset` de meest recente versie van de gegevensset die is geregistreerd bij de werk ruimte.

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
* Zie de voor beelden van [notitie blokken](https://aka.ms/dataset-tutorial)voor meer informatie over het trainen van gegevensset.
