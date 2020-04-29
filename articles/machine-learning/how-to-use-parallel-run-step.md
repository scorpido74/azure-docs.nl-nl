---
title: Batch voorspellingen uitvoeren op big data
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u met behulp van ParallelRunStep in Azure Machine Learning op asynchrone wijze aan de slag kunt met grote hoeveel heden gegevens. ParallelRunStep zorgt voor parallelle verwerkings mogelijkheden en optimaliseert voor hoge door Voer, brand-en verg eten, voor gebruik van Big Data-cases.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477184"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Batch-deinterferentie uitvoeren op grote hoeveel heden gegevens met behulp van Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over hoe u grote hoeveel heden gegevens asynchroon en parallel kunt verwerken door gebruik te maken van Azure Machine Learning. De ParallelRunStep-functionaliteit die hier wordt beschreven, is in open bare preview. Het is een snelle en hoge door Voer manier om gegevens te genereren en te verwerken. Het biedt asynchrone mogelijkheden uit het kader.

Met ParallelRunStep is het eenvoudig om offline-interferenties te schalen naar grote clusters van machines op terabytes aan productie gegevens, wat resulteert in verbeterde productiviteit en optimale kosten.

In dit artikel leert u de volgende taken:

> * Maak een externe Compute-resource.
> * Een aangepast Afleidings script schrijven.
> * Een [machine learning-pijp lijn](concept-ml-pipelines.md) maken om een vooraf getraind afbeeldings classificatie model te registreren op basis van de [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) -gegevensset. 
> * Gebruik het model voor het uitvoeren van batch deinterferentie voor voorbeeld afbeeldingen die beschikbaar zijn in uw Azure Blob Storage-account. 

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer de [gratis of betaalde versie van de Azure machine learning](https://aka.ms/AMLFree).

* Voor een begeleide Snelstartgids voltooit u de [installatie handleiding](tutorial-1st-experiment-sdk-setup.md) als u nog geen Azure machine learning werk ruimte of virtuele notebook-machine hebt. 

* Voor het beheren van uw eigen omgeving en afhankelijkheden raadpleegt u de [hand leiding](how-to-configure-environment.md) voor het configureren van uw eigen omgeving. Voer `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` uit in uw omgeving om de vereiste afhankelijkheden te downloaden.

## <a name="set-up-machine-learning-resources"></a>machine learning-resources instellen

Met de volgende acties worden de resources ingesteld die u nodig hebt om een batch-uitstel pijplijn uit te voeren:

- Maak een gegevens opslag die verwijst naar een BLOB-container met afbeeldingen die moeten worden geinterferentiet.
- Stel gegevens verwijzingen in als invoer en uitvoer voor de pipeline-stap voor batch-defactorion.
- Stel een berekenings cluster in voor het uitvoeren van de stap batch afschermen.

### <a name="create-a-datastore-with-sample-images"></a>Een gegevens opslag maken met voorbeeld installatie kopieën

Haal de MNIST-evaluatieset op uit de open `sampledata` bare BLOB-container `pipelinedata`op een account met de naam. Maak een gegevens opslag met de `mnist_datastore`naam, die verwijst naar deze container. In de volgende aanroep van `register_azure_blob_container`moet de `overwrite` vlag worden ingesteld `True` op alle gegevens opslag die eerder is gemaakt met die naam. 

U kunt deze stap wijzigen zodat deze naar de BLOB-container verwijst door uw eigen waarden `datastore_name`op `container_name`te geven `account_name`voor,, en.

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

Geef vervolgens de standaard gegevens opslag voor de werk ruimte op als uitvoer gegevens opslag. U gebruikt deze voor het afnemen van uitvoer.

Wanneer u uw werk ruimte maakt, worden [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) -en [Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) standaard aan de werk ruimte gekoppeld. Azure Files is de standaard gegevens opslag voor een werk ruimte, maar u kunt ook Blob Storage gebruiken als gegevens opslag. Zie [Opties voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)voor meer informatie.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Invoer en uitvoer van gegevens configureren

U moet nu gegevens invoer en uitvoer configureren, waaronder:

- De map die de invoer installatie kopieën bevat.
- De map waar het vooraf getrainde model wordt opgeslagen.
- De map die de labels bevat.
- De map voor uitvoer.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)is een klasse voor het verkennen, transformeren en beheren van gegevens in Azure Machine Learning. Deze klasse heeft twee typen: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) en [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). In dit voor beeld gebruikt `FileDataset` u als invoer voor de pipeline-stap voor batch afnemen. 

> [!NOTE] 
> `FileDataset`ondersteuning in batch-interferentie is nu beperkt tot Azure Blob-opslag. 

U kunt ook verwijzen naar andere gegevens sets in uw aangepaste in-interferentie script. U kunt dit bijvoorbeeld gebruiken voor toegang tot labels in uw script voor het labelen van afbeeldingen met `Dataset.register` behulp van en `Dataset.get_by_name`.

