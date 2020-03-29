---
title: Aanvragen verzenden naar de Bing Autosuggest API
titleSuffix: Azure Cognitive Services
description: De Bing Automatische suggesties-API retourneert een lijst met voorgestelde query's op basis van de gedeeltelijke queryreeks in het zoekvak. Meer informatie over het verzenden van aanvragen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072849"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Het verzenden van aanvragen naar de Bing Autosuggest API.

Als uw toepassing query's verstuurt naar een van de API's van Bing Search, kunt u de Bing Automatische suggesties-API gebruiken om de ervaring van uw gebruikers te verbeteren. De Bing Automatische suggesties-API retourneert een lijst met voorgestelde query's op basis van de gedeeltelijke queryreeks in het zoekvak. Als tekens in een zoekvak in uw toepassing worden ingevoerd, u suggesties weergeven in een vervolgkeuzelijst. Gebruik dit artikel voor meer informatie over het verzenden van aanvragen naar deze API. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing Automatisch API-eindpunt voorstellen

De **Bing Autosuggest API** bevat één eindpunt, waarmee een lijst met voorgestelde query's van een gedeeltelijke zoekterm wordt geretourneerd.

Als u voorgestelde query's wilt `GET` opvragen met de Bing-API, stuurt u een aanvraag naar het volgende eindpunt. Gebruik de kopteksten en URL-parameters om verdere specificaties te definiëren.

**Eindpunt:** Retourneert zoeksuggesties als JSON-resultaten die relevant zijn `?q=""`voor de invoer van de gebruiker, gedefinieerd door .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Zie de [Bing Autosuggest API v7-referentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) voor meer informatie over kopteksten, parameters, marktcodes, antwoordobjecten, fouten, enz..

De **Bing** Bing-API's ondersteunen zoekacties die resultaten retourneren op basis van hun type.Alle zoekeindpunten retourneren resultaten als JSON-antwoordobjecten.
Alle eindpunten ondersteunen query's die een specifieke taal en/of locatie retourneren op lengte, breedte- en zoekradius.

Zie de referentiepagina's voor elk type voor volledige informatie over de parameters die door elk eindpunt worden ondersteund.
Zie [Snelstarts automatisch voorstellen](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)voor voorbeelden van basisaanvragen met de API Automatisch voorstellen .

## <a name="bing-autosuggest-api-requests"></a>Api-aanvragen voor Bing Autosuggest

> [!NOTE]
> * Aanvragen voor de Bing Autosuggest-API moeten het HTTPS-protocol gebruiken.

Het is raadzaam dat alle aanvragen afkomstig zijn van een server. Het distribueren van de sleutel als onderdeel van een clienttoepassing biedt meer mogelijkheden voor kwaadaardige toegang van derden. Bovendien biedt het voeren van gesprekken vanaf een server één upgradepunt voor toekomstige updates.

De aanvraag moet de queryparameter [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) bevatten, met daarin de gedeeltelijke zoekterm van de gebruiker. Hoewel dit optioneel is, moet de aanvraag ook de queryparameter [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) bevatten. Hiermee geeft u de markt aan waarvan de resultaten afkomstig moeten zijn. Zie [Queryparameters](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) voor een lijst met optionele queryparameters. Alle waarden van queryparameter moeten als een URL zijn gecodeerd.

De aanvraag moet de header [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) bevatten. Hoewel dit optioneel is, wordt u aangeraden ook altijd deze headers op te geven:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

De headers ClientIP en Location zijn belangrijk voor het retourneren van locatiespecifieke inhoud.

Zie [Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers) voor een lijst met alle aanvraag- en antwoordheaders.

> [!NOTE]
> Wanneer u de Bing Autosuggest API vanuit JavaScript aanroept, kunnen de ingebouwde beveiligingsfuncties van uw browser ervoor zorgen dat u geen toegang hebt tot de waarden van deze kopteksten.

Om dit op te lossen, u de Bing Autosuggest API-aanvraag indienen via een CORS-proxy. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers`-header waardoor antwoordheaders worden opgenomen in de whitelist en beschikbaar gemaakt voor JavaScript.

Het is eenvoudig om een CORS-proxy te installeren, zodat onze [zelfstudie-app](../tutorials/autosuggest.md) toegang heeft tot de optionele clientheaders. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

    npm install -g cors-proxy-server

Wijzig vervolgens het API-eindpunt van Bing Automatisch voorstellen in het HTML-bestand in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Start ten slotte de CORS-proxy met de volgende opdracht:

    cors-proxy-server

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

Aanvragen moeten alle voorgestelde queryparameters en kopteksten bevatten. 

In het volgende voorbeeld ziet u een aanvraag die de voorgestelde queryreeksen voor *sail* retourneert.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Als dit de eerste keer is van u een van de Bing-API's aanroept, moet u de header met de client-id weglaten. Voeg de header met de client-id alleen toe als u eerder een Bing-API hebt aangeroepen en Bing een client-id heeft geretourneerd voor de combinatie van gebruiker en apparaat.

De volgende websuggestiegroep is een antwoord op de bovenstaande aanvraag. De groep bevat een lijst met suggesties voor `displayText`zoekopdrachten, waarbij elke suggestie een , `query`, en `url` veld bevat.

Het veld `displayText` bevat de voorgestelde query die u gebruikt voor het vullen van de vervolgkeuzelijst van het zoekvak. U moet alle suggesties uit het antwoord weergeven, en in de opgegeven volgorde.  

Als de gebruiker een query selecteert in de vervolgkeuzelijst, u deze gebruiken om de api's van [Bing Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) aan te `url` roepen en de resultaten zelf weer te geven, of de gebruiker naar de pagina Met resultaaten van Bing te sturen met het veld Geretourneerd.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Wat is Bing Automatische suggesties?](../get-suggested-search-terms.md)
- [Naslaghandleiding Bing Automatische suggesties-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Voorgestelde zoektermen ophalen uit de Bing Autosuggest API](get-suggestions.md)
