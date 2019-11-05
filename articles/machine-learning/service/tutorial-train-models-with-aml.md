---
title: 'Zelf studie over afbeeldings classificatie: modellen trainen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het trainen van een afbeeldings classificatie model met scikit-informatie over een python Jupyter-notebook met Azure Machine Learning. Deze zelfstudie is deel één van een serie van twee.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: dd215e754b7e72c9ac424a53015955332068558e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493564"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Zelf studie: classificatie modellen van een installatie kopie trainen met MNIST-gegevens en scikit-informatie met behulp van Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze zelfstudie gaat u een machine learning-model trainen op externe rekenresources. U gebruikt de trainings-en implementatie werk stroom voor Azure Machine Learning in een python Jupyter-notebook.  Vervolgens kunt u het notebook gebruiken als een sjabloon voor het trainen van uw eigen machine learning-model met uw eigen gegevens. Deze zelfstudie is **deel één van een serie van twee**.  

In deze zelf studie wordt een eenvoudige logistiek regressie getraind met behulp van de [MNIST](http://yann.lecun.com/exdb/mnist/) -gegevensset en [scikit-leer](https://scikit-learn.org) Azure machine learning. MNIST is een populaire gegevensset die bestaat uit 70.000 afbeeldingen in grijstinten. Elke afbeelding is een handgeschreven cijfer van 28 x 28 pixels, dat een getal tussen 0-9 vertegenwoordigt. Het doel is om een classificatiemechanisme met meerdere klassen te maken om het cijfer te identificeren dat een bepaalde afbeelding vertegenwoordigt.

U leert hoe u de volgende acties uitvoert:

> [!div class="checklist"]
> * De ontwikkelomgeving instellen.
> * De gegevens downloaden en controleren.
> * Train een eenvoudig logistiek regressie model op een extern cluster.
> * Trainingsresultaten bekijken en het beste model registreren.

In [deel twee van deze zelfstudie](tutorial-deploy-models-with-aml.md) leert u hoe u een model selecteert en dit implementeert.

Als u nog geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree) .

