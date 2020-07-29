---
title: Git-integratie voor Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe Azure Machine Learning integreert met een lokale Git-opslag plaats. Bij het verzenden van een trainings uitvoering vanuit een lokale map die een Git-opslag plaats is, wordt informatie over opslag plaats, vertakking en huidige door Voer bijgehouden als onderdeel van de uitvoering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78402819"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integratie voor Azure Machine Learning

[Git](https://git-scm.com/) is een populair versie beheersysteem waarmee u uw projecten kunt delen en Hiermee kunnen samen werken. 

Azure Machine Learning volledig ondersteunt Git-opslag plaatsen voor het bijhouden van werk-u kunt opslag plaatsen rechtstreeks naar het bestands systeem van de gedeelde werk ruimte klonen, Git gebruiken op uw lokale werk station of gebruikmaken van Git van een CI/CD-pijp lijn.

Bij het verzenden van een taak naar Azure Machine Learning, als bron bestanden worden opgeslagen in een lokale Git-opslag plaats, wordt informatie over de opslag plaats bijgehouden als onderdeel van het trainings proces.

Omdat Azure Machine Learning gegevens van een lokale Git-opslag plaats registreert, is deze niet gekoppeld aan een specifieke centrale opslag plaats. Uw opslag plaats kan worden gekloond van GitHub, GitLab, bitbucket, Azure DevOps of een andere Git-compatibele service.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-opslagplaatsen klonen in het bestandssysteem van de werkruimte
Azure Machine Learning biedt een gedeeld bestands systeem voor alle gebruikers in de werk ruimte.
Als u een Git-opslag plaats wilt klonen in deze bestands share, kunt u het beste een reken instantie maken & een Terminal te openen.
Zodra de Terminal is geopend, hebt u toegang tot een volledige Git-client en kunt u Git klonen en gebruiken via de Git CLI-ervaring.

We raden u aan de opslag plaats te klonen in de map gebruikers, zodat anderen geen conflicten rechtstreeks op uw werk vertakking kunnen door voeren.

U kunt elke Git-opslag plaats klonen die u kunt verifiëren (GitHub, Azure opslag plaatsen, BitBucket, etc.)

Lees hier hier voor een hand leiding voor het gebruik van de Git [cli.](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>Code traceren die afkomstig is van Git-opslag plaatsen

Wanneer u een training verzendt die wordt uitgevoerd vanuit de python-SDK of Machine Learning CLI, worden de bestanden die nodig zijn om het model te trainen, geüpload naar uw werk ruimte. Als de `git` opdracht beschikbaar is in uw ontwikkelings omgeving, gebruikt het upload proces om te controleren of de bestanden zijn opgeslagen in een Git-opslag plaats. Als dat het geval is, worden de gegevens uit uw Git-opslag plaats ook geüpload als onderdeel van de trainings uitvoering. Deze informatie wordt opgeslagen in de volgende eigenschappen voor het uitvoeren van de training:

| Eigenschap | Git-opdracht die wordt gebruikt om de waarde op te halen | Description |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | De URI waaruit uw opslag plaats is gekloond. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | De URI waaruit uw opslag plaats is gekloond. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking wanneer de uitvoering is ingediend. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking wanneer de uitvoering is ingediend. |
| `azureml.git.commit` | `git rev-parse HEAD` | De doorvoer-hash van de code die is ingediend voor de uitvoering. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | De doorvoer-hash van de code die is ingediend voor de uitvoering. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`Als de vertakking/door Voer is beschadigd; anders, `false` . |

Deze informatie wordt verzonden voor uitvoeringen die gebruikmaken van een Estimator, machine learning pijp lijn of script uitvoering.

Als uw trainings bestanden zich niet in een Git-opslag plaats in uw ontwikkelings omgeving bevinden, of als de `git` opdracht niet beschikbaar is, wordt er geen informatie over git bijgehouden.

> [!TIP]
> Als u wilt controleren of de Git-opdracht beschikbaar is in uw ontwikkelings omgeving, opent u een shell-sessie, opdracht prompt, Power shell of een andere opdracht regel interface en typt u de volgende opdracht:
>
> ```
> git --version
> ```
>
> Als deze is geïnstalleerd en u in het pad een antwoord ontvangt dat vergelijkbaar is met `git version 2.4.1` . Zie de [Git-website](https://git-scm.com/)voor meer informatie over het installeren van git in uw ontwikkel omgeving.

## <a name="view-the-logged-information"></a>De geregistreerde gegevens weer geven

De Git-informatie wordt opgeslagen in de eigenschappen voor een trainings uitvoering. U kunt deze informatie weer geven met behulp van de Azure Portal, python SDK en CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw werk ruimte in de [Azure Portal](https://portal.azure.com).
1. Selecteer __experimenten__en selecteer vervolgens een van uw experimenten.
1. Selecteer een van de uitvoeringen in de kolom __uitvoerings nummer__ .
1. Selecteer __Logboeken__en vouw vervolgens de __Logboeken__ en __azureml__ -vermeldingen uit. Selecteer de koppeling die begint met __ ### \_ Azure__.

    ![De # # #_azure vermelding in de portal](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

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

Na het verzenden van een trainings uitvoering wordt een [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) -object geretourneerd. Het `properties` kenmerk van dit object bevat de geregistreerde Git-informatie. Met de volgende code wordt bijvoorbeeld de commit-hash opgehaald:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

De `az ml run` cli-opdracht kan worden gebruikt om de eigenschappen op te halen uit een run. De volgende opdracht retourneert bijvoorbeeld de eigenschappen voor de laatste uitvoering in het experiment met de naam `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Zie voor meer informatie de documentatie van [AZ ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) Reference.

## <a name="next-steps"></a>Volgende stappen

* [Reken doelen voor model training instellen en gebruiken](how-to-set-up-training-targets.md)
