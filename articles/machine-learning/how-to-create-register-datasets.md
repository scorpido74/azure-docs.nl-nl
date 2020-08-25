---
title: Azure Machine Learning data sets maken voor toegang tot gegevens
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van Azure Machine Learning gegevens sets voor het openen van de machine learning-experimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: ae8dcc02618220750e2d15d815da4b36ea64da2d
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782189"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-gegevenssets maken

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u Azure Machine Learning gegevens sets maakt om toegang te krijgen tot gegevens voor uw lokale of externe experimenten. Zie het artikel over [beveiligde toegang](concept-data.md#data-workflow) als u wilt weten waar gegevens sets passen in de algehele werk stroom van Azure machine learning Data Access.

Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron, samen met een kopie van de bijbehorende metagegevens. Omdat de gegevens zich op de bestaande locatie blijven, ontstaan er geen extra opslag kosten en wordt de integriteit van uw gegevens bronnen niet bedreigd. Ook gegevens sets worden geëvalueerd in vertraagd, die hulp middelen in de snelheid van werk stroom prestaties. U kunt gegevens sets maken op basis van gegevens opslag, open bare Url's en [Azure open gegevens sets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Met Azure Machine Learning gegevens sets kunt u het volgende doen:

* Bewaar één kopie van de gegevens in uw opslag, waarnaar wordt verwezen door gegevens sets.

* Naadloos toegang krijgen tot gegevens tijdens model training zonder dat u zich zorgen hoeft te maken over verbindings reeksen of gegevens paden. Meer [informatie over het trainen van gegevens sets](how-to-train-with-datasets.md).

* Gegevens delen en samen werken met andere gebruikers.

## <a name="prerequisites"></a>Vereisten

Als u gegevens sets wilt maken en gebruiken, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md).

