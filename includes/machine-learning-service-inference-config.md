---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159405"
---
De vermeldingen in `inferenceconfig.json` de documentkaart naar de parameters voor de klasse [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) In de volgende tabel wordt de toewijzing tussen entiteiten in het JSON-document en de parameters voor de methode beschreven:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pad naar een lokaal bestand dat de code bevat die moet worden uitgevoerd voor de afbeelding. |
| `sourceDirectory` | `source_directory` | Optioneel. Ga naar mappen die alle bestanden bevatten om de afbeelding te maken, waardoor het gemakkelijk is om toegang te krijgen tot bestanden in deze map of submap. U een hele map uploaden vanaf uw lokale machine als afhankelijkheden voor de Webservice. Opmerking: uw entry_script-, conda_file- en extra_docker_file_steps paden zijn relatieve paden naar het source_directory pad. |
| `environment` | `environment` | Optioneel.  Azure Machine [Learning-omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

U de volledige specificaties van een Azure Machine [Learning-omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) opnemen in het configuratiebestand ingevolgtrekking. Als deze omgeving niet bestaat in uw werkruimte, maakt Azure Machine Learning deze. Anders werkt Azure Machine Learning de omgeving zo nodig bij. De volgende JSON is een voorbeeld:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

U ook een bestaande Azure Machine [Learning-omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) gebruiken in gescheiden CLI-parameters en de sleutel 'omgeving' uit het configuratiebestand met gevolgtrekking verwijderen. Gebruik -e voor de omgevingsnaam en --ev voor de milieuversie. Als u niet opgeven --ev, zal de nieuwste versie worden gebruikt. Hier is een voorbeeld van een inference configuratiebestand:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Met de volgende opdracht wordt uitgelegd hoe u een model implementeert met behulp van het vorige inferenteisconfiguratiebestand (myInferenceConfig.json). 

Het maakt ook gebruik van de nieuwste versie van een bestaande Azure Machine [Learning-omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (azureml-minimaal genoemd).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
