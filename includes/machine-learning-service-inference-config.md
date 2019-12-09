---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927012"
---
De vermeldingen in het `inferenceconfig.json` document worden toegewezen aan de para meters voor de klasse [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . De volgende tabel beschrijft de toewijzing tussen entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pad naar een lokaal bestand dat de code bevat die moet worden uitgevoerd voor de installatie kopie. |
| `runtime` | `runtime` | Optioneel. Welke runtime moet worden gebruikt voor de installatie kopie. De huidige ondersteunde Runtimes zijn `spark-py` en `python`. Als `environment` is ingesteld, wordt dit genegeerd. |
| `condaFile` | `conda_file` | Optioneel. Pad naar een lokaal bestand dat een Conda-omgevings definitie bevat die moet worden gebruikt voor de installatie kopie.  Als `environment` is ingesteld, wordt dit genegeerd. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Optioneel. Pad naar een lokaal bestand dat extra docker-stappen bevat die moeten worden uitgevoerd bij het instellen van de installatie kopie.  Als `environment` is ingesteld, wordt dit genegeerd.|
| `sourceDirectory` | `source_directory` | Optioneel. Pad naar mappen die alle bestanden bevatten om de installatie kopie te maken. |
| `enableGpu` | `enable_gpu` | Optioneel. Hiermee wordt aangegeven of GPU-ondersteuning in de installatie kopie moet worden ingeschakeld. De GPU-installatie kopie moet worden gebruikt in een Azure-service, zoals Azure Container Instances, Azure Machine Learning compute, Azure Virtual Machines en de Azure Kubernetes-service. De standaardwaarde is Niet waar. Als `environment` is ingesteld, wordt dit genegeerd.|
| `baseImage` | `base_image` | Optioneel. Aangepaste installatie kopie die moet worden gebruikt als basis installatie kopie. Als er geen basis installatie kopie wordt gegeven, wordt de installatie kopie gebaseerd op de meegeleverde runtime parameter. Als `environment` is ingesteld, wordt dit genegeerd. |
| `baseImageRegistry` | `base_image_registry` | Optioneel. Het REGI ster van de installatie kopie met de basis installatie kopie. Als `environment` is ingesteld, wordt dit genegeerd.|
| `cudaVersion` | `cuda_version` | Optioneel. De versie van CUDA die moet worden geïnstalleerd voor installatie kopieën waarvoor GPU-ondersteuning is vereist. De GPU-installatie kopie moet worden gebruikt in een Azure-service, zoals Azure Container Instances, Azure Machine Learning compute, Azure Virtual Machines en de Azure Kubernetes-service. Ondersteunde versies zijn 9,0, 9,1 en 10,0. Als `enable_gpu` is ingesteld, is de standaard waarde 9,1. Als `environment` is ingesteld, wordt dit genegeerd. |
| `description` | `description` | Een beschrijving van de installatie kopie. Als `environment` is ingesteld, wordt dit genegeerd.  |
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

De volgende JSON is een voor beeld van een configuratie waarbij een bestaande Azure Machine Learning [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) met een specifieke versie wordt gebruikt met de cli:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

De volgende JSON is een voor beeld van een configuratie waarbij een bestaande Azure Machine Learning- [omgeving](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) met de meest recente versie wordt gebruikt voor het gebruik van de cli:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
