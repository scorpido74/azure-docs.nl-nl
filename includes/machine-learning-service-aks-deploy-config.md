---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485948"
---
De vermeldingen in `deploymentconfig.json` de documentkaart aan de parameters voor [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters voor de methode beschreven:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor AKS moet de `aks`waarde . |
| `autoScaler` | N.v.t. | Bevat configuratie-elementen voor automatisch schalen. Zie de tabel autoscaler. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Of u automatisch schalen voor de webservice wilt inschakelen. `numReplicas`  = Indien `0` `True`, ; anders. `False` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Het minimum aantal containers dat moet worden gebruikt bij het automatisch schalen van deze webservice. Standaard, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Het maximum aantal containers dat moet worden gebruikt bij het automatisch schalen van deze webservice. Standaard, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Hoe vaak probeert de autoscaler deze webservice te schalen. Standaard, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Het doelgebruik (in procenten van de 100) dat de autoscaler moet proberen te behouden voor deze webservice. Standaard, `70`. |
| `dataCollection` | N.v.t. | Bevat configuratie-elementen voor het verzamelen van gegevens. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Of u modelgegevenswilt verzamelen voor de webservice. Standaard, `False`. |
| `authEnabled` | `auth_enabled` | Al dan niet om sleutelverificatie voor de webservice in te schakelen. Beide `tokenAuthEnabled` `authEnabled` en `True`kan niet . Standaard, `True`. |
| `tokenAuthEnabled` | `token_auth_enabled` | Al dan niet om tokenverificatie in te schakelen voor de webservice. Beide `tokenAuthEnabled` `authEnabled` en `True`kan niet . Standaard, `False`. |
| `containerResourceRequirements` | N.v.t. | Container voor de CPU en geheugenentiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-cores dat moet worden toegewezen voor deze webservice. Standaardinstellingen`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen (in GB) die moet worden toegewezen voor deze webservice. Standaard`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Of u de logboekregistratie van Application Insights voor de webservice wilt inschakelen. Standaard, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Een time-out om af te dwingen voor het scoren van oproepen naar de webservice. Standaard, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | De maximale gelijktijdige aanvragen per knooppunt voor deze webservice. Standaard, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | De maximale tijd dat een aanvraag in de wachtrij blijft (in milliseconden) voordat een 503-fout wordt geretourneerd. Standaard, `500`. |
| `numReplicas` | `num_replicas` | Het aantal containers dat moet worden toegewezen voor deze webservice. Er is geen standaardwaarde. Als deze parameter niet is ingesteld, is de autoscaler standaard ingeschakeld. |
| `keys` | N.v.t. | Bevat configuratie-elementen voor sleutels. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Een primaire auth-toets die u gebruiken voor deze webservice |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Een secundaire auth-sleutel om te gebruiken voor deze Webservice |
| `gpuCores` | `gpu_cores` | Het aantal GPU-cores (per containerreplica) dat moet worden toegewezen voor deze webservice. Standaard is 1. Ondersteunt alleen hele getalwaarden. |
| `livenessProbeRequirements` | N.v.t. | Bevat configuratie-elementen voor vereisten voor levendigheidsssonde. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Hoe vaak (in seconden) om de levendigheid sonde uit te voeren. Standaard tot 10 seconden. Minimumwaarde is 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Aantal seconden nadat de container is gestart voordat levendigheidssondes worden gestart. Standaard waarden tot 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Aantal seconden waarna de levendigheid sonde een tijd uit. Standaard tot 2 seconden. Minimumwaarde is 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimale opeenvolgende successen voor de levendigheid sonde te worden beschouwd als succesvol na te hebben gefaald. Standaard op 1. Minimumwaarde is 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Wanneer een Pod wordt gestart en de liveness sonde mislukt, kubernetes zal proberen failureThreshold tijden alvorens op te geven. Standaard op 3. Minimumwaarde is 1. |
| `namespace` | `namespace` | De Kubernetes-naamruimte waarde webservice wordt geïmplementeerd. Maximaal 63 alfanumerieke kleine letters ('a'-'z', '0'-'9') en koppelteken ('-') tekens. De eerste en laatste tekens kunnen geen koppeltekens zijn. |

De volgende JSON is een voorbeeldimplementatieconfiguratie voor gebruik met de CLI:

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
