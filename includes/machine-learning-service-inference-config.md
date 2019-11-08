---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799781"
---
De vermeldingen in het `inferenceconfig.json` document worden toegewezen aan de para meters voor de klasse [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . De volgende tabel beschrijft de toewijzing tussen entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Pad naar een lokaal bestand dat de code bevat die moet worden uitgevoerd voor de installatie kopie. |
| `runtime` | `runtime` | Welke runtime moet worden gebruikt voor de installatie kopie. De huidige ondersteunde Runtimes zijn `spark-py` en `python`. |
| `condaFile` | `conda_file` | Optioneel. Pad naar een lokaal bestand dat een Conda-omgevings definitie bevat die moet worden gebruikt voor de installatie kopie. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Optioneel. Pad naar een lokaal bestand dat extra docker-stappen bevat die moeten worden uitgevoerd bij het instellen van de installatie kopie. |
| `sourceDirectory` | `source_directory` | Optioneel. Pad naar mappen die alle bestanden bevatten om de installatie kopie te maken. |
| `enableGpu` | `enable_gpu` | Optioneel. Hiermee wordt aangegeven of GPU-ondersteuning in de installatie kopie moet worden ingeschakeld. De GPU-installatie kopie moet worden gebruikt in een Azure-service, zoals Azure Container Instances, Azure Machine Learning compute, Azure Virtual Machines en de Azure Kubernetes-service. De standaard waarde is False. |
| `baseImage` | `base_image` | Optioneel. Aangepaste installatie kopie die moet worden gebruikt als basis installatie kopie. Als er geen basis installatie kopie wordt gegeven, wordt de installatie kopie gebaseerd op de meegeleverde runtime parameter. |
| `baseImageRegistry` | `base_image_registry` | Optioneel. Het REGI ster van de installatie kopie met de basis installatie kopie. |
| `cudaVersion` | `cuda_version` | Optioneel. De versie van CUDA die moet worden geïnstalleerd voor installatie kopieën waarvoor GPU-ondersteuning is vereist. De GPU-installatie kopie moet worden gebruikt in een Azure-service, zoals Azure Container Instances, Azure Machine Learning compute, Azure Virtual Machines en de Azure Kubernetes-service. Ondersteunde versies zijn 9,0, 9,1 en 10,0. Als `enable_gpu` is ingesteld, is de standaard waarde 9,1. |
| `description` | `description` | Een beschrijving van de installatie kopie. |

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