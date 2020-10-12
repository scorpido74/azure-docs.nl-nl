---
title: Zoektermen voorstellen met de Bing Automatische suggesties-API
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt het concept beschreven van het Voorst Ellen van query termen met behulp van de Automatische suggestie-API voor Bing en de impact van de query lengte op relevantie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 060dbd29ee4ddb78e8ae9b2ed4e7814da3c4eebf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072890"
---
# <a name="suggesting-query-terms"></a>Querytermen voorstellen

Normaal gesproken zou u telkens wanneer een gebruiker een nieuwe teken in het zoekvak van uw toepassing typt de Bing Automatische suggesties-API aanroepen. De volledigheid van de queryreeks is van invloed op de relevantie van de voorgestelde querytermen die de API retourneert. Hoe vollediger de queryreeks, des te relevanter de lijst met voorgestelde querytermen. Zo zijn de suggesties die de API mogelijk retourneert voor `s` waarschijnlijk minder relevant dan de query's die worden geretourneerd voor `sailing dinghies`.

## <a name="example-request"></a>Voorbeeldaanvraag

In het volgende voorbeeld ziet u een aanvraag die de voorgestelde queryreeksen voor *sail* retourneert. Vergeet niet om de gedeeltelijke zoekterm van de gebruiker als URL te coderen wanneer u de parameter [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) instelt. Als de gebruiker bijvoorbeeld *sailing les* invoert, stelt u `q` in op `sailing+les` of `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Het volgende antwoord bevat een lijst met [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction)-objecten die de voorgestelde querytermen bevatten.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Voorgestelde querytermen gebruiken

Elke suggestie bevat een veld `displayText`, `query` en `url`. Het veld `displayText` bevat de voorgestelde query die u gebruikt voor het vullen van de vervolgkeuzelijst van het zoekvak. U moet alle suggesties uit het antwoord weergeven, en in de opgegeven volgorde.

In het volgende voorbeeld ziet u een zoekvak met vervolgkeuzelijst dat voorgestelde querytermen uit de Bing Automatische suggesties-API bevat.

![Vervolgkeuzelijst voor zoekvak met automatische suggesties](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Als de gebruiker een voorgestelde query selecteert in de vervolgkeuzelijst, gebruikt u de zoekterm in het veld `query` om de [Bing Webzoekopdrachten-API](../../bing-web-search/search-the-web.md) aan te roepen en zelf de resultaten weer te geven. Een alternatief is om de gebruiker via de URL in het veld `url` om te leiden naar de pagina met zoekresultaten van Bing.

## <a name="next-steps"></a>Volgende stappen

* [Wat is de Bing Automatische suggesties-API?](../get-suggested-search-terms.md)