---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79485948"
---
De vermeldingen in de `deploymentconfig.json` document structuur met de para meters voor [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Description |
| ----- | ----- | ----- |
| `computeType` | NA | Het rekendoel. Voor AKS moet de waarde zijn `aks` . |
| `autoScaler` | NA | Bevat configuratie-elementen voor automatisch schalen. Zie de tabel met automatische schalen. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Hiermee wordt aangegeven of automatisch schalen moet worden ingeschakeld voor de webservice. If `numReplicas`  =  `0` , `True` anders `False` . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Het minimum aantal containers dat moet worden gebruikt bij het automatisch schalen van deze webservice. Standaard, `1` . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Het maximum aantal containers dat moet worden gebruikt bij het automatisch schalen van deze webservice. Standaard, `10` . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hoe vaak de automatische schaalr probeert deze webservice te schalen. Standaard, `1` . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Het doel gebruik (in procenten 100) dat de automatische schaalr moet proberen te onderhouden voor deze webservice. Standaard, `70` . |
| `dataCollection` | NA | Bevat configuratie-elementen voor het verzamelen van gegevens. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Hiermee wordt aangegeven of het verzamelen van model gegevens moet worden ingeschakeld voor de webservice. Standaard, `False` . |
| `authEnabled` | `auth_enabled` | Hiermee wordt aangegeven of sleutel verificatie moet worden ingeschakeld voor de webservice. Beide `tokenAuthEnabled` en `authEnabled` kunnen niet `True` . Standaard, `True` . |
| `tokenAuthEnabled` | `token_auth_enabled` | Hiermee wordt aangegeven of Token verificatie moet worden ingeschakeld voor de webservice. Beide `tokenAuthEnabled` en `authEnabled` kunnen niet `True` . Standaard, `False` . |
| `containerResourceRequirements` | NA | Container voor de CPU-en geheugen entiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-kernen dat moet worden toegewezen voor deze webservice. Standaard`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen (in GB) die voor deze webservice moet worden toegewezen. Prijs`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Hiermee wordt aangegeven of Application Insights logboek registratie moet worden ingeschakeld voor de webservice. Standaard, `False` . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Een time-out voor het afdwingen van het bepalen van aanroepen naar de webservice. Standaard, `60000` . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Het maximum aantal gelijktijdige aanvragen per knoop punt voor deze webservice. Standaard, `1` . |
| `maxQueueWaitMs` | `max_request_wait_time` | De maximale tijd dat een aanvraag in de wachtrij van thee (in milliseconden) blijft voordat een 503-fout wordt geretourneerd. Standaard, `500` . |
| `numReplicas` | `num_replicas` | Het aantal containers dat moet worden toegewezen voor deze webservice. Er is geen standaardwaarde. Als deze para meter niet is ingesteld, wordt automatisch schalen standaard ingeschakeld. |
| `keys` | NA | Bevat configuratie-elementen voor sleutels. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Een primaire verificatie sleutel die moet worden gebruikt voor deze webservice |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Een secundaire verificatie sleutel die moet worden gebruikt voor deze webservice |
| `gpuCores` | `gpu_cores` | Het aantal GPU-kernen (per container replica) dat moet worden toegewezen voor deze webservice. Standaard is 1. Alleen gehele numerieke waarden worden ondersteund. |
| `livenessProbeRequirements` | NA | Bevat configuratie-elementen voor vereisten voor de test op de liveiteit. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Hoe vaak (in seconden) de duur van de test bewerking moet worden uitgevoerd. De standaard waarde is 10 seconden. De minimum waarde is 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Aantal seconden nadat de container is gestart voordat de tests van de live-test worden geïnitieerd. De standaard waarde is 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Aantal seconden waarna een time-out optreedt voor de duur van de online test. De standaard waarde is 2 seconden. De minimum waarde is 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | De minimale opeenvolgende successen voor de test op de eerste keer worden als geslaagd beschouwd als ze zijn mislukt. De standaard waarde is 1. De minimum waarde is 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Wanneer een pod wordt gestart en de test op de productie tijd mislukt, probeert Kubernetes failureThreshold tijden te proberen voordat deze wordt weer geven. De standaard waarde is 3. De minimum waarde is 1. |
| `namespace` | `namespace` | De Kubernetes-naam ruimte waarin de webservice is geïmplementeerd. Maxi maal 63 kleine letters (' a-z ', ' 0 '-' 9 ') en afbreek streepjes ('-') tekens. De eerste en laatste tekens kunnen geen afbreek streepjes zijn. |

De volgende JSON is een voorbeeld implementatie configuratie voor gebruik met de CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
