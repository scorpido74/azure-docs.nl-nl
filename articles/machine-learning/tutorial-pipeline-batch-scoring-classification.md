---
title: 'Zelf studie: ML pijp lijnen voor batch scores'
titleSuffix: Azure Machine Learning
description: In deze zelf studie bouwt u een machine learning-pijp lijn voor het uitvoeren van batch scoreing op een afbeeldings classificatie model. Met Azure Machine Learning kunt u zich richten op machine learning in plaats van infra structuur en automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: bfa39d4a508412322f0caec36d557c3fc6775090
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238648"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Zelf studie: een Azure Machine Learning-pijp lijn bouwen voor batch Score

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het bouwen van een pijp lijn in Azure Machine Learning om een batch Score taak uit te voeren. Machine learning-pijp lijnen Optimaliseer uw werk stroom met snelheid, portabiliteit en hergebruik, zodat u zich kunt concentreren op machine learning in plaats van infra structuur en automatisering. Nadat u een pijp lijn hebt gemaakt en gepubliceerd, configureert u een REST-eind punt dat u kunt gebruiken om de pijp lijn te activeren vanuit elke HTTP-bibliotheek op elk platform. 

In het voor beeld wordt gebruikgemaakt van een vooraf getraind gemaakt [-v3](https://arxiv.org/abs/1512.00567) convolutional Neural-netwerk model dat is geïmplementeerd in tensor flow om niet-gelabelde afbeeldingen te classificeren. Meer [informatie over machine learning pijp lijnen](concept-ml-pipelines.md).

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Werkruimte configureren 
> * Voorbeeld gegevens downloaden en opslaan
> * DataSet-objecten maken om gegevens op te halen en uit te voeren
> * Het model downloaden, voorbereiden en registreren in uw werk ruimte
> * Berekenings doelen inrichten en een score script maken
> * De klasse `ParallelRunStep` voor asynchrone batch scores gebruiken
> * Een pijp lijn bouwen, uitvoeren en publiceren
> * Een REST-eind punt inschakelen voor de pijp lijn

Als u nog geen abonnement op Azure hebt, maak dan een gratis account aan voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

## <a name="prerequisites"></a>Vereisten

* Als u nog geen virtuele machine voor Azure Machine Learning-werk ruimte of-notebook hebt, kunt u [deel 1 van de zelf studie voor Setup](tutorial-1st-experiment-sdk-setup.md)volt ooien.
* Wanneer u de installatie zelf studie hebt voltooid, gebruikt u dezelfde notebook server om de *zelf studies/machine-learning-pipelines-Advanced/tutorial-pipeline-batch-scoring-Classification. ipynb* notebook te openen.

Als u de installatie-zelf studie in uw eigen [lokale omgeving](how-to-configure-environment.md#local)wilt uitvoeren, kunt u de zelf studie openen op [github](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Voer `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` uit om de vereiste pakketten op te halen.

## <a name="configure-workspace-and-create-a-datastore"></a>Een werk ruimte configureren en een gegevens opslag maken

Maak een werkruimte object op basis van de bestaande Azure Machine Learning-werk ruimte.

- Een [werk ruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) is een klasse die uw Azure-abonnement en resource gegevens accepteert. De werk ruimte maakt ook een Cloud resource die u kunt gebruiken om uw model uitvoeringen te bewaken en bij te houden. 
- `Workspace.from_config()` leest het `config.json` bestand en laadt vervolgens de verificatie gegevens in een object met de naam `ws`. Het `ws`-object wordt in de code in deze zelf studie gebruikt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Een gegevens opslag voor voorbeeld installatie kopieën maken

Op het `pipelinedata`-account haalt u het voor beeld van de open bare gegevens van de ImageNet-evaluatie uit de open bare BLOB-container van `sampledata`. Roep `register_azure_blob_container()` aan om de gegevens beschikbaar te maken voor de werk ruimte onder de naam `images_datastore`. Vervolgens stelt u de werk ruimte standaard gegevens opslag in als uitvoer gegevens opslag. Gebruik de uitvoer gegevens opslag voor de Score van de uitvoer in de pijp lijn.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>DataSet-objecten maken

Wanneer u pijp lijnen bouwt, worden `Dataset`-objecten gebruikt voor het lezen van gegevens uit werk ruimte-data stores en worden `PipelineData`-objecten gebruikt voor het overzetten van tussenliggende gegevens tussen pijplijn stappen.

> [!Important]
> In het voor beeld van een batch Score in deze zelf studie wordt slechts één pijplijn stap gebruikt. In use-cases met meerdere stappen bevat de typische stroom de volgende stappen:
>
> 1. Gebruik `Dataset`-objecten als *invoer* om onbewerkte gegevens op te halen, een bepaalde trans formatie uit te voeren en vervolgens een `PipelineData`- *object te laten* uitvoeren.
>
> 2. Gebruik het `PipelineData` *uitvoer object* in de voor gaande stap als *invoer object*. Herhaal dit voor de volgende stappen.

In dit scenario maakt u `Dataset`-objecten die overeenkomen met de gegevens opslag mappen voor zowel de invoer installatie kopieën als de classificatie labels (y-test waarden). U maakt ook een `PipelineData`-object voor de batch Score-uitvoer gegevens.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Registreer vervolgens de gegevens sets in de werk ruimte.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Het model downloaden en registreren

Down load het voortrainde tensor flow-model om het te gebruiken voor batch scoreing in een pijp lijn. Maak eerst een lokale map waarin u het model opslaat. Vervolgens downloadt en extraheert u het model.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Registreer vervolgens het model in uw werk ruimte, zodat u het model eenvoudig kunt ophalen in het pijplijn proces. In de functie `register()` static is de para meter `model_name` de sleutel die u gebruikt om uw model in de SDK te vinden.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Het externe Compute-doel maken en koppelen

Machine learning-pijp lijnen kunnen niet lokaal worden uitgevoerd, dus u kunt ze uitvoeren op cloud resources of *externe Compute-doelen*. Een extern Compute-doel is een herbruikbare virtuele Compute-omgeving waarin u experimenten en machine learning werk stromen uitvoert. 

Voer de volgende code uit om een [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) doel met GPU te maken en koppel deze vervolgens aan uw werk ruimte. Zie het [conceptuele artikel](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)voor meer informatie over Compute-doelen.


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Een score script schrijven

Als u de score wilt uitvoeren, maakt u een batch Score script met de naam `batch_scoring.py`en schrijft u het naar de huidige map. Het script accepteert invoer installatie kopieën, past het classificatie model toe en voert vervolgens de voor spellingen uit naar een bestand met resultaten.

Het `batch_scoring.py` script neemt de volgende para meters, die worden door gegeven aan de `ParallelRunStep` die u later maakt:

- `--model_name`: de naam van het model dat wordt gebruikt.
- `--labels_name`: de naam van de `Dataset` die het `labels.txt`-bestand bevat.

De pijplijn infrastructuur gebruikt de `ArgumentParser` klasse om para meters door te geven aan pijplijn stappen. De eerste argument `--model_name` bijvoorbeeld in de volgende code de eigenschaps-id `model_name`. In de functie `init()` wordt `Model.get_model_path(args.model_name)` gebruikt om toegang te krijgen tot deze eigenschap.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> De pijp lijn in deze zelf studie heeft slechts één stap en schrijft de uitvoer naar een bestand. Voor pijp lijnen met meerdere stappen kunt u `ArgumentParser` ook gebruiken om een directory te definiëren voor het schrijven van uitvoer gegevens voor invoer naar volgende stappen. Zie het [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)voor een voor beeld van het door geven van gegevens tussen meerdere pijplijn stappen met behulp van het `ArgumentParser` ontwerp patroon.

## <a name="build-the-pipeline"></a>De pijp lijn bouwen

Voordat u de pijp lijn uitvoert, maakt u een-object dat de python-omgeving definieert en maakt u de afhankelijkheden die uw `batch_scoring.py` script nodig heeft. De vereiste belangrijkste afhankelijkheid is tensor flow, maar u installeert ook `azureml-defaults` voor achtergrond processen. Maak een `RunConfiguration`-object met behulp van de afhankelijkheden. Geef ook docker en docker-GPU-ondersteuning op.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>De configuratie maken om het script uit te pakken

Maak de pijplijn stap met behulp van het script, de configuratie van de omgeving en de parameters. Geef het berekenings doel op dat u al aan uw werk ruimte hebt gekoppeld.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>De stap pijplijn maken

Een pijplijn stap is een object dat alles inkapselt dat u nodig hebt om een pijp lijn uit te voeren, waaronder:

* Omgeving en afhankelijkheids instellingen
* De reken resource waarmee de pijp lijn moet worden uitgevoerd
* Invoer-en uitvoer gegevens en aangepaste para meters
* Verwijzing naar een script of SDK-logica om uit te voeren tijdens de stap

Meerdere klassen nemen eigenschappen over van de bovenliggende klasse [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). U kunt klassen kiezen voor het gebruik van specifieke frameworks of stapels om een stap te maken. In dit voor beeld gebruikt u de klasse `ParallelRunStep` om uw stap logica te definiëren met behulp van een aangepast python-script. Als een argument voor uw script een invoer is voor de stap of een uitvoer van de stap, moet het argument *zowel* in de `arguments`-matrix *als* in de `input` of de `output` para meter worden gedefinieerd. 

In scenario's waarin er meer dan één stap is, wordt een object verwijzing in de matrix van `outputs` beschikbaar als *invoer* voor een volgende pijplijn stap.

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Zie het [pakket stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)voor een lijst met alle klassen die u voor verschillende stap typen kunt gebruiken.

## <a name="run-the-pipeline"></a>De pijplijn uitvoeren

Voer nu de pijp lijn uit. Maak eerst een `Pipeline`-object met behulp van uw werkruimte referentie en de door u gemaakte pijplijn stap. De para meter `steps` is een matrix met stappen. In dit geval is er slechts één stap voor batch scoreing. Als u pijp lijnen met meerdere stappen wilt maken, plaatst u de stappen in volg orde in deze matrix.

Gebruik vervolgens de functie `Experiment.submit()` om de pijp lijn voor uitvoering in te dienen. U geeft ook de aangepaste para meter `param_batch_size`op. De functie `wait_for_completion` voert Logboeken uit tijdens het bouwen van de pijp lijn. U kunt de Logboeken gebruiken om de huidige voortgang te bekijken.

> [!IMPORTANT]
> De eerste uitvoering van de pijp lijn duurt ongeveer *15 minuten*. Alle afhankelijkheden moeten worden gedownload, een docker-installatie kopie wordt gemaakt en de python-omgeving wordt ingericht en gemaakt. Het uitvoeren van de pijp lijn vergt aanzienlijk minder tijd, omdat deze resources opnieuw worden gebruikt in plaats van worden gemaakt. De totale uitvoerings tijd voor de pijp lijn is echter afhankelijk van de werk belasting van uw scripts en de processen die in elke pijplijn stap worden uitgevoerd.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>De uitvoer downloaden en bekijken

Voer de volgende code uit om het uitvoer bestand te downloaden dat is gemaakt op basis van het `batch_scoring.py` script. Bekijk vervolgens de Score resultaten.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publiceren en uitvoeren vanuit een REST-eind punt

Voer de volgende code uit om de pijp lijn te publiceren naar uw werk ruimte. In uw werk ruimte in Azure Machine Learning Studio kunt u meta gegevens voor de pijp lijn zien, inclusief uitvoerings geschiedenis en duur. U kunt de pijp lijn ook hand matig uitvoeren vanuit de Studio.

Als u de pijp lijn publiceert, wordt een REST-eind punt ingeschakeld dat u kunt gebruiken om de pijp lijn uit te voeren vanuit elke HTTP-bibliotheek op elk platform.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Als u de pijp lijn vanuit het REST-eind punt wilt uitvoeren, hebt u een OAuth2-verificatie-header nodig. In het volgende voor beeld wordt gebruikgemaakt van interactieve verificatie (voor beeld doeleinden), maar voor de meeste productie scenario's waarvoor geautomatiseerde of headless authenticatie is vereist, moet u de Service-Principal-verificatie gebruiken, zoals [beschreven in dit artikel](how-to-setup-authentication.md).

Voor Service-Principal-verificatie is het maken van een *app-registratie* in *Azure Active Directory*vereist. Eerst genereert u een client geheim en verleent u vervolgens uw Service Principal *Role toegang* tot uw machine learning-werk ruimte. Gebruik de klasse [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) om uw verificatie stroom te beheren. 

Zowel [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) als `ServicePrincipalAuthentication` nemen over van `AbstractAuthentication`. In beide gevallen gebruikt u de functie [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) op dezelfde manier om de header op te halen:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Haal de REST-URL op uit de eigenschap `endpoint` van het gepubliceerde pijplijn object. U kunt ook de REST-URL vinden in uw werk ruimte in Azure Machine Learning Studio. 

Bouw een HTTP POST-aanvraag voor het eind punt. Geef uw Authentication-Header op in de aanvraag. Voeg een JSON Payload-object toe met de naam van het experiment en de para meter voor Batch grootte. Zoals eerder in de zelf studie is vermeld, wordt `param_batch_size` door gegeven aan uw `batch_scoring.py` script omdat u deze hebt gedefinieerd als een `PipelineParameter`-object in de stap configuratie.

Voer de aanvraag uit om de uitvoering te activeren. Neem code op om toegang te krijgen tot de `Id` sleutel uit de antwoord woordenlijst om de waarde van de run-ID op te halen.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Gebruik de run-ID om de status van de nieuwe uitvoering te controleren. De nieuwe uitvoering heeft nog een 10-15 minuten om te volt ooien. 

De nieuwe uitvoering ziet er ongeveer uit zoals de pijp lijn die u eerder in de zelf studie hebt uitgevoerd. U kunt ervoor kiezen om de volledige uitvoer niet weer te geven.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Resources opschonen

Vul deze sectie niet in als u andere zelf studies van Azure Machine Learning wilt uitvoeren.

### <a name="stop-the-compute-instance"></a>Het reken exemplaar stoppen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om gebruik te maken van de resources die u hebt gemaakt, kunt u ze verwijderen zodat er geen kosten voor in rekening worden gebracht:

1. Selecteer **resource groepen**in het menu links in de Azure Portal.
1. Selecteer in de lijst met resource groepen de resource groep die u hebt gemaakt.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van de resourcegroup in. Selecteer vervolgens **verwijderen**.

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Geef de werk ruimte-eigenschappen weer en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze machine learning zelf studie over pijp lijnen hebt u de volgende taken gedaan:

> [!div class="checklist"]
> * Een pijp lijn met omgevings afhankelijkheden gebouwd om te worden uitgevoerd op een externe GPU-reken resource.
> * Een score script gemaakt voor het uitvoeren van batch voorspellingen met behulp van een pretraind tensor flow-model.
> * Er is een pijp lijn gepubliceerd en deze ingeschakeld om te worden uitgevoerd vanaf een REST-eind punt.

Voor meer voor beelden van het bouwen van pijp lijnen met behulp van de machine learning SDK, raadpleegt u de [opslag plaats voor notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
