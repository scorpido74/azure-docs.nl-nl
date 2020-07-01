---
title: Wat is de Bing Entiteiten zoeken-API?
titleSuffix: Azure Cognitive Services
description: Gebruik de Bing Entiteiten zoeken-API om te zoeken naar entiteiten en locaties en om deze te extraheren uit zoekquery's.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 4afb20699d52ea80d7ba6cf9760a465b61cc6d6b
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85604808"
---
# <a name="what-is-bing-entity-search-api"></a>Wat is de Bing Entiteiten zoeken-API?

De Bing Entiteiten zoeken-API verzendt een zoekquery naar Bing en haalt resultaten op die zowel entiteiten als plaatsen bevatten. Plaatsresultaten kunnen restaurants, hotels of andere lokale bedrijven zijn. Bing retourneert plaatsen als in de query de naam van een lokaal bedrijf is opgegeven, of als er om een type bedrijf wordt gevraagd (bijvoorbeeld restaurants in de buurt). Bing retourneert entiteiten als de query bekende mensen, plaatsen (toeristische attractions, Staten, landen/regio's enz.) of dingen aangeeft.

|Functie  |Beschrijving  |
|---------|---------|
|[Zoeksuggesties in realtime](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Geef zoeksuggesties op die kunnen worden weergegeven als een vervolgkeuzelijst wanneer gebruikers beginnen te typen.       | 
| [Ondubbelzinnig maken van entiteiten](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Meerdere entiteiten opvragen voor query's met meerdere mogelijke betekenissen. |
| [Locaties zoeken](concepts/search-for-entities.md#find-places) | Zoeken naar lokale bedrijven en entiteiten en de bijbehorende gegevens retourneren.  |

## <a name="workflow"></a>Werkstroom

De Bing Entiteiten zoeken-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd. U kunt de service gebruiken met de REST API of de SDK.

1. Maak een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met toegang tot de Bing zoeken-API's. Als u geen Azure-abonnement hebt, kunt u gratis [een account maken](https://azure.microsoft.com/free/cognitive-services/) .
2. Verzend een aanvraag naar de API met een geldige zoekquery.
3. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

* Probeer de [interactieve demo](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) van de Bing Entiteiten zoeken-API. 
* Volg een [quickstart](quickstarts/csharp.md) om snel aan de slag te gaan met uw eerste aanvraag.
* De [naslaghandleiding over de Bing Entiteiten zoeken-API versie 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* In de [Bing-vereisten voor gebruik en weergave](./use-display-requirements.md) staan het acceptabele gebruik van de inhoud en informatie die is verkregen via de Bing Zoeken-API's.
* Ga naar de [pagina met Bing Search API-hubs](../bing-web-search/search-the-web.md) om de andere beschik bare api's te verkennen.