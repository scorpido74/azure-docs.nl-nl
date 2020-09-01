---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477269"
---
De vermeldingen in de documenttoewijzing `deploymentconfig.json` voor de parameters voor [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters beschreven voor de methode:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | NA | Het rekendoel. Voor lokale doelen moet de waarde `local` zijn. |
| `port` | `port` | De lokale poort waarop het HTTP-eindpunt van de service beschikbaar wordt gemaakt. |

De volgende JSON is een voorbeeld van een implementatieconfiguratie die gebruikt kan worden met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
