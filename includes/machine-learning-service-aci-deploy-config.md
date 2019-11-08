---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: b3c1ad48da0ef28c73e3af5d1e30414534f4e94d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799783"
---
De vermeldingen in het `deploymentconfig.json` document worden toegewezen aan de para meters voor [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor ACI moet de waarde `ACI`zijn. |
| `containerResourceRequirements` | N.v.t. | Container voor de CPU-en geheugen entiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-kernen dat moet worden toegewezen. Standaard waarden, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen (in GB) die voor deze webservice moet worden toegewezen. Standaard, `0.5` |
| `location` | `location` | De Azure-regio voor het implementeren van deze webservice voor. Als niet wordt opgegeven, wordt de werkruimte locatie gebruikt. Meer informatie over beschik bare regio's vindt u hier: [ACI-regio's](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Hiermee wordt aangegeven of auth moet worden ingeschakeld voor deze webservice. De standaard waarde is False |
| `sslEnabled` | `ssl_enabled` | Hiermee wordt aangegeven of SSL moet worden ingeschakeld voor deze webservice. De standaard waarde is False. |
| `appInsightsEnabled` | `enable_app_insights` | Hiermee wordt aangegeven of AppInsights moet worden ingeschakeld voor deze webservice. De standaard waarde is False |
| `sslCertificate` | `ssl_cert_pem_file` | Het certificaat bestand dat nodig is als SSL is ingeschakeld |
| `sslKey` | `ssl_key_pem_file` | Het sleutel bestand dat nodig is als SSL is ingeschakeld |
| `cname` | `ssl_cname` | De CNAME voor als SSL is ingeschakeld |
| `dnsNameLabel` | `dns_name_label` | Het DNS-naam label voor het Score-eind punt. Als er geen uniek DNS-naam label wordt opgegeven, wordt het Score-eind punt gegenereerd. |

De volgende JSON is een voorbeeld implementatie configuratie voor gebruik met de CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```