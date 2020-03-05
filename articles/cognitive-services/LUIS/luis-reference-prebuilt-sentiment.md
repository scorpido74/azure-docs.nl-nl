---
title: Sentiment-analyse-LUIS
titleSuffix: Azure Cognitive Services
description: Als u sentimentanalyse is geconfigureerd, bevat de json-antwoord van LUIS sentimentanalyse.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270422"
---
# <a name="sentiment-analysis"></a>Sentimentanalyse
Als u sentimentanalyse is geconfigureerd, bevat de json-antwoord van LUIS sentimentanalyse. Meer informatie over sentiment analyse vindt u in de [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -documentatie.


## <a name="resolution-for-sentiment"></a>Oplossing voor sentiment

Sentimentsgegevens is een score tussen 1 en 0 waarmee wordt aangegeven welke positieve (dichter bij 1) of een negatieve (dichter bij 0) gevoel van de gegevens.

#### <a name="english-language"></a>[Engelse taal](#tab/english)

Wanneer de cultuur is `en-us`, is de reactie:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andere talen](#tab/other-languages)

Voor alle andere culturen en is het antwoord:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

