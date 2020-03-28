---
title: Batchvoorspellingen uitvoeren op big data
titleSuffix: Azure Machine Learning
description: Meer informatie over het asynchroon krijgen van gevolgtrekkingen op grote hoeveelheden gegevens met ParallelRunStep in Azure Machine Learning. ParallelRunStep biedt parallelle verwerkingsmogelijkheden uit de doos en optimaliseert voor high-throughput, fire-and-forget gevolgtrekking voor big-data use cases.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477184"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Batch-gevolgtrekking uitvoeren op grote hoeveelheden gegevens met Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het asynchroon en parallel verwerken van grote hoeveelheden gegevens met Azure Machine Learning. De parallelrunstep-mogelijkheid die hier wordt beschreven, is in een openbare preview. Het is een krachtige en hoge doorvoer manier om gevolgtrekkingen en verwerking van gegevens te genereren. Het biedt asynchrone mogelijkheden uit de doos.

Met ParallelRunStep is het eenvoudig om offline conclusies te schalen naar grote clusters van machines op terabytes aan productiegegevens, wat resulteert in een hogere productiviteit en geoptimaliseerde kosten.

In dit artikel leert u de volgende taken:

> * Maak een externe compute resource.
> * Schrijf een aangepast gevolgboekscript.
> * Maak een [machine learning-pijplijn](concept-ml-pipelines.md) om een vooraf getraind afbeeldingsclassificatiemodel te registreren op basis van de [MNIST-gegevensset.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> * Gebruik het model om batch-gevolgtrekking uit te voeren op voorbeeldafbeeldingen die beschikbaar zijn in uw Azure Blob-opslagaccount. 

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van de Azure Machine Learning](https://aka.ms/AMLFree).

* Voer de [zelfstudie voor het instellen](tutorial-1st-experiment-sdk-setup.md) van een begeleide quickstart in als u nog geen Azure Machine Learning-werkruimte of virtuele notebookhebt. 

* Zie de [handleiding voor](how-to-configure-environment.md) het configureren van uw eigen omgeving om uw eigen omgeving te beheren om uw eigen omgeving te beheren. Voer `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` in uw omgeving uit om de benodigde afhankelijkheden te downloaden.

## <a name="set-up-machine-learning-resources"></a>Machine learning-resources instellen

Met de volgende acties worden de resources ingesteld die u nodig hebt om een batch-inferenctrekkingpijplijn uit te voeren:

- Maak een gegevensarchief dat verwijst naar een blobcontainer met afbeeldingen die tot gevolgtrekking moeten leiden.
- Gegevensverwijzingen instellen als invoer en uitvoer voor de batch-inferencepipelinestap.
- Stel een compute cluster in om de batch-gevolgtrekkingstap uit te voeren.

### <a name="create-a-datastore-with-sample-images"></a>Een gegevensarchief maken met voorbeeldafbeeldingen

Download de MNIST-evaluatieset van `sampledata` de openbare `pipelinedata`blobcontainer op een account met de naam . Maak een datastore `mnist_datastore`met de naam , die naar deze container verwijst. In de volgende `register_azure_blob_container`aanroep `overwrite` om `True` de vlag in te stellen op het overschrijven van gegevensarchief dat eerder met die naam is gemaakt. 

U deze stap wijzigen om naar uw blobcontainer `container_name`te `account_name`wijzen door uw eigen waarden op te geven voor `datastore_name`, en.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Geef vervolgens het standaardgegevensarchief voor werkruimte op als het uitvoergegevensarchief. Je gebruikt het voor gevolgtrekking output.

Wanneer u uw werkruimte maakt, worden [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) en [Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) standaard aan de werkruimte gekoppeld. Azure Files is het standaardgegevensarchief voor een werkruimte, maar u blob-opslag ook als gegevensarchief gebruiken. Zie [Azure-opslagopties](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)voor meer informatie.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Gegevensinvoer en -uitvoer configureren

Nu moet u gegevensinvoer en -uitvoer configureren, waaronder:

- De map met de invoerafbeeldingen.
- De map waar het vooraf getrainde model wordt opgeslagen.
- De map met de labels.
- De map voor uitvoer.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)is een klasse voor het verkennen, transformeren en beheren van gegevens in Azure Machine Learning. Deze klasse heeft [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) twee [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)soorten: en . In dit voorbeeld gebruikt `FileDataset` u als invoer voor de batch-inferencepipelinestap. 

> [!NOTE] 
> `FileDataset`ondersteuning in batch-gevolgtrekking is voorlopig beperkt tot Azure Blob-opslag. 

U ook verwijzen naar andere gegevenssets in uw aangepaste gevolgtrekkingsscript. U het bijvoorbeeld gebruiken om toegang te krijgen tot `Dataset.register` `Dataset.get_by_name`labels in uw script voor het labelen van afbeeldingen met behulp van en.

