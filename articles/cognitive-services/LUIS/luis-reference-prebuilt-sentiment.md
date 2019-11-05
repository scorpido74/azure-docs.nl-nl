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
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508021"
---
# <a name="sentiment-analysis"></a>Sentimentanalyse
Als sentiment-analyse is geconfigureerd, omvat de LUIS JSON-antwoord sentiment analyse. Meer informatie over sentiment analyse vindt u in de [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -documentatie.


## <a name="resolution-for-sentiment"></a>Oplossing voor sentiment

Sentiment-gegevens is een score tussen 1 en 0 die de positieve waarde (dichter bij 1) of negatief (dichter bij 0) sentiment van de gegevens aangeeft.

#### <a name="english-languagetabenglish"></a>[Engelse taal](#tab/english)

Wanneer de cultuur is `en-us`, is de reactie:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[Andere talen](#tab/other-languages)

Voor alle andere cult uren is de reactie:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [v3-Voorspellings eindpunt](luis-migration-api-v3.md).

