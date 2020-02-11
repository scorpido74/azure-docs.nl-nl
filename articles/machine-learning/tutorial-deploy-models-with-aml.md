---
title: 'Zelf studie voor installatie kopie classificatie: modellen implementeren'
titleSuffix: Azure Machine Learning
description: Deze zelf studie laat zien hoe u Azure Machine Learning kunt gebruiken om een installatie kopie-classificatie model te implementeren met scikit-Learn in een python Jupyter-notebook. Deze zelfstudie is deel 2 van een tweedelige reeks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 071a8dd40d87e5df6fc5c65b789bb63b515dc60a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116501"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Zelf studie: een classificatie model voor een installatie kopie implementeren in Azure Container Instances
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Deze zelfstudie is **deel twee van een tweedelige reeks**. In de [vorige zelfstudie](tutorial-train-models-with-aml.md) hebt u Machine Learning-modellen getraind en vervolgens een model geregistreerd in uw werkruimte in de cloud.  

Nu kunt u het model als een webservice implementeren in [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/). Een webservice is een installatiekopie, in dit geval een Docker-installatiekopie. De service omvat de scoringlogica en het model zelf. 

In dit gedeelte van de zelf studie gebruikt u Azure Machine Learning voor de volgende taken:

> [!div class="checklist"]
> * Uw testomgeving instellen
> * Het model ophalen uit uw werkruimte
> * Het model lokaal testen
> * Het model implementeren in Container Instances
> * Het geïmplementeerde model testen

Container Instances is een uitstekende oplossing voor het testen en inzicht krijgen in de werkstroom. Voor schaalbare productie-implementaties is het misschien beter om Azure Kubernetes Service te gebruiken. Zie [Modellen implementeren met de Azure Machine Learning-service](how-to-deploy-and-where.md) voor meer informatie.

>[!NOTE]
> De code in dit artikel is getest met Azure Machine Learning SDK-versie 1.0.41.

## <a name="prerequisites"></a>Vereisten

Als u het notitie blok wilt uitvoeren, voltooit u eerst de model training in [zelf studie (deel 1): een classificatie model voor een installatie kopie trainen](tutorial-train-models-with-aml.md).   Open vervolgens het notebook *IMG-classificatie-part2-Deploy. ipynb* in uw gekloonde *zelf studies/image-classificatie-mnist-data-* map.

