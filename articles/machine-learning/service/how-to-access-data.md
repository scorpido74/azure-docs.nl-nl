---
title: Toegang tot gegevens in azure Storage-services
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van data stores voor toegang tot Azure Storage-services tijdens de training met Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 9de3232bcd7908f775dadff4dc584f2a687b0c68
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299758"
---
# <a name="access-data-in-azure-storage-services"></a>Toegang tot gegevens in azure Storage-services

In dit artikel leert u hoe u eenvoudig toegang hebt tot uw gegevens in azure Storage-services via Azure Machine Learning gegevens opslag. Data stores worden gebruikt om verbindings gegevens op te slaan, zoals uw abonnements-ID en Token autorisatie. Door data stores te gebruiken, kunt u toegang krijgen tot uw opslag zonder dat u de verbindings gegevens van uw scripts hard kunt coderen.

In deze procedure worden voor beelden van de volgende taken weer gegeven:
* [Gegevens opslag registreren](#access)
* [Gegevens opslag ophalen uit de werk ruimte](#get)
* [Gegevens uploaden en downloaden met behulp van data stores](#up-and-down)
* [Toegang tot gegevens tijdens de training](#train)

## <a name="prerequisites"></a>Vereisten

Voor het gebruik van gegevensopslag, moet u eerst een [werkruimte](concept-workspace.md).

Begin door een van beide [het maken van een nieuwe werkruimte](how-to-manage-workspace.md) of bij het ophalen van een bestaande:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>Gegevens opslag registreren

Alle registratie methoden bevinden zich op [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) de klasse en hebben het formulier register_azure_ *.

In de volgende voor beelden ziet u hoe u een Azure Blob-container of een Azure-bestands share als een gegevens opslag registreert.

+ Gebruik voor een **Azure Blob container-gegevens opslag**[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ [Gebruik`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)voor een **Azure file share-gegevens opslag**. Bijvoorbeeld: 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden Azure Blob-container aan. Zowel Standard-als Premium-opslag zijn beschikbaar voor blobs. Hoewel we nog duurder worden, worden Premium-opslag voorgesteld door snellere doorvoer snelheden die de snelheid van uw trainings uitvoeringen kunnen verbeteren, met name als u traint voor een grote gegevensset. Zie de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) voor informatie over de kosten van het opslag account.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Gegevens opslag ophalen uit uw werk ruimte

Als u een specifieke gegevens opslag die in de huidige werk ruimte is [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) geregistreerd wilt ophalen, gebruikt u de statische methode voor de klasse Data Store:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Als u de lijst met gegevens opslag die is geregistreerd met een bepaalde werk ruimte wilt ophalen [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) , kunt u de eigenschap gebruiken voor een werkruimte object:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Wanneer u een werk ruimte maakt, worden een Azure Blob-container en een Azure-bestands share geregistreerd in `workspaceblobstore` de `workspacefilestore` werk ruimte met de naam en respectievelijk. Ze slaan de verbindings gegevens van de BLOB-container en de bestands share op die zijn ingericht in het opslag account dat aan de werk ruimte is gekoppeld. De `workspaceblobstore` wordt ingesteld als de standaard gegevens opslag.

Ophalen van de werkruimte standaard gegevensopslag:

```Python
datastore = ws.get_default_datastore()
```

Als u een andere standaard gegevens opslag voor de huidige werk ruimte [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) wilt definiëren, gebruikt u de methode voor het object werk ruimte:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Gegevens uploaden & downloaden
De [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) methoden [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) en die in de volgende voor beelden worden beschreven, zijn specifiek voor en worden identiek voor de klassen [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) en [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

### <a name="upload"></a>Uploaden

 Een map of afzonderlijke bestanden uploaden naar de gegevensopslag met behulp van de Python-SDK.

Het uploaden van een map naar een gegevensarchief `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

De `target_path` para meter geeft de locatie in de bestands share (of BLOB-container) op die moet worden geüpload. Wordt standaard `None`, in welk geval de gegevens wordt het geüpload naar de hoofdmap. Wanneer `overwrite=True` een bestaande `target_path` gegevens worden overschreven.

Of upload een lijst met afzonderlijke bestanden naar het gegevens archief via `upload_files()` de-methode.

### <a name="download"></a>Downloaden

Gegevens uit een gegevensarchief op dezelfde manier downloaden naar uw lokale bestandssysteem.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

De `target_path` para meter is de locatie van de lokale map waarnaar de gegevens moeten worden gedownload. Geef het pad naar de map in de bestandsshare (of de blob-container) om te downloaden, geeft dat pad op naar `prefix`. Als `prefix` is `None`, de inhoud van uw bestandsshare (of de blob-container) wordt gedownload.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Toegang tot uw gegevens tijdens de training

> [!IMPORTANT]
> Het gebruik van [Azure machine learning gegevens sets (preview)](how-to-create-register-datasets.md) is de nieuwe aanbevolen manier om toegang te krijgen tot uw gegevens in training. Gegevens sets bieden functies waarmee tabellaire gegevens worden geladen in Panda-of Spark-data frame en de mogelijkheid om bestanden te downloaden of te koppelen van een wille keurige indeling vanuit Azure Blob, Azure file, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Meer informatie over [het trainen van gegevens sets](how-to-train-with-datasets.md).

De volgende tabel bevat de methoden die het reken doel vertellen hoe de gegevens opslag worden gebruikt tijdens de uitvoering. 

Manier|Methode|Description|
----|-----|--------
Koppelen| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Gebruiken om de gegevens opslag te koppelen aan het berekenings doel.
Downloaden|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Gebruik om de inhoud van uw gegevens archief te downloaden naar de locatie `path_on_compute`die is opgegeven door. <br><br> Deze down load gebeurt vóór de uitvoering.
Uploaden|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Gebruiken om een bestand te uploaden vanaf de locatie die `path_on_compute` is opgegeven door naar uw gegevens opslag. <br><br> Deze upload vindt plaats na de uitvoering.

Gebruik de methode Data Store [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) om te verwijzen naar een specifieke map of bestand in uw gegevens opslag en deze beschikbaar te maken op het Compute-doel.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Alle opgegeven `datastore` of `datastore.path` -objecten worden omgezet in de naam van een omgevings `"$AZUREML_DATAREFERENCE_XXXX"`variabele van de indeling, waarvan de waarde het pad voor koppelen/downloaden op de doel comput aangeeft. Het Data Store-pad op de doel Compute kan niet hetzelfde zijn als het pad voor de uitvoering van het trainings script.

### <a name="examples"></a>Voorbeelden 

De volgende code voorbeelden zijn specifiek voor de [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) -klasse om toegang te krijgen tot gegevens tijdens de training. 

`script_params`is een woorden lijst met para meters voor de entry_script. Gebruik deze functie om in een gegevens Archief door te geven en te beschrijven hoe gegevens beschikbaar worden gesteld op het reken doel. Meer informatie vindt u in onze end-to-end [zelf studie](tutorial-train-models-with-aml.md).

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

U kunt ook een lijst met data stores door geven aan de `inputs` para meter Estimator om gegevens te koppelen of kopiëren naar/van uw gegevens opslag (s). Dit code voorbeeld:
* Hiermee wordt alle inhoud in `datastore1` naar het reken doel gedownload voordat het trainings `train.py` script wordt uitgevoerd
* Hiermee wordt de `'./foo'` map `datastore2` in het berekenings `train.py` doel gedownload voordat deze wordt uitgevoerd
* Uploadt het bestand `'./bar.pkl'` van het Compute-doel naar `datastore3` het nadat het script is uitgevoerd

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>Compute-en Data Store-matrix

Data stores ondersteunen momenteel het opslaan van verbindings gegevens naar de opslag services die in de volgende matrix worden weer gegeven. Deze matrix bevat de beschik bare functies voor gegevens toegang voor de verschillende reken doelen en Data Store-scenario's. Meer informatie over de [Compute-doelen voor Azure machine learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Compute|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokaal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning-Computing |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Virtuele machines               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Gegevensoverdracht                  |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)            |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake Analytics       |N/A                                           |N/A                                           |[ML&nbsp;-pijp lijnen](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Er zijn mogelijk scenario's waarin zeer terugkerende gegevens processen sneller worden uitgevoerd met behulp `as_download()` van in `as_mount()`plaats van; dit kan experimenteel worden gevalideerd.

### <a name="accessing-source-code-during-training"></a>Toegang tot de bron code tijdens de training

Azure Blob-opslag heeft hogere doorvoer snelheden dan een Azure-bestands share en schaalt naar een groot aantal taken die parallel worden gestart. Daarom is het raadzaam om uw uitvoeringen te configureren voor het gebruik van Blob-opslag voor het overdragen van broncode bestanden.

In het volgende code voorbeeld wordt de uitvoerings configuratie opgegeven die BLOB-gegevens opslag moet gebruiken voor bron code overdrachten.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Toegang tot gegevens tijdens de Score

Azure Machine Learning biedt verschillende manieren om uw modellen te gebruiken voor het scoren van punten. Sommige van deze methoden bieden geen toegang tot gegevens opslag. Gebruik de volgende tabel om inzicht te krijgen in de methoden waarmee u toegang hebt tot gegevens opslag in de Score:

| Methode | Toegang tot Data Store | Description |
| ----- | :-----: | ----- |
| [Batch-voor spelling](how-to-run-batch-predictions.md) | ✔ | Maak voor spellingen voor grote hoeveel heden gegevens asynchroon. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Model (len) implementeren als een webservice. |
| [Module IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Model (len) implementeren op IoT Edge apparaten. |

In situaties waarin de SDK geen toegang biedt tot gegevens opslag, kunt u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de data. De [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een client bibliotheek die u kunt gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.


## <a name="next-steps"></a>Volgende stappen

* [Een model te trainen](how-to-train-ml-models.md)

* [Een model implementeren](how-to-deploy-and-where.md)
