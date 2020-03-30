---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477269"
---
De vermeldingen in `deploymentconfig.json` de documentkaart naar de parameters voor [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). In de volgende tabel wordt de toewijzing tussen de entiteiten in het JSON-document en de parameters voor de methode beschreven:

| JSON-entiteit | Methodeparameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor lokale doelen moet `local`de waarde . |
| `port` | `port` | De lokale poort waarop het HTTP-eindpunt van de service kan worden weergegeven. |

Deze JSON is een voorbeeldconfiguratie voor implementatie voor gebruik met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
