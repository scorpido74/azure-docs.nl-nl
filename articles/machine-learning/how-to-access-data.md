---
title: Verbinding maken met Azure-opslagservices
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van data stores om veilig verbinding te maken met Azure Storage-services tijdens de training met Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: c5200214946b52ce974a8b7557e38eb57481028a
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782988"
---
# <a name="connect-to-azure-storage-services"></a>Verbinding maken met Azure-opslagservices
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u **verbinding kunt maken met Azure Storage-services via Azure machine learning gegevens opslag**. Data stores maken een beveiligde verbinding met uw Azure Storage-service zonder dat uw verificatie referenties en de integriteit van de oorspronkelijke gegevens bron risico lopen. Er worden verbindings gegevens opgeslagen, zoals uw abonnements-ID en Token autorisatie in uw [Key Vault](https://azure.microsoft.com/services/key-vault/) die aan de werk ruimte zijn gekoppeld, zodat u veilig toegang kunt krijgen tot uw opslag zonder dat u deze hoeft te coderen in uw scripts. U kunt de [Azure machine learning python-SDK](#python) of de [Azure machine learning Studio](#studio) gebruiken om gegevens opslag te maken en te registreren.

Als u gegevens opslag wilt maken en beheren met behulp van de Azure Machine Learning VS code-extensie; Ga naar de [bron beheer handleiding voor het VS code-overzicht](how-to-manage-resources-vscode.md#datastores) voor meer informatie.

U kunt gegevens opslag maken op basis van [deze Azure Storage-oplossingen](#matrix). **Voor niet-ondersteunde opslag oplossingen**en voor het opslaan van de kosten voor de uitvoer van gegevens tijdens ml experimenten, [verplaatst u uw gegevens](#move) naar een ondersteunde Azure Storage-oplossing.  

Zie het artikel over [beveiligde toegang](concept-data.md#data-workflow) als u wilt weten waar gegevens opslag in de werk stroom van Azure machine learning van de gehele Data Access passen.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:
- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

- Een Azure-opslag account met een [ondersteund opslag type](#matrix).

- De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of toegang tot [Azure machine learning Studio](https://ml.azure.com/).

- Een Azure Machine Learning-werkruimte.
  
  [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md) of gebruik een bestaand item via de PYTHON-SDK. 

    Importeer de `Workspace` `Datastore` -en-klasse en laad uw abonnements gegevens uit het bestand `config.json` met behulp van de functie `from_config()` . Hiermee zoekt u standaard naar het JSON-bestand in de huidige map, maar u kunt ook een para meter Path opgeven om naar het bestand te verwijzen met `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Wanneer u een werk ruimte maakt, worden een Azure Blob-container en een Azure-bestands share automatisch geregistreerd als gegevens opslag in de werk ruimte. Ze hebben respectievelijk de naam `workspaceblobstore` en `workspacefilestore` . De `workspaceblobstore` wordt gebruikt voor het opslaan van werk ruimte-artefacten en uw machine learning-experiment-Logboeken. Het is ook ingesteld als de **standaard gegevens opslag** en kan niet worden verwijderd uit de werk ruimte. De `workspacefilestore` wordt gebruikt voor het opslaan van notitie blokken en R-scripts die zijn geautoriseerd via [Compute-instantie](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files).
    
    > [!NOTE]
    > Met Azure Machine Learning Designer (preview) wordt automatisch een gegevens archief met de naam **azureml_globaldatasets** gemaakt wanneer u een voor beeld opent in de start pagina van de ontwerp functie. Dit gegevens archief bevat alleen voorbeeld gegevens sets. Gebruik deze gegevens opslag **niet** voor vertrouwelijke gegevens toegang.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Ondersteunde service typen voor gegevens opslag

Data stores ondersteunen momenteel het opslaan van verbindings gegevens naar de opslag services die in de volgende matrix worden weer gegeven.

| Opslag &nbsp; type | Verificatie &nbsp; type | [Azure &nbsp; machine &nbsp; Learning Studio](https://ml.azure.com/) | [Azure &nbsp; machine &nbsp; Learning &nbsp; python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure &nbsp; machine &nbsp; Learning cli](reference-azure-machine-learning-cli.md) | [&nbsp;Rest API voor Azure machine &nbsp; Learning &nbsp;](https://docs.microsoft.com/rest/api/azureml/) | VS-code
---|---|---|---|---|---|---
[Azure &nbsp; BLOB- &nbsp; opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| Accountsleutel <br> SAS-token | ✓ | ✓ | ✓ |✓ |✓
[Azure- &nbsp; bestands &nbsp; share](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| Accountsleutel <br> SAS-token | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 1](https://docs.microsoft.com/azure/data-lake-store/)| Service-principal| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake &nbsp; Storage gen &nbsp; 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| Service-principal| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; SQL &nbsp; Data Base](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL-verificatie <br>Service-principal| ✓ | ✓ | ✓ |✓|
[Azure- &nbsp; postgresql](https://docs.microsoft.com/azure/postgresql/overview) | SQL-verificatie| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Base &nbsp; for &nbsp; mysql](https://docs.microsoft.com/azure/mysql/overview) | SQL-verificatie|  | ✓* | ✓* |✓*|
[Databricks- &nbsp; bestands &nbsp; systeem](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| Geen verificatie | | ✓** | ✓ ** |✓** |

* MySQL wordt alleen ondersteund voor pijplijn [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). <br>
* * Databricks wordt alleen ondersteund voor pijplijn [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>Richtlijnen voor Azure Storage

We raden u aan een gegevens opslag voor een [Azure Blob-container](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)te maken. Zowel Standard-als Premium-opslag zijn beschikbaar voor blobs. Hoewel Premium-opslag duurder is, kunnen de snellere doorvoer snelheden de snelheid van uw trainings uitvoeringen verbeteren, met name als u traint voor een grote gegevensset. Voor informatie over de kosten van opslag accounts raadpleegt u de [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) is gebaseerd op Azure Blob-opslag en ontworpen voor enter prise Big data-analyses. De toevoeging van een [hiërarchische naamruimte](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) aan Blob Storage vormt een fundamenteel onderdeel van Data Lake Storage Gen2. Met de hiërarchische naamruimte worden objecten/bestanden georganiseerd in een hiërarchie met mappen voor efficiënte toegang tot de gegevens.

## <a name="storage-access-and-permissions"></a>Toegang tot en machtigingen voor opslag

Om ervoor te zorgen dat u veilig verbinding maakt met uw Azure Storage-service, moet u voor Azure Machine Learning toegang hebben tot de bijbehorende gegevens opslag container. Deze toegang is afhankelijk van de verificatie referenties die worden gebruikt voor het registreren van de gegevens opslag. 

### <a name="virtual-network"></a>Virtueel netwerk 

Als uw gegevens opslag account zich in een **virtueel netwerk**bevindt, moet u extra configuratie stappen uitvoeren om ervoor te zorgen dat Azure machine learning toegang heeft tot uw gegevens. Zie [netwerk isolatie & privacy](how-to-enable-virtual-network.md#machine-learning-studio) om te controleren of de juiste configuratie stappen worden toegepast wanneer u uw gegevens opslag maakt en registreert.  

### <a name="access-validation"></a>Toegangs validatie

**Als onderdeel van het proces voor het maken en registreren van de eerste gegevens opslag**, Azure machine learning automatisch gevalideerd of de onderliggende opslag service bestaat en de door de gebruiker opgegeven principal (gebruikers naam, Service-Principal of SAS-token) heeft toegang tot de opgegeven opslag.

**Nadat Data Store is gemaakt**, wordt deze validatie alleen uitgevoerd voor methoden waarvoor toegang tot de onderliggende opslag container is vereist. **niet** elke keer dat de objecten gegevens opslag worden opgehaald. Validatie vindt bijvoorbeeld plaats als u bestanden wilt downloaden uit uw gegevens archief. maar als u de standaard gegevens opslag gewoon wilt wijzigen, vindt validatie niet plaats.

Als u uw toegang tot de onderliggende opslag service wilt verifiëren, kunt u uw account sleutel, SAS-tokens (Shared Access signatures) of een Service-Principal opgeven in de overeenkomstige `register_azure_*()` methode van het gegevens opslag type dat u wilt maken. De [opslag type matrix](#matrix) bevat de ondersteunde verificatie typen die overeenkomen met elk type gegevens opslag.

U vindt de account sleutel, het SAS-token en de gegevens van de Service-Principal op uw [Azure Portal](https://portal.azure.com).

* Als u van plan bent om een account sleutel of SAS-token voor verificatie te gebruiken, selecteert u **opslag accounts** in het linkerdeel venster en kiest u het opslag account dat u wilt registreren. 
  * De **overzichts** pagina bevat informatie zoals de account naam, container en naam van de bestands share. 
      1. Ga voor account sleutels naar **toegangs sleutels** in het deel venster **instellingen** . 
      1. Voor SAS-tokens gaat u naar de **hand tekeningen voor gedeelde toegang** in het deel venster **instellingen** .

* Als u van plan bent om een service-principal te gebruiken voor verificatie, gaat u naar uw **app-registraties** en selecteert u welke app u wilt gebruiken. 
    * De bijbehorende **overzichts** pagina bevat de vereiste informatie, zoals Tenant-id en client-id.

> [!IMPORTANT]
> Uit veiligheids overwegingen moet u mogelijk uw toegangs sleutels wijzigen voor een Azure Storage account (account sleutel of SAS-token). Zorg er daarom voor dat u de nieuwe referenties synchroniseert met uw werk ruimte en de gegevens opslag die ermee zijn verbonden. Meer informatie over hoe u uw bijgewerkte referenties synchroniseert met [deze stappen](how-to-change-storage-access-key.md). 

### <a name="permissions"></a>Machtigingen

Voor Azure Blob-container en Azure Data Lake gen 2-opslag, moet u ervoor zorgen dat uw verificatie referenties toegang hebben tot de **gegevens lezer** van de opslag-blob. Meer informatie over [Storage BLOB data Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

<a name="python"></a>

## <a name="create-and-register-datastores-via-the-sdk"></a>Gegevens opslag maken en registreren via de SDK

Wanneer u een Azure Storage-oplossing registreert als een gegevens opslag, maakt en registreert u die gegevens opslag automatisch aan een specifieke werk ruimte. Raadpleeg de sectie [machtigingen voor opslag toegang &](#storage-access-and-permissions) voor meer informatie over waar u de vereiste verificatie referenties kunt vinden.

In deze sectie vindt u voor beelden van het maken en registreren van een gegevens opslag via de python-SDK voor de volgende opslag typen. De para meters die in deze voor beelden worden opgegeven, zijn de **vereiste para meters** voor het maken en registreren van een gegevens opslag.

* [Azure Blob-container](#azure-blob-container)
* [Azure-bestandsshare](#azure-file-share)
* [Azure Data Lake Storage generatie 2](#azure-data-lake-storage-generation-2)

 Zie de [documentatie van de betreffende `register_azure_*` methoden](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)voor het maken van gegevens opslag voor andere ondersteunde opslag Services.

Als u liever een lage code hebt, raadpleegt u [gegevens opslag maken in azure machine learning Studio](#studio).

> [!NOTE]
> De naam van het gegevens archief mag alleen bestaan uit kleine letters, cijfers en onderstrepings tekens. 

### <a name="azure-blob-container"></a>Azure Blob-container

Als u een Azure Blob-container wilt registreren als gegevens opslag, gebruikt u [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Met de volgende code wordt het gegevens archief gemaakt en geregistreerd `blob_datastore_name` in de `ws` werk ruimte. Deze Data Store heeft toegang tot de `my-container-name` BLOB-container op het `my-account-name` opslag account met behulp van de gegeven toegangs sleutel voor het account.

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

### <a name="azure-file-share"></a>Azure-bestandsshare

Als u een Azure-bestands share wilt registreren als gegevens opslag, gebruikt u [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

Met de volgende code wordt het gegevens archief gemaakt en geregistreerd `file_datastore_name` in de `ws` werk ruimte. Deze Data Store heeft toegang tot de `my-fileshare-name` Bestands share op het `my-account-name` opslag account met behulp van de gegeven toegangs sleutel voor het account.

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generatie 2

Gebruik [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) voor het registreren van een Azure data Lake Storage Generation 2 (ADLS gen 2) om het opslaan van referenties die zijn verbonden met een Azure DataLake gen 2-opslag met [Service-Principal-machtigingen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal). 

Als u de Service-Principal wilt gebruiken, moet u [uw toepassing registreren](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) en de service-principal met toegang tot **BLOB-gegevens lezer** verlenen. Meer informatie over [toegangs beheer dat is ingesteld voor ADLS gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Met de volgende code wordt het gegevens archief gemaakt en geregistreerd `adlsgen2_datastore_name` in de `ws` werk ruimte. Deze Data Store heeft toegang tot het bestands systeem `test` in het `account_name` opslag account met behulp van de gegeven referenties voor de Service-Principal.

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

<a name="studio"></a>


## <a name="create-datastores-in-the-studio"></a>Gegevens opslag maken in de Studio 


Maak in een paar stappen een nieuwe gegevens opslag met behulp van de Azure Machine Learning Studio.

> [!IMPORTANT]
> Als uw gegevens opslag account zich in een virtueel netwerk bevindt, zijn er extra configuratie stappen vereist om ervoor te zorgen dat Studio toegang heeft tot uw gegevens. Zie [netwerk isolatie & privacy](how-to-enable-virtual-network.md#machine-learning-studio) om te controleren of de juiste configuratie stappen worden toegepast. 

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).
1. Selecteer **gegevens opslag** in het linkerdeel venster onder **beheren**.
1. Selecteer **+ Nieuw gegevensarchief**.
1. Vul het formulier in voor een nieuwe gegevens opslag. Het formulier wordt intelligent bijgewerkt op basis van uw selecties voor het Azure-opslag type en verificatie type. Zie de [sectie opslag toegang en-machtigingen](#access-validation) voor meer informatie over waar u de verificatie referenties kunt vinden die u nodig hebt om dit formulier in te vullen.

In het volgende voor beeld ziet u hoe het formulier eruitziet wanneer u een **Azure Blob-gegevens opslag**maakt: 
    
![Formulier voor een nieuwe gegevens opslag](media/how-to-access-data/new-datastore-form.png)

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Gegevens in uw gegevens opslag gebruiken

Nadat u een gegevens opslag hebt gemaakt, [maakt u een Azure machine learning gegevensset](how-to-create-register-datasets.md) om te communiceren met uw gegevens. Met gegevens sets kunt u in een vertraagd geëvalueerd voor machine learning-taken, zoals training. Ze bieden ook de mogelijkheid om bestanden te [downloaden of te koppelen](how-to-train-with-datasets.md#mount-vs-download) van een wille keurige indeling vanuit Azure Storage-services, zoals Azure Blob Storage en ADLS gen 2. U kunt ze ook gebruiken voor het laden van gegevens in tabel vorm in een Panda of Spark-data frame.

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Gegevens opslag ophalen uit uw werk ruimte

Als u een specifieke gegevens opslag die in de huidige werk ruimte is geregistreerd wilt ophalen, gebruikt u de [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) statische methode voor de `Datastore` klasse:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Als u de lijst met gegevens opslag die is geregistreerd met een bepaalde werk ruimte wilt ophalen, kunt u de [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) eigenschap gebruiken voor een werkruimte object:

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
U kunt ook de standaard gegevens opslag wijzigen met de volgende code. Deze mogelijkheid wordt alleen ondersteund via de SDK. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Toegang tot gegevens tijdens de Score

Azure Machine Learning biedt verschillende manieren om uw modellen te gebruiken voor het scoren van punten. Sommige van deze methoden bieden geen toegang tot gegevens opslag. Gebruik de volgende tabel om inzicht te krijgen in de methoden waarmee u toegang hebt tot gegevens opslag in de Score:

| Methode | Toegang tot Data Store | Beschrijving |
| ----- | :-----: | ----- |
| [Batchvoorspelling](how-to-use-parallel-run-step.md) | ✔ | Doe asynchroon voorspellingen op grote hoeveelheden gegevens. |
| [Webservice](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen als een webservice. |
| [Module Azure IoT Edge](how-to-deploy-and-where.md) | &nbsp; | Implementeer modellen om apparaten te IoT Edge. |

In situaties waarin de SDK geen toegang biedt tot gegevens opslag, kunt u mogelijk aangepaste code maken met behulp van de relevante Azure SDK om toegang te krijgen tot de data. De [Azure Storage SDK voor python](https://github.com/Azure/azure-storage-python) is bijvoorbeeld een client bibliotheek die u kunt gebruiken om toegang te krijgen tot gegevens die zijn opgeslagen in blobs of bestanden.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Gegevens verplaatsen naar ondersteunde Azure Storage-oplossingen

Azure Machine Learning ondersteunt het openen van gegevens vanuit Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database for PostgreSQL. Als u niet-ondersteunde opslag gebruikt, raden we u aan om uw gegevens te verplaatsen naar ondersteunde Azure-opslag oplossingen door gebruik te maken van [Azure Data Factory en deze stappen uit te voeren](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool). Het verplaatsen van gegevens naar ondersteunde opslag kan u helpen bij het opslaan van de kosten voor de uitvoer van gegevens tijdens machine learning experimenten. 

Azure Data Factory biedt een efficiënte en robuuste gegevens overdracht met meer dan 80 vooraf ontwikkelde connectors zonder extra kosten. Deze connectors zijn onder andere Azure Data Services, on-premises gegevens bronnen, Amazon S3 en Redshift en Google BigQuery.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure machine learning-gegevensset maken](how-to-create-register-datasets.md)
* [Een model trainen](how-to-train-ml-models.md)
* [Een model implementeren](how-to-deploy-and-where.md)
