---
title: Toegang tot gegevens in Azure Storage services
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van data stores om veilig verbinding te maken met Azure Storage-services tijdens de training met Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540943"
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

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Gegevens opslag maken en registreren

Wanneer u een Azure Storage-oplossing registreert als een gegevens opslag, maakt u het gegevens archief automatisch in een specifieke werk ruimte. U kunt gegevens opslag voor een werk ruimte maken en registreren met behulp van de python-SDK of Azure Machine Learning Studio.

### <a name="python-sdk"></a>Python SDK

Alle registratie methoden bevinden zich op de klasse [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) en hebben het formulier `register_azure_*`.

U vindt de informatie die u nodig hebt om de `register()` methode te vullen met behulp van de [Azure Portal](https://portal.azure.com):

1. Selecteer **opslag accounts** in het linkerdeel venster en kies het opslag account dat u wilt registreren. 
2. Ga naar de pagina **overzicht** voor meer informatie, zoals de account naam, de container en de naam van de bestands share. Ga naar **toegangs sleutels** in het deel venster **instellingen** voor verificatie-informatie, zoals account sleutel of SAS-token. 

> [!IMPORTANT]
> Als uw opslag account zich in een virtueel netwerk bevindt, wordt alleen het maken van een Azure Blob-gegevens opslag ondersteund. Als u uw werk ruimte toegang wilt verlenen tot uw opslag account, stelt u de para meter `grant_workspace_access` in op `True`.

In de volgende voor beelden ziet u hoe u een Azure Blob-container, een Azure-bestands share en Azure SQL-gegevens registreert als een gegevens opslag.

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

#### <a name="sql-data"></a>SQL-gegevens

Gebruik [register_azure_sql_database ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-) voor een Azure SQL-gegevens opslag om een referentie gegevens opslag te registreren die is verbonden met een Azure-SQL database met SQL-verificatie of Service-Principal-machtigingen. 

Registreren via SQL-verificatie:

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

Registreren via een Service-Principal:

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden u aan een Azure Blob-container te vinden. Zowel Standard-als Premium-opslag zijn beschikbaar voor blobs. Hoewel Premium-opslag duurder is, kunnen de snellere doorvoer snelheden de snelheid van uw trainings uitvoeringen verbeteren, met name als u traint voor een grote gegevensset. Voor informatie over de kosten van opslag accounts raadpleegt u de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

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

Wanneer u een werk ruimte maakt, worden er automatisch een Azure Blob-container en een Azure-bestands share geregistreerd in de werk ruimte. Ze hebben respectievelijk de naam `workspaceblobstore` en `workspacefilestore`. Ze slaan de verbindings gegevens voor de BLOB-container en de bestands share op die zijn ingericht in het opslag account dat aan de werk ruimte is gekoppeld. De `workspaceblobstore`-container wordt ingesteld als de standaard gegevens opslag.

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

> [!IMPORTANT]
> We raden u aan om [Azure machine learning gegevens sets](how-to-create-register-datasets.md) te gebruiken voor toegang tot uw gegevens in de training. Gegevens sets bieden functies die tabellaire gegevens laden in een Panda of Spark-data frame. Gegevens sets bieden ook de mogelijkheid om bestanden te downloaden of te koppelen van een wille keurige indeling vanuit Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. Meer [informatie over het trainen van gegevens sets](how-to-train-with-datasets.md).

De volgende tabel bevat de methoden die het reken doel vertellen hoe de gegevens opslag wordt gebruikt tijdens uitvoeringen: 

Manier|Methode|Beschrijving|
----|-----|--------
Koppelen| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Gebruiken om de gegevens opslag te koppelen aan het berekenings doel. Wanneer de gegevens opslag is gekoppeld, worden alle bestanden van uw gegevens opslag toegankelijk gemaakt voor uw reken doel.
Download|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Gebruik om de inhoud van uw gegevens archief te downloaden naar de locatie die is opgegeven door `path_on_compute`. <br><br> Deze down load gebeurt vóór de uitvoering.
Uploaden|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Gebruik dit om een bestand te uploaden vanaf de locatie die is opgegeven door `path_on_compute` naar uw gegevens opslag. <br><br> Deze upload vindt plaats na de uitvoering.

Als u wilt verwijzen naar een specifieke map of bestand in uw gegevens opslag en deze beschikbaar wilt maken op het reken doel, gebruikt u de [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) methode Data Store:

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Alle opgegeven `datastore`-of `datastore.path`-objecten worden omgezet in de naam van een omgevings variabele van de indeling `"$AZUREML_DATAREFERENCE_XXXX"`. De waarde van deze naam vertegenwoordigt het pad voor koppelen/downloaden van het reken doel. Het Data Store-pad op het reken doel is mogelijk niet hetzelfde als het pad voor het uitvoeren van het trainings script.

### <a name="examples"></a>Voorbeelden 

We raden u aan de [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) -klasse te gebruiken voor toegang tot gegevens tijdens de training. 

De variabele `script_params` is een woorden lijst die para meters bevat om te `entry_script`. Gebruik deze functie om in een gegevens Archief door te geven en te beschrijven hoe gegevens beschikbaar worden gesteld op het reken doel. Meer informatie vindt u in de [end-to-end zelf studie](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

U kunt ook een lijst met gegevens records door geven aan de `inputs`-para meter van de `Estimator`-constructor om gegevens te koppelen of kopiëren naar of van uw gegevens opslag. Dit code voorbeeld:
* Hiermee wordt alle inhoud in `datastore1` naar het reken doel gedownload voordat het `train.py` trainings script wordt uitgevoerd.
* Hiermee wordt de `'./foo'` map in `datastore2` naar het reken doel gedownload voordat `train.py` wordt uitgevoerd.
* Hiermee wordt het `'./bar.pkl'` bestand geüpload van het reken doel naar `datastore3` nadat het script is uitgevoerd.

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
Als u liever een `RunConfig`-object gebruikt voor trainingen, moet u een [`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) -object instellen. 

De volgende code laat zien hoe u met een `DataReference`-object in een schattings pijplijn kunt werken. Zie [Dit notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)voor het volledige voor beeld.

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Compute-en Data Store-matrix

Data stores ondersteunen momenteel het opslaan van verbindings gegevens naar de opslag services die in de volgende matrix worden weer gegeven. Deze matrix bevat de beschik bare functies voor gegevens toegang voor de verschillende reken doelen en Data Store-scenario's. Meer [informatie over de compute-doelen voor Azure machine learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Computing|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokaal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning-Computing |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [machine learning pijp lijnen](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [machine learning pijp lijnen](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Virtuele machines               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Gegevensoverdracht                  |[Machine Learning pijp lijnen](concept-ml-pipelines.md)                                               |N/A                                           |[Machine Learning pijp lijnen](concept-ml-pipelines.md)            |[Machine Learning pijp lijnen](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[Machine Learning pijp lijnen](concept-ml-pipelines.md)                                              |N/A                                           |[Machine Learning pijp lijnen](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[Machine Learning pijp lijnen](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure Data Lake Analytics       |N/A                                           |N/A                                           |[Machine Learning pijp lijnen](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Er zijn mogelijk scenario's waarin zeer iteratieve, grote gegevens processen sneller worden uitgevoerd met behulp van `as_download()` in plaats van `as_mount()`. U kunt dit experimenteel valideren.

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
| [Batch-voor spelling](how-to-run-batch-predictions.md) | ✔ | Maak voor spellingen voor grote hoeveel heden gegevens asynchroon. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen als een webservice. |
| [Module Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen om apparaten te IoT Edge. |

In situaties waarin de SDK geen toegang biedt tot gegevens opslag, kunt u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de data. De [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een client bibliotheek die u kunt gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Gegevens verplaatsen naar ondersteunde Azure Storage oplossingen

Azure Machine Learning ondersteunt het openen van gegevens vanuit Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. Als u niet-ondersteunde opslag gebruikt, wordt u aangeraden uw gegevens naar ondersteunde Azure Storage oplossingen te verplaatsen met behulp van Azure Data Factory. Het verplaatsen van gegevens naar ondersteunde opslag kan u helpen bij het opslaan van de kosten voor de uitvoer van gegevens tijdens machine learning experimenten. 

Azure Data Factory biedt een efficiënte en robuuste gegevens overdracht met meer dan 80 vooraf ontwikkelde connectors zonder extra kosten. Deze connectors zijn onder andere Azure Data Services, on-premises gegevens bronnen, Amazon S3 en Redshift en Google BigQuery. [Volg de stapsgewijze hand leiding om uw gegevens te verplaatsen met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Volgende stappen

* [Een model te trainen](how-to-train-ml-models.md)
* [Een model implementeren](how-to-deploy-and-where.md)
