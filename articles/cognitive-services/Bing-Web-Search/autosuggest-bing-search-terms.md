---
title: Zoektermen automatisch voorstellen - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Koppel de Bing Web Search API met de Bing Autosuggest API om gebruikers een verbeterde zoekervaring te bieden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66384863"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Zoektermen van Bing automatisch voorstellen in uw toepassing

Als u een zoekvak aanbiedt waarin de gebruiker een zoekterm invoert, kunt u de [Automatische suggestie-API voor Bing](../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API retourneert voorgestelde queryreeksen op basis van gedeeltelijke zoektermen terwijl de gebruiker typt.

Nadat de gebruiker een zoekterm heeft ingegaan, moet deze URL zijn gecodeerd voordat de [parameter Q-query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) is ingesteld. Als de gebruiker bijvoorbeeld *sailing dinghies* invoert, stelt u `q` in op `sailing+dinghies` of `sailing%20dinghies`.

Als de queryterm een spelfout bevat, bevat het zoekantwoord een [object QueryContext.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) In het object worden de oorspronkelijke spelling weergegeven en de gecorrigeerde spelling die Bing heeft gebruikt voor de zoekopdracht.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

U deze informatie gebruiken om de gebruiker te laten weten dat u de querytekenreeks hebt gewijzigd wanneer u de zoekresultaten weergeeft.

![UX-voorbeeld van querycontext](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Volgende stappen  

* Bekijk voorbeeld [bing Web Search API-antwoorden](search-responses.md).  

## <a name="see-also"></a>Zie ook  

* [Verwijzing naar bing Web Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
