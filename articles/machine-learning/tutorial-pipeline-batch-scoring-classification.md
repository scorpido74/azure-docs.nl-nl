---
title: 'Zelfstudie: ML-pijplijnen voor batchscores'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie bouwt u een machine learning-pijplijn om batchscores uit te voeren op een afbeeldingsclassificatiemodel. Met Azure Machine Learning u zich richten op machine learning in plaats van infrastructuur en automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: 1ccd7a7f33c6ee5cab8b7173d8eb93365b6cb587
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472217"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Zelfstudie: Een Azure Machine Learning-pijplijn maken voor batchscores

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het maken van een pijplijn in Azure Machine Learning om een batchscoringstaak uit te voeren. Machine learning-pijplijnen optimaliseren uw workflow met snelheid, draagbaarheid en hergebruik, zodat u zich concentreren op machine learning in plaats van infrastructuur en automatisering. Nadat u een pijplijn hebt gemaakt en gepubliceerd, configureert u een REST-eindpunt dat u gebruiken om de pijplijn vanuit elke HTTP-bibliotheek op elk platform te activeren. 

Het voorbeeld maakt gebruik van een vooraf getraind [Inception-V3](https://arxiv.org/abs/1512.00567) convolutional neurale netwerk model geïmplementeerd in Tensorflow om niet-gelabelde afbeeldingen te classificeren. [Meer informatie over machine learning-pijplijnen](concept-ml-pipelines.md).

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Werkruimte configureren 
> * Voorbeeldgegevens downloaden en opslaan
> * Gegevenssetobjecten maken om gegevens op te halen en uit te brengen
> * Het model downloaden, voorbereiden en registreren in uw werkruimte
> * Compute targets inrichten en een scorescript maken
> * De `ParallelRunStep` klasse gebruiken voor het scoren van async-batch
> * Een pijplijn bouwen, uitvoeren en publiceren
> * Een REST-eindpunt voor de pijplijn inschakelen

Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Vereisten

* Als u nog geen Azure Machine Learning-werkruimte of virtuele notebookhebt, vult u [deel 1 van de installatiezelfstudie in.](tutorial-1st-experiment-sdk-setup.md)
* Wanneer u klaar bent met de zelfstudie met de installatie, gebruikt u dezelfde notebookserver om het *notitieblok tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb* te openen.

Als u de zelfstudie voor het instellen in uw eigen [lokale omgeving](how-to-configure-environment.md#local)wilt uitvoeren, u de zelfstudie openen op [GitHub.](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) Run `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` om de vereiste pakketten te krijgen.

## <a name="configure-workspace-and-create-a-datastore"></a>Werkruimte configureren en een gegevensarchief maken

Maak een werkruimteobject uit de bestaande Azure Machine Learning-werkruimte.

- Een [werkruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) is een klasse die uw Azure-abonnement en brongegevens accepteert. De werkruimte maakt ook een cloudbron die u gebruiken om uw modeluitvoeringen te controleren en bij te houden. 
- `Workspace.from_config()`leest `config.json` het bestand en laadt vervolgens de `ws`verificatiegegevens in een object met de naam . Het `ws` object wordt gebruikt in de code tijdens deze zelfstudie.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Een gegevensarchief maken voor voorbeeldafbeeldingen

Download `pipelinedata` op het account het voorbeeld van `sampledata` openbare gegevens van ImageNet-evaluatie uit de openbare blobcontainer. Oproep `register_azure_blob_container()` om de gegevens beschikbaar te `images_datastore`maken voor de werkruimte onder de naam . Stel vervolgens het standaardgegevensarchief van de werkruimte in als het uitvoergegevensarchief. Gebruik het uitvoergegevensarchief om de uitvoer in de pijplijn te scoren.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Gegevenssetobjecten maken

Bij het bouwen `Dataset` van pijplijnen worden objecten gebruikt `PipelineData` voor het lezen van gegevens uit gegevenswinkels van werkruimteen en worden objecten gebruikt voor het overbrengen van tussenliggende gegevens tussen pijplijnstappen.

> [!Important]
> Het voorbeeld van batchscores in deze zelfstudie gebruikt slechts één pijplijnstap. In use cases met meerdere stappen bevat de typische stroom de volgende stappen:
>
> 1. Gebruik `Dataset` objecten als *invoer* om ruwe gegevens op te `PipelineData` halen, een transformatie uit te voeren en vervolgens een object *uit te voeren.*
>
> 2. Gebruik `PipelineData` het *uitvoerobject* in de vorige stap als *invoerobject*. Herhaal het voor de volgende stappen.

In dit scenario `Dataset` maakt u objecten die overeenkomen met de mappen van de datastore voor zowel de invoerafbeeldingen als de classificatielabels (y-testwaarden). U maakt `PipelineData` ook een object voor de uitvoergegevens van batchscores.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Registreer vervolgens de gegevenssets in de werkruimte.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Het model downloaden en registreren

Download het vooraf getrainde Tensorflow-model om het te gebruiken voor batchscores in een pijplijn. Maak eerst een lokale map waar u het model opslaat. Download en haal vervolgens het model eruit.

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

Registreer vervolgens het model in uw werkruimte, zodat u het model eenvoudig ophalen in het pijplijnproces. In `register()` de statische `model_name` functie is de parameter de sleutel die u gebruikt om uw model in de SDK te lokaliseren.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Het externe rekendoel maken en koppelen

Machine learning-pijplijnen kunnen niet lokaal worden uitgevoerd, dus u voert ze uit op cloudbronnen of *externe rekendoelen.* Een remote compute target is een herbruikbare virtuele compute omgeving waar u experimenten en machine learning workflows uitvoert. 

Voer de volgende code uit om [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) een GPU-doel te maken en deze vervolgens aan uw werkruimte toe te voegen. Zie het [conceptueel artikel voor](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)meer informatie over rekendoelen.


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

## <a name="write-a-scoring-script"></a>Een scorescript schrijven

Als u de score wilt uitvoeren, maakt u een script voor batchscores met de aangeroepen `batch_scoring.py`en schrijft u het naar de huidige map. Het script neemt invoerafbeeldingen, past het classificatiemodel toe en voert vervolgens de voorspellingen uit naar een resultatenbestand.

Het `batch_scoring.py` script neemt de volgende parameters, `ParallelRunStep` die worden doorgegeven van de u later maken:

- `--model_name`: De naam van het model dat wordt gebruikt.
- `--labels_name`: De naam `Dataset` van `labels.txt` de die het bestand bevat.

De pijplijninfrastructuur `ArgumentParser` gebruikt de klasse om parameters door te geven in pijplijnstappen. In de volgende code wordt bijvoorbeeld `--model_name` het eerste `model_name`argument de eigenschaps-id gegeven . In `init()` de `Model.get_model_path(args.model_name)` functie wordt gebruikt om toegang te krijgen tot deze eigenschap.


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
> De pijplijn in deze zelfstudie heeft slechts één stap en schrijft de uitvoer naar een bestand. Voor pijplijnen in meerdere stappen `ArgumentParser` gebruikt u ook een map om uitvoergegevens te schrijven voor invoer naar volgende stappen. Zie het [notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)voor een voorbeeld van `ArgumentParser` het doorgeven van gegevens tussen meerdere pijplijnstappen met behulp van het ontwerppatroon.

## <a name="build-the-pipeline"></a>De pijplijn bouwen

Voordat u de pijplijn uitvoert, maakt u een object dat `batch_scoring.py` de Python-omgeving definieert en de afhankelijkheden maakt die uw script vereist. De belangrijkste vereiste afhankelijkheid is Tensorflow, `azureml-defaults` maar u installeert ook voor achtergrondprocessen. Maak `RunConfiguration` een object met behulp van de afhankelijkheden. Geef ook Docker- en Docker-GPU-ondersteuning op.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>De configuratie maken om het script te verpakken

Maak de pijplijnstap met behulp van het script, de omgevingsconfiguratie en de parameters. Geef het rekendoel op dat u al aan uw werkruimte hebt gekoppeld.

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

### <a name="create-the-pipeline-step"></a>De pijplijnstap maken

Een pijplijnstap is een object dat alles inkapselt wat u nodig hebt om een pijplijn uit te voeren, waaronder:

* Omgevings- en afhankelijkheidsinstellingen
* De rekenbron waarop de pijplijn moet worden uitgevoerd
* Invoer- en uitvoergegevens en eventuele aangepaste parameters
* Verwijzing naar een script- of SDK-logica die tijdens de stap moet worden uitgevoerd

Meerdere klassen erven van [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)de bovenliggende klasse . U klassen kiezen om specifieke frameworks of stapels te gebruiken om een stap te maken. In dit voorbeeld gebruikt `ParallelRunStep` u de klasse om uw staplogica te definiëren met behulp van een aangepast Python-script. Als een argument voor uw script een invoer is voor de stap of een uitvoer `arguments` van de stap, moet het argument zowel in de array *als* in de *and* `input` of de `output` parameter worden gedefinieerd. 

In scenario's waarin er meer dan één `outputs` stap is, wordt een objectverwijzing in de array beschikbaar als *invoer* voor een volgende pijplijnstap.

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

Zie het [stappenpakket](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)voor een lijst met alle klassen die u voor verschillende staptypen gebruiken.

## <a name="submit-the-pipeline"></a>De pijplijn verzenden

Nu, voer de pijpleiding. Maak eerst `Pipeline` een object met behulp van uw werkruimteverwijzing en de pijplijnstap die u hebt gemaakt. De `steps` parameter is een reeks stappen. In dit geval is er slechts één stap voor batchscores. Als u pijplijnen wilt maken met meerdere stappen, plaatst u de stappen in volgorde in deze array.

Gebruik vervolgens `Experiment.submit()` de functie om de pijplijn in te dienen voor uitvoering. U geeft ook `param_batch_size`de aangepaste parameter op. De `wait_for_completion` functie-uitgangen logboeken tijdens het pijplijnbouwproces. U de logboeken gebruiken om de huidige voortgang te zien.

> [!IMPORTANT]
> De eerste pijplijnrun duurt ongeveer *15 minuten.* Alle afhankelijkheden moeten worden gedownload, er wordt een Docker-afbeelding gemaakt en de Python-omgeving is ingericht en gemaakt. Het opnieuw uitvoeren van de pijplijn neemt aanzienlijk minder tijd in beslag omdat deze resources opnieuw worden gebruikt in plaats van worden gemaakt. De totale looptijd van de pijplijn is echter afhankelijk van de werkbelasting van uw scripts en de processen die in elke pijplijnstap worden uitgevoerd.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Uitvoer downloaden en controleren

Voer de volgende code uit om het uitvoerbestand te downloaden dat is gemaakt met het `batch_scoring.py` script. Bekijk vervolgens de scoreresultaten.

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

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publiceren en uitvoeren vanaf een REST-eindpunt

Voer de volgende code uit om de pijplijn naar uw werkruimte te publiceren. In uw werkruimte in Azure Machine Learning-studio u metagegevens voor de pijplijn bekijken, inclusief rungeschiedenis en -duur. U de pijplijn ook handmatig uitvoeren vanuit de studio.

Als u de pijplijn publiceert, u een REST-eindpunt gebruiken waarmee u de pijplijn vanuit elke HTTP-bibliotheek op elk platform uitvoeren.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Als u de pijplijn vanaf het REST-eindpunt wilt uitvoeren, hebt u een verificatiekop van OAuth2-drager nodig. In het volgende voorbeeld wordt interactieve verificatie (ter illustratie) gebruikt, maar voor de meeste productiescenario's waarvoor automatische of headless-verificatie vereist is, gebruikt u serviceprincipal-verificatie zoals [beschreven in dit artikel.](how-to-setup-authentication.md)

Serviceprincipal authentication omvat het maken van een *app-registratie* in *Azure Active Directory*. Eerst genereert u een klantgeheim en vervolgens verleent u uw servicebelangrijkste *rol toegang tot* uw machine learning-werkruimte. Gebruik [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) de klasse om uw verificatiestroom te beheren. 

Beide [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) `ServicePrincipalAuthentication` en `AbstractAuthentication`erven van . Gebruik de [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) functie in beide gevallen op dezelfde manier om de koptekst op te halen:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Haal de URL `endpoint` REST uit de eigenschap van het gepubliceerde pijplijnobject. U de URL van REST ook vinden in uw werkruimte in Azure Machine Learning-studio. 

Een HTTP POST-verzoek bouwen naar het eindpunt. Geef uw verificatiekop op in de aanvraag. Voeg een JSON-payloadobject toe met de naam van het experiment en de parameter batchgrootte. Zoals eerder in de `param_batch_size` zelfstudie is `batch_scoring.py` opgemerkt, wordt doorgegeven `PipelineParameter` aan uw script, omdat u het gedefinieerd als een object in de stap configuratie.

Doe het verzoek om de run te activeren. Voeg code toe `Id` om toegang te krijgen tot de sleutel uit het antwoordwoordenboek om de waarde van de run-id op te halen.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Gebruik de run-ID om de status van de nieuwe run te controleren. De nieuwe run duurt nog 10-15 min te voltooien. 

De nieuwe run ziet er vergelijkbaar uit als de pijplijn die u eerder in de zelfstudie hebt uitgevoerd. U ervoor kiezen om de volledige uitvoer niet te bekijken.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Resources opschonen

Voltooi deze sectie niet als u van plan bent andere Azure Machine Learning-zelfstudies uit te voeren.

### <a name="stop-the-compute-instance"></a>De rekeninstantie stoppen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om gebruik te maken van de resources die u hebt gemaakt, kunt u ze verwijderen zodat er geen kosten voor in rekening worden gebracht:

1. Selecteer **resourcegroepen**in de Azure-portal in het linkermenu .
1. Selecteer in de lijst met resourcegroepen de resourcegroep die u hebt gemaakt.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Geef de eigenschappen van de werkruimte weer en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie met machine learning-pijplijnen hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> * Bouwde een pijplijn met omgevingsafhankelijkheden om uit te voeren op een externe GPU-compute resource.
> * Een scorescript gemaakt om batchvoorspellingen uit te voeren met behulp van een vooraf getraind Tensorflow-model.
> * Een pijplijn gepubliceerd en deze kunnen worden uitgevoerd vanaf een REST-eindpunt.

Zie de [notebookrepository](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)voor meer voorbeelden van het bouwen van pijplijnen met behulp van de machine learning SDK.
