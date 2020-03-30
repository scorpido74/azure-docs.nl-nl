---
title: Experimenten visualiseren met TensorBoard
titleSuffix: Azure Machine Learning
description: Start TensorBoard om experimentrungeschiedenissen te visualiseren en potentiële gebieden te identificeren voor hyperparametertuning en omscholing.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195376"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Experimentruns en -statistieken visualiseren met TensorBoard en Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u uw experimentuitvoeringen en -statistieken in TensorBoard weergeven met behulp van [het `tensorboard` pakket](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) in de belangrijkste Azure Machine Learning SDK. Zodra u uw experiment hebt geïnspecteerd, u uw machine learning-modellen beter afstemmen en omscholen.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) is een suite van webapplicaties voor het inspecteren en begrijpen van uw experimentstructuur en prestaties.

Hoe u TensorBoard start met Azure Machine Learning-experimenten, is afhankelijk van het type experiment:
+ Als uw experiment standaard logboekbestanden uitvoert die door TensorBoard worden verbruikt, zoals PyTorch, Chainer en TensorFlow-experimenten, u [TensorBoard rechtstreeks starten](#direct) vanuit de rungeschiedenis van het experiment. 

+ Voor experimenten die niet native TensorBoard-verbruiksbestanden uitvoeren, zoals Scikit-learn of Azure Machine Learning-experimenten, gebruikt u [de `export_to_tensorboard()` methode](#export) om de run-geschiedenissen te exporteren als TensorBoard-logboeken en vanaf daar TensorBoard te starten. 

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevenswetenschappers en ontwikkelaars die het modeltrainingsproces willen volgen. Zie Azure [Machine Learning](monitor-azure-machine-learning.md)controleren als u een beheerder bent die het gebruik van resources en gebeurtenissen van Azure Machine Learning wil controleren, zoals quota, voltooide trainingsuitvoeringen of voltooide modelimplementaties.

## <a name="prerequisites"></a>Vereisten

* Als u TensorBoard wilt starten en de uitvoering geschiedenissen van uw experiment wilt bekijken, moeten uw experimenten logboekregistratie eerder hebben ingeschakeld om de statistieken en prestaties bij te houden.  

* De code in dit document kan worden uitgevoerd in een van de volgende omgevingen: 

    * Azure Machine Learning rekeninstantie - geen downloads of installatie nodig

        * De [zelfstudie: installatieomgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md) voltooien om een speciale notebookserver te maken die vooraf is geladen met de SDK en de voorbeeldopslagplaats.

        * Zoek in de map met voorbeelden op de notitieblokserver twee voltooide en uitgebreide notitieblokken door naar deze mappen te navigeren:
            * **how-to-use-azureml > training-with-deep-learning > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**

            * **how-to-use-azureml > track-and-monitor-experimenten > tensorboard.ipynb**

    * Uw eigen Juptyer-notebookserver
       * [Installeer de Azure Machine](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) `tensorboard` Learning SDK met de extra
        * [Maak een Azure Machine Learning-werkruimte](how-to-manage-workspace.md).  
        * [Een configuratiebestand voor werkruimtes maken](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Optie 1: Run history direct bekijken in TensorBoard

Deze optie werkt voor experimenten die logboekbestanden die door TensorBoard worden verbruikt, zoals PyTorch- en Chainer- en TensorFlow-experimenten, native uitvoeruitvoeren. Als dat niet het geval is bij uw experiment, gebruikt u [de `export_to_tensorboard()` methode](#export) in plaats daarvan.

In de volgende voorbeeldcode wordt het [MNIST-demo-experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) uit de repository van TensorFlow gebruikt in een extern rekendoel, Azure Machine Learning Compute. Vervolgens trainen we ons model met de aangepaste [TensorFlow-schatter van](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)de SDK en starten we TensorBoard tegen dit TensorFlow-experiment, dat wil zeggen een experiment dat native TensorBoard-gebeurtenisbestanden uitvoert.

### <a name="set-experiment-name-and-create-project-folder"></a>De naam van het experiment instellen en projectmap maken

Hier noemen we het experiment en maken we de map. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>StudentenorFlow-demo-experimentcode downloaden

TensorFlow's repository heeft een MNIST demo met uitgebreide TensorBoard instrumentatie. We hoeven de code van deze demo niet te wijzigen om te kunnen werken met Azure Machine Learning. In de volgende code downloaden we de MNIST-code en slaan deze op in onze nieuw gemaakte experimentmap.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
In het MNIST-codebestand mnist_with_summaries.py, merk op `tf.summary.scalar()` `tf.summary.histogram()`dat `tf.summary.FileWriter()` er lijnen zijn die aanroepen, enz. Deze methoden groeperen, registreren en taggen belangrijke statistieken van uw experimenten in rungeschiedenis. Het `tf.summary.FileWriter()` is vooral belangrijk omdat het de gegevens van uw geregistreerde experimentstatistieken serialiseert, waardoor TensorBoard visualisaties van hen kan genereren.

 ### <a name="configure-experiment"></a>Experiment configureren

In het volgende configureren we ons experiment en stellen we mappen in voor logboeken en gegevens. Deze logboeken worden geüpload naar de Artefact-service, waar TensorBoard later toegang toe heeft.

>[!Note]
> Voor dit tensorflow-voorbeeld moet u TensorFlow op uw lokale machine installeren. Verder moet de TensorBoard-module (dat wil zeggen de module die bij TensorFlow is meegeleverd) toegankelijk zijn voor de kernel van deze notebook, omdat de lokale machine is wat TensorBoard draait.

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
We maken een AmlCompute-cluster voor dit experiment, maar uw experimenten kunnen in elke omgeving worden gemaakt en u TensorBoard nog steeds starten tegen de rungeschiedenis van het experiment. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Run verzenden met TensorFlow-schatter

De TensorFlow-schatter biedt een eenvoudige manier om een TensorFlow-trainingstaak te starten op een compute target. Het wordt geïmplementeerd via [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) de generieke klasse, die kan worden gebruikt om elk framework te ondersteunen. Zie [treinmodellen met Azure Machine Learning met behulp van schatter](how-to-train-ml-models.md) voor meer informatie over trainingsmodellen met behulp van de generieke schatter

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Start TensorBoard

U TensorBoard starten tijdens het hardlopen of nadat deze is voltooid. In het volgende maken we een TensorBoard object instance, `tb`dat de `run`experiment run geschiedenis geladen `start()` in de , en lanceert vervolgens TensorBoard met de methode. 
  
De [TensorBoard constructor](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) neemt een scala aan runs, dus zorg ervoor dat en geef het in als een single-element array.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 Terwijl dit voorbeeld TensorFlow gebruikte, kan TensorBoard net zo gemakkelijk worden gebruikt met PyTorch- of Chainer-modellen. TensorFlow moet beschikbaar zijn op de machine met TensorBoard, maar is niet nodig op de machine die PyTorch- of Chainer-berekeningen uitvoert. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Optie 2: Geschiedenis exporteren als logboek om te bekijken in TensorBoard

Met de volgende code wordt een voorbeeldexperiment ingesteld, wordt het logboekregistratieproces gestart met de Azure Machine Learning-runhistory API's en wordt de runsgeschiedenis van het experiment geëxporteerd naar logboeken die door TensorBoard worden verbruikt voor visualisatie. 

### <a name="set-up-experiment"></a>Experiment instellen

Met de volgende code wordt een nieuw `root_run`experiment ingesteld en wordt de run directory genoemd. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Hier laden we de diabetesdataset, een ingebouwde kleine dataset die wordt geleverd met scikit-learn, en splitsen deze op in test- en trainingssets.

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

### <a name="run-experiment-and-log-metrics"></a>Metgegevens voor experimenten en logboeken uitvoeren

Voor deze code trainen we een lineair regressiemodel en `alpha`registreren we sleutelstatistieken, `mse`de alfacoëfficiënt en gemiddelde kwadraatfout, in de rungeschiedenis.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Export loopt naar TensorBoard

Met de [export_to_tensorboard()-methode](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) van de SDK kunnen we de rungeschiedenis van ons Azure machine learning-experiment exporteren naar TensorBoard-logboeken, zodat we ze kunnen bekijken via TensorBoard.  

In de volgende code maken `logdir` we de map in onze huidige werkmap. In deze map exporteren we onze experimentrungeschiedenis en logboeken van `root_run` en markeren we die run als voltooid. 

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
 U een bepaalde run ook exporteren naar TensorBoard door de naam van de run op te geven`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>TensorBoard starten en stoppen
Zodra onze run geschiedenis voor dit experiment is geëxporteerd, kunnen we TensorBoard starten met de [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) methode. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Wanneer u klaar bent, moet u de [methode stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) van het object TensorBoard aanroepen. Anders blijft TensorBoard draaien totdat u de notebookkernel afsluit. 

```python
tb.stop()
```

## <a name="next-steps"></a>Volgende stappen

In deze how-to you, creëerde twee experimenten en geleerd hoe TensorBoard te lanceren tegen hun run geschiedenissen om gebieden voor potentiële tuning en omscholing te identificeren. 

* Als u tevreden bent met uw model, ga dan naar ons [modelartikel implementeren.](how-to-deploy-and-where.md) 
* Meer informatie over [hyperparametertuning](how-to-tune-hyperparameters.md). 