Deze zelf studie is ook beschikbaar op [github](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) als u deze wilt gebruiken in uw eigen [lokale omgeving](how-to-configure-environment.md#local).  Zorg ervoor dat u `matplotlib` en `scikit-learn` hebt geïnstalleerd in uw omgeving. 

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als u ziet in het notitie blok.  
>
> Schakel nu over naar het Jupyter-notebook als u wilt lezen tijdens het uitvoeren van de code.
> Als u één code-cel in een notitie blok wilt uitvoeren, klikt u op de cel code en drukt u op **SHIFT + ENTER**. U kunt ook het hele notitie blok uitvoeren door **alles uitvoeren** op de bovenste werk balk te kiezen.

## <a name="start"></a>Stel de omgeving in

Begin met het instellen van een testomgeving.

### <a name="import-packages"></a>Pakketten importeren

Importeer de Python-pakketten die nodig zijn voor deze zelfstudie:

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>Het model ophalen

Bij de vorige zelfstudie hebt u een model geregistreerd in uw werkruimte. Nu gaat u deze werkruimte laden en het model downloaden naar uw lokale map:


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>Het model lokaal testen

Voordat u het model implementeert, moet u controleren of het model lokaal werkt. Voer hiervoor deze stappen uit:
* Laad testgegevens.
* Voorspel testgegevens.
* Bekijk de verwarringsmatrix.

### <a name="load-test-data"></a>Testgegevens laden

Laad de testgegevens uit de map **./data/** die u hebt gemaakt tijdens de zelfstudie over het trainen van modellen:

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>Testgegevens voorspellen

U kunt voorspellingen opvragen door de set met testgegevens door te geven aan het model:

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>De verwarringsmatrix bestuderen

Genereer een verwarringsmatrix om te zien hoeveel voorbeelden uit de testset juist zijn geclassificeerd. Let op de onjuist geclassificeerde waarden voor de onjuiste voorspellingen: 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

In de uitvoer wordt de verwarringsmatrix weergegeven:

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

Gebruik `matplotlib` om de verwarringsmatrix weer te geven in een grafiek. In deze grafiek bevat de X-as de daadwerkelijke waarden en de Y-as de voorspelde waarden. De kleur in elk raster geeft de foutfrequentie aan. Hoe lichter de kleur, hoe hoger de foutfrequentie. De 5 wordt vaak verkeerd ingedeeld als een 3. Daarom ziet u een lichtgekleurd raster bij (5,3):

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Grafiek met een verwarringsmatrix](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Als webservice implementeren

Nadat u het model hebt getest en als tevreden bent met de resultaten, kunt u het model implementeren als een webservice die wordt gehost in Container Instances. 

U hebt de volgende onderdelen nodig om de juiste omgeving in te richten voor Container Instances:
* Een scoring-script dat aangeeft hoe het model moet worden gebruikt.
* Een omgevingsbestand dat aangeeft welke pakketten moeten worden geïnstalleerd.
* Een configuratiebestand om de containerinstantie mee te bouwen.
* Het model dat u eerder hebt getraind.

<a name="make-script"></a>

### <a name="create-scoring-script"></a>Scoring-script maken

Maak een scoring-script met de naam **score.py**. Bij het aanroepen van de webservice wordt dit script gebruikt om te laten zien hoe het model moet worden gebruikt.

Deze twee functies moet u verplicht opnemen in het scoring-script:
* De functie `init()`, die het model doorgaans in een algemeen object laadt. Deze functie wordt slechts één keer uitgevoerd wanneer de Docker-container wordt gestart. 

* De functie `run(input_data)` gebruikt het model om een waarde te voorspellen op basis van de invoergegevens. De in- en uitvoer van de uitvoerbewerking maken doorgaans gebruik van JSON voor serialisatie en deserialisatie, maar andere indelingen worden ook ondersteund.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>Omgevingsbestand maken

Maak vervolgens een omgevingsbestand, met de naam **myenv.yml**, waarin alle pakketafhankelijkheden van het script zijn opgenomen. Dit bestand wordt gebruikt om ervoor te zorgen dat al die afhankelijkheden in de Docker-installatiekopie worden geïnstalleerd. Voor dit model zijn `scikit-learn` en `azureml-sdk` nodig. Alle aangepaste omgevings bestanden moeten de waarden van azureml-standaard met versie > = 1.0.45 als een PIP-afhankelijkheid vermelden. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")
myenv.add_pip_package("azureml-defaults")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
Controleer de inhoud van het bestand `myenv.yml`:

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>Een configuratiebestand maken

Maak een configuratiebestand voor de implementatie. Geef het aantal CPU's en het aantal gigabytes aan RAM-geheugen op dat nodig is voor uw Container Instances-container. Hoewel dit per model verschilt, is de standaardhoeveelheid van 1 kerngeheugen en 1 gigabyte aan RAM-geheugen voldoende voor de meeste modellen. Als u later meer resources nodig hebt, moet u de installatiekopie opnieuw maken en de service opnieuw implementeren.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Implementeren in Container Instances
De geschatte tijd voor het voltooien van de implementatie is **ongeveer zeven tot acht minuten**.

Configureer en implementeer de installatiekopie. De volgende code doorloopt de volgende stappen:

1. Bouw een installatiekopie met behulp van deze bestanden:
   * Het scoring-bestand, `score.py`.
   * Het omgevingsbestand, `myenv.yml`.
   * Het modelbestand.
1. Registreer de installatiekopie in de werkruimte. 
1. Verzend de installatiekopie naar de Container Instances-container.
1. Start een Container Instances-container met behulp van de installatiekopie.
1. Haal het HTTP-eindpunt van de webservice op.

Houd er rekening mee dat als u uw eigen omgevings bestand definieert, de standaard waarden van azureml en Version > = 1.0.45 als een PIP-afhankelijkheid. Dit pakket bevat de functionaliteit die nodig is om het model als een webservice te hosten.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Haal het HTTP-eindpunt van de scoring-webservice op, die REST-clientaanroepen accepteert. Dit eindpunt kan worden gedeeld met iedereen die de webservice wil testen of wil integreren in een toepassing: 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>De geïmplementeerde service testen

Eerder hebt u alle testgegevens gescoord met de lokale versie van het model. Nu kunt u het geïmplementeerde model testen met een steekproef van 30 afbeeldingen uit de testgegevens.  

De volgende code doorloopt de volgende stappen:
1. Verzend de gegevens als JSON-matrix naar de webservice die wordt gehost in Container Instances. 

1. Gebruik de API `run` van de SDK om de service aan te roepen. U kunt ook onbewerkte aanroepen doen met behulp van een HTTP-hulpprogramma zoals **curl**.

1. Druk de geretourneerde voorspellingen af en geef die weer samen met de invoerafbeeldingen. De verkeerd geclassificeerde voorbeelden worden aangegeven met een rood lettertype en inversie (wit op zwart). 

Aangezien de nauwkeurigheid van het model hoog is, kan het nodig zijn om de volgende code een paar keer uit te voeren voordat u een voorbeeld van een onjuiste classificatie ziet.

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

Dit is het resultaat van één steekproef van testafbeeldingen:

![Afbeelding met resultaten](./media/tutorial-deploy-models-with-aml/results.png)

U kunt ook HTTP-aanvragen op basis van onbewerkte gegevens verzenden om de webservice te testen:

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep en werkruimte wilt bewaren voor andere zelfstudies en voor verkenning, kunt u deze API-aanroep gebruiken om alleen de Container Instances-implementatie te verwijderen:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over de [implementatie opties voor Azure machine learning](how-to-deploy-and-where.md).
+ Meer informatie over het [maken van clients voor de webservice](how-to-consume-web-service.md).
+  Doe asynchroon [voorspellingen op grote hoeveelheden gegevens](how-to-use-parallel-run-step.md).
+ Bewaak uw Azure Machine Learning-modellen met [Application Insights](how-to-enable-app-insights.md).
+ Ga ook aan de slag met de zelfstudie [Automatisch algoritmen selecteren](tutorial-auto-train-models.md). 
