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
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021072"
---
Met versie 3 van de Text Analytics-API kunt u het Text Analytics model kiezen dat u voor uw gegevens gebruikt. Gebruik de optionele para meter `model-version` om een versie van het model in uw aanvragen te selecteren. Als deze para meter niet is opgegeven, wordt de API standaard ingesteld op `latest`, de nieuwste stabiele model versie.

Beschik bare model versies:
* `2019-10-01` (`latest`)

Elke reactie van de V3-eind punten bevat een `model-version` veld waarin de gebruikte model versie wordt opgegeven.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
