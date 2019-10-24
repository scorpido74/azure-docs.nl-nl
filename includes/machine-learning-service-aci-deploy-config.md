---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: 6e2a3a75181cf381f09e06b52c9bb3928dee4896
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556856"
---
De vermeldingen in de `deploymentconfig.json` document structuur met de para meters voor [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Description |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor ACI moet de waarde zijn `ACI`. |
| `containerResourceRequirements` | N.v.t. | Container voor de CPU-en geheugen entiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-kernen dat moet worden toegewezen. Standaard`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen (in GB) die voor deze webservice moet worden toegewezen. Prijs`0.5` |
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