---
title: Model versie beheer
titleSuffix: Azure Cognitive Services
description: Model versies opgeven in de V3-eind punten
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488636"
---
Met versie 3 van de Text Analytics-API kunt u het Text Analytics model kiezen dat u voor uw gegevens gebruikt. Gebruik de optionele para meter `model-version` om een versie van het model in uw aanvragen te selecteren. Als deze para meter niet is opgegeven, wordt de API standaard ingesteld op `latest`, de nieuwste stabiele model versie.

Beschik bare model versies:
* `2019-10-01` (`latest`)

Elke reactie van de V3-eind punten bevat een `model-version` veld waarin de gebruikte model versie wordt opgegeven.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
