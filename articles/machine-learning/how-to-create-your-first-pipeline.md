---
title: ML-pijplijnen maken, uitvoeren & bijhouden
titleSuffix: Azure Machine Learning
description: Maak en voer een machine learning-pijplijn uit met de Azure Machine Learning SDK voor Python. Gebruik ML-pijplijnen om de werkstromen te maken en te beheren die ml-fasen (machine learning) aan elkaar naaien. Deze fasen omvatten gegevensvoorbereiding, modeltraining, modelimplementatie en gevolgtrekking/score.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 2f62be94c901b383e34608508baa87ea37c893af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283600"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Machine learning-pijplijnen maken en uitvoeren met Azure Machine Learning SDK
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een [machine learning-pijplijn](concept-ml-pipelines.md) maakt, publiceert, uitvoert en bijhoudt met behulp van de [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Gebruik **ML-pijplijnen** om een werkstroom te maken die verschillende ML-fasen samenvoegt en vervolgens die pijplijn in uw Azure Machine Learning-werkruimte publiceert om later toegang te krijgen of met anderen te delen.  ML-pijplijnen zijn ideaal voor batchscoringscenario's, waarbij verschillende gegevens worden gebruikt, stappen worden hergebruikt in plaats van opnieuw worden uitgevoerd en ml-workflows met anderen worden gedeeld.

Hoewel u een ander soort pijplijn gebruiken, een [Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) voor CI/CD-automatisering van ML-taken, wordt dat type pijplijn nooit opgeslagen in uw werkruimte. [Vergelijk deze verschillende pijplijnen](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Elke fase van een ML-pijplijn, zoals gegevensvoorbereiding en modeltraining, kan een of meer stappen bevatten.

De ML-pijplijnen die u maakt, zijn zichtbaar voor de leden van uw Azure Machine [Learning-werkruimte.](how-to-manage-workspace.md) 

ML-pijplijnen gebruiken externe rekendoelen voor berekening en de opslag van de tussenliggende en uiteindelijke gegevens die aan die pijplijn zijn gekoppeld. Ze kunnen gegevens van en naar ondersteunde [Azure Storage-locaties](https://docs.microsoft.com/azure/storage/) lezen en schrijven.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md) met al uw pijplijnbronnen.

* [Configureer uw ontwikkelomgeving](how-to-configure-environment.md) om de Azure Machine Learning SDK te installeren of gebruik een [Azure Machine Learning-rekeninstantie (voorbeeld)](concept-compute-instance.md) waarbij de SDK al is geïnstalleerd.

Begin met het koppelen van uw werkruimte:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Machine learning-resources instellen

Maak de resources die nodig zijn om een ML-pijplijn uit te voeren:

* Stel een gegevensarchief in dat wordt gebruikt om toegang te krijgen tot de gegevens die nodig zijn in de pijplijnstappen.

* Een `DataReference` object configureren om te wijzen op gegevens die in een gegevensarchief leven of toegankelijk zijn.

* Stel de [rekendoelen](concept-azure-machine-learning-architecture.md#compute-targets) in waarop uw pijplijnstappen worden uitgevoerd.

### <a name="set-up-a-datastore"></a>Een gegevensarchief instellen

Een gegevensarchief slaat de gegevens op voor toegang tot de pijplijn. Elke werkruimte heeft een standaard gegevensarchief. U aanvullende datastores registreren. 

Wanneer u uw werkruimte maakt, worden [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) aan de werkruimte gekoppeld. Er is een standaardgegevensarchief geregistreerd om verbinding te maken met de Azure Blob-opslag. Zie [Beslissen wanneer Azure Files, Azure Blobs of Azure Disks moeten worden gebruikt](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)voor meer informatie. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Upload gegevensbestanden of mappen naar het datastore om toegankelijk te zijn vanuit uw pijplijnen. In dit voorbeeld wordt de Blob-opslag als gegevensarchief gebruikt:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Een pijplijn bestaat uit een of meer stappen. Een stap is een eenheid die wordt uitgevoerd op een compute-doel. Stappen kunnen gegevensbronnen verbruiken en 'tussenliggende' gegevens produceren. Een stap kan gegevens maken, zoals een model, een map met model- en afhankelijke bestanden of tijdelijke gegevens. Deze gegevens zijn dan beschikbaar voor andere stappen later in de pijplijn.

Zie de artikelen [Hoe u gegevens openen](how-to-access-data.md) en [gegevenssets registreren](how-to-create-register-datasets.md)voor meer informatie over het koppelen van uw pijplijn aan uw gegevens. 

### <a name="configure-data-reference"></a>Gegevensverwijzing configureren

U hebt zojuist een gegevensbron gemaakt waarnaar kan worden verwezen in een pijplijn als invoer voor een stap. Een gegevensbron in een pijplijn wordt weergegeven door een [Object DataReference.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) Het `DataReference` object verwijst naar gegevens die in een datastore leven of toegankelijk zijn.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Tussenliggende gegevens (of uitvoer van een stap) worden weergegeven door een [object PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) `output_data1`wordt geproduceerd als de output van een stap en wordt gebruikt als de input van een of meer toekomstige stappen. `PipelineData`hiermee wordt een gegevensafhankelijkheid tussen stappen geïntroduceerd en wordt een impliciete uitvoeringsorder in de pijplijn gemaakt. Dit object wordt later gebruikt bij het maken van pijplijnstappen.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Gegevens configureren met behulp van gegevenssets

Als u tabelgegevens hebt opgeslagen in een bestand of een set bestanden, `DataReference`is een [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) een efficiënt alternatief voor een . `TabularDataset`objecten ondersteunen versieweergave, diffs en overzichtsstatistieken. `TabularDataset`s worden lui geëvalueerd (zoals Python generatoren) en het is efficiënt om ze te subseten door te splitsen of te filteren. De `FileDataset` klasse biedt vergelijkbare lui geëvalueerde gegevens die een of meer bestanden vertegenwoordigen. 

U maakt `TabularDataset` een met behulp van methoden zoals [from_delimited_files.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-)

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 U maakt `FileDataset` een gebruiks- [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 U meer informatie krijgen over het werken met gegevenssets van [Gegevenssets toevoegen & gegevenssets](how-to-create-register-datasets.md) of [dit voorbeeldnotitieblok .](https://aka.ms/train-datasets)

## <a name="set-up-compute-target"></a>Compute-doel instellen

In Azure Machine Learning verwijst de term __computes__ (of __compute target)__ naar de machines of clusters die de computationele stappen in uw machine learning-pijplijn uitvoeren.   Bekijk [rekendoelen voor modeltraining](how-to-set-up-training-targets.md) voor een volledige lijst met rekendoelen en hoe u deze maken en koppelen aan uw werkruimte.  Het proces voor het maken en of koppelen van een compute target is hetzelfde, ongeacht of u een model traint of een pijplijnstap uitvoert. Nadat u uw rekendoel hebt `ComputeTarget` gemaakt en bevestigd, gebruikt u het object in de [pijplijnstap](#steps).

> [!IMPORTANT]
> Het uitvoeren van beheerbewerkingen op compute targets wordt niet ondersteund vanuit externe taken. Aangezien machine learning-pijplijnen worden ingediend als een externe taak, gebruikt u geen beheerbewerkingen op compute-doelen vanuit de pijplijn.

Hieronder vindt u voorbeelden van het maken en koppelen van compute targets voor:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning compute

U een Azure Machine Learning-gegevensberekening maken voor het uitvoeren van uw stappen.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks is een op Apache Spark gebaseerde omgeving in de Azure-cloud. Het kan worden gebruikt als een compute target met een Azure Machine Learning-pijplijn.

Maak een Azure Databricks-werkruimte voordat u deze gebruikt. Zie de taak Een [Spark uitvoeren op Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) als u een werkruimtebron wilt maken.

Als u Azure Databricks als rekendoel wilt koppelen, geeft u de volgende gegevens op:

* __Gegevensbricks compute name:__ de naam die u wilt toewijzen aan deze compute resource.
* __Naam van de Databricks-werkruimte:__ de naam van de azure databricks-werkruimte.
* __Databricks access token:__ het toegangstoken dat wordt gebruikt om te verifiëren tot Azure Databricks. Zie het [verificatiedocument](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) om een toegangstoken te genereren.

De volgende code laat zien hoe u Azure Databricks als rekendoel koppelt aan de Azure Machine Learning SDK __(de Werkruimte Databricks moet aanwezig zijn in hetzelfde abonnement als uw AML-werkruimte):__

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Zie een [voorbeeldnotitieblok](https://aka.ms/pl-databricks) op GitHub voor een gedetailleerder voorbeeld.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics is een big data analytics platform in de Azure cloud. Het kan worden gebruikt als een compute target met een Azure Machine Learning-pijplijn.

Maak een Azure Data Lake Analytics-account voordat u het gebruikt. Zie het document [Aan de slag met Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) als u deze bron wilt maken.

Als u Data Lake Analytics als rekendoel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie verstrekken:

* __Compute-naam:__ de naam die u aan deze rekenbron wilt toewijzen.
* __Resourcegroep:__ de resourcegroep die het Data Lake Analytics-account bevat.
* __Accountnaam:__ De naam van het Data Lake Analytics-account.

De volgende code laat zien hoe u Data Lake Analytics als rekendoel koppelt:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Zie een [voorbeeldnotitieblok](https://aka.ms/pl-adla) op GitHub voor een gedetailleerder voorbeeld.

> [!TIP]
> Azure Machine Learning-pijplijnen kunnen alleen werken met gegevens die zijn opgeslagen in het standaardgegevensarchief van het Data Lake Analytics-account. Als de gegevens waarmee u moet werken zich in een [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) niet-standaardarchief bevindt, u een gegevens gebruiken om de gegevens te kopiëren voordat u wordt getraind.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Uw pijplijnstappen maken

Zodra u een rekendoel aan uw werkruimte hebt gemaakt en toegevoegd, bent u klaar om een pijplijnstap te definiëren. Er zijn veel ingebouwde stappen beschikbaar via de Azure Machine Learning SDK. De meest elementaire van deze stappen is een [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), die een Python-script in een opgegeven compute-doel uitvoert:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Hergebruik van eerdere`allow_reuse`resultaten ( ) is de sleutel bij het gebruik van pijpleidingen in een collaboratieve omgeving, omdat het elimineren van onnodige herhalingen biedt flexibiliteit. Hergebruik is het standaardgedrag wanneer de script_name, ingangen en de parameters van een stap hetzelfde blijven. Wanneer de uitvoer van de stap opnieuw wordt gebruikt, wordt de taak niet naar de compute verzonden, in plaats daarvan zijn de resultaten van de vorige run onmiddellijk beschikbaar voor de volgende stap. Als `allow_reuse` deze is ingesteld op false, wordt er altijd een nieuwe run gegenereerd voor deze stap tijdens de uitvoering van de pijplijn. 

Nadat u uw stappen hebt gedefinieerd, bouwt u de pijplijn met behulp van sommige of al deze stappen.

> [!NOTE]
> Er worden geen bestanden of gegevens geüpload naar Azure Machine Learning wanneer u de stappen definieert of de pijplijn bouwt.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

In het volgende voorbeeld wordt het eerder gemaakte rekendoel Azure Databricks gebruikt: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Een gegevensset gebruiken 

Als u `TabularDataset` een `FileDataset` of een pijplijn wilt gebruiken, moet u het object [DatasetConsumptionConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) gebruiken door [as_named_input(naam)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-)aan te roepen. U geeft `DatasetConsumptionConfig` dit object `inputs` door als een van de stappen naar uw pijplijn. 

Gegevenssets die zijn gemaakt met Azure Blob-opslag, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database en Azure Database voor PostgreSQL, kunnen worden gebruikt als invoer voor elke pijplijnstap. Met uitzondering van het schrijven van uitvoer naar een [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) of [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py), kunnen uitvoergegevens ([PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)) alleen worden geschreven naar Azure Blob en Azure File share datastores.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Vervolgens haalt u de gegevensset in uw pijplijn op met het woordenboek [Run.input_datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Zie het pakket [azure-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) en naslagverwijzing [naar pijplijnklassen](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) voor meer informatie.

## <a name="submit-the-pipeline"></a>De pijplijn verzenden

Wanneer u de pijplijn verzendt, controleert Azure Machine Learning de afhankelijkheden voor elke stap en uploadt een momentopname van de bronmap die u hebt opgegeven. Als er geen bronmap is opgegeven, wordt de huidige lokale map geüpload. De momentopname wordt ook opgeslagen als onderdeel van het experiment in uw werkruimte.

> [!IMPORTANT]
> Als u wilt voorkomen dat bestanden in de momentopname `.amlignore` worden opgenomen, maakt u een [.gitignore](https://git-scm.com/docs/gitignore) of bestand in de map en voegt u de bestanden eraan toe. Het `.amlignore` bestand gebruikt dezelfde syntaxis en patronen als het [.gitignore-bestand.](https://git-scm.com/docs/gitignore) Als beide bestanden `.amlignore` bestaan, heeft het bestand voorrang.
>
> Zie [Momentopnamen voor](concept-azure-machine-learning-architecture.md#snapshots)meer informatie.

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Wanneer u een pijplijn voor het eerst uitvoert, azure machine learning:

* Downloadt de projectmomentopname naar het rekendoel vanuit de Blob-opslag die is gekoppeld aan de werkruimte.
* Hiermee bouwt u een Docker-afbeelding die overeenkomt met elke stap in de pijplijn.
* Downloadt de Docker-afbeelding voor elke stap naar het rekendoel vanuit het containerregister.
* Monteert het gegevensarchief `DataReference` als een object in een stap is opgegeven. Als de bevestiging niet wordt ondersteund, worden de gegevens in plaats daarvan gekopieerd naar het rekendoel.
* Met de stap in het rekendoel dat is opgegeven in de stapdefinitie. 
* Hiermee worden artefacten, zoals logboeken, stdout en stderr, statistieken en uitvoer die door de stap zijn opgegeven, maakt. Deze artefacten worden vervolgens geüpload en bewaard in het standaardgegevensarchief van de gebruiker.

![Diagram voor het uitvoeren van een experiment als pijplijn](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Zie de verwijzing [naar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) de klasse Experiment voor meer informatie.

### <a name="view-results-of-a-pipeline"></a>Resultaten van een pijplijn weergeven

Bekijk de lijst met al je pijplijnen en hun hardloopgegevens in de studio:

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com).

1. [Uw werkruimte weergeven.](how-to-manage-workspace.md#view)

1. Selecteer aan de linkerkant **Pijplijnen** om al uw pijplijnuitvoeringen te bekijken.
 ![lijst met machine learning-pijplijnen](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Selecteer een specifieke pijplijn om de runresultaten te bekijken.

## <a name="git-tracking-and-integration"></a>Git tracking en integratie

Wanneer u een trainingsrun start waarbij de bronmap een lokale Git-opslagplaats is, wordt informatie over de opslagplaats opgeslagen in de rungeschiedenis. Zie [Git-integratie voor Azure Machine Learning voor](concept-train-model-git-integration.md)meer informatie.

## <a name="publish-a-pipeline"></a>Een pijplijn publiceren

U een pijplijn publiceren om deze later met verschillende ingangen uit te voeren. Als u het REST-eindpunt van een reeds gepubliceerde pijplijn wilt accepteren, moet u de pijplijn parameteriseren voordat u deze publiceert.

1. Als u een parameter voor pijplijn wilt maken, gebruikt u een object [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) met een standaardwaarde.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Voeg `PipelineParameter` dit object als parameter als een van de stappen in de pijplijn als volgt toe:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publiceer deze pijplijn die een parameter accepteert wanneer deze wordt aangeroepen.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Een gepubliceerde pijplijn uitvoeren

Alle gepubliceerde pijplijnen hebben een REST-eindpunt. Dit eindpunt roept de uitvoering van de pijplijn op van externe systemen, zoals niet-Python-clients. Dit eindpunt maakt "beheerde herhaalbaarheid" mogelijk in batchscoring- en omscholingsscenario's.

Als u de uitvoering van de vorige pijplijn wilt aanroepen, hebt u een Azure Active Directory-verificatiekoptoken nodig, zoals beschreven in [azureCliAuthentication-klasseverwijzing](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) of meer details in het [verificatiescherm in Azure Machine](https://aka.ms/pl-restep-auth) Learning-notitieblok.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Een versiepunt van een versie
U een pijplijneindpunt maken met meerdere gepubliceerde pijplijnen erachter. Dit kan worden gebruikt als een gepubliceerde pijplijn, maar geeft u een vast REST-eindpunt terwijl u uw ML-pijplijnen aangeeft en bijwerkt.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Een taak verzenden naar een eindpunt van een pijplijn
U een taak indienen bij de standaardversie van een eindpunt van een pijplijn:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
U ook een taak indienen bij een specifieke versie:
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Hetzelfde kan worden bereikt met behulp van de REST API:
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Gepubliceerde pijplijnen gebruiken in de studio

U ook een gepubliceerde pijplijn vanuit de studio uitvoeren:

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com).

1. [Uw werkruimte weergeven.](how-to-manage-workspace.md#view)

1. Selecteer Aan de linkerkant **Eindpunten**.

1. Selecteer bovenaan **Pijplijneindpunten**.
 ![lijst van gepubliceerde pijplijnen voor machine learning](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Selecteer een specifieke pijplijn om de resultaten van eerdere uitvoeringen van het eindpunt van de pijplijn uit te voeren, te consumeren of te bekijken.


### <a name="disable-a-published-pipeline"></a>Een gepubliceerde pijplijn uitschakelen

Als u een pijplijn wilt verbergen voor uw lijst met gepubliceerde pijplijnen, schakelt u deze uit, in de studio of in de SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

U het `p.enable()`opnieuw inschakelen met. Zie [Verwijzing naar de klasse PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) voor meer informatie.


## <a name="caching--reuse"></a>Caching & hergebruik  

Om het gedrag van uw pijplijnen te optimaliseren en aan te passen, u een paar dingen doen rond caching en hergebruik. U er bijvoorbeeld voor kiezen om:
+ **Schakel het standaardhergebruik van de uitvoer uitvoer uitvoeren van de stap uit** door in te stellen `allow_reuse=False` tijdens de [stapsgewijze definitie](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Hergebruik is essentieel bij het gebruik van pijpleidingen in een samenwerkingsomgeving, omdat het elimineren van onnodige runs flexibiliteit biedt. U zich echter afmelden voor hergebruik.
+ **Uitvoerregeneratie forceren voor alle stappen in een run** met`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Standaard is `allow_reuse` voor stappen ingeschakeld `source_directory` en wordt de opgegeven stapdefinitie gehasht. Dus, als het script voor een bepaalde`script_name`stap hetzelfde blijft ( ,` source_directory` ingangen en de parameters), en niets anders in de is veranderd, wordt de uitvoer van een vorige stap opnieuw gebruikt, wordt de taak niet voorgelegd aan de compute en zijn de resultaten van de vorige run onmiddellijk beschikbaar voor de volgende stap.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Volgende stappen

- Gebruik [deze Jupyter-notebooks op GitHub](https://aka.ms/aml-pipeline-readme) om machine learning-pijplijnen verder te verkennen.
- Zie de SDK-naslaghulp voor het [azureml-pipelines-core-pakket](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en het pakket [azureml-pipelines-stappen.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Bekijk de [how-to](how-to-debug-pipelines.md) voor tips over foutopsporing en het oplossen van problemen met pijplijnen.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
