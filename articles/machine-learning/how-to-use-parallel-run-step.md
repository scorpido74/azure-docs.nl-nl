---
title: Batch-voorspellingen uitvoeren voor big data
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u asynchrone deducties kunt verkrijgen uit grote hoeveelheden gegevens met ParallelRunStep in Azure Machine Learning. ParallelRunStep biedt kant-en-klare parallelle verwerkingsopties en optimalisaties voor gebruiksgevallen waarbij in een hoog tempo deducties worden gemaakt uit grote hoeveelheden big data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: dddb332498f41437eba77d75c38218c58b8c8379
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507111"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Batchdeductie uitvoeren op grote hoeveelheden gegevens met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dit artikel laat u zien hoe u uw Azure Machine Learning-model parallel kunt uitvoeren om zo snel grote hoeveelheden gegevens te evalueren. 

Deductie van grote gegevenssets of met complexe modellen kan tijdrovend zijn. Met de klasse `ParallelRunStep` kunt u parallelle verwerking uitvoeren, waardoor de algemene resultaten sneller worden verkregen. Zelfs als het uitvoeren van één evaluatie redelijk snel gaat, zijn er veel scenario's (objectdetectie, videoverwerking, verwerking van natuurlijke taal, enz.) waarvoor een groot aantal evaluaties vereist is. 

Met `ParallelRunStep` kunt u batchdeducties eenvoudig schalen naar grote clusters van machines. Dergelijke clusters kunnen terabytes aan gestructureerde of ongestructureerde gegevens verwerken, met als voordelen verbeterde productiviteit en geoptimaliseerde kosten.

In dit artikel komen de volgende taken aan bod:

> 1. Resources voor machine learning instellen.
> 1. Gegevensinvoer en -uitvoer voor batchdeductie configureren.
> 1. Het vooraf getrainde afbeeldingsclassificatiemodel voorbereiden op basis van de [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/)-gegevensset. 
> 1.  Uw deductiescript schrijven.
> 1. Een [pijplijn voor machine learning](concept-ml-pipelines.md) maken met ParallelRunStep en batchdeductie uitvoeren op MNIST-testafbeeldingen. 
> 1. Een batchdeductie opnieuw verzenden met nieuwe gegevensinvoer en parameters. 
> 1. Bekijk de resultaten.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Voor een begeleide snelstart doorloopt u de [installatiezelfstudie](tutorial-1st-experiment-sdk-setup.md) als u nog geen Azure Machine Learning-werkruimte hebt. 

* Bekijk de [instructiegids](how-to-configure-environment.md) voor de configuratie van uw eigen omgeving om uw eigen omgeving en afhankelijkheden te beheren.

## <a name="set-up-machine-learning-resources"></a>Resources voor machine learning instellen

Met de volgende acties worden de machine learning resources ingesteld die u nodig hebt om pijplijn voor batchdeductie uit te voeren:

- Verbinding maken met een werkruimte.
- Bestaande rekenresource maken of koppelen.

### <a name="configure-workspace"></a>Werkruimte configureren

