---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845135"
---
De vermeldingen in het `inferenceconfig.json` document worden toegewezen aan de para meters voor de klasse [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . De volgende tabel beschrijft de toewijzing tussen entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pad naar een lokaal bestand dat de code bevat die moet worden uitgevoerd voor de installatie kopie. |
| `runtime` | `runtime` | Optioneel. Welke runtime moet worden gebruikt voor de installatie kopie. Ondersteunde Runtimes zijn `spark-py` en `python`. Als `environment` is ingesteld, wordt deze vermelding genegeerd. |
| `condaFile` | `conda_file` | Optioneel. Pad naar een lokaal bestand dat een Conda-omgevings definitie bevat die moet worden gebruikt voor de installatie kopie.  Als `environment` is ingesteld, wordt deze vermelding genegeerd. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Optioneel. Pad naar een lokaal bestand dat extra docker-stappen bevat die moeten worden uitgevoerd bij het instellen van de installatie kopie.  Als `environment` is ingesteld, wordt deze vermelding genegeerd.|
| `sourceDirectory` | `source_directory` | Optioneel. Het pad naar mappen die alle bestanden bevatten voor het maken van de installatie kopie, waarmee u eenvoudig toegang hebt tot bestanden in deze map of submap. U kunt een volledige map uploaden van uw lokale computer als afhankelijkheden voor de webservice. Opmerking: de paden entry_script, conda_file en extra_docker_file_steps zijn relatieve paden naar het source_directory pad. |
| `enableGpu` | `enable_gpu` | Optioneel. Hiermee wordt aangegeven of GPU-ondersteuning in de installatie kopie moet worden ingeschakeld. De GPU-installatie kopie moet worden gebruikt in een Azure-service, zoals Azure Container Instances. Bijvoorbeeld Azure Machine Learning compute, Azure Virtual Machines en de Azure Kubernetes-service. De standaard waarde is False. Als `environment` is ingesteld, wordt deze vermelding genegeerd.|
| `baseImage` | `base_image` | Optioneel. Aangepaste installatie kopie die moet worden gebruikt als basis installatie kopie. Als er geen basis installatie kopie wordt gegeven, wordt de installatie kopie gebaseerd op de meegeleverde runtime parameter. Als `environment` is ingesteld, wordt deze vermelding genegeerd. |
| `baseImageRegistry` | `base_image_registry` | Optioneel. Het REGI ster van de installatie kopie met de basis installatie kopie. Als `environment` is ingesteld, wordt deze vermelding genegeerd.|
| `cudaVersion` | `cuda_version` | Optioneel. De versie van CUDA die moet worden geïnstalleerd voor installatie kopieën waarvoor GPU-ondersteuning is vereist. De GPU-installatie kopie moet worden gebruikt voor een Azure-service. Bijvoorbeeld Azure Container Instances, Azure Machine Learning compute, Azure Virtual Machines en Azure Kubernetes service. Ondersteunde versies zijn 9,0, 9,1 en 10,0. Als `enable_gpu` is ingesteld, is de standaard waarde 9,1. Als `environment` is ingesteld, wordt deze vermelding genegeerd. |
| `description` | `description` | Een beschrijving van de installatie kopie. Als `environment` is ingesteld, wordt deze vermelding genegeerd.  |
| `environment` | `environment` | Optioneel.  Azure Machine Learning [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

De volgende JSON is een voor beeld van een configuratie voor het afgebruiken van de CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

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

De volgende opdracht laat zien hoe u een model implementeert met behulp van het vorige configuratie bestand voor inschakeling (met de naam myInferenceConfig. json). 

Ook wordt de nieuwste versie van een bestaande Azure Machine Learning- [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) gebruikt (met de naam ' AzureML-minimal ').

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
