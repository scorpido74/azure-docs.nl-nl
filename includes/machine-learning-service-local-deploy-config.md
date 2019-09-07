---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: ec0f85ff568bcd89d74ccd727d1c5ecfd8aab398
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390653"
---
De vermeldingen in de `deploymentconfig.json` document structuur met de para meters voor [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Description |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor lokale doelen moet de waarde zijn `local`. |
| `port` | `port` | De lokale poort waarop het HTTP-eind punt van de service wordt weer gegeven. |

Deze JSON is een voorbeeld implementatie configuratie voor gebruik met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
