---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477269"
---
De vermeldingen in de `deploymentconfig.json` document structuur met de para meters voor [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | NA | Het rekendoel. Voor lokale doelen moet de waarde zijn `local` . |
| `port` | `port` | De lokale poort waarop het HTTP-eind punt van de service wordt weer gegeven. |

Deze JSON is een voorbeeld implementatie configuratie voor gebruik met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
