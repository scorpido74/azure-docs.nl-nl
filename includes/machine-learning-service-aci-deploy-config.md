---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486063"
---
De vermeldingen in `deploymentconfig.json` de documentkaart naar de parameters voor [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters voor de methode beschreven:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor ACI moet de `ACI`waarde . |
| `containerResourceRequirements` | N.v.t. | Container voor de CPU en geheugenentiteiten. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Het aantal CPU-cores dat moet worden toegewezen. Standaardinstellingen`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | De hoeveelheid geheugen (in GB) die moet worden toegewezen voor deze webservice. Standaard`0.5` |
| `location` | `location` | De Azure-regio om deze webservice te implementeren. Als deze niet is opgegeven, wordt de locatie werkruimte gebruikt. Meer informatie over beschikbare regio's vindt u hier: [ACI-regio's](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Of u auth inschakelt voor deze Webservice. Standaardinstellingen voor False |
| `sslEnabled` | `ssl_enabled` | Of ssl voor deze webservice moet worden ingeschakeld. Standaard ingesteld op False. |
| `appInsightsEnabled` | `enable_app_insights` | Of u AppInsights voor deze Webservice wilt inschakelen. Standaardinstellingen voor False |
| `sslCertificate` | `ssl_cert_pem_file` | Het cert-bestand dat nodig is als SSL is ingeschakeld |
| `sslKey` | `ssl_key_pem_file` | Het sleutelbestand dat nodig is als SSL is ingeschakeld |
| `cname` | `ssl_cname` | De cname voor als SSL is ingeschakeld |
| `dnsNameLabel` | `dns_name_label` | Het dns-naamlabel voor het scoreeindpunt. Als dit niet is opgegeven, wordt er een uniek dns-naamlabel gegenereerd voor het scoreeindpunt. |

De volgende JSON is een voorbeeldimplementatieconfiguratie voor gebruik met de CLI:

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