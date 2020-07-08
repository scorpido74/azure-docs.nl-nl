---
title: Experimenten visualiseren met TensorBoard
titleSuffix: Azure Machine Learning
description: Start TensorBoard om de geschiedenis van experimentele uitvoeringen te visualiseren en mogelijke gebieden te identificeren voor het afstemmen en opnieuw trainen van afstemming.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: 317c771daca146660c62eb0cba14facc701b1b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430149"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Experimentele uitvoeringen en metrische gegevens visualiseren met TensorBoard en Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de uitvoeringen van uw experiment en metrische gegevens in TensorBoard kunt weer geven met behulp van [het `tensorboard` pakket](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) in de belangrijkste Azure machine learning-SDK. Wanneer u de experimentele uitvoeringen hebt geïnspecteerd, kunt u uw machine learning modellen beter afstemmen en opnieuw trainen.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) is een suite van webtoepassingen voor het inspecteren en leren van uw experiment structuur en prestaties.

Hoe u TensorBoard start met Azure Machine Learning experimenten is afhankelijk van het type experiment:
+ Als uw experiment systeem eigen logboek bestanden uitvoert die kunnen worden gebruikt door TensorBoard, zoals PyTorch, Chainer en tensor flow experimenten, kunt u [TensorBoard rechtstreeks](#direct) vanuit de uitvoerings geschiedenis van het experiment starten. 

+ Voor experimenten waarbij geen systeem eigen TensorBoard kan worden uitgevoerd, zoals Scikit-leren of Azure Machine Learning experimenten, gebruikt u [de `export_to_tensorboard()` methode](#export) om de run-runs te exporteren als TensorBoard-logboeken en TensorBoard van daaruit te starten. 

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevens wetenschappers en ontwikkel aars die het model trainings proces willen bewaken. Als u een beheerder bent die geïnteresseerd is in het bewaken van het resource gebruik en gebeurtenissen van Azure machine learning, zoals quota's, voltooide trainings uitvoeringen of voltooide model implementaties, raadpleegt u [bewaking Azure machine learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Vereisten

* Voor het starten van TensorBoard en het weer geven van de geschiedenis van uw experiment, moeten uw experimenten eerder logboek registratie hebben ingeschakeld om de metrische gegevens en prestaties bij te houden.  

* De code in dit document kan worden uitgevoerd in een van de volgende omgevingen: 

    * Azure Machine Learning Compute-instantie-geen down loads of installatie vereist

        * Voltooi de [zelf studie: installatie omgeving en werk ruimte](tutorial-1st-experiment-sdk-setup.md) om een toegewezen notebook server te maken vooraf geladen met de SDK en de voor beeld-opslag plaats.

        * Zoek in de map met voor beelden op de notebook server twee voltooide en uitgevouwen notitie blokken door te navigeren naar deze directory's:
            * **instructies voor het gebruik van azureml > training-met-diep Learning > export-run-out-to-tensorboard > export-run-history-to-tensorboard. ipynb**

            * **instructies voor het gebruik van azureml > track-and-monitor-experimenten > tensorboard. ipynb**

    * Uw eigen Juptyer-notebook server
       * [Installeer de Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) met de `tensorboard` extra
        * [Maak een Azure machine learning-werk ruimte](how-to-manage-workspace.md).  
        * [Maak een configuratie bestand voor de werk ruimte](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Optie 1: uitvoerings geschiedenis rechtstreeks weer geven in TensorBoard

Deze optie werkt voor experimenten waarbij logboek bestanden met de eigen TensorBoard worden uitgevoerd, zoals PyTorch, Chainer en tensor flow experimenten. Als dat niet het geval is, gebruikt u [de `export_to_tensorboard()` methode](#export) in plaats daarvan.

De volgende voorbeeld code maakt gebruik van het [MNIST-demo experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) van de opslag plaats van tensor flow in een extern Compute-doel Azure machine learning compute. We trainen ons model met de aangepaste [tensor flow-Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)van de SDK en starten vervolgens TensorBoard op basis van dit tensor flow experiment, dat wil zeggen, een experiment waarbij TensorBoard-gebeurtenis bestanden systeem eigen worden uitgevoerd.

### <a name="set-experiment-name-and-create-project-folder"></a>De naam van het experiment instellen en de projectmap maken

Hier noemen we het experiment en maken we de map. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Tensor flow-demo-experiment code downloaden

De opslag plaats van tensor flow heeft een MNIST-demo met uitgebreide TensorBoard-instrumentatie. Het is niet nodig om de code van deze demo te wijzigen zodat deze werkt met Azure Machine Learning. In de volgende code wordt de MNIST-code gedownload en in de zojuist gemaakte map experiment opgeslagen.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
In het MNIST-code bestand, mnist_with_summaries. py, ziet u dat er regels zijn die aanroepen, `tf.summary.scalar()` `tf.summary.histogram()` `tf.summary.FileWriter()` enzovoort. Deze methoden groeperen, registreren en labelen de metrische gegevens van uw experimenten in uitvoerings geschiedenis. De `tf.summary.FileWriter()` is vooral belang rijk omdat deze gegevens uit de metrische experimentele meet waarden serialeert, waardoor TensorBoard er visualisaties uit kan genereren.

 ### <a name="configure-experiment"></a>Experiment configureren

In het volgende worden we het experiment geconfigureerd en mappen voor logboeken en gegevens ingesteld. Deze logboeken worden geüpload naar de artefact service, waar TensorBoard later toegang toe hebt.

>[!Note]
> Voor dit tensor flow-voor beeld moet u tensor flow installeren op uw lokale computer. De TensorBoard-module (dat wil zeggen, het onderdeel dat deel uitmaakt van tensor flow), moet toegankelijk zijn voor de kernel van dit notebook, omdat de lokale machine de TensorBoard uitvoert.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Een cluster maken voor uw experiment
We maken een AmlCompute-cluster voor dit experiment, maar uw experimenten kunnen in elke omgeving worden gemaakt en u kunt TensorBoard nog steeds starten met de geschiedenis van het experiment. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Run verzenden met tensor flow estimator

De tensor flow Estimator biedt een eenvoudige manier om een tensor flow-trainings taak te starten op een compute-doel. Het wordt geïmplementeerd via de algemene [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klasse, die kan worden gebruikt voor de ondersteuning van elk Framework. Voor meer informatie over trainings modellen die gebruikmaken van de algemene Estimator, raadpleegt [u modellen met Azure machine learning met behulp van Estimator](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>TensorBoard starten

U kunt TensorBoard starten tijdens het uitvoeren of nadat het is voltooid. In het volgende voor beeld maken we een exemplaar van het TensorBoard-object, `tb` dat de geschiedenis van de experimentele uitvoering in de heeft geladen `run` en vervolgens TensorBoard start met de- `start()` methode. 
  
De [TensorBoard-constructor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) neemt een matrix van uitvoeringen, dus zorg ervoor dat u deze in als een matrix met één element doorgeeft.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Hoewel in dit voor beeld tensor flow wordt gebruikt, kan TensorBoard net zo gemakkelijk worden gebruikt met PyTorch-of Chainer-modellen. Tensor flow moet beschikbaar zijn op de machine waarop TensorBoard wordt uitgevoerd, maar is niet nodig op de machine die PyTorch of keten berekeningen uitvoert. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Optie 2: Geschiedenis exporteren als logboek om weer te geven in TensorBoard

Met de volgende code wordt een voor beeld van een experiment ingesteld, wordt het logboek registratie gestart met behulp van de Api's van de Azure Machine Learning-uitvoerings geschiedenis en wordt de geschiedenis van het experiment uitgevoerd naar logboeken die kunnen worden gebruikt door TensorBoard voor visualisatie. 

### <a name="set-up-experiment"></a>Experiment instellen

Met de volgende code wordt een nieuw experiment ingesteld en wordt de map run met een naam gemaakt `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Hier laden we de diabetes-gegevensset: een ingebouwde kleine gegevensset die wordt geleverd met scikit-Learn en deze opsplitsen in test-en trainings sets.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Experiment-en logboek metrieken uitvoeren

Voor deze code trainen we een lineair regressie model en de metrische gegevens van de sleutel, de Alfa coëfficiënt, `alpha` en de gemiddelde fout, `mse` in de uitvoerings geschiedenis.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exporteren wordt uitgevoerd naar TensorBoard

Met de [export_to_tensorboard-](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) methode van de SDK kunt u de uitvoerings geschiedenis van onze Azure machine learning-experiment exporteren naar tensorboard-logboeken, zodat we deze kunnen bekijken via tensorboard.  

In de volgende code maken we de map `logdir` in de huidige werkmap. In deze map worden de geschiedenis en logboeken van de experimentele uitvoering geëxporteerd `root_run` en vervolgens gemarkeerd als voltooid. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 U kunt ook een bepaalde uitvoering exporteren naar TensorBoard door de naam van de uitvoeringsrun op te geven`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>TensorBoard starten en stoppen
Zodra de uitvoerings geschiedenis van dit experiment is geëxporteerd, kunnen we TensorBoard starten met de methode [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Wanneer u klaar bent, moet u ervoor zorgen dat u de methode [stop ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) aanroept voor het object TensorBoard. Anders blijft TensorBoard actief totdat u de notebook-kernel afsluit. 

```python
tb.stop()
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebben we twee experimenten gemaakt en geleerd hoe u TensorBoard kunt starten op basis van hun uitvoerings geschiedenis om gebieden te identificeren voor potentiële afstemming en retraining. 

* Als u tevreden bent met uw model, gaat u naar onze [informatie over het implementeren van een model](how-to-deploy-and-where.md) artikel. 
* Meer informatie over het [afstemmen van afstemming](how-to-tune-hyperparameters.md). 
