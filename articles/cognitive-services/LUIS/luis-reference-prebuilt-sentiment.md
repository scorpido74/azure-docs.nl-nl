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
ms.date: 07/01/2020
ms.author: diberry
ms.openlocfilehash: 2d15170e3785d8978b9cb21eae3b94b002f9172e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857179"
---
# <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentiment-analyse is geconfigureerd, omvat de LUIS JSON-antwoord sentiment analyse. Meer informatie over sentiment analyse vindt u in de [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -documentatie.

LUIS maakt gebruik van Text Analytics v2. 

## <a name="resolution-for-sentiment"></a>Oplossing voor sentiment

Sentiment-gegevens is een score tussen 1 en 0 die de positieve waarde (dichter bij 1) of negatief (dichter bij 0) sentiment van de gegevens aangeeft.

#### <a name="english-language"></a>[Engelse taal](#tab/english)

Wanneer de cultuur is `en-us` , is de reactie:

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

