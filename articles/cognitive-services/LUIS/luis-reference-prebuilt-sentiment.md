---
title: Sentiment-analyse-LUIS
titleSuffix: Azure Cognitive Services
description: Als sentiment-analyse is geconfigureerd, omvat de LUIS JSON-antwoord sentiment analyse.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270422"
---
# <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentiment-analyse is geconfigureerd, omvat de LUIS JSON-antwoord sentiment analyse. Meer informatie over sentiment analyse vindt u in de [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -documentatie.


## <a name="resolution-for-sentiment"></a>Oplossing voor sentiment

Sentiment-gegevens is een score tussen 1 en 0 die de positieve waarde (dichter bij 1) of negatief (dichter bij 0) sentiment van de gegevens aangeeft.

#### <a name="english-language"></a>[Engelse taal](#tab/english)

Wanneer de cultuur `en-us`is, is de reactie:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andere talen](#tab/other-languages)

Voor alle andere cult uren is de reactie:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

