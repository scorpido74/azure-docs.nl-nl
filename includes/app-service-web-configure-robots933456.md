---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255827"
---
## <a name="robots933456-in-logs"></a>robots933456 in Logboeken

Mogelijk wordt het volgende bericht weer gegeven in de container logboeken:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

U kunt dit bericht veilig negeren. `/robots933456.txt` is een dummy URL-pad dat door App Service wordt gebruikt om te controleren of de container aanvragen kan ondersteunen. Een 404-antwoord geeft alleen aan dat het pad niet bestaat, maar wel App Service weet dat de container in orde is en klaar is om te reageren op aanvragen.

