---
title: 'Zelfstudie: Uw eerste machine learning-model - Python'
titleSuffix: Azure Machine Learning
description: In deel 3 van de Aan de slag-serie voor Azure Machine Learning ziet u hoe u een machine learning-model traint.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: f5c2690ea97136c2b7887a8450c2788e3902d4e3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369957"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Zelfstudie: Uw eerste machine learning-model trainen (deel 3 van 4)

Deze zelfstudie laat zien hoe u een machine learning-model traint in Azure Machine Learning.

Deze zelfstudie is *deel 3 van een vierdelige zelfstudiereeks* waarin u de grondbeginselen van Azure Machine Learning kunt zien en machine learning-taken op basis van taken uitvoert in Azure. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 1: Instellen](tutorial-1st-experiment-sdk-setup-local.md) en [Deel 2: 'Hello world!' uitvoeren](tutorial-1st-experiment-hello-world.md) van de serie.

In deze zelfstudie voert u de volgende stap uit door een script te verzenden dat een machine learning-model traint. Dit voorbeeld helpt u inzicht te krijgen in de manier waarop Azure Machine Learning consistent gedrag tussen lokale foutopsporing en externe uitvoeringen vereenvoudigt.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een trainingsscript maken.
> * Conda gebruiken om een Azure Machine Learning-omgeving te definiëren.
> * Een besturingsscript maken.
> * Inzicht krijgen in Azure Machine Learning-klassen (`Environment`, `Run`, `Metrics`).
> * Uw trainingsscript verzenden en uitvoeren.
> * De code-uitvoer weergeven in de cloud.
> * De metrische gegevens vastleggen in Azure Machine Learning.
> * Uw metrische gegevens bekijken in de cloud.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [Deel 2](tutorial-1st-experiment-hello-world.md) van de serie.
* Introductiekennis van de Python-taal en machine learning-werkstromen.
* Lokale ontwikkelomgeving, zoals Visual Studio Code, Jupyter of PyCharm.
* Python (versie 3.5 tot 3.7).

## <a name="create-training-scripts"></a>Trainingsscripts maken

Eerst definieert u de architectuur van het neurale netwerk in een `model.py`-bestand. Al uw trainingscode gaat naar de submap van `src`, met inbegrip van `model.py`.

De volgende code wordt gehaald uit [dit inleidende voorbeeld](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) van PyTorch. Houd er rekening mee dat de concepten van Azure Machine Learning van toepassing zijn op alle machine learning-code, niet alleen op PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Vervolgens definieert u het trainingsscript. Met dit script wordt de CIFAR10-gegevensset gedownload met behulp van PyTorch `torchvision.dataset`-API's, wordt het netwerk ingesteld dat is gedefinieerd in `model.py` en wordt het voor twee periodes getraind met behulp van standaard SGD en verlies van meerdere entropieën.

Een `train.py`-script maken in de `src`-submap:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

U hebt nu de volgende mapstructuur:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="create-a-python-environment"></a>Een Python-omgeving maken

Voor deze demonstratie gebruiken we een Conda-omgeving. (De stappen voor een virtuele PIP-omgeving zijn bijna identiek.)

Maak een bestand met de naam `pytorch-env.yml` in de verborgen map van `.azureml`:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Deze omgeving heeft alle afhankelijkheden die zijn vereist voor uw model- en trainingsscript. U ziet dat er geen afhankelijkheid is van de Azure Machine Learning-SDK voor Python.

## <a name="test-locally"></a>Lokaal testen