Zie [gegevens sets maken en openen (preview)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)voor meer informatie over het Azure machine learning gegevens sets.

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)objecten worden gebruikt voor het overzetten van tussenliggende gegevens tussen pijplijn stappen. In dit voor beeld gebruikt u dit voor de afleiding van uitvoer.

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

### <a name="set-up-a-compute-target"></a>Een reken doel instellen

In Azure Machine Learning, *Compute* (of *Compute target*) verwijst naar de computers of clusters die de reken stappen in uw machine learning pijp lijn uitvoeren. Voer de volgende code uit om een op een CPU gebaseerd [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) -doel te maken.

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

[Down load het vooraf getrainde afbeeldings classificatie model](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)en pak het vervolgens `models` uit naar de Directory.

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

Registreer vervolgens het model bij uw werk ruimte zodat het beschikbaar is voor uw externe Compute-resource.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Uw Afleidings script schrijven

>[!Warning]
>De volgende code is slechts een voor beeld van het gebruik van het voor [beeld-notebook](https://aka.ms/batch-inference-notebooks) . U moet uw eigen script maken voor uw scenario.

Het script *moet* twee functies bevatten:
- `init()`: Gebruik deze functie voor elke kost bare of gemeen schappelijke voor bereiding voor later gebruik. U kunt dit bijvoorbeeld gebruiken om het model in een globaal object te laden. Deze functie wordt slechts eenmaal aan het begin van het proces aangeroepen.
-  `run(mini_batch)`: De functie wordt uitgevoerd voor elk `mini_batch` exemplaar.
    -  `mini_batch`: Bij parallelle uitvoering wordt de methode Run aangeroepen en wordt een lijst of Panda data frame als argument aan de methode door gegeven. Elke vermelding in min_batch is een bestandspad als invoer een FileDataset is, een Panda data frame als de invoer een TabularDataset is.
    -  `response`: de methode Run () moet een Panda data frame of een matrix retour neren. Voor append_row output_action worden deze geretourneerde elementen toegevoegd aan het algemene uitvoer bestand. Voor summary_only wordt de inhoud van de elementen genegeerd. Voor alle uitvoer acties geeft elk geretourneerd uitvoer element een geslaagde uitvoering van het input-element aan in de invoer-mini-batch. Zorg ervoor dat er voldoende gegevens zijn opgenomen in het resultaat van de uitvoering om de invoer toe te wijzen om het resultaat uit te voeren. Uitvoer wordt geschreven in het uitvoer bestand en niet gegarandeerd in de juiste volg orde. u moet een bepaalde sleutel in de uitvoer gebruiken om deze te koppelen aan invoer.

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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Toegang krijgen tot andere bestanden in de bron directory in entry_script

Als u een ander bestand of een andere map in dezelfde map hebt als uw invoer script, kunt u ernaar verwijzen door de huidige werkmap te zoeken.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>De pijp lijn met ParallelRunStep bouwen en uitvoeren

Nu hebt u alles wat u nodig hebt om de pijp lijn te bouwen.

### <a name="prepare-the-run-environment"></a>De uitvoerings omgeving voorbereiden

Geef eerst de afhankelijkheden voor uw script op. U kunt dit object later gebruiken bij het maken van de pijplijn stap.

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

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Geef de para meters op voor de pipeline-stap voor de batch-deinterferentie

`ParallelRunConfig`is de belangrijkste configuratie voor de zojuist ingevoerde batch-deinterferentie `ParallelRunStep` -instantie binnen de Azure machine learning-pijp lijn. U gebruikt deze om uw script uit te pakken en de vereiste para meters te configureren, inclusief alle volgende para meters:
- `entry_script`: Een gebruikers script als een lokaal bestandspad dat parallel op meerdere knoop punten wordt uitgevoerd. Als `source_directory` aanwezig is, gebruikt u een relatief pad. Als dat niet het geval is, gebruikt u een wille keurig pad dat toegankelijk is op de computer.
- `mini_batch_size`: De grootte van de mini-batch die aan een enkele `run()` aanroep is door gegeven. (optioneel; de standaard waarde is `10` bestanden voor FileDataset en `1MB` voor TabularDataset.)
    - Voor `FileDataset`is het het aantal bestanden met een minimum waarde van `1`. U kunt meerdere bestanden combi neren in één mini-batch.
    - Voor `TabularDataset`is dit de grootte van de gegevens. Voor beelden van `1024`waarden `1024KB`zijn `10MB`,, `1GB`en. De aanbevolen waarde is `1MB`. De mini batch van gaat `TabularDataset` nooit over de grenzen van bestanden. Als u bijvoorbeeld. CSV-bestanden met verschillende grootten hebt, is het kleinste bestand 100 KB en de grootste waarde is 10 MB. Als u instelt `mini_batch_size = 1MB`, worden bestanden met een grootte die kleiner is dan 1 MB beschouwd als één mini-batch. Bestanden met een grootte die groter is dan 1 MB, worden in meerdere mini-batches gesplitst.
- `error_threshold`: Het aantal record fouten voor `TabularDataset` en bestands fouten voor `FileDataset` die moeten worden genegeerd tijdens de verwerking. Als het aantal fouten voor de volledige invoer boven deze waarde komt, wordt de taak afgebroken. De drempel waarde voor de fout is voor de volledige invoer en niet voor afzonderlijke mini batches die `run()` naar de methode worden verzonden. Het bereik is `[-1, int.max]`. Het `-1` deel geeft aan dat alle fouten tijdens de verwerking worden genegeerd.
- `output_action`: Een van de volgende waarden geeft aan hoe de uitvoer wordt georganiseerd:
    - `summary_only`: De uitvoer wordt opgeslagen met het gebruikers script. `ParallelRunStep`de uitvoer wordt alleen gebruikt voor de berekening van de drempel waarde voor de fout.
    - `append_row`: Voor alle invoer bestanden wordt slechts één bestand gemaakt in de uitvoermap om alle uitvoer gescheiden door regel toe te voegen. De bestands naam is `parallel_run_step.txt`.
- `source_directory`: Paden naar mappen die alle bestanden bevatten die moeten worden uitgevoerd op het Compute-doel (optioneel).
- `compute_target`: Alleen `AmlCompute` wordt ondersteund.
- `node_count`: Het aantal reken knooppunten dat moet worden gebruikt voor het uitvoeren van het gebruikers script.
- `process_count_per_node`: Het aantal processen per knoop punt.
- `environment`: De definitie van de python-omgeving. U kunt deze configureren voor het gebruik van een bestaande python-omgeving of voor het instellen van een tijdelijke omgeving voor het experiment. De definitie is ook verantwoordelijk voor het instellen van de vereiste toepassings afhankelijkheden (optioneel).
- `logging_level`: Uitgebreide logboek registratie. De waarden in een verhoogde uitgebreider `WARNING`zijn `INFO`:, `DEBUG`en. (optioneel; de standaard waarde is `INFO`)
- `run_invocation_timeout`: De `run()` time-out voor het aanroepen van de methode in seconden. (optioneel; standaard waarde is `60`)

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

### <a name="create-the-pipeline-step"></a>De pijplijn stap maken

Maak de pijplijn stap met behulp van het script, de omgevings configuratie en de para meters. Geef het reken doel op dat u al aan uw werk ruimte hebt gekoppeld als het doel van de uitvoering van het script. Gebruik `ParallelRunStep` voor het maken van de pipeline-stap voor batch-deinterferentie, die alle volgende para meters gebruikt:
- `name`: De naam van de stap, met de volgende naam beperkingen: unieke, 3-32 tekens en regex ^\[a-z\]([-a-z0-9] * [a-z0-9])? $.
- `models`: Er zijn al nul of meer model namen geregistreerd in het REGI ster van het Azure Machine Learning model.
- `parallel_run_config`: Een `ParallelRunConfig` object, zoals eerder is gedefinieerd.
- `inputs`: Een of meer Azure Machine Learning gegevens sets met één type.
- `output`: Een `PipelineData` object dat overeenkomt met de uitvoermap.
- `arguments`: Een lijst met argumenten die zijn door gegeven aan het gebruikers script (optioneel).
- `allow_reuse`: Of de stap eerdere resultaten moet hergebruiken wanneer deze met dezelfde instellingen/invoer worden uitgevoerd. Als deze para meter `False`is, wordt er altijd een nieuwe uitvoering gegenereerd voor deze stap tijdens de uitvoering van de pijp lijn. (optioneel; de standaard waarde is `True`.)

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
> De bovenstaande stap is afhankelijk van `azureml-contrib-pipeline-steps`, zoals beschreven in [vereisten](#prerequisites). 

### <a name="submit-the-pipeline"></a>De pijp lijn verzenden

Voer nu de pijp lijn uit. Maak eerst een [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) -object met behulp van uw werkruimte referentie en de door u gemaakte pijplijn stap. De `steps` para meter is een matrix met stappen. In dit geval is er slechts één stap voor batch scoreing. Als u pijp lijnen met meerdere stappen wilt maken, plaatst u de stappen in volg orde in deze matrix.

Gebruik vervolgens de `Experiment.submit()` functie om de pijp lijn voor uitvoering in te dienen.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>De parallelle uitvoerings taak controleren

Het volt ooien van een batch-Afleidings taak kan lang duren. In dit voor beeld wordt de voortgang gecontroleerd met behulp van een Jupyter-widget. U kunt de voortgang van de taak ook beheren met behulp van:

* Azure Machine Learning Studio. 
* Console-uitvoer van [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) het object.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Volgende stappen

Als u wilt zien hoe dit proces werkt, kunt u de batch-Afleidings [notitieblok](https://aka.ms/batch-inference-notebooks)gebruiken. 

Voor fout opsporing en probleemoplossings richtlijnen voor ParallelRunStep raadpleegt u de [hand leiding](how-to-debug-parallel-run-step.md)voor het oplossen van problemen.

Raadpleeg de [hand leiding](how-to-debug-pipelines.md)voor fout opsporing en probleemoplossings richtlijnen voor pijp lijnen.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

