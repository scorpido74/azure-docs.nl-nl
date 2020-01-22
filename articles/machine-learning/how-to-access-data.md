---
title: Toegang tot gegevens in Azure Storage services
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van data stores om veilig verbinding te maken met Azure Storage-services tijdens de training met Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: 4de8b032bd284ce0a2d6d8fd168b9ea3dd4937d3
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289047"
---
# <a name="access-data-in-azure-storage-services"></a>Toegang tot gegevens in azure Storage-services
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u eenvoudig toegang hebt tot uw gegevens in Azure Storage services via Azure Machine Learning gegevens opslag. Data stores worden gebruikt om verbindings gegevens op te slaan, zoals uw abonnements-ID en Token autorisatie. Wanneer u data stores gebruikt, hebt u toegang tot uw opslag zonder dat u de verbindings gegevens van uw scripts hoeft vast te geven. 

U kunt gegevens opslag maken op basis van [deze Azure Storage-oplossingen](#matrix). Voor niet-ondersteunde opslag oplossingen en voor het opslaan van de kosten voor de verlies van gegevens tijdens machine learning experimenten, raden we u aan om [uw gegevens](#move) naar ondersteunde Azure Storage oplossingen te verplaatsen. 

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig:
- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- Een Azure-opslag account met een [Azure Blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) of een [Azure-bestands share](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een bestaand item via de PYTHON-SDK:

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Ondersteunde service typen voor gegevens opslag

Data stores ondersteunen momenteel het opslaan van verbindings gegevens naar de opslag services die in de volgende matrix worden weer gegeven.

| Type opslag&nbsp; | Type verificatie&nbsp; | [Azure&nbsp;machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;machine&nbsp;Learning&nbsp; python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;machine&nbsp;Learning&nbsp; rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;BLOB&nbsp;opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Accountcode <br> SAS-token | ✓ | ✓ | ✓ |✓
[Bestands&nbsp;share van Azure&nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Accountcode <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| Service-principal| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Service-principal| ✓ | ✓ | ✓ |✓
Azure&nbsp;SQL&nbsp;-data base| SQL-verificatie <br>Service-principal| ✓ | ✓ | ✓ |✓
Azure&nbsp;PostgreSQL | SQL-verificatie| ✓ | ✓ | ✓ |✓
Azure&nbsp;data base-&nbsp;voor&nbsp;MySQL | SQL-verificatie|  | ✓ | ✓ |✓
Databricks&nbsp;bestand&nbsp;systeem| Geen verificatie | | ✓* | ✓ * |✓* 

\* alleen ondersteund in lokale Compute-doel scenario's

### <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden u aan een gegevens opslag voor een Azure Blob-container te maken.  
Zowel Standard-als Premium-opslag zijn beschikbaar voor blobs. Hoewel Premium-opslag duurder is, kunnen de snellere doorvoer snelheden de snelheid van uw trainings uitvoeringen verbeteren, met name als u traint voor een grote gegevensset. Voor informatie over de kosten van opslag accounts raadpleegt u de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

Wanneer u een werk ruimte maakt, worden er automatisch een Azure Blob-container en een Azure-bestands share geregistreerd in de werk ruimte. Ze hebben respectievelijk de naam `workspaceblobstore` en `workspacefilestore`. Ze slaan de verbindings gegevens voor de BLOB-container en de bestands share op die zijn ingericht in het opslag account dat aan de werk ruimte is gekoppeld. De `workspaceblobstore`-container wordt ingesteld als de standaard gegevens opslag.

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Gegevens opslag maken en registreren

Wanneer u een Azure Storage-oplossing registreert als een gegevens opslag, maakt en registreert u die gegevens opslag automatisch aan een specifieke werk ruimte. U kunt gegevens opslag voor een werk ruimte maken en registreren met behulp van de python-SDK of Azure Machine Learning Studio.

>[!IMPORTANT]
> Als onderdeel van het proces voor het maken en registreren van de huidige gegevens opslag heeft Azure Machine Learning gecontroleerd of de door de gebruiker aangelegde principal (gebruikers naam, Service-Principal of SAS-token) toegang heeft tot de onderliggende opslag service. 
<br>
Voor Azure Data Lake Storage data stores van 1 en 2 wordt deze validatie echter later uitgevoerd wanneer methoden voor gegevens toegang, zoals [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) of [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) , worden aangeroepen. 

### <a name="python-sdk"></a>Python SDK

Alle registratie methoden bevinden zich op de klasse [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) en hebben het formulier `register_azure_*`.

U vindt de informatie die u nodig hebt om de `register()` methode te vullen met behulp van de [Azure Portal](https://portal.azure.com):

1. Selecteer **opslag accounts** in het linkerdeel venster en kies het opslag account dat u wilt registreren. 
2. Ga naar de pagina **overzicht** voor meer informatie, zoals de account naam, de container en de naam van de bestands share. Ga naar **toegangs sleutels** in het deel venster **instellingen** voor verificatie-informatie, zoals account sleutel of SAS-token. 

> [!IMPORTANT]
> Als uw opslag account zich in een virtueel netwerk bevindt, wordt alleen het maken van een Azure Blob-gegevens opslag ondersteund. Als u uw werk ruimte toegang wilt verlenen tot uw opslag account, stelt u de para meter `grant_workspace_access` in op `True`.

In de volgende voor beelden ziet u hoe u een Azure Blob-container, een Azure-bestands share en Azure Data Lake Storage generatie 2 kunt registreren als gegevens opslag. Raadpleeg voor andere opslag Services de [referentie documentatie voor de `register_azure_*`-methoden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods).

#### <a name="blob-container"></a>Blobcontainer

Gebruik [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)om een Azure Blob-container als gegevens opslag te registreren.

Met de volgende code wordt de `blob_datastore_name` gegevens opslag voor de `ws`-werk ruimte gemaakt en geregistreerd. Deze Data Store heeft toegang tot de `my-container-name` BLOB-container op het `my-account-name` Storage-account met behulp van de meegeleverde account sleutel.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Bestandsshare

Gebruik [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)om een Azure-bestands share als een gegevens opslag te registreren. 

Met de volgende code wordt de `file_datastore_name` gegevens opslag voor de `ws`-werk ruimte gemaakt en geregistreerd. Deze Data Store heeft toegang tot de `my-fileshare-name`-bestands share op het `my-account-name` Storage-account met behulp van de meegeleverde account sleutel.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generatie 2

Gebruik [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) voor het registreren van een Azure data Lake Storage Generation 2 (ADLS gen 2) om het opslaan van referenties die zijn verbonden met een Azure DataLake gen 2-opslag met Service-Principal-machtigingen. Meer informatie over [toegangs beheer dat is ingesteld voor ADLS gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Met de volgende code wordt de `adlsgen2_datastore_name` gegevens opslag voor de `ws`-werk ruimte gemaakt en geregistreerd. Met deze data store krijgt u toegang tot het bestands systeem `test` op het `account_name` Storage-account met behulp van de gegeven referenties voor de Service-Principal.

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

Maak een nieuwe gegevens opslag in een paar stappen in Azure Machine Learning studio:

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gegevens opslag** in het linkerdeel venster onder **beheren**.
1. Selecteer **+ nieuwe gegevens opslag**.
1. Vul het formulier in voor een nieuwe gegevens opslag. Het formulier wordt intelligent bijgewerkt op basis van uw selecties voor Azure Storage type en verificatie type.
  
U kunt de informatie vinden die u nodig hebt om het formulier op de [Azure Portal](https://portal.azure.com)in te vullen. Selecteer **opslag accounts** in het linkerdeel venster en kies het opslag account dat u wilt registreren. De **overzichts** pagina bevat informatie zoals de account naam, container en naam van de bestands share. Ga voor verificatie-items, zoals account sleutel of SAS-token, naar **account sleutels** in het deel venster **instellingen** .

In het volgende voor beeld ziet u hoe het formulier eruitziet wanneer u een Azure Blob-gegevens opslag maakt: 
    
![Formulier voor een nieuwe gegevens opslag](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Gegevens opslag ophalen uit uw werk ruimte

Als u een specifieke gegevens opslag die in de huidige werk ruimte is geregistreerd wilt ophalen, gebruikt u de statische methode [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) op de `Datastore`-klasse:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Als u de lijst met gegevens opslag die is geregistreerd met een bepaalde werk ruimte wilt ophalen, kunt u de eigenschap [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) gebruiken voor een werkruimte object:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Als u de standaard gegevens opslag van de werk ruimte wilt ophalen, gebruikt u deze regel:

```Python
datastore = ws.get_default_datastore()
```

Als u een andere standaard gegevens opslag voor de huidige werk ruimte wilt definiëren, gebruikt u de methode [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) voor het object werk ruimte:

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Uploaden en downloaden van gegevens

De methoden [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) en [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) die in de volgende voor beelden worden beschreven, zijn specifiek voor de klassen [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) en [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Uploaden

Een map of afzonderlijke bestanden naar het gegevens archief uploaden met behulp van de python-SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

Met de para meter `target_path` geeft u de locatie in de bestands share (of BLOB-container) op die moet worden geüpload. De standaard instelling is `None`, zodat de gegevens naar de hoofdmap worden geüpload. Als `overwrite=True`, worden alle bestaande gegevens op `target_path` overschreven.

U kunt ook een lijst met afzonderlijke bestanden uploaden naar het gegevens archief via de `upload_files()` methode.

### <a name="download"></a>Download

Gegevens uit een gegevens opslag naar uw lokale bestands systeem downloaden:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

De para meter `target_path` is de locatie van de lokale map waarnaar de gegevens moeten worden gedownload. Geef het pad naar de map in de bestandsshare (of de blob-container) om te downloaden, geeft dat pad op naar `prefix`. Als `prefix` `None`is, wordt de inhoud van de bestands share (of BLOB-container) gedownload.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Toegang tot uw gegevens tijdens de training

Als u wilt werken met gegevens in uw gegevens opslag of als u uw gegevens wilt inpakken in een verbruikt object voor machine learning taken, zoals training, [maakt u een Azure machine learning-gegevensset](how-to-create-register-datasets.md). Gegevens sets bieden functies die tabellaire gegevens laden in een Panda of Spark-data frame. Gegevens sets bieden ook de mogelijkheid om bestanden te downloaden of te koppelen van een wille keurige indeling vanuit Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. Meer [informatie over het trainen van gegevens sets](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Toegang tot de bron code tijdens de training

Azure Blob-opslag heeft hogere doorvoer snelheden dan een Azure-bestands share en schaalt naar een groot aantal taken die parallel worden gestart. Daarom is het raadzaam om uw uitvoeringen te configureren voor het gebruik van Blob-opslag voor het overdragen van broncode bestanden.

In het volgende code voorbeeld wordt de uitvoerings configuratie opgegeven die BLOB-gegevens opslag moet gebruiken voor bron code overdrachten:

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Toegang tot gegevens tijdens de Score

Azure Machine Learning biedt verschillende manieren om uw modellen te gebruiken voor het scoren van punten. Sommige van deze methoden bieden geen toegang tot gegevens opslag. Gebruik de volgende tabel om inzicht te krijgen in de methoden waarmee u toegang hebt tot gegevens opslag in de Score:

| Methode | Toegang tot Data Store | Beschrijving |
| ----- | :-----: | ----- |
| [Batch-voor spelling](how-to-use-parallel-run-step.md) | ✔ | Maak voor spellingen voor grote hoeveel heden gegevens asynchroon. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen als een webservice. |
| [Module Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen om apparaten te IoT Edge. |

In situaties waarin de SDK geen toegang biedt tot gegevens opslag, kunt u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de data. De [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een client bibliotheek die u kunt gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Gegevens verplaatsen naar ondersteunde Azure Storage oplossingen

Azure Machine Learning ondersteunt het openen van gegevens vanuit Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. Als u niet-ondersteunde opslag gebruikt, wordt u aangeraden uw gegevens naar ondersteunde Azure Storage oplossingen te verplaatsen met behulp van [Azure Data Factory en deze stappen](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Het verplaatsen van gegevens naar ondersteunde opslag kan u helpen bij het opslaan van de kosten voor de uitvoer van gegevens tijdens machine learning experimenten. 

Azure Data Factory biedt een efficiënte en robuuste gegevens overdracht met meer dan 80 vooraf ontwikkelde connectors zonder extra kosten. Deze connectors zijn onder andere Azure Data Services, on-premises gegevens bronnen, Amazon S3 en Redshift en Google BigQuery.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [Een model te trainen](how-to-train-ml-models.md)
* [Een model implementeren](how-to-deploy-and-where.md)
