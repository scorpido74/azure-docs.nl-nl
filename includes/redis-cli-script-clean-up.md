---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175839"
---
## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het scriptvoorbeeld is uitgevoerd, kan de opdracht volgen worden gebruikt om de brongroep, azure cache voor bijvoorbeeld Redis en alle gerelateerde bronnen in de brongroep te verwijderen.

```azurecli
az group delete --name contosoGroup
```