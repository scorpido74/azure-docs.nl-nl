---
title: Wat is de Bing Video's zoeken-API?
titleSuffix: Azure Cognitive Services
description: Hier vindt u informatie over het zoeken naar video's op internet, met behulp van de Bing Video's zoeken-API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d5a4c3110186329516f10465c5e80a10b0ceb7b7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379817"
---
# <a name="what-is-the-bing-video-search-api"></a>Wat is de Bing Video's zoeken-API?

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met de Bing Video's zoeken-API kunt u eenvoudig mogelijkheden voor het zoeken van video's toevoegen aan uw services en toepassingen. Door zoekquery's van gebruikers te verzenden met de API kunt u relevante en hoogwaardige video's ophalen en weergeven die vergelijkbaar zijn met [Bing Video's](https://www.bing.com/video). Gebruik deze API voor zoekresultaten die alleen video's bevatten. De [Bing Webzoekopdrachten-API](../bing-web-search/overview.md) kan andere typen webinhoud retourneren, waaronder webpagina's, video's, nieuws en afbeeldingen.

## <a name="bing-video-search-api-features"></a>Functies van Bing Video's zoeken-API

| Functie                                                                                                                                                                                 | Beschrijving                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Zoektermen in realtime voorstellen](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Verbeter uw app-ervaring met de [Bing Automatische suggestie-API](../bing-autosuggest/get-suggested-search-terms.md) om voorgestelde zoektermen weer te geven wanneer deze worden getypt. |
| [Videoresultaten filteren en beperken](concepts/get-videos.md#filtering-videos)                      | Filter de video's die worden geretourneerd door queryparameters te bewerken.                                                                                                       |
| [Miniaturen bijsnijden en weergeven en het formaat hiervan wijzigen](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Bewerk miniatuurweergaven voor de video's die zijn geretourneerd door de Bing Video's zoeken-API en geef deze weer.                                                                                      |
| [Trending video's ophalen](trending-videos.md) | Zoek naar trending video's van over de hele wereld.                                                                                                          |
| [Meer inzicht krijgen dankzij video](video-insights.md) | Pas een zoekopdracht voor trending video's van over de hele wereld aan.                                                                                                          |

## <a name="workflow"></a>Werkstroom

De Bing Video's zoeken-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de service gebruiken met de [REST API](./quickstarts/csharp.md) of de [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Maak een [Account voor Cognitive Services-API](../cognitive-services-apis-create-account.md) met toegang tot de Bing Zoeken-API's. Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/).
2. Verzend een aanvraag naar de API met een geldige zoekquery.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.


## <a name="next-steps"></a>Volgende stappen

De [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) over de Bing Video's zoeken-API laat zien hoe u een zoekquery kunt aanpassen en op internet kunt zoeken naar video's.

Gebruik de [quickstart](./quickstarts/csharp.md) om snel uw eerste API-aanvraag te maken.

## <a name="see-also"></a>Zie tevens

* De naslaghandleiding over de [Bing Video's zoeken-API versie 7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) bevat de lijst met eindpunten, headers en queryparameters die u nodig hebt om zoekresultaten op te vragen.

* In de [Bing-vereisten voor gebruik en weergave](../bing-web-search/use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.

* Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/overview.md) om de andere beschikbare API's te verkennen.