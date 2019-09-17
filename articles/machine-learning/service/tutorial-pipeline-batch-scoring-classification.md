---
title: 'Zelfstudie: Azure ML-pijp lijnen voor batch Score'
titleSuffix: Azure Machine Learning
description: Bouw een ML-pijp lijn voor het uitvoeren van batch scoreing op een afbeeldings classificatie model. Machine learning-pijp lijnen Optimaliseer uw werk stroom met snelheid, portabiliteit en hergebruik zodat u zich kunt concentreren op uw expertise, machine learning in plaats van op de infra structuur en automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 15a11ba74262ec5a354f0cb3fe22c09167c8d5a6
ms.sourcegitcommit: d15b23e23328ce7502dd3d2846b49fd2d6d8209c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005399"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Azure Machine Learning pijplijnen gebruiken voor batch scores

In deze zelf studie gebruikt u Azure Machine Learning pijp lijnen om een batch Score taak uit te voeren. In dit voor beeld wordt [het convolutional Neural](https://arxiv.org/abs/1512.00567) Network tensor flow model van vooraf getraind gemaakt. Nadat u een pijp lijn hebt gemaakt en gepubliceerd, configureert u een REST-eind punt zodat u de pijp lijn kunt activeren vanuit elke HTTP-bibliotheek op elk platform.

Machine learning-pijp lijnen Optimaliseer uw werk stroom met snelheid, portabiliteit en hergebruik zodat u zich kunt concentreren op uw expertise, machine learning in plaats van op de infra structuur en automatisering. Meer [informatie over ml-pijp lijnen](concept-ml-pipelines.md).

In deze zelf studie leert u de volgende taken:

> [!div class="checklist"]
> * Een werk ruimte configureren en voorbeeld gegevens downloaden
> * Gegevens objecten maken om gegevens op te halen en uit te voeren
> * Het model downloaden, voorbereiden en registreren in uw werk ruimte
> * Berekenings doelen inrichten en een score script maken
> * Een pijp lijn bouwen, uitvoeren en publiceren
> * Een REST-eind punt inschakelen voor de pijp lijn

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

## <a name="prerequisites"></a>Vereisten

* Voltooi [deel 1 van de zelf studie voor Setup](tutorial-1st-experiment-sdk-setup.md) als u nog geen virtuele Machine voor Azure machine learning-werk ruimte of-notebook hebt.
* Nadat u de installatie zelf studie hebt voltooid, opent u de notebook **zelf studies/tutorial-pipeline-batch-scoring-Classification. ipynb** met dezelfde notebook server.

Deze zelf studie is ook beschikbaar op [github](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) als u deze wilt uitvoeren in uw eigen [lokale omgeving](how-to-configure-environment.md#local). Voer `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` uit om de vereiste pakketten op te halen.

## <a name="configure-workspace-and-create-datastore"></a>Werk ruimte configureren en gegevens opslag maken

Maak een werkruimte object op basis van de bestaande Azure Machine Learning-werk ruimte. 
+ Een [werk ruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) is een klasse die uw Azure-abonnement en resource gegevens accepteert. Hier wordt ook een cloudresource gemaakt om de uitvoeringen van uw model te controleren en bij te houden. 

+ `Workspace.from_config()`leest het bestand **config. json** en laadt de verificatie gegevens in een object `ws`met de naam. `ws` wordt gebruikt in de rest van de code in deze zelfstudie.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Een gegevens opslag voor voorbeeld installatie kopieën maken

Haal het voor beeld van open bare gegevens van de ImageNet- `sampledata` evaluatie versie op `pipelinedata`uit de open bare BLOB-container op het account. Met `register_azure_blob_container()` aanroepen worden de gegevens beschikbaar gemaakt voor de werk `images_datastore`ruimte onder de naam. Geef vervolgens de standaard gegevens opslag voor de werk ruimte op als uitvoer gegevens opslag, die u gebruikt voor het scoren van de uitvoer in de pijp lijn.

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

Wanneer u pijp lijnen bouwt `DataReference` , worden er objecten gebruikt voor het lezen van gegevens uit werk `PipelineData` ruimte-data stores en worden er objecten gebruikt voor het overzetten van tussenliggende gegevens tussen pijplijn stappen.

> [!Important]
> In dit voor beeld van een batch-score wordt slechts één pijplijn stap gebruikt, maar in use-cases met meerdere stappen omvat de typische stroom:
>
> 1. Het `DataReference` gebruik van objecten als **invoer** om onbewerkte gegevens op te halen, een aantal trans formaties uit te voeren **en vervolgens een** `PipelineData` object op te slaan.
>
> 2. Gebruik het **uitvoer object** van `PipelineData` de vorige stap als een *invoer object*, dat wordt herhaald voor volgende stappen.

Voor dit scenario maakt `DataReference` u objecten die overeenkomen met de gegevens opslag mappen voor zowel de invoer installatie kopieën als de classificatie labels (y-test waarden). U maakt ook een `PipelineData` object voor de batch Score-uitvoer gegevens.

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

Down load het vooraf getrainde tensor flow-model voor het gebruik van batch scores in de pijp lijn. Maak eerst een lokale map waar u het model opslaat en down load en pak het uit.

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

U registreert het model nu in uw werk ruimte, zodat u het eenvoudig kunt ophalen in het pijplijn proces. In de `register()` statische functie is de `model_name` para meter de sleutel die u gebruikt om uw model in de SDK te vinden.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Een extern Compute-doel maken en koppelen

Aangezien ML-pijp lijnen niet lokaal kunnen worden uitgevoerd, moet u deze uitvoeren op cloud resources. We verwijzen ernaar als externe Compute-doelen, een herbruikbare virtuele Compute-omgeving waarin u experimenten en ML werk stromen uitvoert. Voer de volgende code uit om een [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) doel voor GPU te maken en koppel dit aan uw werk ruimte. Zie het [conceptuele artikel](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target) voor meer informatie over Compute-doelen.


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

Als u de score wilt uitvoeren, maakt u een batch `batch_scoring.py`Score script en schrijft u het naar de huidige map. Het script accepteert invoer installatie kopieën, past het classificatie model toe en voert de voor spellingen uit naar een bestand met resultaten.

Het script `batch_scoring.py` neemt de volgende para meters, die worden opgehaald uit de pijplijn stap die u later in deze zelf studie maakt:

- `--model_name`: de naam van het model dat wordt gebruikt
- `--label_dir`: de map waarin het `labels.txt` bestand is ingedrukt 
- `--dataset_path`: de map met de invoer installatie kopieën
- `--output_dir`: het script voert het model uit op de gegevens en uitvoer a `results-label.txt` naar deze map
- `--batch_size`: de Batch grootte die wordt gebruikt voor het uitvoeren van het model

De infra structuur van pijp lijnen `ArgumentParser` gebruikt de-klasse om para meters door te geven aan pijplijn stappen. In de code onder het eerste argument `--model_name` krijgt u bijvoorbeeld de eigenschaps-id. `model_name` In de `main()` functie wordt deze eigenschap geopend met `Model.get_model_path(args.model_name)`.


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
> De pijp lijn in deze zelf studie heeft slechts één stap en schrijft de uitvoer naar een bestand, maar in pijp lijnen met meerdere stappen kunt u ook `ArgumentParser` een directory definiëren voor het schrijven van uitvoer gegevens voor invoer naar volgende stappen. Bekijk het [notitie blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) voor een voor beeld van het door geven van gegevens tussen meerdere `ArgumentParser` pijplijn stappen met behulp van het ontwerp patroon.

## <a name="build-and-run-the-pipeline"></a>De pijp lijn bouwen en uitvoeren

Voordat u de pijp lijn uitvoert, maakt u een-object dat de python-omgeving definieert en de afhankelijkheden die nodig zijn voor uw script `batch_scoring.py`. De vereiste belangrijkste afhankelijkheid is tensor flow, maar u kunt `azureml-defaults` ook voor achtergrond processen van de SDK installeren. Maak een `RunConfiguration` object met behulp van de afhankelijkheden en geef ook docker-en docker-GPU-ondersteuning op.

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

Definieer een aangepaste para meter voor de pijp lijn om de Batch grootte te bepalen. Nadat de pijp lijn is gepubliceerd en weer beschikbaar is via een REST-eind punt, worden geconfigureerde para meters ook weer gegeven en kunnen ze worden opgegeven in de JSON-Payload wanneer de pijp lijn opnieuw wordt uitgevoerd met een HTTP-aanvraag.

Maak een `PipelineParameter` object om dit gedrag in te scha kelen en definieer een naam en standaard waarde.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>De stap pijplijn maken

Een pijplijn stap is een object dat alles inkapselt dat u nodig hebt om een pijp lijn uit te voeren, waaronder:

* omgeving en afhankelijkheids instellingen
* de reken resource waarmee de pijp lijn moet worden uitgevoerd
* invoer-en uitvoer gegevens en aangepaste para meters
* verwijzing naar een script of SDK-logica die tijdens de stap moet worden uitgevoerd

Er zijn meerdere klassen die van de bovenliggende klasse [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) overnemen om te helpen bij het bouwen van een stap met bepaalde frameworks en stacks. In dit voor beeld gebruikt u de [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) -klasse om uw stap logica te definiëren met behulp van een aangepast python-script. Houd er rekening mee dat als een argument voor uw script een invoer is voor de stap of uitvoer van de stap, het moet **zowel** in de `arguments` matrix **als** in respectievelijk de `input` para meter or `output` worden gedefinieerd. 

Een object verwijzing in de `outputs` matrix wordt beschikbaar als **invoer** voor een volgende pijplijn stap, voor scenario's waarin er meer dan één stap is.

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

Voor een lijst met alle klassen voor verschillende stap typen raadpleegt u het [pakket met stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren

Nu voert u de pijp lijn uit. Maak eerst een `Pipeline` object met uw werkruimte referentie en de door u gemaakte pijplijn stap. De `steps` para meter is een reeks stappen, en in dit geval is er slechts één stap voor batch scoreing. Als u pijp lijnen met meerdere stappen wilt maken, plaatst u de stappen in de aangegeven volg orde in deze matrix.

Gebruik vervolgens de `Experiment.submit()` functie om de pijp lijn voor uitvoering in te dienen. U kunt ook de aangepaste para `param_batch_size`meter opgeven. De `wait_for_completion` functie voert Logboeken uit tijdens het opbouw proces van de pijp lijn, zodat u de huidige voortgang kunt zien.

> [!IMPORTANT]
> De eerste uitvoering van de pijp lijn duurt ongeveer **15 minuten**, omdat alle afhankelijkheden moeten worden gedownload, er een docker-installatie kopie wordt gemaakt en de python-omgeving wordt ingericht/gemaakt. Als deze opnieuw wordt uitgevoerd, duurt het aanzienlijk minder tijd wanneer deze resources opnieuw worden gebruikt. De totale uitvoerings tijd is echter afhankelijk van de werk belasting van uw scripts en processen die in elke pijplijn stap worden uitgevoerd.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>De uitvoer downloaden en bekijken

Voer de volgende code uit om het uitvoer bestand te downloaden dat `batch_scoring.py` u uit het script hebt gemaakt en bekijk vervolgens de Score resultaten.

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
      <th>Bestandsnaam</th>
      <th>voorspelling</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian ridgeback</td>
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

## <a name="publish-and-run-from-rest-endpoint"></a>Publiceren en uitvoeren vanaf REST-eind punt

Voer de volgende code uit om de pijp lijn te publiceren naar uw werk ruimte. In uw werk ruimte in de portal kunt u meta gegevens voor de pijp lijn zien, inclusief uitvoerings geschiedenis en duur. U kunt de pijp lijn ook hand matig uitvoeren vanuit de portal.

Daarnaast kan het publiceren van de pijp lijn een REST-eind punt uitvoeren om de pijp lijn uit te voeren vanuit elke HTTP-bibliotheek op elk platform.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Als u de pijp lijn vanuit het REST-eind punt wilt uitvoeren, hebt u eerst een OAuth2-verificatie-header nodig. In dit voor beeld wordt interactieve verificatie gebruikt voor afbeeldings doeleinden, maar voor de meeste productie scenario's waarvoor geautomatiseerd of headless verificatie is vereist, kunt u gebruikmaken van Service Principle-verificatie zoals [beschreven in dit notitie blok](https://aka.ms/pl-restep-auth).

Verificatie van Service principe bestaat uit het maken van een **app-registratie** in **Azure Active Directory**, het genereren van een client geheim en het verlenen van uw Service Principal **Role toegang** tot uw machine learning-werk ruimte. Vervolgens gebruikt u de [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) -klasse om uw verificatie stroom te beheren. 

Zowel `InteractiveLoginAuthentication` als `ServicePrincipalAuthentication` overnemen `get_authentication_header()` van `AbstractAuthentication`, en in beide gevallen gebruikt u de functie op dezelfde manier om de koptekst op te halen.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Haal de rest-URL op `endpoint` uit de eigenschap van het gepubliceerde pijplijn object. U kunt ook de REST-URL vinden in uw werk ruimte in de portal. Bouw een HTTP POST-aanvraag naar het eind punt, waarbij u de verificatie-header opgeeft. Voeg daarnaast een JSON Payload-object toe met de naam van het experiment en de para meter Batch grootte. Als herinnering wordt de `param_batch_size` door gegeven aan uw `batch_scoring.py` script omdat u het hebt gedefinieerd als een `PipelineParameter` object in de stap configuratie.

Voer de aanvraag uit om de uitvoering te activeren. Open de `Id` sleutel in de antwoord woordenlijst om de waarde van de run-id op te halen.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Gebruik de run-ID om de status van de nieuwe uitvoering te controleren. Dit duurt nog een aantal van 10-15 minuten en ziet er ongeveer uit als de vorige pijplijn uitvoering, dus als u geen andere pijplijn uitvoering wilt zien, kunt u de volledige uitvoer overs Laan.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Resources opschonen

Voltooi deze sectie niet als u van plan bent andere Azure Machine Learning zelf studies uit te voeren.

### <a name="stop-the-notebook-vm"></a>De VM van het notebook stoppen

Als u een Cloud notebook server hebt gebruikt, stopt u de virtuele machine wanneer u deze niet gebruikt om de kosten te verlagen.

1. Selecteer in uw werk ruimte de optie **laptop vm's**.
1. Selecteer de VM in de lijst.
1. Selecteer **stoppen**.
1. Wanneer u klaar bent om de server opnieuw te gebruiken, selecteert u **starten**.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om de resources te gebruiken die u hebt gemaakt, verwijdert u deze, zodat er geen kosten in rekening worden gebracht.

1. Selecteer **Resourcegroepen** links in Azure Portal.
1. Selecteer de resourcegroep die u eerder hebt gemaakt uit de lijst.
1. Selecteer **Resourcegroep verwijderen**.
1. Voer de naam van de resourcegroup in. Selecteer vervolgens **Verwijderen**.

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Bekijk de eigenschappen van de werkruimte en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze machine learning zelf studie over pijp lijnen hebt u de volgende taken gedaan:

> [!div class="checklist"]
> * Een pijp lijn met omgevings afhankelijkheden gebouwd om te worden uitgevoerd op een externe GPU-reken resource
> * Een score script gemaakt om batch voorspellingen uit te voeren met een vooraf getraind tensor flow-model
> * Een pijp lijn gepubliceerd en deze inschakelen voor uitvoering vanaf een REST-eind punt

Raadpleeg de [opslag plaats voor notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) voor meer voor beelden van het bouwen van pijp lijnen met de machine learning SDK.
