---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: c288ea4a2c62999f27b3f07c39df40dae7ab1426
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926946"
---
De vermeldingen in het `deploymentconfig.json` document worden toegewezen aan de para meters voor [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | N.V.T. | Het rekendoel. Voor lokale doelen moet de waarde `local`zijn. |
| `port` | `port` | De lokale poort waarop het HTTP-eind punt van de service wordt weer gegeven. |

Deze JSON is een voorbeeld implementatie configuratie voor gebruik met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
