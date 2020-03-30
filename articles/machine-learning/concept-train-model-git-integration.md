---
title: Git-integratie voor Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ontdek hoe Azure Machine Learning integreert met een lokale Git-repository. Bij het indienen van een trainingsrun vanuit een lokale map die een Git-opslagplaats is, worden informatie over repo, branch en huidige commit bijgehouden als onderdeel van de run.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402819"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integratie voor Azure Machine Learning

[Git](https://git-scm.com/) is een populair versiebeheersysteem waarmee u uw projecten delen en samenwerken. 

Azure Machine Learning ondersteunt git-repositories volledig voor het bijhouden van werk - u repositories rechtstreeks op uw bestandssysteem voor gedeelde werkruimtes klonen, Git gebruiken op uw lokale werkstation of Git gebruiken vanuit een CI/CD-pijplijn.

Wanneer u een taak indient bij Azure Machine Learning, wordt als bronbestanden worden opgeslagen in een lokale git-opslagplaats, informatie over de repo bijgehouden als onderdeel van het trainingsproces.

Aangezien Azure Machine Learning informatie van een lokale git repo bijhoudt, is deze niet gekoppeld aan een specifieke centrale opslagplaats. Uw repository kan worden gekloond van GitHub, GitLab, Bitbucket, Azure DevOps of een andere git-compatibele service.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git-opslagplaatsen klonen in uw werkruimtebestandssysteem
Azure Machine Learning biedt een gedeeld bestandssysteem voor alle gebruikers in de werkruimte.
Als u een Git-repository wilt klonen in deze bestandsshare, raden we u aan een Compute Instance te maken & een terminal te openen.
Zodra de terminal is geopend, heeft u toegang tot een volledige Git-client en u Git klonen en met Git werken via de Git CLI-ervaring.

We raden u aan de repository in uw gebruikersmap te klonen, zodat anderen geen botsingen rechtstreeks op uw werkkantoor maken.

U elke Git-repository klonen die u verifiëren (GitHub, Azure Repos, BitBucket, enz.)

Voor een gids over hoe de Git CLI te gebruiken, lees [hier](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Track code die afkomstig is van Git repositories

Wanneer u een trainingsrun indient vanuit de Python SDK of Machine Learning CLI, worden de bestanden die nodig zijn om het model te trainen, geüpload naar uw werkruimte. Als `git` de opdracht beschikbaar is in uw ontwikkelomgeving, wordt het uploadproces gebruikt om te controleren of de bestanden zijn opgeslagen in een git-opslagplaats. Als dat zo is, wordt informatie uit je git repository ook geüpload als onderdeel van de training run. Deze informatie wordt opgeslagen in de volgende eigenschappen voor de trainingsrun:

| Eigenschap | Git-opdracht gebruikt om de waarde te krijgen | Beschrijving |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | De URI waar je repository vandaan is gekloond. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | De URI waar je repository vandaan is gekloond. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking bij indiening van de run. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | De actieve vertakking bij indiening van de run. |
| `azureml.git.commit` | `git rev-parse HEAD` | De commit hash van de code die is ingediend voor de run. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | De commit hash van de code die is ingediend voor de run. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, als de tak/commit vuil is; anders. `false` |

Deze informatie wordt verzonden voor uitvoeringen die gebruik maken van een schatter, machine learning-pijplijn of scriptuitvoering.

Als uw trainingsbestanden zich niet in een git-opslagplaats `git` op uw ontwikkelomgeving bevinden of als de opdracht niet beschikbaar is, wordt er geen git-gerelateerde informatie bijgehouden.

> [!TIP]
> Als u wilt controleren of de git-opdracht beschikbaar is in uw ontwikkelomgeving, opent u een shell-sessie, opdrachtprompt, PowerShell of een andere opdrachtregelinterface en typt u de volgende opdracht:
>
> ```
> git --version
> ```
>
> Als u is geïnstalleerd en op het `git version 2.4.1`pad, ontvangt u een antwoord dat vergelijkbaar is met . Zie de [Git-website](https://git-scm.com/)voor meer informatie over het installeren van git op uw ontwikkelomgeving.

## <a name="view-the-logged-information"></a>De geregistreerde gegevens weergeven

De git-informatie wordt opgeslagen in de eigenschappen voor een training. U deze informatie bekijken via de Azure-portal, Python SDK en CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Selecteer uw werkruimte in de [Azure-portal.](https://portal.azure.com)
1. Selecteer __Experimenten__en selecteer vervolgens een van uw experimenten.
1. Selecteer een van de runs in de kolom __RUN-NUMMER.__
1. Selecteer __Logboeken__en vouw de __logboeken__ en __azureml-vermeldingen__ uit. Selecteer de koppeling __ ### \___ die begint met azure .

    ![De ###_azure vermelding in het portaal](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

De geregistreerde informatie bevat tekst die lijkt op de volgende JSON:

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

### <a name="python-sdk"></a>Python SDK

Na het indienen van een trainingsrun wordt een [Run-object](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) geretourneerd. Het `properties` kenmerk van dit object bevat de aangemelde git-informatie. De volgende code haalt bijvoorbeeld de commit hash op:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

De `az ml run` opdracht CLI kan worden gebruikt om de eigenschappen uit een run op te halen. Met de volgende opdracht worden bijvoorbeeld de eigenschappen voor `train-on-amlcompute`de laatste run in het experiment met de naam :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Zie voor meer informatie de referentiedocumentatie [az ml run.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Volgende stappen

* [Rekendoelen instellen en gebruiken voor modeltraining](how-to-set-up-training-targets.md)
