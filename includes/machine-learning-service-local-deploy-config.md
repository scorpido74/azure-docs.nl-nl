---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799782"
---
De vermeldingen in het `deploymentconfig.json` document worden toegewezen aan de para meters voor [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). De volgende tabel beschrijft de toewijzing tussen de entiteiten in het JSON-document en de para meters voor de-methode:

| JSON-entiteit | Methode parameter | Beschrijving |
| ----- | ----- | ----- |
| `computeType` | N.v.t. | Het rekendoel. Voor lokale doelen moet de waarde `local`zijn. |
| `port` | `port` | De lokale poort waarop het HTTP-eind punt van de service wordt weer gegeven. |

Deze JSON is een voorbeeld implementatie configuratie voor gebruik met de CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