Zie [Gegevenssets maken en openen (voorbeeld) voor](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)meer informatie over Azure Machine Learning-gegevenssets.

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)objecten worden gebruikt voor het overbrengen van tussenliggende gegevens tussen pijplijnstappen. In dit voorbeeld gebruikt u het voor gevolgtrekkingsuitvoer.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Een rekendoel instellen

In Azure Machine Learning verwijst *rekenkracht* (of *rekendoel)* naar de machines of clusters die de computationele stappen in uw machine learning-pijplijn uitvoeren. Voer de volgende code uit om een Op CPU gebaseerd [AmlCompute-doel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) te maken.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
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

## <a name="prepare-the-model"></a>Het model voorbereiden

[Download het vooraf getrainde classificatiemodel](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)voor afbeeldingen `models` en haal het vervolgens naar de map.

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

Registreer het model vervolgens bij uw werkruimte, zodat het beschikbaar is voor uw externe rekenbron.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Uw inferencescript schrijven

>[!Warning]
>De volgende code is slechts een voorbeeld dat het [voorbeeldnotitieblok](https://aka.ms/batch-inference-notebooks) gebruikt. Je moet je eigen script maken voor je scenario.

Het script moet twee functies *bevatten:*
- `init()`: Gebruik deze functie voor een kostbare of gemeenschappelijke voorbereiding voor latere gevolgtrekking. Gebruik het bijvoorbeeld om het model in een globaal object te laden. Deze functie wordt slechts eenmaal aangeroepen bij het begin van het proces.
-  `run(mini_batch)`: De functie wordt `mini_batch` voor elke instantie uitgevoerd.
    -  `mini_batch`: Parallelle run stap zal aanroepen run methode en geef ofwel een lijst of Pandas DataFrame als een argument om de methode. Elke vermelding in min_batch is - een bestandspad als invoer een FileDataset is, een Pandas DataFrame als invoer een TabularDataset is.
    -  `response`: run() methode moet een Pandas DataFrame of een array retourneren. Voor append_row output_action worden deze geretourneerde elementen toegevoegd aan het algemene uitvoerbestand. Voor summary_only wordt de inhoud van de elementen genegeerd. Voor alle uitvoeracties geeft elk geretourneerd uitvoerelement één geslaagde uitvoerelement in de invoerminibatch aan. Zorg ervoor dat er voldoende gegevens zijn opgenomen in het run-resultaat om invoer in kaart te brengen om het resultaat uit te voeren. Run output zal worden geschreven in uitvoerbestand en niet gegarandeerd in orde te zijn, moet u gebruik maken van een aantal sleutel in de uitvoer om het in kaart te brengen om in te voeren.

```python
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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Toegang krijgen tot andere bestanden in de bronmap in entry_script

Als u een ander bestand of map in dezelfde map hebt als uw invoerscript, u ernaar verwijzen door de huidige werkmap te zoeken.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>De pijplijn met ParallelRunStep bouwen en uitvoeren

Nu heb je alles wat je nodig hebt om de pijpleiding te bouwen.

### <a name="prepare-the-run-environment"></a>De run-omgeving voorbereiden

Geef eerst de afhankelijkheden voor uw script op. U gebruikt dit object later wanneer u de pijplijnstap maakt.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>De parameters opgeven voor de stap van de batch-gevolgtrekkingspijplijn

`ParallelRunConfig`is de belangrijkste configuratie voor de nieuw `ParallelRunStep` geïntroduceerde batch-inference-instantie binnen de Azure Machine Learning-pijplijn. U gebruikt het om uw script te verpakken en de benodigde parameters te configureren, inclusief alle volgende parameters:
- `entry_script`: Een gebruikersscript als een lokaal bestandspad dat parallel wordt uitgevoerd op meerdere knooppunten. Als `source_directory` aanwezig is, gebruikt u een relatief pad. Gebruik anders elk pad dat toegankelijk is op de machine.
- `mini_batch_size`: De grootte van de mini-batch is doorgegeven aan een enkele `run()` oproep. (optioneel; de standaardwaarde is `10` bestanden `1MB` voor FileDataset en voor TabularDataset.)
    - Want, `FileDataset`het is het aantal bestanden met `1`een minimumwaarde van . U meerdere bestanden combineren tot één minibatch.
    - Want, `TabularDataset`het is de grootte van de gegevens. Voorbeeldwaarden `1024`zijn `1024KB` `10MB`, `1GB`, en . De aanbevolen waarde `1MB`is . De mini-batch `TabularDataset` uit zal nooit de grenzen van het bestand overschrijden. Als u bijvoorbeeld CSV-bestanden met verschillende formaten hebt, is het kleinste bestand 100 KB en het grootste bestand 10 MB. Als u `mini_batch_size = 1MB`instelt, worden bestanden met een grootte kleiner dan 1 MB behandeld als één minibatch. Bestanden met een grootte groter dan 1 MB worden opgesplitst in meerdere minibatches.
- `error_threshold`: Het aantal recordfouten voor `TabularDataset` `FileDataset` en bestandsfouten daarvoor moet tijdens de verwerking worden genegeerd. Als het aantal fouten voor de volledige invoer boven deze waarde komt, wordt de taak afgebroken. De foutdrempel is voor de volledige invoer en niet `run()` voor afzonderlijke minibatches die naar de methode worden verzonden. Het bereik `[-1, int.max]`is. Het `-1` onderdeel geeft aan dat u alle fouten tijdens de verwerking negeert.
- `output_action`: Een van de volgende waarden geeft aan hoe de uitvoer wordt georganiseerd:
    - `summary_only`: Het gebruikersscript slaat de uitvoer op. `ParallelRunStep`gebruikt de uitvoer alleen voor de berekening van de foutdrempel.
    - `append_row`: Voor alle invoerbestanden wordt slechts één bestand gemaakt in de uitvoermap om alle uitvoeruitvoer toe te voegen die door regel worden gescheiden. De bestandsnaam `parallel_run_step.txt`is .
- `source_directory`: Paden naar mappen die alle bestanden bevatten die op het rekendoel moeten worden uitgevoerd (optioneel).
- `compute_target`: `AmlCompute` Alleen wordt ondersteund.
- `node_count`: Het aantal compute nodes dat moet worden gebruikt voor het uitvoeren van het gebruikersscript.
- `process_count_per_node`: Het aantal processen per knooppunt.
- `environment`: De definitie van de Python-omgeving. U het configureren om een bestaande Python-omgeving te gebruiken of om een tijdelijke omgeving voor het experiment in te stellen. De definitie is ook verantwoordelijk voor het instellen van de vereiste toepassingsafhankelijkheden (optioneel).
- `logging_level`: Log verbosity. Waarden in toenemende verbositeit zijn: `WARNING`, `INFO`, en `DEBUG`. (optioneel; de standaardwaarde is `INFO`)
- `run_invocation_timeout`: `run()` De methode aanroepen time-out in seconden. (optioneel; standaardwaarde `60`is )

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>De pijplijnstap maken

Maak de pijplijnstap met behulp van het script, de omgevingsconfiguratie en de parameters. Geef het rekendoel op dat u al aan uw werkruimte hebt gekoppeld als het doel van de uitvoering voor het script. Gebruik `ParallelRunStep` om de batch-inference pipeline stap te maken, die alle volgende parameters neemt:
- `name`: De naam van de stap, met de volgende naamgevingsbeperkingen: uniek, 3-32 tekens en regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `models`: Nul of meer modelnamen die al zijn geregistreerd in het Azure Machine Learning-modelregister.
- `parallel_run_config`: `ParallelRunConfig` Een object, zoals eerder gedefinieerd.
- `inputs`: Een of meer Azure Machine Learning-gegevenssets met één type.
- `output`: `PipelineData` Een object dat overeenkomt met de uitvoermap.
- `arguments`: Een lijst met argumenten die worden doorgegeven aan het gebruikersscript (optioneel).
- `allow_reuse`: Of de stap eerdere resultaten opnieuw moet gebruiken wanneer ze met dezelfde instellingen/ingangen worden uitgevoerd. Als deze `False`parameter dit is, wordt er altijd een nieuwe run gegenereerd voor deze stap tijdens de uitvoering van de pijplijn. (optioneel; de standaardwaarde is `True`.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> De bovenstaande stap `azureml-contrib-pipeline-steps`is afhankelijk van , zoals beschreven in [Voorwaarden](#prerequisites). 

### <a name="submit-the-pipeline"></a>De pijplijn verzenden

Nu, voer de pijpleiding. Maak eerst [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) een object met behulp van uw werkruimteverwijzing en de pijplijnstap die u hebt gemaakt. De `steps` parameter is een reeks stappen. In dit geval is er slechts één stap voor batchscores. Als u pijplijnen wilt maken met meerdere stappen, plaatst u de stappen in volgorde in deze array.

Gebruik vervolgens `Experiment.submit()` de functie om de pijplijn in te dienen voor uitvoering.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>De parallelle runtaak controleren

Een batch gevolgtrekking taak kan een lange tijd duren om te voltooien. In dit voorbeeld wordt de voortgang bewaakt met behulp van een Jupyter-widget. U ook de voortgang van de taak beheren met behulp van:

* Azure Machine Learning Studio. 
* Console-uitvoer [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) van het object.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Volgende stappen

Als u wilt dat dit proces van begin tot eind werkt, probeert u het [notitienotitieblok van](https://aka.ms/batch-inference-notebooks)de batch . 

Zie de [handleiding](how-to-debug-parallel-run-step.md)voor het opsporen van foutopsporing en het oplossen van problemen voor ParallelRunStep.

Zie de [handleiding](how-to-debug-pipelines.md)voor het opsporen van foutopsporing en het oplossen van problemen voor pijplijnen.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