>[!NOTE]
> De code in dit artikel is getest met [Azure machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -versie 1.0.65.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [zelf studie: Ga aan de slag met het maken van uw eerste ml-experiment](tutorial-1st-experiment-sdk-setup.md) tot:
    * Een werkruimte maken
    * Kopieer de notebooks van de zelf studies naar uw map in de werk ruimte.
    * Maak een cloud-gebaseerd reken exemplaar.

* Open in de map gekloonde **zelf studies** de notebook **IMG-classificatie-part1-training. ipynb** . 


De zelf studie en het bijbehorende **utils.py** -bestand zijn ook beschikbaar op [github](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) als u het wilt gebruiken in uw eigen [lokale omgeving](how-to-configure-environment.md#local). Voer `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` uit om afhankelijkheden te installeren voor deze zelf studie.

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als u ziet in het notitie blok.  
>
> Schakel nu over naar het Jupyter-notebook als u wilt lezen tijdens het uitvoeren van de code. 
> Als u één code-cel in een notitie blok wilt uitvoeren, klikt u op de cel code en drukt u op **SHIFT + ENTER**. U kunt ook het hele notitie blok uitvoeren door **alles uitvoeren** op de bovenste werk balk te kiezen.

## <a name="start"></a>De ontwikkelomgeving instellen

De configuratie van uw ontwikkelomgeving kan worden uitgevoerd met een Python-notebook. De configuratie bestaat uit de volgende acties:

* Python-pakketten importeren.
* Verbinding maken met een werkruimte, zodat uw lokale computer met externe bronnen kan communiceren.
* Een experiment maken om alle uitvoeringen (runs) bij te houden.
* Een extern rekendoel voor training maken.

### <a name="import-packages"></a>Pakketten importeren

Importeer de Python-pakketten die u nodig hebt in deze sessie. Controleer ook wat de versie is van de SDK voor Azure Machine Learning:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Verbinding maken met een werkruimte

Maak een werkruimte-object van de bestaande werkruimte. `Workspace.from_config()` leest het bestand **config.json** en laadt de gegevens in een object met de naam `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Een experiment maken

Maak een experiment voor het volgen van de runs in uw werkruimte. Een werkruimte kan meerdere experimenten bevatten:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Een bestaand Compute-doel maken of koppelen

Met Azure Machine Learning Compute, een beheerde service, kunnen gegevenswetenschappers Machine Learning-modellen trainen op clusters met virtuele Azure-machines. Voorbeelden hiervan zijn virtuele machines met GPU-ondersteuning. In deze zelfstudie maakt u Azure Machine Learning Compute als uw trainingsomgeving. Met de onderstaande code wordt het rekencluster voor u gemaakt als dat nog niet in uw werkruimte bestaat.

 **Het maken van het reken doel duurt ongeveer vijf minuten.** Als de compute-resource al in de werk ruimte staat, wordt deze door de code gebruikt en wordt het proces voor het maken overs Laan.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

U beschikt nu over de vereiste pakketten en rekenresources voor het trainen van een model in de cloud.

## <a name="explore-data"></a>Gegevens verkennen

Voordat u een model gaat trainen, is het belangrijk dat u de gegevens begrijpt die u gebruikt voor het trainen. In dit gedeelte leert u het volgende:

* De MNIST-gegevensset downloaden.
* Enkele voorbeeldafbeeldingen weergeven.

### <a name="download-the-mnist-dataset"></a>De MNIST-gegevensset downloaden

Gebruik Azure open gegevens sets om de onbewerkte MNIST-gegevens bestanden op te halen. [Azure open gegevens sets](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) zijn alle open bare gegevens sets die u kunt gebruiken om scenario's met specifieke functies toe te voegen aan Machine Learning oplossingen voor nauw keurigere modellen. Elke gegevensset heeft een bijbehorende klasse, `MNIST` in dit geval om de gegevens op verschillende manieren op te halen.

Met deze code worden de gegevens opgehaald als een `FileDataset`-object, een subklasse van `Dataset`. Een `FileDataset` verwijst naar één of meer bestanden van een indeling in uw gegevens opslag of open bare URL. De-klasse biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw Compute door een verwijzing naar de locatie van de gegevens bron te maken. Daarnaast registreert u de gegevensset voor uw werk ruimte, zodat u deze eenvoudig kunt ophalen tijdens de training.

Volg de [instructies](how-to-create-register-datasets.md) om meer te weten te komen over gegevens sets en hun gebruik in de SDK.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Enkele voorbeeldafbeeldingen weergeven

Laad de gecomprimeerde bestanden in `numpy`-matrices. Gebruik vervolgens `matplotlib` om 30 willekeurige afbeeldingen uit de gegevensset te tekenen, met de bijbehorende labels erboven. Voor deze stap hebt u een `load_data`-functie nodig die is opgenomen in een `util.py`-bestand. Dit bestand staat in de map met voorbeelden. Zorg ervoor dat u het bestand in de map met dit notebook zet. De functie `load_data` parseert de gecomprimeerde bestanden simpelweg in numpy-matrices.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, "train-images-idx3-ubyte.gz"), False) / 255.0
X_test = load_data(os.path.join(data_folder, "t10k-images-idx3-ubyte.gz"), False) / 255.0
y_train = load_data(os.path.join(data_folder, "train-labels-idx1-ubyte.gz"), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, "t10k-labels-idx1-ubyte.gz"), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Er wordt een steekproef van afbeeldingen weergegeven:

![Steekproef van afbeeldingen](./media/tutorial-train-models-with-aml/digits.png)

U hebt nu een beter beeld van hoe deze afbeeldingen eruit zien en wat u voor resultaat kunt verwachten van de voorspelling.

## <a name="train-on-a-remote-cluster"></a>Trainen op een extern cluster

Voor deze taak verstuurt u de taak naar het cluster voor externe training dat u eerder hebt ingesteld.  Om een taak te verzenden, moet u het volgende doen:
* Een map maken
* Een trainingsscript maken
* Een estimator-object maken
* De taak verzenden

### <a name="create-a-directory"></a>Een map maken

Maak een map om de benodigde code vanaf uw computer aan te bieden aan de externe resource.

```python
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Een trainingsscript maken

Als u de taak naar het cluster wilt verzenden, moet u eerst een trainingsscript maken. Voer de volgende code uit om in de map die u net hebt gemaakt een trainingsscript te maken met de naam `train.py`.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

U ziet hoe met het script gegevens worden opgehaald en modellen worden opgeslagen:

+ Met het trainingsscript leest u een argument om de map met de gegevens te vinden. Als u de taak later verstuurt, wijst u naar het gegevensarchief voor dit argument: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Het trainings script slaat uw model op in een map met de naam **uitvoer**. Alles gegevens die naar deze map worden geschreven, worden automatisch geüpload naar uw werkruimte. Verderop in de zelfstudie gaat u dit model openen vanuit deze map. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Voor het trainings script moet het bestand `utils.py` de gegevensset correct laden. Met de volgende code worden `utils.py` naar `script_folder` gekopieerd, zodat het bestand kan worden geopend samen met het trainings script op de externe bron.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Een estimator maken

Een [SKLearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) -object wordt gebruikt om de uitvoering in te dienen. Maak de estimator door de volgende code uit te voeren en deze items te definiëren:

* De naam van het estimator-object, `est`.
* De map met uw scripts. Alle bestanden in deze map worden naar de clusterknooppunten geüpload voor uitvoering.
* Het rekendoel. In dit geval gebruikt u het Azure Machine Learning-rekencluster dat u hebt gemaakt.
* De naam van het trainingsscript, **train.py**.
* Vereiste parameters uit het trainingsscript.

In deze zelfstudie bestaat dit doel uit AmlCompute. Alle bestanden in de scriptmap worden naar de clusterknooppunten geüpload om te worden uitgevoerd. De **DATA_FOLDER** is ingesteld op het gebruik van de gegevensset. Maak eerst een omgevings object dat de afhankelijkheden specificeert die vereist zijn voor de training. 

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

env = Environment('my_env')
cd = CondaDependencies.create(pip_packages=['azureml-sdk','scikit-learn','azureml-dataprep[pandas,fuse]>=1.1.14'])
env.python.conda_dependencies = cd
```

Maak vervolgens de Estimator met de volgende code.

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': mnist_file_dataset.as_named_input('mnist_opendataset').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env, 
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>De taak verzenden naar het cluster

Voer het experiment uit door het estimator-object in te dienen:

```python
run = exp.submit(config=est)
run
```

Aangezien de aanroep asynchroon is, retourneert deze een status **Preparing** of **Running** zodra de taak is gestart.

## <a name="monitor-a-remote-run"></a>Een externe run controleren

In totaal duurt de eerste run **ongeveer tien minuten**. Voor latere runs wordt dezelfde afbeelding echter opnieuw gebruikt indien de scriptafhankelijkheden niet zijn gewijzigd. Hierdoor is de opstarttijd van de container veel korter.

Terwijl u wacht, gebeurt het volgende:

- **Installatie kopie maken**: er wordt een docker-installatie kopie gemaakt die overeenkomt met de python-omgeving die is opgegeven door de Estimator. De afbeelding wordt naar de werkruimte geüpload. Het maken en uploaden van de afbeelding duurt **circa vijf minuten**.

  Deze fase vindt eenmaal plaats voor elke Python-omgeving, aangezien de container voor volgende runs in de cache wordt opgeslagen. Tijdens het maken van de afbeelding, worden er logboeken gestreamd naar de uitvoeringsgeschiedenis. U kunt de voortgang van het maken van afbeeldingen volgen aan de hand van deze logboeken.

- **Schalen**: als het externe cluster meer knoop punten nodig heeft om de uitvoering dan momenteel beschikbaar te maken, worden extra knoop punten automatisch toegevoegd. Het schalen duurt meestal **ongeveer vijf minuten.**

- **Uitvoeren**: in deze fase worden de benodigde scripts en bestanden naar het reken doel verzonden. Vervolgens worden gegevensarchieven gekoppeld of gekopieerd. Ten slotte wordt het **entry_script** uitgevoerd. Terwijl de taak wordt uitgevoerd, worden **stdout** en de map **./logs** naar de uitvoeringsgeschiedenis gestreamd. U kunt de voortgang van de run volgen aan de hand van deze logboeken.

- **Na de verwerking**: de map **./outputs** van de uitvoering wordt gekopieerd naar de uitvoerings geschiedenis in uw werk ruimte, zodat u deze resultaten kunt openen.

U kunt de voortgang van een actieve taak op verschillende manieren controleren. In deze zelfstudie wordt gebruikgemaakt van een Jupyter-widget en de methode `wait_for_completion`.

### <a name="jupyter-widget"></a>Jupyter-widget

Bekijk de voortgang van het uitvoeren met een [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10 tot 15 seconden live updates totdat de taak is voltooid:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

De widget ziet er als volgt uit als het einde van de training:

![Notebook-widget](./media/tutorial-train-models-with-aml/widget.png)

Als u een uitvoering wilt annuleren, kunt u [deze instructies](https://aka.ms/aml-docs-cancel-run) volgen.

### <a name="get-log-results-upon-completion"></a>Resultaten van logboeken weergeven bij voltooiing

Het trainen en controleren van het model gebeurt op de achtergrond. Wacht totdat het trainen van het model is voltooid voordat u andere code uitvoert. Gebruik `wait_for_completion` om weer te geven wanneer het trainen van het model is voltooid:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Resultaten van run weergeven

U hebt nu een model dat is getraind op een extern cluster. De nauwkeurigheid van het model opvragen:

```python
print(run.get_metrics())
```

De uitvoer toont dat het externe model een nauwkeurigheid van 0,9204 heeft:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

In de volgende zelfstudie wordt dit model in meer detail besproken.

## <a name="register-model"></a>Model registreren

Met de laatste stap in het trainingsscript wordt het bestand `outputs/sklearn_mnist_model.pkl` weggeschreven naar een map met de naam `outputs` op de VM van het cluster waarop de taak wordt uitgevoerd. `outputs` is een speciale map omdat alle inhoud in deze map automatisch wordt geüpload naar uw werkruimte. Deze inhoud wordt opgenomen in de uitvoeringsrecord in het experiment, onder uw werkruimte. Hierdoor is het modelbestand nu ook beschikbaar in uw werkruimte.

U kunt zien welke bestanden aan die run zijn gekoppeld:

```python
print(run.get_file_names())
```

Registreer het model in de werkruimte, zodat u of anderen query's op het model kunnen uitvoeren, of het model kunnen onderzoeken en implementeren:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

U kunt ook gewoon alleen het Azure Machine Learning Compute-cluster verwijderen. Automatische schaalaanpassing is ingeschakeld en het clusterminimum is 0. Hierdoor worden er voor deze specifieke resource geen extra rekenkosten in rekening gebracht wanneer deze niet in gebruik is:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Volgende stappen

In deze Azure Machine Learning zelf studie hebt u python gebruikt voor de volgende taken:

> [!div class="checklist"]
> * De ontwikkelomgeving instellen.
> * De gegevens downloaden en controleren.
> * Meerdere modellen trainen op een extern cluster met behulp van de populaire scikit-learn machine learning-bibliotheek
> * Trainingsgegevens bekijken en het beste model registreren.

U kunt dit geregistreerde model nu implementeren aan de hand van de instructies in het volgende deel van de reeks zelfstudies:

> [!div class="nextstepaction"]
> [Zelfstudie 2: Modellen implementeren](tutorial-deploy-models-with-aml.md)
