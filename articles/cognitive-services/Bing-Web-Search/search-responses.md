---
title: Reactie- en antwoordtypen van Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Wanneer u Bing Web Search een zoekverzoek verzendt, wordt een `SearchResponse` object in de antwoordtekst geretourneerd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110613"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Reactie- en antwoordtypen van Bing Web Search API  

Wanneer u Bing Web Search een zoekverzoek verzendt, wordt een [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) object in de antwoordtekst geretourneerd. Het object bevat een veld voor elk antwoord dat door Bing is bepaald, relevant was voor query's. In dit voorbeeld wordt een antwoordobject weergegeven als Bing alle antwoorden heeft geretourneerd:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Bing Web Search retourneert doorgaans een subset van de antwoorden. Als de queryterm bijvoorbeeld *rubberboten was,* kan `webPages`het `images`antwoord `rankingResponse`, en . Tenzij u [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) hebt gebruikt om webpagina's uit `webpages` `rankingResponse` te filteren, bevat het antwoord altijd de antwoorden en antwoorden.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Webpagina's beantwoorden

Het [webPages-antwoord](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) bevat een lijst met koppelingen naar webpagina's waarvan Bing Web Search heeft bepaald dat ze relevant waren voor de query. Elke [webpagina](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) in de lijst bevat: de naam, url, weergave-URL van de pagina, een korte beschrijving van de inhoud en de datum waarop Bing de inhoud heeft gevonden.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Een `name` `url` hyperlink gebruiken en maken die de gebruiker naar de webpagina brengt.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Afbeeldingen antwoord

Het antwoord van [de afbeeldingen](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) bevat een lijst met afbeeldingen waarvan Bing dacht dat ze relevant waren voor de query. Elke [afbeelding](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) in de lijst bevat de URL van de afbeelding, de grootte, de afmetingen en de coderingsindeling. Het Image-object bevat ook de URL van een miniatuur van de afbeelding en de afmetingen van de miniatuur.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Afhankelijk van het apparaat van de gebruiker, zou je meestal een subset van de miniaturen weer te geven, met een optie voor de gebruiker om [pagina door](paging-webpages.md) de resterende beelden.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

U kunt de miniatuur ook uitbreiden op het moment dat de gebruiker er met de cursor naar wijst. Vergeet niet om een geschikt kenmerk toe wijzen aan de afbeelding als u dat wilt doen. Bijvoorbeeld door de host uit `hostPageDisplayUrl` te halen en onder de afbeelding weer te geven. Zie [Formaat van miniaturen wijzigen en miniaturen bijsnijden](./resize-and-crop-thumbnails.md) voor informatie over het aanpassen van het formaat van de miniatuur.

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Als de gebruiker op `webSearchUrl` de miniatuur klikt, gebruikt u de gebruiker naar de pagina met zoekresultaten van Bing te brengen voor afbeeldingen, die een collage van de afbeeldingen bevat.

Zie [Image Search API](../bing-image-search/search-the-web.md)voor meer informatie over het antwoord en afbeeldingen van afbeeldingen .

## <a name="related-searches-answer"></a>Gerelateerde zoekopdrachten antwoord

De [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) antwoord bevat een lijst van de meest populaire gerelateerde query's gemaakt door andere gebruikers. Elke [query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) in de lijst`text`bevat een querytekenreeks (`displayText`), een querytekenreeks met hitmarkeringstekens ( ) en een URL (`webSearchUrl`) naar de pagina met zoekresultaten van Bing voor die query.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Gebruik `displayText` de querytekenreeks `webSearchUrl` en de URL om een hyperlink te maken die de gebruiker naar de pagina Met zoekresultaten van Bing brengt voor de gerelateerde query. U de `text` querytekenreeks ook gebruiken in uw eigen Web Search API-query en de resultaten zelf weergeven.

Zie [Markeren op treffers voor](../bing-web-search/hit-highlighting.md)informatie over het omgaan met de markeringsmarkeringen `displayText`.

Hieronder ziet u een voorbeeld van het gebruik van gerelateerde query's in Bing.com.

