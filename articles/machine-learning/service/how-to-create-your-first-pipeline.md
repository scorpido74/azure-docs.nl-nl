---
title: ML-pijp lijnen maken, uitvoeren, & bijhouden
titleSuffix: Azure Machine Learning
description: Maken en uitvoeren van een machine learning-pijplijn met de Azure Machine Learning-SDK voor Python. Gebruik ML-pijp lijnen om de werk stromen te maken en te beheren die samen machine learning (ML) fasen. Deze fasen omvatten gegevens voorbereiding, model training, model implementatie en defactory/Score.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: f1a0db395b86f473d2372a5ca779020e54186e45
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034842"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>machine learning-pijp lijnen maken en uitvoeren met Azure Machine Learning SDK

In dit artikel leert u hoe u een [machine learning pijp lijn](concept-ml-pipelines.md) kunt maken, publiceren, uitvoeren en bijhouden met behulp van de [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Gebruik **ml-pijp lijnen** om een werk stroom te maken die verschillende stadia van de milliliter bundelt en vervolgens die pijp lijn naar uw Azure machine learning-werk ruimte te publiceren om deze later te openen of te delen met anderen.  ML-pijp lijnen zijn ideaal voor batch Score scenario's, met behulp van verschillende reken processen, het opnieuw gebruiken van stappen in plaats van deze opnieuw uit te voeren, evenals het delen van werk stromen met anderen. 

Hoewel u een ander type pijp lijn kunt gebruiken dat een [Azure-pijp lijn](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) voor CI/cd-automatisering van ml-taken wordt genoemd, wordt die soort pijp lijn nooit opgeslagen in uw werk ruimte. [Vergelijk deze verschillende pijp lijnen](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Elke fase van een ML-pijp lijn, zoals gegevens voorbereiding en model training, kan een of meer stappen bevatten.

De ML pijp lijnen die u maakt, zijn zichtbaar voor de leden van uw Azure Machine Learning- [werk ruimte](how-to-manage-workspace.md). 

ML-pijp lijnen gebruiken externe Compute-doelen voor berekening en de opslag van de tussenliggende en laatste gegevens die zijn gekoppeld aan deze pijp lijn. Ze kunnen gegevens lezen en schrijven naar ondersteunde [Azure Storage](https://docs.microsoft.com/azure/storage/) locaties.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Vereisten

* Maak een [Azure Machine Learning-werkruimte](how-to-manage-workspace.md) voor het opslaan van alle resources van uw pijplijn.

* [Configureer uw ontwikkel omgeving](how-to-configure-environment.md) om de Azure machine learning SDK te installeren of gebruik een [notebook-VM](tutorial-1st-experiment-sdk-setup.md#azure) met de SDK die al is geïnstalleerd.

Begin met het koppelen van uw werk ruimte:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Machine learning-resources instellen

Maak de resources die vereist zijn voor het uitvoeren van een ML-pijp lijn:

* Instellen van een gegevensarchief gebruikt voor toegang tot de gegevens die nodig zijn in de stappen van de pijplijn.

* Een `DataReference` object configureren om te verwijzen naar gegevens die zich in bevinden of toegankelijk zijn in, een gegevens opslag.

* Instellen van de [compute-doelen](concept-azure-machine-learning-architecture.md#compute-targets) op waarmee de stappen van de pijplijn wordt uitgevoerd.

### <a name="set-up-a-datastore"></a>Instellen van een gegevensarchief

De gegevens voor de pijplijn voor toegang tot worden opgeslagen in een gegevensarchief. Elke werkruimte heeft een standaard-gegevensopslag. U kunt aanvullende gegevensopslag registreren. 

Wanneer u uw werk ruimte maakt, worden [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) gekoppeld aan de werk ruimte. Er is een standaard gegevens opslag geregistreerd om verbinding te maken met de Azure Blob-opslag. Zie [bepalen wanneer u Azure files, Azure-blobs of Azure-schijven wilt gebruiken](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)voor meer informatie. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Upload bestanden of mappen met het gegevensarchief zodat deze toegankelijk is vanaf uw pijplijnen. In dit voor beeld wordt de Blob-opslag gebruikt als het gegevens archief:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Een pijplijn bestaat uit een of meer stappen. Een stap is een eenheid worden uitgevoerd op een compute-doel. Stappen mogelijk gegevensbronnen gebruiken en 'tussenliggende' gegevens produceren. Een stap kunt maken, zoals een model, een map met het model en de afhankelijke bestanden of tijdelijke gegevens. Deze gegevens zijn vervolgens beschikbaar voor andere stappen die verderop in de pijplijn.

### <a name="configure-data-reference"></a>Naslaginformatie voor data configureren

U alleen een gegevensbron die kan worden verwezen in een pijplijn gemaakt als invoer voor een stap. Een gegevensbron in een pijplijn wordt vertegenwoordigd door een [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) object. De `DataReference` object verwijst naar gegevens die zich bevinden of uit een gegevensarchief toegankelijk is.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Tussenliggende gegevens (of de uitvoer van een stap) wordt vertegenwoordigd door een [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) object. `output_data1`wordt geproduceerd als uitvoer van een stap en wordt gebruikt als invoer van een of meer toekomstige stappen. `PipelineData`introduceert een gegevens afhankelijkheid tussen de stappen en maakt een impliciete uitvoerings volgorde in de pijp lijn.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Compute-doel instellen

In Azure Machine Learning verwijst de term computes__ (of __reken doel__) naar de computers of clusters die de reken stappen in uw machine learning pijp lijn uitvoeren.   Zie [Compute-doelen voor model training](how-to-set-up-training-targets.md) voor een volledige lijst met Compute-doelen en hoe u deze kunt maken en koppelen aan uw werk ruimte.  Het proces voor het maken en of koppelen van een reken doel is hetzelfde, ongeacht of u een model traint of een pijplijn stap uitvoert. Nadat u het reken doel hebt gemaakt en gekoppeld, gebruikt `ComputeTarget` u het object in de [pijplijn stap](#steps).

> [!IMPORTANT]
> Het uitvoeren van beheer bewerkingen op Compute-doelen wordt niet ondersteund vanuit externe taken. Omdat machine learning-pijp lijnen worden verzonden als een externe taak, mag u geen beheer bewerkingen gebruiken op reken doelen vanuit de pijp lijn.

Hieronder vindt u voor beelden van het maken en koppelen van reken doelen voor:

* Azure Machine Learning-Computing
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-Computing

U kunt een Azure Machine Learning Compute maken om uw stappen uit te voeren.

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

### <a id="databricks"></a>Azure Databricks

Azure Databricks is een omgeving op basis van Apache Spark in de Azure-cloud. Het kan worden gebruikt als een reken doel met een Azure Machine Learning-pijp lijn.

Maak een Azure Databricks-werk ruimte voordat u deze gebruikt. Als u een werkruimte resource wilt maken, raadpleegt u de [taak een Spark uitvoeren op Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) document.

Als u Azure Databricks als een reken doel wilt koppelen, geeft u de volgende informatie op:

* __Databricks Compute name__: De naam die u wilt toewijzen aan deze Compute-resource.
* __Naam van Databricks-werk ruimte__: De naam van de Azure Databricks-werk ruimte.
* __Databricks-toegangs token__: Het toegangs token dat wordt gebruikt om te verifiëren bij Azure Databricks. Zie voor het genereren van een toegangstoken de [verificatie](https://docs.azuredatabricks.net/api/latest/authentication.html) document.

De volgende code laat zien hoe u Azure Databricks als reken doel koppelt aan de Azure Machine Learning SDK:

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

Bekijk een [voor beeld](https://aka.ms/pl-databricks) van een notebook op github voor een meer gedetailleerd voor beeld.

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics is een platform voor big data-analyses in de Azure-cloud. Het kan worden gebruikt als een reken doel met een Azure Machine Learning-pijp lijn.

Maak een Azure Data Lake Analytics-account voordat u het gebruikt. Zie voor het maken van deze resource, de [aan de slag met Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) document.

Als u wilt koppelen Data Lake Analytics als een compute-doel, moet u de Azure Machine Learning-SDK gebruiken en geef de volgende informatie:

* __Compute name__: De naam die u wilt toewijzen aan deze Compute-resource.
* __Resourcegroep__: De resource groep die het Data Lake Analytics-account bevat.
* __Accountnaam__: De naam van het Data Lake Analytics-account.

De volgende code ziet u hoe u Data Lake Analytics als een compute-doel toevoegen:

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

Bekijk een [voor beeld](https://aka.ms/pl-adla) van een notebook op github voor een meer gedetailleerd voor beeld.

> [!TIP]
> Azure Machine Learning-pijplijnen kunnen uitsluitend worden gebruikt met gegevens die zijn opgeslagen in het standaardarchief van gegevens van het Data Lake Analytics-account. Als de gegevens die u nodig hebt om te werken met in een niet-standaard-archief is, kunt u een [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) om de gegevens voordat een training te kopiëren.

## <a id="steps"></a>Uw pijplijn stappen maken

Wanneer u een compute-doel maakt en koppelt aan uw werk ruimte, bent u klaar om een pijplijn stap te definiëren. Er zijn veel ingebouwde stappen beschikbaar via de SDK van Azure Machine Learning. De meest elementaire van deze stappen is een [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), waarmee een python-script wordt uitgevoerd in een opgegeven Compute-doel:

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

Hergebruik van vorige resultaten`allow_reuse`() is Key wanneer u pijp lijnen in een samenwerkings omgeving gebruikt, omdat overbodige reacties minder flexibel zijn. Hergebruik is het standaard gedrag wanneer de SCRIPT_NAME, invoer en de para meters van een stap hetzelfde blijven. Wanneer de uitvoer van de stap opnieuw wordt gebruikt, wordt de taak niet verzonden naar de compute, in plaats daarvan zijn de resultaten van de vorige uitvoering onmiddellijk beschikbaar voor de uitvoering van de volgende stap. Als `allow_reuse` is ingesteld op False, wordt voor deze stap altijd een nieuwe uitvoering gegenereerd tijdens de uitvoering van de pijp lijn. 

Nadat u de stappen hebt gedefinieerd, bouwt u de pijp lijn met behulp van enkele of al deze stappen.

> [!NOTE]
> Er worden geen bestanden of gegevens geüpload naar Azure Machine Learning wanneer u de stappen definieert of de pijp lijn bouwt.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

In het volgende voor beeld wordt het Azure Databricks Compute-doel gebruikt dat eerder is gemaakt: 

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

Zie voor meer informatie het [pakket met Azure-pipeline-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) en [pijplijn klassen](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Verzenden van de pijplijn

Wanneer u de pijp lijn verzendt, controleert Azure Machine Learning de afhankelijkheden voor elke stap en uploadt een moment opname van de bronmap die u hebt opgegeven. Als er geen bronmap is opgegeven, wordt de huidige lokale map geüpload. De moment opname wordt ook opgeslagen als onderdeel van het experiment in uw werk ruimte.

> [!IMPORTANT]
> Als u wilt voor komen dat bestanden worden opgenomen in de moment opname, maakt `.amlignore` u een [. gitignore](https://git-scm.com/docs/gitignore) -of-bestand in de map en voegt u de bestanden hieraan toe. Het `.amlignore` bestand gebruikt dezelfde syntaxis en patronen als het [. gitignore](https://git-scm.com/docs/gitignore) -bestand. Als beide bestanden bestaan, heeft `.amlignore` het bestand voor rang.
>
> Zie [moment opnamen](concept-azure-machine-learning-architecture.md#snapshots)voor meer informatie.

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Wanneer u een pijp lijn voor het eerst uitvoert, Azure Machine Learning:

* Downloadt de moment opname van het project naar het reken doel van de Blob-opslag die is gekoppeld aan de werk ruimte.
* Bouwt een docker-installatie kopie die overeenkomt met elke stap in de pijp lijn.
* Downloadt de docker-installatie kopie voor elke stap naar het reken doel vanuit het container register.
* Koppelt het gegevens archief als `DataReference` er een object is opgegeven in een stap. Als het koppelpunt wordt niet ondersteund, worden de gegevens worden in plaats daarvan gekopieerd naar de compute-doel.
* Voert de stap uit in het berekenings doel dat is opgegeven in de stap definitie. 
* Maakt artefacten, zoals Logboeken, stdout, stderr, metrische gegevens en uitvoer die zijn opgegeven door de stap. Deze artefacten worden vervolgens geüpload en opgeslagen in de standaard gegevens opslag van de gebruiker.

![Diagram van het uitvoeren van een experiment als een pijp lijn](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Zie voor meer informatie de referentie over de experimentele [klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .



## <a name="github-tracking-and-integration"></a>GitHub bijhouden en integreren

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. De huidige doorvoer-ID voor de opslag plaats wordt bijvoorbeeld vastgelegd als onderdeel van de geschiedenis.

## <a name="publish-a-pipeline"></a>Publiceren van een pijplijn

U kunt een pijplijn uit te voeren met verschillende soorten invoer later publiceren. Voor het REST-eind punt van een al gepubliceerde pijp lijn om para meters te accepteren, moet u de pijp lijn para meters voordat u deze publiceert.

1. Voor het maken van een pijplijnparameter gebruikt een [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) object met een standaardwaarde.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Voeg deze toe `PipelineParameter` object als parameter voor een van de stappen in de pijplijn als volgt:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. Publiceer deze pijplijn waarmee een parameter wanneer aangeroepen worden geaccepteerd.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Een gepubliceerde pijplijn uitvoeren

Alle gepubliceerde pijp lijnen hebben een REST-eind punt. Met dit eind punt wordt de uitvoering van de pijp lijn vanuit externe systemen aangeroepen, zoals niet-python-clients. Met dit eind punt wordt ' beheerde Herhaal baarheid ' ingeschakeld in batch-scores en retraining-scenario's.

Als u de uitvoering van de voor gaande pijp lijn wilt aanroepen, hebt u een token voor de Azure Active Directory-verificatie header nodig, zoals beschreven in de [AzureCliAuthentication-klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) referentie of krijgt u meer details in de [verificatie in azure machine learning](https://aka.ms/pl-restep-auth) notebook.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

### <a name="view-results-of-a-published-pipeline"></a>Resultaten van een gepubliceerde pijp lijn weer geven

Bekijk de lijst met alle gepubliceerde pijp lijnen en de details van de uitvoering:
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Uw werkruimte weergeven](how-to-manage-workspace.md#view) te vinden van de lijst met pijplijnen.
 ![lijst met machine learning-pijplijnen](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Selecteer een specifieke pijplijn om te zien van de resultaten van de uitvoering.

Deze resultaten zijn ook beschikbaar op de [landings pagina van uw werk ruimte (preview)](https://ml.azure.com).

### <a name="disable-a-published-pipeline"></a>Een gepubliceerde pijp lijn uitschakelen

Als u een pijp lijn wilt verbergen in de lijst met gepubliceerde pijp lijnen, schakelt u deze uit:

```
# Get the pipeline by using its ID from the Azure portal
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

U kunt deze opnieuw inschakelen met `p.enable()`. Zie referentie [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) voor meer informatie.


## <a name="caching--reuse"></a>Caching & opnieuw gebruiken  

Om het gedrag van uw pijp lijnen te optimaliseren en aan te passen, kunt u enkele dingen doen om in de cache te plaatsen en opnieuw te gebruiken. U kunt bijvoorbeeld het volgende kiezen:
+ `allow_reuse=False` **Schakel de standaard instelling voor het opnieuw gebruiken van de uitvoer uitvoering** uit tijdens [stap definitie](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Hergebruik is essentieel voor het gebruik van pijp lijnen in een samenwerkings omgeving omdat het elimineren van overbodige uitvoeringen flexibiliteit biedt. U kunt het echter niet opnieuw gebruiken.
+ **Breid hashing uit met het script**, om ook een absoluut pad of relatieve paden naar de bronmap naar andere bestanden en mappen toe te voegen met behulp van de`hash_paths=['<file or directory']` 
+ **Het opnieuw genereren van uitvoer afdwingen voor alle stappen in een run** with`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

`allow_reuse` Voor stappen is standaard ingeschakeld en alleen het hoofd script bestand wordt gehasht. Als het script voor een gegeven stap hetzelfde blijft (`script_name`, invoer en de para meters), wordt de uitvoer van een vorige stap opnieuw gebruikt, wordt de taak niet verzonden naar de compute en worden de resultaten van de vorige uitvoering onmiddellijk beschikbaar voor de volgende stap. .  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```
 

## <a name="next-steps"></a>Volgende stappen

- Gebruik [deze Jupyter-notebooks in GitHub](https://aka.ms/aml-pipeline-readme) machine learning-pijplijnen verder verkennen.
- Zie de SDK-Naslag informatie voor het pakket met het kern pakket voor [azureml-pijp lijnen](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) en de stappen voor het [oplossen van azureml-pijp lijnen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
