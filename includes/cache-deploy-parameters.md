---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175844"
---
### <a name="cacheskuname"></a>cacheSKUName

De prijscategorie van de nieuwe Azure-cache voor Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

De sjabloon definieert de waarden die zijn toegestaan voor deze parameter (Basic, Standard of Premium) en wijst een standaardwaarde (Basic) toe als er geen waarde is opgegeven. Basic biedt één knooppunt met meerdere formaten tot 53 GB. Standard biedt primaire/replica met twee nodes met meerdere formaten die beschikbaar zijn tot 53 GB en 99,9% SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

De familie voor de sku.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

De grootte van de nieuwe Azure-cache voor bijvoorbeeld Redis.

Voor de Basic en Standard families:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

De capaciteit van de Premium-waardecache wordt hetzelfde gedefinieerd, met uitzondering van de toegestane waarden die van 1 tot 5 lopen in plaats van van 0 tot 6.

De sjabloon definieert de gehele waarden die zijn toegestaan voor deze parameter (0 tot en met 6 voor de basis- en standaardfamilies; 1 tot en met 5 voor de Premium-familie). Als er geen waarde is opgegeven, kent de sjabloon een standaardwaarde van 0 toe voor Basic en Standard, 1 voor Premium.

De waarden komen overeen met de volgende cacheformaten:

| Waarde | Basis en Standaard<br>cachegrootte | Premium<br>cachegrootte |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (standaard)                 | N.v.t.                   |
| 1     | 1 GB                             | 6 GB (standaard)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | N.v.t.                   |