![Voorbeeld van gerelateerde zoekopdrachten op Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Video's beantwoorden

Het antwoord van [de video's](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) bevat een lijst met video's waarvan Bing dacht dat ze relevant waren voor de query. Elke [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) in de lijst bevat de URL van de video, de duur, de afmetingen en de coderingsindeling. Het Video-object bevat ook de URL van een miniatuur van de video en de afmetingen van de miniatuur.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Afhankelijk van het apparaat van de gebruiker, zou je meestal een subset van de video's weer geven met een optie voor de gebruiker om de resterende video's te bekijken. U zou een miniatuur van de video weergeven met de lengte, beschrijving (naam) en toeschrijving van de video (uitgever).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Terwijl de gebruiker over de miniatuur `motionThumbnailUrl` zweeft, kun je een miniatuurversie van de video afspelen. Vergeet niet om het kenmerk motionThumbnailUrl in te stellen wanneer u de miniatuur weergeeft.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Als de gebruiker op de miniatuur klikt, zijn dit de opties voor het bekijken van de video:

- De `hostPageUrl` video op de hostwebsite bekijken (bijvoorbeeld YouTube)
- De `webSearchUrl` video weergeven in de Bing-videobrowser
- De `embedHtml` video insluiten in uw eigen ervaring

Zie [Video Search API](../bing-video-search/search-the-web.md)voor meer informatie over het videoantwoord en de video's.

## <a name="news-answer"></a>Nieuwsantwoord

Het [nieuws](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) antwoord bevat een lijst van nieuwsberichten die Bing dacht dat relevant waren voor de query. Elk [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) in de lijst bevat de naam en beschrijving van het artikel, plus de URL naar het artikel op de website van de host. Als in het artikel een afbeelding staat, bevat het object een miniatuur van de afbeelding.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Afhankelijk van het apparaat van de gebruiker, zou u een subset van de nieuwsartikelen weergeven met een optie voor de gebruiker om de resterende artikelen te bekijken. Gebruik `name` en `url` om een hyperlink te maken waarmee de gebruiker naar het nieuwsartikel op de site van de host gaat. Als het artikel een afbeelding bevat, `url`maakt u de afbeelding klikbaar met behulp van . Vergeet niet om met `provider` bronvermelding toe te passen voor het artikel.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Zie [News Search API voor](../bing-news-search/search-the-web.md)meer informatie over het nieuwsantwoord en nieuwsartikelen.

## <a name="computation-answer"></a>Berekeningsantwoord

Als de gebruiker een wiskundige expressie of een eenheidsconversiequery invoert, kan het antwoord een [berekeningsantwoord](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) bevatten. Het `computation` antwoord bevat de genormaliseerde expressie en het resultaat ervan.

Een eenheidsconversiequery is een query die de ene eenheid omzet naar de andere. Bijvoorbeeld, *Hoeveel voeten in 10 meter?* of *Hoeveel eetlepels in een 1/4 kopje?*

Het volgende `computation` toont het antwoord voor *Hoeveel voeten in 10 meter?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Het volgende toont voorbeelden van wiskundige `computation` query's en de bijbehorende antwoorden.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Een wiskundige expressie kan de volgende symbolen bevatten:

|Symbool|Beschrijving|
|------------|-----------------|
|+|Optelling|
|-|Aftrekking|
|/|Deling|
|*|Vermenigvuldiging|
|^|Power|
|!|Faculteit|
|.|Decimal|
|()|Voorrangsgroepering|
|[]|Functie|

Een wiskundige expressie kan de volgende constanten bevatten:

|Symbool|Beschrijving|
|------------|-----------------|
|Pi|3.14159...|
|Mate|Mate|
|i|Denkbeeldig getal|
|a|e, 2.71828...|
|GoldenRatio (GoldenRatio)|Gulden snede, 1,61803...|

Een wiskundige expressie kan de volgende functies bevatten:

|Symbool|Beschrijving|
|------------|-----------------|
|Sorteren|Vierkantswortel|
|Zonde[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Trigonometrische functies (met argumenten in radialen)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Omgekeerde trigonometrische functies (resultaat in radialen)|
|Exp[x], E^x|Exponentiële functie|
|Log[x]|Natuurlijke logaritme|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Hyperbolische functies|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Inverse hyperbolische functies|

Wiskundige expressies die variabelen bevatten (bijvoorbeeld 4x+6=18, waarbij x de variabele is) worden niet ondersteund.

## <a name="timezone-answer"></a>TimeZone-antwoord

Als de gebruiker een tijd- of datumquery invoert, kan het antwoord een [TimeZone-antwoord](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) bevatten. Dit antwoord ondersteunt impliciete of expliciete query's. Een impliciete query, zoals *Hoe laat is het?* Een expliciete query, zoals *Hoe laat is het in Seattle?*

Het `timeZone` antwoord geeft de naam van de locatie, de huidige UTC-datum en -tijd op de opgegeven locatie en de UTC-verschuiving. Als de grens van de locatie zich binnen meerdere tijdzones bevindt, bevat het antwoord de huidige UTC-datum en -tijd van alle tijdzones binnen de grens. Omdat de staat Florida bijvoorbeeld binnen twee tijdzones valt, bevat het antwoord de lokale datum en tijd van beide tijdzones.  

Als de query de tijd van een staat of land/regio opvraagt, bepaalt Bing de `primaryCityTime` primaire plaats binnen de geografische grens van de locatie en retourneert deze in het veld. Als de grens meerdere tijdzones bevat, worden `otherCityTimes` de resterende tijdzones geretourneerd in het veld.

In het volgende ziet u `timeZone` voorbeeldquery's die het antwoord retourneren.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion antwoord

Als Bing vaststelt dat de gebruiker mogelijk iets anders heeft willen zoeken, bevat het antwoord een object [SpellSuggestions.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) Als de gebruiker bijvoorbeeld naar *carlos pen*zoekt, kan Bing bepalen dat de gebruiker waarschijnlijk van plan was om in plaats daarvan naar Carlos Pena te zoeken (op basis van eerdere zoekopdrachten door anderen van *carlos pen).* Als volgt ziet u een voorbeeldreactie van de spreuk.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Antwoordheaders

Reacties uit de Bing Web Search API kunnen de volgende kopteksten bevatten:

|||
|-|-|
|`X-MSEdge-ClientID`|De unieke id die Bing aan de gebruiker heeft toegewezen|
|`BingAPIs-Market`|De markt die werd gebruikt om aan het verzoek te voldoen|
|`BingAPIs-TraceId`|De logboekvermelding op de Bing API-server voor dit verzoek (voor ondersteuning)|

Het is vooral belangrijk om de client-ID voort te zetten en deze terug te sturen met volgende verzoeken. Wanneer u dit doet, gebruikt de zoekopdracht eerdere context in het rangschikken van zoekresultaten en biedt het ook een consistente gebruikerservaring.

Wanneer u echter de Bing Web Search API van JavaScript aanroept, kunnen de ingebouwde beveiligingsfuncties (CORS) van uw browser ervoor zorgen dat u geen toegang krijgt tot de waarden van deze kopteksten.

Als u toegang wilt krijgen tot de kopteksten, u de Bing Web Search API-aanvraag indienen via een CORS-proxy. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers`-header waardoor antwoordheaders worden opgenomen in de whitelist en beschikbaar gemaakt voor JavaScript.

Het is eenvoudig om een CORS-proxy te installeren, zodat onze [zelfstudie-app](tutorial-bing-web-search-single-page-app.md) toegang heeft tot de optionele clientheaders. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

    npm install -g cors-proxy-server

Wijzig vervolgens het eindpunt van de Bing Web Search API in het HTML-bestand in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Start ten slotte de CORS-proxy met de volgende opdracht:

    cors-proxy-server

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

## <a name="response-headers-in-production"></a>Reactiekoppen in productie

De CORS proxy benadering beschreven in het vorige antwoord is geschikt voor ontwikkeling, testen en leren.

In een productieomgeving moet u een serverscript hosten op hetzelfde domein als de webpagina die de Bing Web Search API gebruikt. Dit script moet API-aanroepen op verzoek van de webpagina JavaScript en alle resultaten, inclusief kopteksten, teruggeven aan de client. Aangezien de twee bronnen (pagina en script) een oorsprong delen, wordt CORS niet gebruikt en zijn de speciale koppen toegankelijk voor het JavaScript op de webpagina.

Deze aanpak beschermt uw API-sleutel ook tegen blootstelling aan het publiek, omdat alleen het script aan de serverzijde het nodig heeft. Het script kan een andere methode gebruiken om ervoor te zorgen dat de aanvraag is geautoriseerd.

Hieronder ziet u hoe Bing de spellingsuggestie gebruikt.

![Voorbeeld van bing-spellingsuggestie](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Volgende stappen  

* Controleer de documentatie [van de beperking van het verzoek.](throttling-requests.md)  

## <a name="see-also"></a>Zie ook  

* [Verwijzing naar bing Web Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
