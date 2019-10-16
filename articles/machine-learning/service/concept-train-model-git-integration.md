---
title: Git-integratie voor Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning integreert met een lokale Git-opslag plaats.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377510"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integratie voor Azure Machine Learning

[Git](https://git-scm.com/) is een populair versie beheersysteem waarmee u uw projecten kunt delen en samen werken. Bij het verzenden van een trainings taak naar Azure Machine Learning, als de trainings bestanden worden opgeslagen in een lokale Git-opslag plaats, wordt informatie over de opslag plaats bijgehouden als onderdeel van het trainings proces.

Omdat Azure Machine Learning gegevens van een lokale Git-opslag plaats registreert, is deze niet gekoppeld aan een specifieke centrale opslag plaats. Uw opslag plaats kan worden gekloond van GitHub, GitLab, bitbucket, Azure DevOps of een andere Git-compatibele service.

## <a name="how-does-git-integration-work"></a>Hoe werkt Git-integratie?

Wanneer u een training verzendt die wordt uitgevoerd vanuit de python-SDK of Machine Learning CLI, worden de bestanden die nodig zijn om het model te trainen, geüpload naar uw werk ruimte. Als de `git`-opdracht beschikbaar is in uw ontwikkelings omgeving, gebruikt het upload proces om te controleren of de bestanden zijn opgeslagen in een Git-opslag plaats. Als dat het geval is, worden de gegevens uit uw Git-opslag plaats ook geüpload als onderdeel van de trainings uitvoering. Deze informatie wordt opgeslagen in de volgende eigenschappen voor het uitvoeren van de training:

| Eigenschap | Beschrijving |
| ----- | ----- |
| `azureml.git.repository_uri` | De URI waaruit uw opslag plaats is gekloond. |
| `mlflow.source.git.repoURL` | De URI waaruit uw opslag plaats is gekloond. |
| `azureml.git.branch` | De actieve vertakking wanneer de uitvoering is ingediend. |
| `mlflow.source.git.branch` | De actieve vertakking wanneer de uitvoering is ingediend. |
| `azureml.git.commit` | De doorvoer-hash van de code die is ingediend voor de uitvoering. |
| `mlflow.source.git.commit` | De doorvoer-hash van de code die is ingediend voor de uitvoering. |
| `azureml.git.dirty` | `True` als de commit-bewerking vuil is; zo niet, `false`. |

Deze informatie wordt verzonden voor uitvoeringen die gebruikmaken van een Estimator, machine learning pijp lijn of script uitvoering.

Als uw trainings bestanden zich niet in een Git-opslag plaats in uw ontwikkelings omgeving bevinden, of als de `git`-opdracht niet beschikbaar is, worden er geen gegevens met betrekking tot Git bijgehouden.

## <a name="view-the-logged-information"></a>De geregistreerde gegevens weer geven

De Git-informatie wordt opgeslagen in de eigenschappen voor een trainings uitvoering. U kunt deze informatie weer geven met behulp van de Azure Portal, python SDK en CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw werk ruimte in de [Azure Portal](https://portal.azure.com).
1. Selecteer __experimenten__en selecteer vervolgens een van uw experimenten.
1. Selecteer een van de uitvoeringen in de kolom __uitvoerings nummer__ .
1. Selecteer __Logboeken__en vouw vervolgens de __Logboeken__ en __azureml__ -vermeldingen uit. Selecteer de koppeling die begint met __### @ no__t-2azure__.

De geregistreerde gegevens bevatten tekst die vergelijkbaar is met de volgende JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python-SDK

Na het verzenden van een trainings uitvoering wordt een [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) -object geretourneerd. Het kenmerk `properties` van dit object bevat de geregistreerde Git-informatie. Met de volgende code wordt bijvoorbeeld de commit-hash opgehaald:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

De `az ml run` CLI-opdracht kan worden gebruikt om de eigenschappen uit een run op te halen. De volgende opdracht retourneert bijvoorbeeld de eigenschappen voor de laatste uitvoering in het experiment met de naam `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Zie voor meer informatie de documentatie van [AZ ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) Reference.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: modellen trainen met Azure machine learning](tutorial-train-models-with-aml.md)voor een overzicht van het trainen van Azure machine learning in Visual Studio code.
* Zie de zelf studie over het gebruik van de [python Hello-wereld](https://code.visualstudio.com/docs/Python/Python-tutorial)voor een overzicht van het lokaal bewerken, uitvoeren en fout opsporing van code.