* De [Azure machine learning SDK voor python is geïnstalleerd](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), waaronder het pakket met de azureml-gegevens sets.

    * Maak een [Azure machine learning Compute-exemplaar](concept-compute-instance.md#managing-a-compute-instance), een volledig geconfigureerde en beheerde ontwikkel omgeving met daarin geïntegreerde notebooks en de SDK die al is geïnstalleerd.

    **OF**

    * Werk met uw eigen Jupyter-notebook en installeer de SDK zelf met [deze instructies](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Sommige dataset-klassen hebben afhankelijkheden van het pakket voor [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Dit is alleen compatibel met 64-bits python. Voor Linux-gebruikers worden deze klassen alleen ondersteund in de volgende distributies: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) en CentOS (7).

## <a name="compute-size-guidance"></a>Richt lijn voor reken capaciteit

Wanneer u een gegevensset maakt, controleert u de verwerkings kracht van de reken capaciteit en de grootte van uw gegevens in het geheugen. De grootte van uw gegevens in de opslag is niet hetzelfde als de grootte van de gegevens in een data frame. Gegevens in CSV-bestanden kunnen bijvoorbeeld tot 10x worden uitgebreid in een data frame, zodat een CSV-bestand van 1 GB 10 GB kan worden in een data frame. 

Als uw gegevens zijn gecomprimeerd, kunnen ze verder worden uitgebreid. 20 GB aan relatief sparse gegevens die zijn opgeslagen in de gecomprimeerde Parquet-indeling kunnen worden uitgebreid naar ~ 800 GB in het geheugen. Aangezien Parquet-bestanden gegevens in een kolom indeling opslaan en u alleen de helft van de kolommen nodig hebt, hoeft u niet meer dan ~ 400 GB in het geheugen te laden.

Meer [informatie over het optimaliseren van gegevens verwerking in azure machine learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Typen gegevenssets

Er zijn twee typen gegevensset, op basis van de manier waarop gebruikers ze in training gebruiken. FileDatasets en TabularDatasets. Beide typen kunnen worden gebruikt in Azure Machine Learning trainings werk stromen met inschattingen, AutoML, hyperDrive en pijp lijnen. 

### <a name="filedataset"></a>FileDataset

Een [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. Als uw gegevens al zijn gereinigd en klaar zijn om te worden gebruikt in trainings experimenten, kunt u de bestanden [downloaden of koppelen](how-to-train-with-datasets.md#mount-vs-download) aan uw Compute als een FileDataset-object. 

We raden FileDatasets aan voor uw machine learning-werk stromen, aangezien de bron bestanden een wille keurige indeling hebben, waardoor een breder scala van machine learning scenario's mogelijk is, waaronder diep gaande lessen.

Een FileDataset maken met de [python-SDK](#create-a-filedataset) of de [Azure machine learning Studio](#create-datasets-in-the-studio)

### <a name="tabulardataset"></a>TabularDataset

Een [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) vertegenwoordigt gegevens in tabel vorm door het bestand of de lijst met bestanden te parseren. Dit biedt u de mogelijkheid om de gegevens in een Panda of Spark-data frame te realiseren, zodat u kunt werken met vertrouwde gegevens voorbereiding en trainings bibliotheken zonder dat u uw notebook hoeft te verlaten. U kunt een `TabularDataset` object maken van. CSV-,. tsv-,. Parquet-,. json-bestanden en uit [SQL-query resultaten](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Met TabularDatasets kunt u een tijds tempel opgeven van een kolom in de gegevens of van de locatie waar de patroon gegevens voor het pad zijn opgeslagen om een time series-eigenschappen in te scha kelen. Met deze specificatie kunt u eenvoudig en efficiënt filteren op tijd. Zie voor een voor beeld [Time Series-gerelateerde API-demo met NOAA-weer gegevens](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Maak een TabularDataset met [de python-SDK](#create-a-tabulardataset) of [Azure machine learning Studio](#create-datasets-in-the-studio).

>[!NOTE]
> AutoML-werk stromen die zijn gegenereerd via de Azure Machine Learning Studio ondersteunen momenteel alleen TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Toegang tot gegevens sets in een virtueel netwerk

Als uw werk ruimte zich in een virtueel netwerk bevindt, moet u de gegevensset configureren om validatie over te slaan. Voor meer informatie over het gebruik van data stores en gegevens sets in een virtueel netwerk, raadpleegt u [netwerk isolatie tijdens de training & afwijzen met persoonlijke virtuele netwerken](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>Gegevens sets maken via de SDK

 Gegevens sets moeten worden gemaakt op basis van paden in [Azure-data stores](how-to-access-data.md) of open bare Web-url's, zodat deze door Azure machine learning toegankelijk zijn. 

Gegevens sets maken op basis van een [Azure-gegevens archief](how-to-access-data.md) met de PYTHON-SDK:

1. Controleer of u toegang hebt tot `contributor` `owner` het geregistreerde Azure-gegevens archief.

2. Maak de gegevensset door te verwijzen naar de paden in het gegevens archief. U kunt een gegevensset maken op basis van meerdere paden in meerdere gegevens opslag. Er is geen vaste limiet voor het aantal bestanden of gegevens grootte waaruit u een gegevensset kunt maken. 

> [!Note]
> Voor elk gegevenspad worden enkele aanvragen verzonden naar de opslag service om te controleren of deze naar een bestand of map verwijst. Deze overhead kan leiden tot slechtere prestaties of storingen. Een gegevensset die verwijst naar één map met 1000 bestanden in, wordt beschouwd als verwijzingen naar één gegevenspad. U kunt het beste een gegevensset maken die verwijst naar minder dan 100 paden in gegevens opslag voor optimale prestaties.

### <a name="create-a-filedataset"></a>Een FileDataset maken

Gebruik de [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) methode in de- `FileDatasetFactory` klasse om bestanden in een wille keurige indeling te laden en een niet-geregistreerde FileDataset te maken. 

Als uw opslag zich achter een virtueel netwerk of een firewall bevindt, stelt u de para meter `validate=False` in uw `from_files()` methode in. Hiermee wordt de eerste validatie stap omzeild en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. Meer informatie over het gebruik [van data stores en gegevens sets in een virtueel netwerk](how-to-enable-virtual-network.md#use-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>Een TabularDataset maken

Gebruik de [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) methode in de `TabularDatasetFactory` klasse om bestanden te lezen in de CSV-of. TSV-indeling en om een niet-geregistreerde TabularDataset te maken. Als u een lees bewerking uitvoert van meerdere bestanden, worden de resultaten samengevoegd in één tabel weergave. 

Als uw opslag zich achter een virtueel netwerk of een firewall bevindt, stelt u de para meter `validate=False` in uw `from_delimited_files()` methode in. Hiermee wordt de eerste validatie stap omzeild en zorgt u ervoor dat u uw gegevensset kunt maken op basis van deze beveiligde bestanden. Meer informatie over het gebruik [van data stores en gegevens sets in een virtueel netwerk](how-to-enable-virtual-network.md#use-datastores-and-datasets).

Met de volgende code worden de bestaande werk ruimte en de gewenste gegevens opslag op naam opgehaald. En vervolgens worden de gegevens opslag en bestands locaties door gegeven aan de `path` para meter om een nieuwe TabularDataset te maken `weather_ds` .

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

Wanneer u een TabularDataset maakt, worden de kolom gegevens typen standaard automatisch afgeleid. Als de instelde typen niet overeenkomen met uw verwachtingen, kunt u kolom typen opgeven door de volgende code te gebruiken. De para meter `infer_column_type` is alleen van toepassing op gegevens sets die zijn gemaakt van bestanden met scheidings tekens. Meer [informatie over ondersteunde gegevens typen](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|TabIndex|PassengerId|Dummy tekst|Pclass|Name|Seks|Ouderdom|SibSp|Parch|Ticket|Tickets|Hand|Ingeschepend
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Niet waar|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Waar|1|Cumings, Mevr. John Bradley (Florence Briggs th...|vrouwelijk|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Waar|3|Heikkinen, missen. Laina|vrouwelijk|26,0|0|0|STON/O2. 3101282|7,9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>Een gegevensset maken op basis van Pandas data frame

Als u een TabularDataset wilt maken van een in geheugen Panda data frame, schrijft u de gegevens naar een lokaal bestand, zoals een CSV, en maakt u uw gegevensset vanuit dat bestand. De volgende code toont deze werk stroom.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

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

## <a name="register-datasets"></a>Gegevens sets registreren

Als u het aanmaak proces wilt volt ooien, registreert u uw gegevens sets met een werk ruimte. Gebruik de [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) methode voor het registreren van gegevens sets met uw werk ruimte om ze te delen met anderen en ze opnieuw te gebruiken voor experimenten in uw werk ruimte:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>Gegevens sets maken in de Studio
In de volgende stappen en animatie ziet u hoe u een gegevensset maakt in [Azure machine learning Studio](https://ml.azure.com).

> [!Note]
> Gegevens sets die zijn gemaakt via Azure Machine Learning Studio, worden automatisch geregistreerd bij de werk ruimte.

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

## <a name="create-datasets-with-azure-open-datasets"></a>Gegevens sets maken met Azure open gegevens sets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) zijn samengestelde openbare gegevenssets die u kunt gebruiken om scenariospecifieke functies toe te voegen aan machine learning-oplossingen voor nauwkeurigere modellen. Gegevens sets bevatten informatie over openbaar domein voor weer, telling, feest dagen, open bare veiligheid en locatie die u helpen bij het trainen van machine learning modellen en verrijkende voorspellende oplossingen. Open gegevens sets bevinden zich in de Cloud op Microsoft Azure en zijn opgenomen in zowel de SDK als de Studio.

Meer informatie over het maken [van Azure machine learning gegevens sets van Azure open gegevens sets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Trainen met gegevenssets

Gebruik uw gegevens sets in uw machine learning experimenten voor trainings ML-modellen. [Meer informatie over het trainen van gegevens sets](how-to-train-with-datasets.md)

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

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het trainen van gegevens sets](how-to-train-with-datasets.md).
* Gebruik automatische machine learning om [met TabularDatasets te trainen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Zie de voor beelden van [notitie blokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)voor meer informatie over het trainen van gegevensset.
