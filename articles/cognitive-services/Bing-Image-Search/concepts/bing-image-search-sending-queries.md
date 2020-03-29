---
title: Zoekopdrachten voor afbeeldingen aanpassen en voorstellen - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het aanpassen van de zoekopdrachten die u naar de Bing Image Search API verzendt.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542743"
---
# <a name="customize-and-suggest-image-search-queries"></a>Zoekopdrachten voor afbeeldingen aanpassen en voorstellen

Gebruik dit artikel voor meer informatie over het aanpassen van query's en het voorstellen van zoektermen die naar de Bing Image Search API moeten worden verzonden.

## <a name="suggest-search-terms"></a>Zoektermen voorstellen

Als uw app een zoekvak heeft waarin zoektermen worden ingevoerd, u de [Bing Autosuggest-API](../../bing-autosuggest/get-suggested-search-terms.md) gebruiken om de ervaring te verbeteren. De API kan voorgestelde zoektermen in realtime weergeven. De API retourneert voorgestelde querytekenreeksen op basis van gedeeltelijke zoektermen en cognitieve services.

## <a name="pivot-the-query"></a>De query draaien

Als Bing de oorspronkelijke zoekopdracht kan [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) segmenteren, bevat `pivotSuggestions`het object geretourneerde afbeeldingen . Draaisuggesties kunnen worden weergegeven als optionele zoektermen voor de gebruiker. Als de oorspronkelijke query bijvoorbeeld *Microsoft Surface*was, kan Bing de query segmenteren naar *Microsoft* en *Surface* en voor elk van deze query's voorgestelde draaipunten verstrekken. Deze suggesties kunnen worden weergegeven als optionele queryvoorwaarden voor de gebruiker.

In het volgende voorbeeld worden de draaisuggesties voor *Microsoft Surface*weergegeven:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Het veld `pivotSuggestions` bevat de lijst met segmenten (draaipunten) waarin de oorspronkelijke query is onderverdeeld. Voor elk draaipunt bevat de respons een lijst met [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objecten met voorgestelde query's. Het `text` veld bevat de voorgestelde query. Het `displayText` veld bevat de term die de spil in de oorspronkelijke query vervangt. Een voorbeeld is releasedatum van surface.

Als de draaiquerytekenreeks is wat de gebruiker `text` `thumbnail` zoekt, gebruikt u de velden en de velden om de draaiquerytekenreeksen weer te geven. Maak de miniatuur en tekst `webSearchUrl` klikbaar `searchLink` met behulp van de URL of de URL. De `webSearchUrl` gebruiker naar de zoekresultaten van Bing sturen. Als u uw eigen resultatenpagina opgeeft, gebruikt u `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>De query uitvouwen

Als Bing de query kan uitbreiden om de oorspronkelijke zoekopdracht te beperken, bevat het object [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) het veld `queryExpansions`. Als de query bijvoorbeeld *Microsoft Surface*was, kunnen de uitgebreide query's het als:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

In het volgende voorbeeld ziet u de uitgebreide query's voor *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Het veld `queryExpansions` bevat een lijst met [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objecten. Het `text` veld bevat de uitgebreide query. Het `displayText` veld bevat de uitbreidingsterm. Als de uitgebreide querytekenreeks is wat de gebruiker `text` `thumbnail` zoekt, gebruikt u de velden en velden om de uitgebreide querytekenreeksen weer te geven. Maak de miniatuur en tekst `webSearchUrl` klikbaar `searchLink` met behulp van de URL of de URL. De `webSearchUrl` gebruiker naar de zoekresultaten van Bing sturen. Als u uw eigen resultatenpagina opgeeft, gebruikt u `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Aanvraagbeperkingen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Volgende stappen

Als u de Bing Image Search API nog niet eerder hebt geprobeerd, probeert u een [snelle start](../quickstarts/csharp.md). Als u op zoek bent naar iets complexers, probeert u de zelfstudie om een [web-app met één pagina](../tutorial-bing-image-search-single-page-app.md)te maken.
