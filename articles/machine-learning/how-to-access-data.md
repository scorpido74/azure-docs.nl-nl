---
title: Verbinding maken met Azure-opslagservices
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van datastores om veilig verbinding te maken met Azure-opslagservices tijdens training met Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ca892b5f360f523ee2b5ff875dfb0707136a5ab5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383444"
---
# <a name="connect-to-azure-storage-services"></a>Verbinding maken met Azure-opslagservices
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leest u hoe u verbinding maken met Azure-opslagservices via Azure Machine Learning-gegevensarchieven. Datastores slaan verbindingsgegevens op, zoals uw abonnements-ID en tokenautorisatie in uw [Key Vault](https://azure.microsoft.com/services/key-vault/) die aan de werkruimte is gekoppeld, zodat u veilig toegang hebt tot uw opslag zonder dat u ze in uw scripts hoeft te coderen.

U gegevenswinkels maken met [deze Azure-opslagoplossingen.](#matrix) Voor niet-ondersteunde opslagoplossingen en om kosten te besparen voor het overschrijden van gegevens tijdens machine learning-experimenten, raden we u aan [uw gegevens te verplaatsen](#move) naar ondersteunde Azure-opslagoplossingen. 

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:
- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

- Een Azure-opslagaccount met een [Azure blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) of [Azure-bestandsshare](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

- De [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot Azure Machine [Learning-studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  Maak [een Azure Machine Learning-werkruimte](how-to-manage-workspace.md) of gebruik een bestaande werkruimte via de Python SDK. Importeer `Workspace` de `Datastore` klasse en de klasse en `config.json` laad `from_config()`uw abonnementsgegevens uit het bestand met de functie . Hiermee wordt standaard gezocht naar het JSON-bestand in de huidige map, maar `from_config(path="your/file/path")`u ook een padparameter opgeven die naar het bestand moet wijzen met behulp van.

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Ondersteunde servicetypen voor gegevensopslag

Datastores ondersteunen momenteel het opslaan van verbindingsgegevens op de opslagservices die in de volgende matrix worden vermeld.

| Opslagtype&nbsp; | Verificatietype&nbsp; | [Azure&nbsp;&nbsp;Machine Learning-studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;&nbsp;Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Accountsleutel <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure-bestandsshare&nbsp;&nbsp;](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Accountsleutel <br> SAS-token | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 1](https://docs.microsoft.com/azure/data-lake-store/)| Service-principal| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data&nbsp;Lake&nbsp;Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Service-principal| ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;SQL-database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-verificatie <br>Service-principal| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL-verificatie| ✓ | ✓ | ✓ |✓
[Azure-database&nbsp;&nbsp;voor&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | SQL-verificatie|  | ✓* | ✓* |✓*
[Gegevensbricks-bestandssysteem&nbsp;&nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Geen verificatie | | ✓** | ✓ ** |✓** 

*MySQL wordt alleen ondersteund voor pipeline [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
**Databricks wordt alleen ondersteund voor pipeline [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden u aan een gegevensarchief te maken voor een [Azure Blob-container.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Zowel standaard als premium opslag zijn beschikbaar voor blobs. Hoewel premium opslag duurder is, kunnen de hogere doorvoersnelheden de snelheid van uw trainingsruns verbeteren, vooral als u traint tegen een grote gegevensset. Zie de [azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)voor informatie over de kosten van opslagaccounts.

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) is gebouwd bovenop Azure Blob-opslag en is ontworpen voor big data-analyses voor bedrijven. Een fundamenteel onderdeel van Data Lake Storage Gen2 is de toevoeging van een [hiërarchische naamruimte](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) aan Blob-opslag. De hiërarchische naamruimte organiseert objecten/bestanden in een hiërarchie van mappen voor efficiënte gegevenstoegang.

Wanneer u een werkruimte maakt, worden een Azure blob-container en een Azure-bestandsshare automatisch geregistreerd in de werkruimte. Ze zijn `workspaceblobstore` genoemd `workspacefilestore`en , respectievelijk. `workspaceblobstore`wordt gebruikt om artefacten voor werkruimtes en uw machine learning-experimentlogboeken op te slaan. `workspacefilestore`wordt gebruikt om notitieblokken en R-scripts op te slaan die zijn geautoriseerd via [compute instance.](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files) De `workspaceblobstore` container is ingesteld als het standaardgegevensarchief.

> [!IMPORTANT]
> Azure Machine Learning designer (preview) maakt automatisch een datastore met de naam **azureml_globaldatasets** wanneer u een voorbeeld opent op de startpagina van de ontwerper. Deze datastore bevat alleen voorbeeldgegevenssets. Gebruik deze datastore **niet** voor vertrouwelijke toegang tot gegevens.
> ![Automatisch gemaakt gegevensarchief voor gegevenssets voor designervoorbeelden](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>Datastores maken en registreren

Wanneer u een Azure-opslagoplossing registreert als een gegevensarchief, maakt en registreert u die gegevenswinkel automatisch bij een specifieke werkruimte. U gegevenswinkels maken en registreren voor een werkruimte met behulp van de Python SDK of Azure Machine Learning-studio.

>[!IMPORTANT]
> Als onderdeel van het oorspronkelijke proces voor het maken en registreren van gegevensarchief valideert Azure Machine Learning dat de onderliggende opslagservice bestaat en dat de door de gebruiker verstrekte hoofd (gebruikersnaam, serviceprincipal of SAS-token) toegang heeft tot die opslag. Voor Azure Data Lake Storage Gen 1 en 2-gegevensarchieven vindt deze [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) validatie [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) echter later plaats wanneer methoden voor gegevenstoegang zoals of worden aangeroepen. 
<br><br>
Na het maken van gegevensarchief wordt deze validatie alleen uitgevoerd voor methoden die toegang tot de onderliggende opslagcontainer vereisen, **niet** telkens wanneer gegevensarchiefobjecten worden opgehaald. Validatie vindt bijvoorbeeld plaats als u bestanden wilt downloaden van uw datastore; maar als u gewoon uw standaard gegevensarchief wilt wijzigen, gebeurt de validatie niet.

### <a name="python-sdk"></a>Python-SDK

Alle register methoden zijn [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) op de klasse `register_azure_*`en hebben het formulier .

U de informatie vinden die `register()` u nodig hebt om de methode in te vullen op de [Azure-portal.](https://portal.azure.com)
Selecteer **Opslagaccounts** in het linkerdeelvenster en kies het opslagaccount dat u wilt registreren. De pagina **Overzicht** bevat informatie zoals de accountnaam, de container en de naam van bestandsdelen. 

* Ga voor verificatieitems, zoals accountsleutel of SAS-token, naar **Toegangssleutels** in het deelvenster **Instellingen.** 

* Ga voor servicehoofditems zoals tenant-id en client-id naar uw **app-registraties** en selecteer welke app u wilt gebruiken. De bijbehorende **overzichtspagina** bevat deze items.

> [!IMPORTANT]
> Als uw opslagaccount zich in een virtueel netwerk bevindt, wordt alleen het maken van Blob, File share, ADLS Gen 1 en ADLS Gen 2-datastores **via de SDK** ondersteund. Als u uw werkruimte toegang wilt verlenen `grant_workspace_access` `True`tot uw opslagaccount, stelt u de parameter in op .

In de volgende voorbeelden ziet u hoe u een Azure blob-container, een Azure-bestandsshare en Azure Data Lake Storage Generation 2 als gegevensarchief registreert. Voor andere opslagservices raadpleegt u de [referentiedocumentatie voor de `register_azure_*` toepasselijke methoden.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)

#### <a name="blob-container"></a>Blobcontainer

Als u een Azure blob-container [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)als gegevensarchief wilt registreren, gebruikt u .

Met de volgende code `blob_datastore_name` wordt het `ws` gegevensarchief aan de werkruimte aangetrokken en geregistreerd. Deze datastore heeft `my-container-name` toegang tot `my-account-name` de blobcontainer op het opslagaccount met behulp van de meegeleverde accounttoegangssleutel.

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>Bestandsshare

Als u een Azure-bestandsshare als [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)gegevensarchief wilt registreren, gebruikt u . 

Met de volgende code `file_datastore_name` wordt het `ws` gegevensarchief aan de werkruimte aangetrokken en geregistreerd. Deze datastore heeft `my-fileshare-name` toegang tot `my-account-name` de bestandsshare op het opslagaccount met behulp van de meegeleverde accounttoegangssleutel.

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

Voor een Azure Data Lake Storage Generation 2 (ADLS Gen 2) datastore u [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) gebruiken om een gegevensarchief met referenties te registreren die is verbonden met een Azure DataLake Gen 2-opslag met [serviceprincipal-machtigingen.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Om gebruik te kunnen maken van uw serviceprincipal moet u [uw aanvraag registreren](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) en de serviceprincipal de juiste gegevenstoegang verlenen. Meer informatie over [toegangscontrole die is ingesteld voor ADLS Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Met de volgende code `adlsgen2_datastore_name` wordt het `ws` gegevensarchief aan de werkruimte aangetrokken en geregistreerd. Deze datastore heeft toegang `test` tot `account_name` het bestandssysteem op het opslagaccount, met behulp van de meegeleverde servicehoofdreferenties.

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

Maak een nieuwe datastore in een paar stappen in Azure Machine Learning-studio:

> [!IMPORTANT]
> Als uw opslagaccount zich in een virtueel netwerk bevindt, wordt alleen het maken van datastores [via de SDK](#python-sdk) ondersteund. 

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com/).
1. Selecteer **Gegevensopslag** in het linkerdeelvenster onder **Beheren**.
1. Selecteer **+ Nieuw gegevensarchief**.
1. Vul het formulier in voor een nieuwe datastore. Het formulier wordt op intelligente wijze bijgewerkt op basis van uw selecties voor Azure-opslagtype en verificatietype.
  
U de informatie vinden die u nodig hebt om het formulier in te vullen op de [Azure-portal.](https://portal.azure.com) Selecteer **Opslagaccounts** in het linkerdeelvenster en kies het opslagaccount dat u wilt registreren. De pagina **Overzicht** bevat informatie zoals de accountnaam, de container en de naam van bestandsdelen. 

* Ga voor verificatieitems, zoals accountsleutel of SAS-token, naar **Toegangssleutels** in het deelvenster **Instellingen.** 

* Ga voor servicehoofditems zoals tenant-id en client-id naar uw **app-registraties** en selecteer welke app u wilt gebruiken. De bijbehorende **overzichtspagina** bevat deze items. 

In het volgende voorbeeld wordt getoond hoe het formulier eruit ziet wanneer u een Azure blob-gegevensarchief maakt: 
    
![Formulier voor een nieuwe datastore](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Gegevenswinkels ophalen vanuit uw werkruimte

Als u een specifiek gegevensarchief wilt laten [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) registreren in `Datastore` de huidige werkruimte, gebruikt u de statische methode in de klasse:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Als u de lijst met gegevenswinkels wilt openen [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) met een bepaalde werkruimte, u de eigenschap op een werkruimteobject gebruiken:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

Als u het standaardgegevensarchief van de werkruimte wilt opvragen, gebruikt u deze regel:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>Gegevens uploaden en downloaden

De [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) methoden en methoden die in de volgende voorbeelden worden beschreven, zijn specifiek voor en werken identiek voor de klassen [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) en [AzureFileDatastore.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)

### <a name="upload"></a>Uploaden

Upload een map of afzonderlijke bestanden naar de datastore met de Python SDK:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

De `target_path` parameter geeft de locatie op in de bestandsshare (of blobcontainer) die moet worden geüpload. Het standaard `None`om, zodat de gegevens worden geüpload naar root. Als `overwrite=True`, bestaande `target_path` gegevens op is overschreven.

U ook een lijst met afzonderlijke bestanden `upload_files()` uploaden naar de datastore via de methode.

### <a name="download"></a>Download

Download gegevens van een datastore naar uw lokale bestandssysteem:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

De `target_path` parameter is de locatie van de lokale map om de gegevens te downloaden. Als u een pad wilt opgeven naar de map in de bestandsshare (of blobcontainer) die u wilt downloaden, geeft u dat pad op naar `prefix`. Als `prefix` `None`dat zo is, wordt alle inhoud van uw bestandsshare (of blobcontainer) gedownload.

<a name="train"></a>

## <a name="access-your-data-during-training"></a>Toegang tot uw gegevens tijdens de training

Als u wilt communiceren met gegevens in uw gegevensarchieven of uw gegevens wilt verpakken in een verbruiksobject voor machine learning-taken, zoals training, [maakt u een Azure Machine Learning-gegevensset.](how-to-create-register-datasets.md) Gegevenssets bieden functies die tabelgegevens laden in een panda's of Spark DataFrame. Gegevenssets bieden ook de mogelijkheid om bestanden van elke indeling te downloaden of te monteren van Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database voor PostgreSQL. [Meer informatie over trainen met gegevenssets](how-to-train-with-datasets.md).

### <a name="accessing-source-code-during-training"></a>Toegang tot broncode tijdens de training

Azure Blob-opslag heeft hogere doorvoersnelheden dan een Azure-bestandsshare en wordt geschaald naar grote aantallen taken die parallel zijn gestart. Daarom raden we u aan uw uitvoeringen te configureren om Blob-opslag te gebruiken voor het overbrengen van broncodebestanden.

In het volgende codevoorbeeld wordt in de runconfiguratie aangegeven welke blob-gegevensarchief moet worden gebruikt voor broncodeoverdrachten.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>Toegang tot gegevens tijdens het scoren

Azure Machine Learning biedt verschillende manieren om uw modellen te gebruiken om te scoren. Sommige van deze methoden bieden geen toegang tot datastores. Gebruik de volgende tabel om te begrijpen welke methoden u toestaan om toegang te krijgen tot datastores tijdens het scoren:

| Methode | Toegang tot Datastore | Beschrijving |
| ----- | :-----: | ----- |
| [Batchvoorspelling](how-to-use-parallel-run-step.md) | ✔ | Doe asynchroon voorspellingen op grote hoeveelheden gegevens. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Modellen implementeren als een webservice. |
| [Azure IoT Edge-module](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen op IoT Edge-apparaten. |

Voor situaties waarin de SDK geen toegang biedt tot datastores, u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de gegevens. De Azure [Storage SDK voor Python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een clientbibliotheek die u gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Gegevens verplaatsen naar ondersteunde Azure-opslagoplossingen

Azure Machine Learning ondersteunt de toegang tot gegevens uit Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database voor PostgreSQL. Als u niet-ondersteunde opslag gebruikt, raden we u aan uw gegevens te verplaatsen naar ondersteunde Azure-opslagoplossingen met [Azure Data Factory en deze stappen.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool) Door gegevens naar ondersteunde opslag te verplaatsen, u kosten besparen bij het uitvoeren van gegevens tijdens machine learning-experimenten. 

Azure Data Factory biedt efficiënte en veerkrachtige gegevensoverdracht met meer dan 80 vooraf gebouwde connectors zonder extra kosten. Deze connectors omvatten Azure-gegevensservices, on-premises gegevensbronnen, Amazon S3 en Redshift en Google BigQuery.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [Een model trainen](how-to-train-ml-models.md)
* [Een model implementeren](how-to-deploy-and-where.md)
