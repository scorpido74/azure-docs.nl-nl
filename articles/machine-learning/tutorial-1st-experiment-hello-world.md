---
title: "Zelfstudie: Python-script 'Hallo wereld' uitvoeren"
titleSuffix: Azure Machine Learning
description: In deel 2 van de aan de slag-reeks van Azure ML ziet u hoe u een alledaags python-script van 'hallo wereld' naar de cloud verzendt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 98f4c30d03763e070b1bdc32a5e6e099556916ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929335"
---
# <a name="tutorial-run-hello-world-python-script-part-2-of-4"></a>Zelfstudie: Python-script 'Hallo wereld' uitvoeren (deel 2 van 4)

In deze zelfstudie leert u hoe u de Azure Machine Learning Python-SDK kunt gebruiken om een Python-script 'Hallo wereld' te verzenden en uit te voeren.

Deze zelfstudie is **deel twee van een vierdelige zelfstudiereeks** waarin u de grondbeginselen van Azure Machine Learning kunt zien en machine learning-taken op basis van taken in Azure uitvoert. Deze zelfstudie borduurt verder op het werk dat u hebt voltooid in [Zelfstudie deel 1: uw lokale machine instellen voor Azure Machine Learning](
tutorial-1st-experiment-sdk-setup-local.md).

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een Python-script 'Hallo wereld' lokaal maken en uitvoeren
> * Een Python-besturingsscript maken om 'Hallo wereld' te verzenden naar Azure Machine Learning
> * Inzicht krijgen in de Azure Machine Learning-concepten in het besturingsscript
> * 'Hallo wereld' verzenden en uitvoeren
> * De code-uitvoer weergeven in de cloud

## <a name="prerequisites"></a>Vereisten

- Doorloop [Deel 1 van de installatiezelfstudie voor lokale machines](tutorial-1st-experiment-sdk-setup-local.md) als u nog geen Azure Machine Learning-werkruimte hebt.
- Introductiekennis van de Python-taal en machine learning-werkstromen.
- Lokale ontwikkelomgeving. Dit omvat, maar is niet beperkt tot Visual Studio-code, Jupyter of PyCharm.
- Python (versie 3.5-3.7).

## <a name="create-and-run-a-python-script-locally"></a>Een Python-script lokaal maken en uitvoeren

Maak een nieuwe submap met de naam `src` onder de `tutorial`-map om de code op te slaan die u op een Azure Machine Learning Compute-cluster wilt uitvoeren. Maak in de submap `src` het `hello.py` Python-script:

```python
# src/hello.py
print("Hello world!")
```

De structuur van uw projectmap ziet er nu als volgt uit:

```Bash
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
└──01-create-workspace.py
└──02-create-compute.py
```

### <a name="test-your-script-locally"></a>Uw script lokaal testen

U kunt uw code lokaal uitvoeren, met het voordeel van interactieve foutopsporing van code, met behulp van uw favoriete IDE of via een terminal:

```bash
cd <path/to/tutorial>
python ./src/hello.py
```

## <a name="create-a-control-script"></a>Een besturingsscript maken

Met een *besturingsscript* kunt u uw `hello.py`-script uitvoeren in de cloud.  Met het besturingsscript kunt u bepalen hoe en waar uw machine learning-code wordt uitgevoerd.  

Maak in uw zelfstudiemap een nieuw python-bestand met de naam `03-run-hello.py` en kopieer en plak de onderstaande code in dat bestand:

```python
# tutorial/03-run-hello.py
from azureml.core import Workspace, Experiment, Environment, ScriptRunConfig

ws = Workspace.from_config()
experiment = Experiment(workspace=ws, name='day1-experiment-hello')

config = ScriptRunConfig(source_directory='./src', script='hello.py', compute_target='cpu-cluster')

run = experiment.submit(config)
aml_url = run.get_portal_url()
print(aml_url)
```

### <a name="understand-the-code"></a>De code begrijpen

Hier volgt een beschrijving van hoe het besturingsscript werkt:

