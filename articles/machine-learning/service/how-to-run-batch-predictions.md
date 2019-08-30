---
title: Batch voorspellingen uitvoeren op grote gegevens met pijp lijnen
titleSuffix: Azure Machine Learning service
description: Leer hoe u batch om voorspellingen te doen asynchroon op grote hoeveelheden gegevens met behulp van Azure Machine Learning-service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: b0d8eef025efb6398f1d7c734ca558540b157fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128259"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>Batch voorspellingen uitvoeren voor grote gegevens sets met Azure Machine Learning pijp lijnen

In dit artikel leert u hoe u voor spellingen voor grote hoeveel heden gegevens asynchroon kunt maken met behulp van de ML-pijp lijnen met Azure Machine Learning service.

Batch voorspelling (of batch scoreing) biedt rendabele interferentie, met een niet-parallelle door Voer voor asynchrone toepassingen. Batch voorspelling pijplijnen kunnen schalen om uit te voeren Deductie op terabytes aan productiegegevens. Batch voorspellingen zijn geoptimaliseerd voor voor spellingen met hoge door Voer, brand-en-vergeet voor een grote verzameling gegevens.

>[!TIP]
> Als uw systeem verwerking met lage latentie vereist (om één document of een kleine set documenten snel te verwerken), gebruikt u in [realtime Score](how-to-consume-web-service.md) in plaats van batch voorspelling.

In de volgende stappen maakt u een [machine learning-pijp lijn](concept-ml-pipelines.md) voor het registreren van een vooraf opgeleid computer vision model ([begin v3](https://arxiv.org/abs/1512.00567)). Vervolgens gebruikt u het vooraf getrainde model om batch scoreing uit te voeren op installatie kopieën die beschikbaar zijn in uw Azure Blob Storage-account. Deze installatiekopieën gebruikt voor het scoren zijn niet-gelabelde afbeeldingen van de [ImageNet](http://image-net.org/) gegevensset.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer de [gratis of betaalde versie van Azure machine learning service](https://aka.ms/AMLFree).

- Configureer uw ontwikkelomgeving voor het installeren van de SDK van Azure Machine Learning. Zie voor meer informatie, [een ontwikkelomgeving configureren voor Azure Machine Learning](how-to-configure-environment.md).

- Maak een Azure Machine Learning-werkruimte waarin alle resources van uw pijplijn. U kunt de volgende code gebruiken of Zie voor meer opties [maken van een configuratiebestand werkruimte](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Machine learning-resources instellen

De volgende stappen zijn ingesteld voor de resources die u nodig hebt om een pijp lijn uit te voeren:

- Toegang tot het gegevensarchief die al heeft het model dat, invoer labels en afbeeldingen om te beoordelen (dit is al ingesteld voor u).
- Instellen van een gegevensarchief voor het opslaan van de uitvoer.
-  `DataReference`Configureer objecten zodanig dat ze verwijzen naar de gegevens in de voor gaande data stores.
- Instellen van de compute-machines of clusters waarop de pijplijn-stappen uitvoert.

### <a name="access-the-datastores"></a>Toegang tot de gegevensopslag

Eerst toegang krijgen tot de gegevensopslag met het model, labels en afbeeldingen.

Gebruik een open bare BLOB-container met de naam *sampleData*in het *pipelinedata* -account dat installatie kopieën van de ImageNet Evaluation set bevat. De naam van de gegevensopslag voor deze openbare container is *images_datastore*. Registreer deze gegevensopslag met uw werkruimte:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Stel vervolgens in dat u de standaard gegevens opslag voor de uitvoer wilt gebruiken.

Wanneer u uw werk ruimte maakt, worden [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) -en [Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) standaard aan de werk ruimte gekoppeld. Azure Files is de standaard gegevens opslag voor een werk ruimte, maar u kunt ook Blob Storage gebruiken als gegevens opslag. Zie [Opties voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)voor meer informatie.

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Gegevensverwijzingen configureren

Nu verwijzen naar de gegevens in de pijplijn als invoer voor de stappen van de pijplijn.

Een gegevensbron in een pijplijn wordt vertegenwoordigd door een [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) object.  `DataReference`Het object verwijst naar gegevens die zich in of toegankelijk zijn vanuit een gegevens opslag. U hebt `DataReference`objecten nodig  voor de map die wordt gebruikt voor invoer installatie kopieën, de map waarin het voortrainde model is opgeslagen, de Directory voor labels en de uitvoermap.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Compute-doel instellen

In Azure Machine Learning, *Compute* (of *Compute target*) verwijst naar de computers of clusters die de reken stappen in uw machine learning pijp lijn uitvoeren. U kunt bijvoorbeeld een Azure Machine Learning Compute maken met de klasse [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) .

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Voorbereiden van het model

Voordat u het pretrained model gebruiken kunt, moet u het model downloaden en te registreren bij uw werkruimte.

### <a name="download-the-pretrained-model"></a>Download het pretrained model

Download de dat computer vision-model (InceptionV3) van <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Pak het vervolgens uit naar `models` de submap.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registreer het model

U kunt als volgt het model registreren:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>De scoring-script schrijven

>[!Warning]
>De volgende code is slechts een voor beeld van wat is opgenomen in de [batch_score. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) die wordt gebruikt door het [voorbeeld notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). U moet uw eigen score script maken voor uw scenario.

De `batch_score.py` script neemt invoer afbeeldingen *dataset_path*, modellen in dat *model_dir,* en levert *resultaten label.txt* naar *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

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

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Bouwen en uitvoeren van de batchscore-pijplijn

### <a name="prepare-the-run-environment"></a>Voorbereiden van de uitvoeringsomgeving

Geef het conda-afhankelijkheden voor het script. U hebt dit object later nodig wanneer u de stap voor het maken van de pijp lijn maakt.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Geef de parameter voor de pijplijn

Een pijplijn parameter maken met behulp van een [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) -object met een standaard waarde.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>De stap pijplijn maken

Maak de pijplijn stap met behulp van het script, de omgevings configuratie en de para meters. Geef de compute-doel dat u al gekoppeld aan uw werkruimte als het doel van de uitvoering van het script. Gebruik [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) om de stap van de pijplijn te maken.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren

Voer nu de pijp lijn uit en Bekijk de geproduceerde uitvoer. De uitvoer bevat een score die overeenkomt met elke invoer afbeelding.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>De pijplijn publiceren

Nadat u tevreden bent met het resultaat van de uitvoering, publiceert u de pijp lijn zodat u deze met andere invoer waarden later kunt uitvoeren. Wanneer u een pijp lijn publiceert, krijgt u een REST-eind punt. Dit eind punt accepteert het aanroepen van de pijp lijn met de set para meters die u al hebt opgenomen met behulp van [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>De pijp lijn opnieuw uitvoeren met het REST-eind punt

Als u de pijp lijn opnieuw wilt uitvoeren, hebt u een token voor de Azure Active Directory-verificatie header nodig, zoals beschreven in de [AzureCliAuthentication-klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Volgende stappen

Als u wilt zien hoe deze end-to-end werkt, probeert u de batch Score notebook in [github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)of gaat u naar het [Azure Architecture Center](/azure/architecture/reference-architectures/ai/batch-scoring-python) om een voor beeld van een oplossings architectuur te bekijken.