Gebruik de volgende code om te testen of uw script lokaal wordt uitgevoerd in deze omgeving:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env                      # activate conda environment
python src/train.py                             # train model
```

Nadat u dit script hebt uitgevoerd, ziet u dat de gegevens zijn gedownload naar een map met de naam `tutorial/data`.

## <a name="create-the-control-script"></a>Het besturingsscript maken

Het verschil tussen het volgende besturingsscript en het script dat u hebt gebruikt voor het verzenden van 'Hello World!' is dat u een paar extra regels toevoegt om de omgeving in te stellen.

Maak een nieuw Python-bestand in de `tutorial`-map met de naam `04-run-pytorch.py`:

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>De codewijzigingen begrijpen

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning biedt het concept van een [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) als een reproduceerbare Python-omgeving met versies waarin u experimenten kunt uitvoeren. Het is eenvoudig om een omgeving te maken op basis van een lokale Conda- of PIP-omgeving.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      De omgeving wordt toegevoegd aan [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-the-run-to-azure-machine-learning"></a>De uitvoering versturen naar Microsoft Azure Machine Learning

Als u naar lokale omgevingen bent overgeschakeld, gaat u terug naar een omgeving waarin de Azure Machine Learning-SDK voor Python is geïnstalleerd. 

Voer vervolgens

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> De eerste keer dat u dit script uitvoert, wordt in Azure Machine Learning een nieuwe docker-installatiekopie van uw PyTorch-omgeving gemaakt. De hele uitvoering duurt 5 tot 10 minuten. 
>
> U kunt de docker-buildlogboeken zien in Azure Machine Learning Studio. Volg de link naar de studio, selecteer het tabblad **Uitvoer en logboeken** en selecteer `20_image_build_log.txt`.
>
> Deze installatiekopie wordt opnieuw gebruikt in toekomstige uitvoeringen, waardoor deze veel sneller worden uitgevoerd.

Wanneer de installatiekopie is gemaakt, selecteert u `70_driver_log.txt` om de uitvoer van het trainingsscript weer te geven.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Als de foutmelding `Your total snapshot size exceeds the limit` wordt weergegeven, bevindt de map `data` zich in de waarde `source_directory` die wordt gebruikt in `ScriptRunConfig`.
>
> Plaats `data` buiten `src`.

Omgevingen kunnen worden geregistreerd in een werkruimte met `env.register(ws)`. Ze kunnen dan eenvoudig worden gedeeld en opnieuw gebruikt en er kan een versie van worden gemaakt. Met omgevingen is het eenvoudig om eerdere resultaten te reproduceren en samen te werken met uw team.

Azure Machine Learning houdt ook een verzameling van de gegroepeerde omgevingen bij. Deze omgevingen beslaan algemene machine learning-scenario's en worden ondersteund door docker-installatiekopieën in de cache. In cache geplaatste docker-installatiekopieën maken de eerste externe uitvoering sneller.

Kortom, met behulp van geregistreerde omgevingen kunt u tijd besparen. Lees [Omgevingen gebruiken](./how-to-use-environments.md) voor meer informatie.

## <a name="log-training-metrics"></a>Metrische gegevens van training registreren

Nu u een modeltraining in Azure Machine Learning hebt, kunt u een aantal prestatiegegevens gaan bijhouden.

Met het huidige trainingsscript worden metrische gegevens naar de terminal afgedrukt. Azure Machine Learning biedt een mechanisme voor het vastleggen van metrische gegevens met meer functionaliteit. Door een paar regels code toe te voegen, beschikt u over de mogelijkheid om metrische gegevens te visualiseren in de studio en om de metrische gegevens tussen meerdere uitvoeringen te vergelijken.

### <a name="modify-trainpy-to-include-logging"></a>Wijzig `train.py` om logboekregistratie op te nemen

Wijzig uw `train.py`-script en voeg er nog twee regels code aan toe:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>Meer informatie over de twee extra regels code

In `train.py` opent u het uitvoeringsobject _vanuit_ het trainingsscript zelf met behulp van de methode `Run.get_context()` en gebruikt u dit om metrische gegevens vast te leggen:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

De metrische gegevens in Azure Machine Learning zijn:

- Geordend op experiment en uitvoering, zodat u gemakkelijk metrische gegevens kunt bijhouden en vergelijken.
- Voorzien van een gebruikersinterface, zodat u de trainingsprestaties in de studio kunt visualiseren.
- Ontworpen om te worden geschaald, dus u behoudt deze voordelen, zelfs als u honderden experimenten uitvoert.

### <a name="update-the-conda-environment-file"></a>Het Conda-omgevingsbestand bijwerken

Het `train.py`-script heeft zojuist een nieuwe afhankelijkheid van `azureml.core` gekregen. Werk `pytorch-env.yml` bij om deze wijziging weer te geven:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-the-run-to-azure-machine-learning"></a>De uitvoering versturen naar Microsoft Azure Machine Learning
Verstuur dit script nog een keer:

```bash
python 04-run-pytorch.py
```

Wanneer u deze keer de studio bezoekt, gaat u naar het tabblad **Metrische gegevens**, waar u nu live-updates kunt zien van het modeltrainingsverlies.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Grafiek met trainingsverlies op het tabblad Metrische gegevens.":::

## <a name="next-steps"></a>Volgende stappen

In deze sessie hebt u een upgrade uitgevoerd van een basis 'Hallo wereld!' script naar een realistischer trainingsscript dat vereist dat een specifieke Python-omgeving wordt uitgevoerd. U hebt gezien hoe u een lokale Conda-omgeving naar de cloud kunt brengen met Azure Machine Learning-omgevingen. Ten slotte hebt u gezien hoe u in een paar regels code de metrische gegevens kunt vastleggen voor Azure Machine Learning.

Er zijn andere manieren om Azure Machine Learning-omgevingen te maken, zoals [van een TXT-bestand met PIP-vereisten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-) of [van een bestaande lokale Conda-omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-).

In de volgende sessie ziet u hoe u kunt werken met gegevens in Azure Machine Learning door de CIFAR10-gegevensset naar Azure te uploaden.

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eigen gegevens meenemen](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Als u de reeks zelfstudies nu wilt voltooien en niet wilt doorgaan met de volgende stap, moet u [uw resources opschonen](tutorial-1st-experiment-bring-data.md#clean-up-resources)