:::row:::
   :::column span="":::
      `ws = Workspace.from_config()`
   :::column-end:::
   :::column span="2":::
      [Werkruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) maakt verbinding met uw Azure Machine Learning-werkruimte, zodat u kunt communiceren met uw Azure Machine Learning-resources.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `experiment =  Experiment( ... )`
   :::column-end:::
   :::column span="2":::
      [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true) biedt een eenvoudige manier om meerdere uitvoeringen onder één naam te organiseren. Later kunt u zien hoe experimenten het eenvoudig maken om metrische gegevens te vergelijken tussen tientallen uitvoeringen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig( ... )` 
   :::column-end:::
   :::column span="2":::
      Met [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) wordt uw `hello.py`-code ingepakt en doorgegeven aan uw werkruimte. Zoals de naam suggereert, kunt u deze klasse gebruiken om te _configureren_ hoe u wilt dat uw _script_ wordt _uitgevoerd_ in Azure Machine Learning. Specificeert ook op welk rekendoel het script wordt uitgevoerd.  In deze code is het doel het rekencluster dat u hebt gemaakt in de [installatiezelfstudie](tutorial-1st-experiment-sdk-setup-local.md).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `run = experiment.submit(config)`
   :::column-end:::
   :::column span="2":::
       Hiermee wordt het script verzonden. Deze inzending wordt een [Uitvoering](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) genoemd.  Een uitvoering bevat één uitvoering van uw code. Gebruik een uitvoering om de voortgang van het script te bewaken, de uitvoer vast te leggen, de resultaten te analyseren, metrische gegevens te visualiseren en meer.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `aml_url = run.get_portal_url()` 
   :::column-end:::
   :::column span="2":::
        Het `run`-object biedt een ingang voor de uitvoering van uw code. Bewaak de voortgang vanuit de Azure Machine Learning Studio met de URL die wordt afgedrukt vanuit het python-script.  
   :::column-end:::
:::row-end:::

## <a name="submit-and-run-your-code-in-the-cloud"></a>Uw code in de cloud verzenden en uitvoeren

Voer uw besturingsscript uit, dat op zijn beurt `hello.py` uitvoert op het rekencluster dat u hebt gemaakt in de [installatiezelfstudie](tutorial-1st-experiment-sdk-setup-local.md).

```bash
python 03-run-hello.py
```

## <a name="monitor-your-code-in-the-cloud-using-studio"></a>Uw code in de cloud bewaken met studio

De uitvoer bevat een link naar de Azure Machine Learning-studio die er ongeveer als volgt uitziet: `https://ml.azure.com/experiments/hello-world/runs/<run-id>?wsid=/subscriptions/<subscription-id>/resourcegroups/<resource-group>/workspaces/<workspace-name>`.

Volg de link en navigeer naar het tabblad **Uitvoer en logboeken**. Hier ziet u een bestand `70_driver_log.txt`, als volgt:

```txt
 1: [2020-08-04T22:15:44.407305] Entering context manager injector.
 2: [context_manager_injector.py] Command line Options: Namespace(inject=['ProjectPythonPath:context_managers.ProjectPythonPath', 'RunHistory:context_managers.RunHistory', 'TrackUserError:context_managers.TrackUserError', 'UserExceptions:context_managers.UserExceptions'], invocation=['hello.py'])
 3: Starting the daemon thread to refresh tokens in background for process with pid = 31263
 4: Entering Run History Context Manager.
 5: Preparing to call script [ hello.py ] with arguments: []
 6: After variable expansion, calling script [ hello.py ] with arguments: []
 7:
 8: Hello world!
 9: Starting the daemon thread to refresh tokens in background for process with pid = 31263
10:
11:
12: The experiment completed successfully. Finalizing run...
13: Logging experiment finalizing status in history service.
14: [2020-08-04T22:15:46.541334] TimeoutHandler __init__
15: [2020-08-04T22:15:46.541396] TimeoutHandler __enter__
16: Cleaning up all outstanding Run operations, waiting 300.0 seconds
17: 1 items cleaning up...
18: Cleanup took 0.1812913417816162 seconds
19: [2020-08-04T22:15:47.040203] TimeoutHandler __exit__
```

Op regel 8 ziet u de 'Hallo wereld!' uitvoer.

Het `70_driver_log.txt`-bestand bevat de standaarduitvoer van een uitvoering. Dit bestand kan handig zijn bij het opsporen van fouten op externe uitvoeringen in de cloud.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een eenvoudig 'Hallo wereld'-script gemaakt en dit uitgevoerd in Azure. U hebt gezien hoe u verbinding maakt met uw Azure Machine Learning-werkruimte, een experiment maakt en uw `hello.py`-code naar de cloud verzendt.

In de volgende zelfstudie maakt u gebruik van de opgedane kennis en voert u iets interessanters uit dan `print("Hello world!")`.

> [!div class="nextstepaction"]
> [Zelfstudie: een model trainen](tutorial-1st-experiment-sdk-train.md)

>[!NOTE] 
> Als u de reeks zelfstudies hier wilt voltooien en niet door wilt gaan met de volgende stap, moet u [uw resources opschonen](tutorial-1st-experiment-bring-data.md#clean-up-resources)
