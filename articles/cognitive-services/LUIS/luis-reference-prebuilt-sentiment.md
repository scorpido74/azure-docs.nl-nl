---
title: Sentimentanalyse - LUIS
titleSuffix: Azure Cognitive Services
description: Als sentimentanalyse is geconfigureerd, bevat de LUIS-json-respons sentimentanalyse.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270422"
---
# <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentimentanalyse is geconfigureerd, bevat de LUIS-json-respons sentimentanalyse. Meer informatie over sentimentanalyse vindt u in de [text analytics-documentatie.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)


## <a name="resolution-for-sentiment"></a>Oplossing voor sentiment

Sentimentgegevens zijn een score tussen 1 en 0 die het positieve (dichter bij 1) of negatieve (dichter bij 0) sentiment van de gegevens aangeven.

#### <a name="english-language"></a>[Engelstalig](#tab/english)

Wanneer cultuur `en-us`is, is het antwoord:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andere talen](#tab/other-languages)

Voor alle andere culturen is het antwoord:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [V3-voorspellingseindpunt](luis-migration-api-v3.md).

