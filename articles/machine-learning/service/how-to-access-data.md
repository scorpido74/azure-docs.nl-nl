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
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: d2e86c06cca26da2776459f3c20bf921a02ed89b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894713"
---
# <a name="access-data-in-azure-storage-services"></a>Toegang tot gegevens in azure Storage-services
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u eenvoudig toegang hebt tot uw gegevens in azure Storage-services via Azure Machine Learning gegevens opslag. Data stores worden gebruikt om verbindings gegevens op te slaan, zoals uw abonnements-ID en Token autorisatie. Door data stores te gebruiken, kunt u toegang krijgen tot uw opslag zonder dat u de verbindings gegevens van uw scripts hard kunt coderen. U kunt gegevens opslag maken op basis van deze [Azure Storage-oplossingen](#matrix). Voor niet-ondersteunde opslag oplossingen kunt u uw gegevens het beste verplaatsen naar onze ondersteunde Azure-opslag oplossingen om de kosten voor de uitvoer van gegevens op te slaan tijdens machine learning experimenten. [Meer informatie over het verplaatsen van uw gegevens](#move). 

In deze procedure worden voor beelden van de volgende taken weer gegeven:
* [Gegevens opslag registreren](#access)
* [Gegevens opslag ophalen uit de werk ruimte](#get)
* [Gegevens uploaden en downloaden met behulp van data stores](#up-and-down)
* [Toegang tot gegevens tijdens de training](#train)
* [Gegevens verplaatsen naar Azure](#move)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

- Een Azure-opslag account met een [Azure Blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) of een [Azure-bestands share](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte. 
    - [Maak een Azure machine learning werkruimte](how-to-manage-workspace.md) of gebruik een bestaande werk ruimte met behulp van de PYTHON-SDK.

        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore
        
        ws = Workspace.from_config()
        ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Gegevens opslag maken en registreren

Wanneer u een Azure Storage-oplossing registreert als een gegevens opslag, maakt u het gegevens archief automatisch in een specifieke werk ruimte. U kunt gegevens opslag voor een werk ruimte maken en registreren met behulp van de python-SDK of Azure Machine Learning Studio.

### <a name="using-the-python-sdk"></a>De Python-SDK gebruiken

Alle registratie methoden bevinden zich op de klasse [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) en hebben het formulier register_azure_ *.

De informatie die u nodig hebt om de methode REGI ster () te vullen, vindt u via [Azure Portal](https://portal.azure.com). Selecteer **opslag accounts** in het linkerdeel venster en kies het opslag account dat u wilt registreren. De pagina **overzicht** bevat informatie zoals de account naam en de naam van de container of de bestands share. Voor verificatie-informatie, zoals account sleutel of SAS-token, navigeert u naar **account sleutels** in het deel venster **instellingen** aan de linkerkant. 

In de volgende voor beelden ziet u hoe u een Azure Blob-container of een Azure-bestands share als een gegevens opslag registreert.

+ Gebruik [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) voor een **Azure Blob container-gegevens opslag**.

    Met de volgende code wordt het gegevens archief, de `my_datastore`, gemaakt en geregistreerd in de werk ruimte `ws`. Deze Data Store heeft toegang tot de Azure Blob-container `my_blob_container`, op het Azure-opslag account, `my_storage_account` met behulp van de meegeleverde account sleutel.

    ```Python
       datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                          datastore_name='my_datastore', 
                                                          container_name='my_blob_container',
                                                          account_name='my_storage_account', 
                                                          account_key='your storage account key',
                                                          create_if_not_exists=True)
    ```
    Als uw opslag account zich in een VNET bevindt, wordt alleen het maken van Azure Blob Data Store ondersteund. Stel de para meter `grant_workspace_access` in op `True` om uw werk ruimte toegang te verlenen tot uw opslag account.

+ Gebruik [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)voor een **Azure file share-gegevens opslag**. 

    Met de volgende code wordt het gegevens archief, de `my_datastore`, gemaakt en geregistreerd in de werk ruimte `ws`. Deze Data Store heeft toegang tot de Azure-bestands share `my_file_share`, op het Azure-opslag account, `my_storage_account` met behulp van de meegeleverde account sleutel.

    ```Python
       datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                      datastore_name='my_datastore', 
                                                      file_share_name='my_file_share',
                                                      account_name='my_storage account', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
    ```

####  <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden Azure Blob-container aan. Zowel Standard-als Premium-opslag zijn beschikbaar voor blobs. Hoewel we nog duurder worden, worden Premium-opslag voorgesteld door snellere doorvoer snelheden die de snelheid van uw trainings uitvoeringen kunnen verbeteren, met name als u traint voor een grote gegevensset. Zie de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service) voor informatie over de kosten van het opslag account.

### <a name="using-azure-machine-learning-studio"></a>Azure Machine Learning Studio gebruiken 

Maak een nieuwe gegevens opslag in een paar stappen in Azure Machine Learning Studio.

1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com/).
1. Selecteer **gegevens opslag** in het linkerdeel venster onder **beheren**.
1. Selecteer **+ nieuwe gegevens opslag**.
1. Vul het nieuwe gegevens opslag formulier in. Het formulier wordt op intelligente wijze bijgewerkt op basis van de selecties voor het type Azure-opslag en het verificatie type.
  
De informatie die u nodig hebt om het formulier in te vullen, vindt u via [Azure Portal](https://portal.azure.com). Selecteer **opslag accounts** in het linkerdeel venster en kies het opslag account dat u wilt registreren. De pagina **overzicht** bevat informatie zoals de account naam en de naam van de container of de bestands share. Voor verificatie-items, zoals account sleutel of SAS-token, navigeert u naar **account sleutels** in het deel venster **instellingen** aan de linkerkant.

In het volgende voor beeld ziet u hoe het formulier eruitziet voor het maken van een Azure Blob-gegevens opslag. 
    
 ![Nieuwe gegevens opslag](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Gegevens opslag ophalen uit uw werk ruimte

Als u een specifieke gegevens opslag die in de huidige werk ruimte is geregistreerd wilt ophalen, gebruikt u de methode [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) static op de klasse Data Store:

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Als u de lijst met gegevens opslag die is geregistreerd met een bepaalde werk ruimte wilt ophalen, kunt u de eigenschap [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) gebruiken voor een werkruimte object:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Wanneer u een werk ruimte maakt, worden een Azure Blob-container en een Azure-bestands share geregistreerd in de werk ruimte met de naam `workspaceblobstore` en `workspacefilestore` respectievelijk. Ze slaan de verbindings gegevens van de BLOB-container en de bestands share op die zijn ingericht in het opslag account dat aan de werk ruimte is gekoppeld. De `workspaceblobstore` is ingesteld als de standaard gegevens opslag.

Ophalen van de werkruimte standaard gegevensopslag:

```Python
datastore = ws.get_default_datastore()
```

Als u een andere standaard gegevens opslag voor de huidige werk ruimte wilt definiëren, gebruikt u [`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) methode voor het werkruimte object:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>Gegevens uploaden & downloaden
De methoden [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) en [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) die in de volgende voor beelden worden beschreven, zijn specifiek voor en worden identiek voor de klassen [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) en [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) .

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

Met de para meter `target_path` geeft u de locatie in de bestands share (of BLOB-container) op die moet worden geüpload. Wordt standaard `None`, in welk geval de gegevens wordt het geüpload naar de hoofdmap. Zo niet, als `overwrite=True` bestaande gegevens op `target_path` worden overschreven.

Of upload een lijst met afzonderlijke bestanden naar het gegevens archief via de `upload_files()` methode.

### <a name="download"></a>Downloaden

Gegevens uit een gegevensarchief op dezelfde manier downloaden naar uw lokale bestandssysteem.

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

De para meter `target_path` is de locatie van de lokale map waarnaar de gegevens moeten worden gedownload. Geef het pad naar de map in de bestandsshare (of de blob-container) om te downloaden, geeft dat pad op naar `prefix`. Als `prefix` is `None`, de inhoud van uw bestandsshare (of de blob-container) wordt gedownload.

<a name="train"></a>
## <a name="access-your-data-during-training"></a>Toegang tot uw gegevens tijdens de training

> [!IMPORTANT]
> Het gebruik van [Azure machine learning gegevens sets](how-to-create-register-datasets.md) is de nieuwe aanbevolen manier om uw gegevens in de training te benaderen. Gegevens sets bieden functies waarmee tabellaire gegevens worden geladen in Panda-of Spark-data frame en de mogelijkheid om bestanden te downloaden of te koppelen van een wille keurige indeling vanuit Azure Blob, Azure file, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Meer informatie over [het trainen van gegevens sets](how-to-train-with-datasets.md).

De volgende tabel bevat de methoden die het reken doel vertellen hoe de gegevens opslag worden gebruikt tijdens de uitvoering. 

Manier|Methode|Beschrijving|
----|-----|--------
Koppelen| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| Gebruiken om de gegevens opslag te koppelen aan het berekenings doel.
Downloaden|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|Gebruik om de inhoud van uw gegevens archief te downloaden naar de locatie die is opgegeven door `path_on_compute`. <br><br> Deze down load gebeurt vóór de uitvoering.
Uploaden|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| Gebruik dit om een bestand te uploaden vanaf de locatie die is opgegeven door `path_on_compute` naar uw gegevens opslag. <br><br> Deze upload vindt plaats na de uitvoering.

Gebruik de methode Data Store [`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-) om te verwijzen naar een specifieke map of bestand in uw gegevens opslag en deze beschikbaar te maken op het berekenings doel.

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> Alle opgegeven `datastore`-of `datastore.path`-objecten worden omgezet in een naam van een omgevings variabele van de indeling `"$AZUREML_DATAREFERENCE_XXXX"`, waarvan de waarde het pad voor koppelen/downloaden op de doel computer aangeeft. Het Data Store-pad op de doel Compute kan niet hetzelfde zijn als het pad voor de uitvoering van het trainings script.

### <a name="examples"></a>Voorbeelden 

De volgende code voorbeelden zijn specifiek voor de [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) -klasse voor het verkrijgen van toegang tot gegevens tijdens de training. 

`script_params` is een woorden lijst met para meters voor de entry_script. Gebruik deze functie om in een gegevens Archief door te geven en te beschrijven hoe gegevens beschikbaar worden gesteld op het reken doel. Meer informatie vindt u in onze end-to-end [zelf studie](tutorial-train-models-with-aml.md).

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

U kunt ook een lijst met gegevens records door geven aan de Estimator-constructor `inputs` para meter voor het koppelen of kopiëren van gegevens naar/van uw gegevens opslag (s). Dit code voorbeeld:
* Hiermee wordt alle inhoud in `datastore1` naar het reken doel gedownload voordat het trainings script `train.py` wordt uitgevoerd
* Hiermee wordt de map `'./foo'` in `datastore2` naar het reken doel gedownload voordat `train.py` wordt uitgevoerd
* Hiermee uploadt u het bestand `'./bar.pkl'` van het Compute-doel naar de `datastore3` nadat het script is uitgevoerd

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>Compute-en Data Store-matrix

Data stores ondersteunen momenteel het opslaan van verbindings gegevens naar de opslag services die in de volgende matrix worden weer gegeven. Deze matrix bevat de beschik bare functies voor gegevens toegang voor de verschillende reken doelen en Data Store-scenario's. Meer informatie over de [Compute-doelen voor Azure machine learning](how-to-set-up-training-targets.md#compute-targets-for-training).

|Computing|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Lokaal|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure Machine Learning-Computing |[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|[as_mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--), [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-), [ML&nbsp;pipelines](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| Virtuele machines               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Gegevensoverdracht                  |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)                                               |N/A                                           |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)            |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)                                              |N/A                                           |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure Batch                    |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure DataLake Analytics       |N/A                                           |N/A                                           |[ML&nbsp;pijp lijnen](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> Er zijn mogelijk scenario's waarin zeer terugkerende gegevens processen sneller worden uitgevoerd met behulp van `as_download()` in plaats van `as_mount()`. Dit kan experimenteel worden gevalideerd.

### <a name="accessing-source-code-during-training"></a>Toegang tot de bron code tijdens de training

Azure Blob-opslag heeft hogere doorvoer snelheden dan een Azure-bestands share en schaalt naar een groot aantal taken die parallel worden gestart. Daarom is het raadzaam om uw uitvoeringen te configureren voor het gebruik van Blob-opslag voor het overdragen van broncode bestanden.

In het volgende code voorbeeld wordt de uitvoerings configuratie opgegeven die BLOB-gegevens opslag moet gebruiken voor bron code overdrachten.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Toegang tot gegevens tijdens de Score

Azure Machine Learning biedt verschillende manieren om uw modellen te gebruiken voor het scoren van punten. Sommige van deze methoden bieden geen toegang tot gegevens opslag. Gebruik de volgende tabel om inzicht te krijgen in de methoden waarmee u toegang hebt tot gegevens opslag in de Score:

| Methode | Toegang tot Data Store | Beschrijving |
| ----- | :-----: | ----- |
| [Batch-voor spelling](how-to-run-batch-predictions.md) | ✔ | Maak voor spellingen voor grote hoeveel heden gegevens asynchroon. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Model (len) implementeren als een webservice. |
| [Module IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Model (len) implementeren op IoT Edge apparaten. |

In situaties waarin de SDK geen toegang biedt tot gegevens opslag, kunt u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de data. De [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een client bibliotheek die u kunt gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>Gegevens verplaatsen naar ondersteunde Azure Storage-oplossingen

Azure Machine Learning ondersteunt het openen van gegevens uit Azure Blob, Azure file, Azure Data Lake gen 1, Azure Data Lake gen 2, Azure SQL, Azure PostgreSQL. Voor niet-ondersteunde opslag is het raadzaam om uw gegevens tijdens machine learning experimenten op te slaan naar onze ondersteunde Azure Storage-oplossingen met behulp van Azure Data Factory. Azure Data Factory biedt een efficiënte en robuuste gegevens overdracht met meer dan 80 vooraf ontwikkelde connectors, waaronder Azure Data Services, on-premises gegevens bronnen, Amazon S3 en Redshift en Google BigQuery, zonder extra kosten. [Volg de stapsgewijze hand leiding om uw gegevens te verplaatsen met behulp van Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

## <a name="next-steps"></a>Volgende stappen

* [Een model te trainen](how-to-train-ml-models.md).

* [Implementeer een model](how-to-deploy-and-where.md).
