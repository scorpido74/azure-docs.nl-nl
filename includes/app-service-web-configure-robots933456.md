---
title: Include-bestand
description: Include-bestand
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78255827"
---
## <a name="robots933456-in-logs"></a>robots933456 in Logboeken

Mogelijk wordt het volgende bericht weer gegeven in de container logboeken:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

U kunt dit bericht veilig negeren. `/robots933456.txt`is een dummy URL-pad dat App Service gebruikt om te controleren of de container aanvragen kan ondersteunen. Een 404-antwoord geeft alleen aan dat het pad niet bestaat, maar wel App Service weet dat de container in orde is en klaar is om te reageren op aanvragen.

