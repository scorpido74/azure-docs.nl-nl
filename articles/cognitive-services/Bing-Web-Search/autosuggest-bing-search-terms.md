---
title: Zoek termen automatisch suggereren-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Koppel de Bing Webzoekopdrachten-API aan de Automatische suggestie-API voor Bing om gebruikers een betere Zoek ervaring te bieden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "66384863"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatische suggestie voor zoek termen van Bing in uw toepassing

Als u een zoekvak aanbiedt waarin de gebruiker een zoekterm invoert, kunt u de [Automatische suggestie-API voor Bing](../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API retourneert voorgestelde queryreeksen op basis van gedeeltelijke zoektermen terwijl de gebruiker typt.

Nadat de gebruiker een zoek term heeft ingevoerd, moet deze URL worden gecodeerd voordat de [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) query-para meter is ingesteld. Als de gebruiker bijvoorbeeld *sailing dinghies* invoert, stelt u `q` in op `sailing+dinghies` of `sailing%20dinghies`.

Als de query term een spel fout bevat, bevat het zoek antwoord een [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) -object. In het object worden de oorspronkelijke spelling weergegeven en de gecorrigeerde spelling die Bing heeft gebruikt voor de zoekopdracht.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

U kunt deze informatie gebruiken om de gebruiker te laten weten dat u de query reeks hebt gewijzigd wanneer u de zoek resultaten weergeeft.

![Query context UX-voor beeld](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Volgende stappen  

* Bekijk de voorbeeld [Bing webzoekopdrachten-API reacties](search-responses.md).  

## <a name="see-also"></a>Zie ook  

* [Bing Webzoekopdrachten-API referentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