Maak een werkruimte-object van de bestaande werkruimte. `Workspace.from_config()` leest het bestand config.json en laadt de gegevens in een object met de naam ws.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Dit codefragment verwacht dat de werkruimteconfiguratie wordt opgeslagen in de huidige of de bovenliggende map. Zie [Azure Machine Learning-werkruimten maken en beheren](how-to-manage-workspace.md) voor meer informatie over het maken van een werkruimte. Zie [Een configuratiebestand voor een werkruimte maken](how-to-configure-environment.md#workspace) voor meer informatie over de configuratie als bestand opslaan.

### <a name="create-a-compute-target"></a>Een rekendoel maken

In Azure Machine Learning verwijst *Berekening* (of *Rekendoel*) naar de computers of clusters die de rekenstappen in uw machine learning-pijplijn uitvoeren. Voer de volgende code uit om een op CPU gebaseerd [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)-doel te maken.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Invoer en uitvoer configureren

### <a name="create-a-datastore-with-sample-images"></a>Een gegevensarchief maken met voorbeeldinstallatiekopieën

Zoek de MNIST-evaluatieset in de openbare blob-container `sampledata` op een account met de naam `pipelinedata`. Maak een gegevensarchief met de naam `mnist_datastore` dat verwijst naar deze container. Als u in de volgende aanroep van `register_azure_blob_container` de `overwrite`-vlag instelt op `True` overschrijft u elk gegevensarchief dat voorheen met die naam werd aangemaakt. 

U kunt deze stap wijzigen om naar uw blob-container te verwijzen door uw eigen waarden op te geven voor `datastore_name`, `container_name` en `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Geef vervolgens het gegevensarchief voor uitvoer op als het standaard gegevensarchief voor de werkruimte. U gebruikt dit voor de uitvoer van de deductie.

Wanneer u uw werkruimte maakt, worden [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) standaard aan de werkruimte gekoppeld. Azure Files is het standaard gegevensarchief voor een werkruimte, maar u kunt ook Blob Storage gebruiken als gegevensarchief. Zie [Azure-opties voor opslag](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks) voor meer informatie.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>De gegevensinvoerwaarden maken

De invoerwaarden voor batchdeductie zijn de gegevens die u wilt partitioneren voor parallelle verwerking. Een pijplijn voor batchdeductie aanvaardt gegevensinvoer via [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py).

`Dataset` is voor het doorzoeken, transformeren en beheren van gegevens in Azure Machine Learning. Er zijn twee typen: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) en [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). In dit voorbeeld gebruikt u `FileDataset` als de invoerwaarden. Met `FileDataset` kunt u de bestanden downloaden of koppelen aan uw berekening. Als u een gegevensset maakt, maakt u ook een verwijzing naar de locatie van de gegevensbron. Als u transformaties voor deelverzamelingen hebt toegepast op de gegevensset, dan worden deze ook opgeslagen in de gegevensset. De gegevens blijven bewaard op de bestaande locatie, dus maakt u geen extra opslagkosten.

Zie [Gegevenssets maken en openen (preview)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) voor meer informatie over Azure Machine Learning-gegevenssets.

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Als u dynamische gegevensinvoer wilt gebruiken bij het uitvoeren van de batchdeductie, dan kunt u de invoerwaarden `Dataset` definiëren als een [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py). Elke keer dat u een pijplijnuitvoering van een batchdeductie indient, kunt u de gegevensset met invoerwaarden opgeven.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>De uitvoer maken

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)-objecten worden gebruikt voor het overzetten van tussenliggende gegevens tussen stappen in de pijplijn. In dit voorbeeld gebruikt u dit voor de uitvoer van de deductie.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>Het model voorbereiden

[Download het vooraf getrainde model voor de classificatie van afbeeldingen](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)en pak het uit naar de map `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Registreer vervolgens het model bij uw werkruimte zodat het beschikbaar is voor uw rekenresource.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Uw deductiescript schrijven

>[!Warning]
>De volgende code is slechts een voorbeeld dat gebruik wordt door het [voorbeeldnotebook](https://aka.ms/batch-inference-notebooks). U moet uw eigen script maken voor uw scenario.

Het script *moet* twee functies bevatten:
- `init()`: Gebruik deze functie voor een kostbare of algemene voorbereiding voor latere deductie. Gebruik het bijvoorbeeld om het model in een algemeen object te laden. Deze functie wordt slecht één keer aangeroepen, aan het begin van het proces.
-  `run(mini_batch)`: De functie wordt uitgevoerd voor elk exemplaar van `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` roept de uitvoermethode aan en geeft een lijst of pandas-`DataFrame` op als argument aan de methode. Elke vermelding in mini_batch is een bestandspad als de invoer een `FileDataset` is, of een pandas-`DataFrame` als de invoer een `TabularDataset` is.
    -  `response`: run()-methode moet resulteren in een pandas-`DataFrame` of een matrix. Voor append_row output_action worden deze geretourneerde elementen toegevoegd aan het algemene uitvoerbestand. Voor summary_only wordt de inhoud van de elementen genegeerd. Voor alle uitvoeracties geeft elk geretourneerde uitvoerelement een geslaagde uitvoering van een invoerelement in de mini-batch aan. Zorg dat er voldoende gegevens worden opgenomen in het resultaat van de uitvoering om invoer toe te wijzen aan de uitvoerresultaten. Uitvoer wordt naar het uitvoerbestand geschreven en bevindt zich niet altijd in de juiste volgorde. Gebruik een sleutel in de uitvoer om deze toe te wijzen aan de invoer.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Als u een ander bestand of een andere map hebt in dezelfde map als uw deductiescript, dan kunt u ernaar verwijzen door de huidige werkmap te zoeken.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>De pijplijn met ParallelRunStep bouwen en uitvoeren

Nu hebt u alles wat u nodig hebt: de gegevensinvoerwaarden, het model, de uitvoer en uw deductiescript. Laten we de pijplijn voor batchdeductie met ParallelRunStep bouwen.

### <a name="prepare-the-environment"></a>De omgeving voorbereiden

Geef eerst de afhankelijkheden voor uw script op. Hierdoor kunt u PIP-pakketten installeren en de omgeving configureren.

Neem altijd **azureml-core** en **azureml-dataset-runtime[pandas, fuse]** op in de lijst met PIP-pakketten. Als u een aangepaste docker-installatiekopie (user_managed_dependencies = True) gebruikt, installeer dan ook conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>De parameters opgeven met ParallelRunConfig

`ParallelRunConfig` is de voornaamste configuratie voor `ParallelRunStep`-exemplaar in de Azure Machine Learning-pijplijn. U gebruikt dit om uw script te verpakken en de nodige parameters te configureren, waaronder alle volgende vermeldingen:
- `entry_script`: Een gebruikersscript als een lokaal bestandspad dat parallel wordt uitgevoerd op meerdere knooppunten. Als `source_directory` aanwezig is, gebruikt dan een relatief pad. Zoniet, gebruik dan een pad dat toegankelijk is op de machine.
- `mini_batch_size`: De grootte van de mini-batch die is doorgegeven aan een enkele `run()`-aanroep. (optioneel; de standaardwaarde is `10`-bestanden voor `FileDataset` en `1MB` voor `TabularDataset`.)
    - Voor `FileDataset` is dit het aantal bestanden met een minimumwaarde `1`. U kunt meerdere bestanden combineren in één mini-batch.
    - Voor `TabularDataset` is dit de grootte van de gegevens. Voorbeeldwaarden zijn `1024`, `1024KB`, `10MB` en `1GB`. De aanbevolen waarde is `1MB`. De mini-batch van `TabularDataset` zal nooit bestandsgrenzen overschrijden. Als u bijvoorbeeld een .csv-bestand heeft met verschillende groottes, dan is het kleinste bestan 100 KB en het grootste 10 MB. Als u `mini_batch_size = 1MB` instelt, worden bestanden met een grootte van minder dan 1 MB beschouwd als één mini-batch. Bestanden met een grootte van meer dan 1 MB, worden in verschillende mini-batches opgedeeld.
- `error_threshold`: Het aantal recordfouten voor `TabularDataset` en bestandsfouten voor `FileDataset` die tijdens de verwerking genegeerd mogen worden. Als het aantal fouten voor de volledige invoer boven deze waarde komt, wordt de taak afgebroken. De drempelwaarde voor fouten geldt voor de volledige invoer, niet voor afzonderlijke mini-batches die naar de `run()`-methode verzonden worden. Het bereik is `[-1, int.max]`. Het deel `-1` geeft aan dat alle fouten tijdens de verwerking genegeerd worden.
- `output_action`: Een van de volgende waarden geeft aan wat er met de uitvoer gebeurt:
    - `summary_only`: Het gebruikersscript slaat de uitvoer op. `ParallelRunStep` gebruikt de uitvoer enkel voor de berekening van de drempelwaarde voor fouten.
    - `append_row`: Er wordt slechts één bestand gemaakt in de uitvoermap voor alle invoerwaarden, waarbij elke uitvoerwaarde een aparte regel krijgt.
- `append_row_file_name`: Om de naam van het uitvoerbestand voor append_row output_action aan te passen (optioneel; de standaardwaarde is `parallel_run_step.txt`).
- `source_directory`: Paden naar mappen met alle bestanden die moeten worden uitgevoerd op het rekendoel (optioneel).
- `compute_target`: Alleen `AmlCompute` wordt ondersteund.
- `node_count`: Het aantal rekenknooppunten dat moet worden gebruikt voor de uitvoering van het gebruikersscript.
- `process_count_per_node`: Het aantal processen per knooppunt. Het is aanbevolen om het aantal GPU of CPU voor één knooppunt in te stellen (optioneel; standaardwaarde is `1`).
- `environment`: De definitie van de Python-omgeving. U kunt deze configureren om een bestaande Python-omgeving te gebruiken of om een tijdelijke omgeving in te stellen. De definitie zorgt er ook voor dat de vereiste toepassingsafhankelijkheden worden ingesteld (optioneel).
- `logging_level`: Uitgebreidheid van het logboek. De waarden om uitgebreidheid te verhogen zijn: `WARNING`, `INFO` en `DEBUG`. (optioneel; de standaardwaarde is `INFO`)
- `run_invocation_timeout`: De aanroeptime-out voor de `run()`-methode in seconden. (optioneel; standaardwaarde is `60`)
- `run_max_try`: Maximum aantal pogingen van `run()` voor een mini-batch. Een `run()` is mislukt als er een uitzondering wordt gegenereerd of er niets wordt geretourneerd wanneer `run_invocation_timeout` is bereikt (optioneel; de standaardwaarde is `3`). 

U kunt `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` en `run_max_try` als `PipelineParameter` opgeven, zodat u de parameterwaarden kunt aanpassen wanneer u een pijplijnuitvoering opnieuw verzendt. In dit voorbeeld gebruikt u `PipelineParameter` voor `mini_batch_size` en `Process_count_per_node`. Wanneer u later een uitvoering opnieuw verzendt, verandert u deze waarden. 

In dit voorbeeld wordt ervan uitgegaan dat u het `digit_identification.py`-script gebruikt dat eerder besproken werd. Als u uw eigen script gebruikt, wijzigt u de parameters `source_directory` en `entry_script`.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>De ParallelRunStep maken

Maak de ParallelRunStep met het script, de omgevingsconfiguratie en de parameters. Geef het rekendoel op dat u al aan uw werkruimte hebt gekoppeld als het doel van de uitvoering voor uw deductiescript. Gebruik `ParallelRunStep` om de stap voor de batchdeductiepijplijn te maken, met de volgende parameters:
- `name`: De naam van de stap, met de volgende beperkingen voor de naamgeving: uniek, 3-32 tekens en regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Een `ParallelRunConfig`-object, zoals eerder gedefinieerd.
- `inputs`: Een of meer Azure Machine Learning-gegevenssets van één type die moeten worden gepartitioneerd voor parallelle verwerking.
- `side_inputs`: Een of meer referentiegegevens of gegevenssets die worden gebruikt als aanvullende invoerwaarden en niet gepartitioneerd moeten worden.
- `output`: Een `PipelineData`-object dat overeenkomt met de uitvoermap.
- `arguments`: Een lijst van argumenten die worden doorgegeven aan het gebruikersscript. Gebruik unknown_args om deze op te halen in uw invoerscript (optioneel).
- `allow_reuse`: Of de stap vorige resultaten moet hergebruiken wanneer dezelfde instellingen/invoerwaarden worden uitgevoerd. Als deze parameter `False` is, dan wordt er altijd een nieuwe uitvoering gegenereerd voor deze stap tijdens pijplijnuitvoering. (optioneel; de standaardwaarde is `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>de pijplijn maken en uitvoeren

Voer nu de pijplijn uit. Maak eerst een [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)-object met behulp van uw werkruimtereferentie en de pijplijn die u hebt gemaakt. De `steps`-parameter is een matrix van stappen. In dit geval is er slechts één stap voor batchdeductie. Als u pijplijnen met meerdere stappen wilt bouwen, plaatst u de stappen in volgorde in deze matrix.

Gebruik vervolgens de functie `Experiment.submit()` om de pijplijn te verzenden voor uitvoering.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>De batchdeductietaak controleren

Het kan lang duren voor een batchdeductietaak voltooid is. Dit voorbeeld controleert de voortgang met een Jypiter-widget. U kunt de voortgang van de taak ook controleren met behulp van:

* Azure Machine Learning Studio. 
* Console-uitvoer van het [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py)-object.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Een uitvoering opnieuw verzenden met nieuwe gegevensinvoer en parameters

Aangezien u de invoerwaarden en verschillende configuraties als `PipelineParameter` heeft gemaakt, kunt u de uitvoering van een batchdeductie opnieuw verzenden met een andere gegevensset en de parameters wijzigen zonder dat u daarvoor een geheel nieuwe pijplijn moet maken. U gebruikt hetzelfde gegevensarchief, maar slechts één afbeelding als gegevensinvoer.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>De resultaten bekijken

De resultaten van de bovenstaande uitvoering worden als uitvoergegevens geschreven naar het `DataStore` dat is opgegeven in het `PipelineData`-object, dat in dit geval *inferences* wordt genoemd. De resultaten worden opgeslagen in de standaard-blobcontainer. U kunt naar uw opslagaccount navigeren en de resultaten bekijken via Storage Explorer. Het bestandspad is azureml-blobstore-*GUID*/azureml/*RunId*/*uitvoermap*.

U kunt deze gegevens ook downloaden om de resultaten te bekijken. Hieronder ziet u de voorbeeldcode om de eerste tien rijen weer te geven.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Volgende stappen

Ga naar de [notebook voor batchdeductie](https://aka.ms/batch-inference-notebooks) om te zien hoe het volledige proces werkt. 

Bekijk de [instructiegids](how-to-debug-parallel-run-step.md) voor hulp bij foutopsporing en probleemoplossing voor ParallelRunStep.

Bekijk de [instructiegids](how-to-debug-pipelines.md) voor hulp bij foutopsporing en probleemoplossing voor pijplijnen.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

