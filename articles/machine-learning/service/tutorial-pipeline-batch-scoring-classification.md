---
title: 'Zelf studie: ML pijp lijnen voor batch scores'
titleSuffix: Azure Machine Learning
description: In deze zelf studie bouwt u een machine learning-pijp lijn voor het uitvoeren van batch scoreing op een afbeeldings classificatie model in Azure Machine Learning. Machine learning-pijp lijnen Optimaliseer uw werk stroom met snelheid, portabiliteit en hergebruik, zodat u zich kunt concentreren op uw expertise-machine learning in plaats van op infra structuur en automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 11d57031405939e37afade9a9452c5d3a8b9e1e4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483292"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Zelf studie: een Azure Machine Learning-pijp lijn bouwen voor batch Score

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelf studie gebruikt u een pijp lijn in Azure Machine Learning om een batch Score taak uit te voeren. In het voor beeld wordt het convolutional Neural Network tensor flow model van het vooraf getrainde start [-v3-](https://arxiv.org/abs/1512.00567) netwerk gebruikt om niet-gelabelde afbeeldingen te classificeren. Nadat u een pijp lijn hebt gemaakt en gepubliceerd, configureert u een REST-eind punt dat u kunt gebruiken om de pijp lijn te activeren vanuit elke HTTP-bibliotheek op elk platform.

Machine learning-pijp lijnen Optimaliseer uw werk stroom met snelheid, portabiliteit en hergebruik, zodat u zich kunt concentreren op uw expertise-machine learning in plaats van op infra structuur en automatisering. Meer [informatie over machine learning pijp lijnen](concept-ml-pipelines.md).

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een werk ruimte configureren en voorbeeld gegevens downloaden
> * Gegevens objecten maken om gegevens op te halen en uit te voeren
> * Het model downloaden, voorbereiden en registreren in uw werk ruimte
> * Berekenings doelen inrichten en een score script maken
> * Een pijp lijn bouwen, uitvoeren en publiceren
> * Een REST-eind punt inschakelen voor de pijp lijn

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

## <a name="prerequisites"></a>Vereisten

* Als u nog geen virtuele machine voor Azure Machine Learning-werk ruimte of-notebook hebt, kunt u [deel 1 van de zelf studie voor Setup](tutorial-1st-experiment-sdk-setup.md)volt ooien.
* Wanneer u de installatie-zelf studie hebt voltooid, gebruikt u dezelfde notebook server om het notitie blok *zelf studies/tutorial-pipeline-batch-scoring-Classification. ipynb* te openen.

Als u de installatie-zelf studie in uw eigen [lokale omgeving](how-to-configure-environment.md#local)wilt uitvoeren, kunt u de zelf studie openen op [github](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Voer `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` uit om de vereiste pakketten op te halen.

## <a name="configure-workspace-and-create-a-datastore"></a>Een werk ruimte configureren en een gegevens opslag maken

Maak een werkruimte object op basis van de bestaande Azure Machine Learning-werk ruimte.

- Een [werk ruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) is een klasse die uw Azure-abonnement en resource gegevens accepteert. De werk ruimte maakt ook een Cloud resource die u kunt gebruiken om uw model uitvoeringen te bewaken en bij te houden. 
- `Workspace.from_config()` leest het `config.json` bestand en laadt vervolgens de verificatie gegevens in een object met de naam `ws`. Het `ws`-object wordt in de code in deze zelf studie gebruikt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Een gegevens opslag voor voorbeeld installatie kopieën maken

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

## <a name="create-data-objects"></a>Gegevens objecten maken

Wanneer u een pijp lijn bouwt, worden in een `DataReference`-object gegevens uit de werk ruimte opslag plek gelezen. Met een `PipelineData` object worden tussenliggende gegevens tussen pijplijn stappen overgedragen.

> [!Important]
> In het voor beeld van een batch Score in deze zelf studie wordt slechts één pijplijn stap gebruikt. In use-cases met meerdere stappen bevat de typische stroom de volgende stappen:
>
> 1. Gebruik `DataReference`-objecten als *invoer* om onbewerkte gegevens op te halen, een bepaalde trans formatie uit te voeren en vervolgens een `PipelineData`- *object te laten* uitvoeren.
>
> 2. Gebruik het `PipelineData` *uitvoer object* in de voor gaande stap als *invoer object*. Herhaal dit voor de volgende stappen.

In dit scenario maakt u `DataReference`-objecten die overeenkomen met de gegevens opslag mappen voor zowel de invoer installatie kopieën als de classificatie labels (y-test waarden). U maakt ook een `PipelineData`-object voor de batch Score-uitvoer gegevens.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
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

Voer de volgende code uit om een [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) doel met GPU te maken en koppel deze vervolgens aan uw werk ruimte. Zie het [conceptuele artikel](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target)voor meer informatie over Compute-doelen.


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

Het `batch_scoring.py` script neemt de volgende para meters, die worden door gegeven uit de pijplijn stap die u later in deze zelf studie maakt:

- `--model_name`: de naam van het model dat wordt gebruikt.
- `--label_dir`: de map die het `labels.txt`-bestand bevat.
- `--dataset_path`: de map met de invoer installatie kopieën.
- `--output_dir`: de uitvoermap voor het `results-label.txt`-bestand nadat het script het model op de gegevens heeft uitgevoerd.
- `--batch_size`: de Batch grootte die wordt gebruikt voor het uitvoeren van het model.

De pijplijn infrastructuur gebruikt de `ArgumentParser` klasse om para meters door te geven aan pijplijn stappen. De eerste argument `--model_name` bijvoorbeeld in de volgende code de eigenschaps-id `model_name`. In de functie `main()` wordt `Model.get_model_path(args.model_name)` gebruikt om toegang te krijgen tot deze eigenschap.


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
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> De pijp lijn in deze zelf studie heeft slechts één stap en schrijft de uitvoer naar een bestand. Voor pijp lijnen met meerdere stappen kunt u `ArgumentParser` ook gebruiken om een directory te definiëren voor het schrijven van uitvoer gegevens voor invoer naar volgende stappen. Zie het [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)voor een voor beeld van het door geven van gegevens tussen meerdere pijplijn stappen met behulp van het `ArgumentParser` ontwerp patroon.

## <a name="build-and-run-the-pipeline"></a>De pijp lijn bouwen en uitvoeren

Voordat u de pijp lijn uitvoert, maakt u een-object dat de python-omgeving definieert en maakt u de afhankelijkheden die uw `batch_scoring.py` script nodig heeft. De vereiste belangrijkste afhankelijkheid is tensor flow, maar u installeert ook `azureml-defaults` van de SDK voor achtergrond processen. Maak een `RunConfiguration`-object met behulp van de afhankelijkheden. Geef ook docker en docker-GPU-ondersteuning op.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>De pijp lijn para meters

Definieer een aangepaste para meter voor de pijp lijn om de Batch grootte te bepalen. Nadat de pijp lijn is gepubliceerd en weer beschikbaar is via een REST-eind punt, worden geconfigureerde para meters ook weer gegeven. U kunt aangepaste para meters opgeven in de JSON-nettolading wanneer u de pijp lijn opnieuw uitvoert via een HTTP-aanvraag.

Maak een `PipelineParameter`-object om dit gedrag in te scha kelen en een naam en standaard waarde te definiëren.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>De stap pijplijn maken

Een pijplijn stap is een object dat alles inkapselt dat u nodig hebt om een pijp lijn uit te voeren, waaronder:

* Omgeving en afhankelijkheids instellingen
* De reken resource waarmee de pijp lijn moet worden uitgevoerd
* Invoer-en uitvoer gegevens en aangepaste para meters
* Verwijzing naar een script of SDK-logica om uit te voeren tijdens de stap

Meerdere klassen nemen eigenschappen over van de bovenliggende klasse [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). U kunt klassen kiezen voor het gebruik van specifieke frameworks of stapels om een stap te maken. In dit voor beeld gebruikt u de klasse [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) om uw stap logica te definiëren met behulp van een aangepast python-script. Als een argument voor uw script een invoer is voor de stap of een uitvoer van de stap, moet het argument *zowel* in de `arguments`-matrix *als* in de `input` of de `output` para meter worden gedefinieerd. 

In scenario's waarin er meer dan één stap is, wordt een object verwijzing in de matrix van `outputs` beschikbaar als *invoer* voor een volgende pijplijn stap.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Zie het [pakket stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)voor een lijst met alle klassen die u voor verschillende stap typen kunt gebruiken.

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren

Voer nu de pijp lijn uit. Maak eerst een `Pipeline`-object met behulp van uw werkruimte referentie en de door u gemaakte pijplijn stap. De para meter `steps` is een matrix met stappen. In dit geval is er slechts één stap voor batch scoreing. Als u pijp lijnen met meerdere stappen wilt maken, plaatst u de stappen in volg orde in deze matrix.

Gebruik vervolgens de functie `Experiment.submit()` om de pijp lijn voor uitvoering in te dienen. U geeft ook de aangepaste para meter `param_batch_size`op. De functie `wait_for_completion` voert Logboeken uit tijdens het bouwen van de pijp lijn. U kunt de Logboeken gebruiken om de huidige voortgang te bekijken.

> [!IMPORTANT]
> De eerste uitvoering van de pijp lijn duurt ongeveer *15 minuten*. Alle afhankelijkheden moeten worden gedownload, een docker-installatie kopie wordt gemaakt en de python-omgeving wordt ingericht en gemaakt. Het uitvoeren van de pijp lijn vergt aanzienlijk minder tijd, omdat deze resources opnieuw worden gebruikt in plaats van worden gemaakt. De totale uitvoerings tijd voor de pijp lijn is echter afhankelijk van de werk belasting van uw scripts en de processen die in elke pijplijn stap worden uitgevoerd.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>De uitvoer downloaden en bekijken

Voer de volgende code uit om het uitvoer bestand te downloaden dat is gemaakt op basis van het `batch_scoring.py` script. Bekijk vervolgens de Score resultaten.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Bestands</th>
      <th>voorspelling</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian Ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>stat</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>Typewriter-toetsen bord</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>zijde Terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Windsor koppelen</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>oogstman</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>viool</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>luid</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>apron</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>Amerikaans Lobster</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publiceren en uitvoeren vanuit een REST-eind punt

Voer de volgende code uit om de pijp lijn te publiceren naar uw werk ruimte. In uw werk ruimte in Azure Machine Learning Studio kunt u meta gegevens voor de pijp lijn zien, inclusief uitvoerings geschiedenis en duur. U kunt de pijp lijn ook hand matig uitvoeren vanuit de Studio.

Als u de pijp lijn publiceert, wordt een REST-eind punt ingeschakeld dat u kunt gebruiken om de pijp lijn uit te voeren vanuit elke HTTP-bibliotheek op elk platform.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Als u de pijp lijn vanuit het REST-eind punt wilt uitvoeren, hebt u een OAuth2-verificatie-header nodig. In het volgende voor beeld wordt gebruikgemaakt van interactieve verificatie (voor beeld doeleinden), maar voor de meeste productie scenario's waarvoor geautomatiseerde of headless authenticatie is vereist, gebruikt u Service-Principal-verificatie, zoals [beschreven in dit notitie blok](https://aka.ms/pl-restep-auth).

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

### <a name="stop-the-notebook-vm"></a>De VM van het notebook stoppen

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

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
