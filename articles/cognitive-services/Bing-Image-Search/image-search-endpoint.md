---
title: Eind punten voor de Bing Afbeeldingen zoeken-API
titleSuffix: Azure Cognitive Services
description: De Afbeeldingen zoeken-API bevat drie eind punten. Eind punt 1 retourneert installatie kopieën van het web. Eind punt 2 retourneert ImageInsights. Eind punt 3 retourneert trend afbeeldingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 4ed48a46a01cee5a7a981d4e863f981010b44112
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084252"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Eind punten voor de Bing Afbeeldingen zoeken-API

> [!WARNING]
> Bing Zoeken-API's van Cognitive Services naar Bing Search-Services verplaatsen. Vanaf **30 oktober 2020** moeten nieuwe exemplaren van Bing Search worden ingericht volgens het proces dat [hier](https://aka.ms/cogsvcs/bingmove)wordt beschreven.
> Bing Zoeken-API's ingericht met Cognitive Services wordt voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst gebeurt.
> Zie [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)voor migratie-instructies.

De **afbeeldingen zoeken-API**  bevat drie eind punten.  Eind punt 1 retourneert installatie kopieën van het web op basis van een query. Eind punt 2 retourneert [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  Eind punt 3 retourneert trend afbeeldingen.

## <a name="endpoints"></a>Eindpunten

Als u afbeeldings resultaten wilt ophalen met behulp van de Bing API, verzendt u een aanvraag naar een van de volgende eind punten. Gebruik de para meters headers en URL om verdere specificaties te definiëren.

**Eind punt 1:** Hiermee worden installatie kopieën geretourneerd die relevant zijn voor de zoek query van de gebruiker die is gedefinieerd door `?q=""` .
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Eind punt 2:** Retourneert inzichten over een installatie kopie met behulp van `GET` of `POST` .
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Een GET-aanvraag retourneert inzichten over een afbeelding, zoals webpagina's die de afbeelding bevatten. Neem de para meter [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) op met een `GET` aanvraag.

U kunt ook een binaire installatie kopie in de hoofd tekst van een `POST` aanvraag toevoegen en de para meter [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) instellen op `RecognizedEntities` . Hiermee wordt een [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) geretourneerd dat als para meter wordt gebruikt in een volgende `GET` aanvraag, die informatie over personen in de installatie kopie retourneert.  Stel deze waarde in `modules` `All` op om alle inzichten op te halen, behalve `RecognizedEntities` in de resultaten van de `POST` zonder een andere aanroep te gebruiken `insightsToken` .


**Eind punt 3:** Hiermee worden afbeeldingen geretourneerd die worden getrendd op basis van zoek opdrachten die door anderen zijn gemaakt. De afbeeldingen worden onderverdeeld in verschillende categorieën, bijvoorbeeld op basis van de vervelende personen of gebeurtenissen.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Zie [trending afbeeldingen](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)voor een lijst met markten die trending afbeeldingen ondersteunen.

Zie de naslag informatie over [Bing afbeeldingen zoeken-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) voor meer informatie over kopteksten, para meters, markt codes, reactie objecten, fouten, enzovoort.
## <a name="response-json"></a>JSON van antwoord
Het antwoord op een aanvraag voor het zoeken naar een afbeelding bevat resultaten als JSON-objecten. Raadpleeg de [zelf studie](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) en de [bron code](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source)voor voor beelden van het parseren van de resultaten.

## <a name="next-steps"></a>Volgende stappen
De **Bing** api's ondersteunen zoek acties die resultaten retour neren op basis van hun type. Alle zoek eindpunten retour neren resultaten als JSON-antwoord objecten.  Alle eind punten ondersteunen query's die een specifieke taal en/of locatie retour neren met de lengte graad, breedte graad en de zoek RADIUS.

Zie de naslag pagina's voor elk type voor volledige informatie over de para meters die door elk eind punt worden ondersteund.
Zie [afbeeldingen zoeken Quick Start (](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web)Engelstalig) voor voor beelden van basis aanvragen met behulp van de afbeelding zoeken-API.
