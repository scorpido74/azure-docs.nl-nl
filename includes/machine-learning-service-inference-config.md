---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159405"
---
De vermeldingen in de `inferenceconfig.json` document structuur met de para meters voor de [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) -klasse. De volgende tabel beschrijft de toewijzing tussen entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pad naar een lokaal bestand dat de code bevat die moet worden uitgevoerd voor de installatie kopie. |
| `sourceDirectory` | `source_directory` | Optioneel. Het pad naar mappen die alle bestanden bevatten voor het maken van de installatie kopie, waarmee u eenvoudig toegang hebt tot bestanden in deze map of submap. U kunt een volledige map uploaden van uw lokale computer als afhankelijkheden voor de webservice. Opmerking: de paden entry_script, conda_file en extra_docker_file_steps zijn relatieve paden naar het source_directory pad. |
| `environment` | `environment` | Optioneel.  Azure Machine Learning [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

U kunt de volledige specificaties van een Azure Machine Learning [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) in het bestand voor de deinterferentie configuratie toevoegen. Als deze omgeving niet aanwezig is in uw werk ruimte, wordt deze door Azure Machine Learning gemaakt. Anders werkt Azure Machine Learning de omgeving zo nodig bij. De volgende JSON is een voor beeld:

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

U kunt ook een bestaande Azure Machine Learning- [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) gebruiken in gescheiden cli-para meters en de "omgevings sleutel" uit het configuratie bestand voor afwijzen verwijderen. Gebruik-e voor de omgevings naam en--EV voor de omgevings versie. Als u niet--EV opgeeft, wordt de meest recente versie gebruikt. Hier volgt een voor beeld van een configuratie bestand voor ingrijpen:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

De volgende opdracht laat zien hoe u een model implementeert met behulp van het vorige configuratie bestand voor inschakeling (met de naam myInferenceConfig.jsaan). 

Ook wordt de nieuwste versie van een bestaande Azure Machine Learning- [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) gebruikt (met de naam ' AzureML-minimal ').

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
