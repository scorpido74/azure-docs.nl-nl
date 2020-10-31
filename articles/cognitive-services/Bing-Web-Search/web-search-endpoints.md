---
title: Eind punt voor zoeken op Internet
titleSuffix: Azure Cognitive Services
description: Als u zoek resultaten voor het web wilt ophalen, verzendt `GET` u een aanvraag naar het volgende eind punt. De para meters headers en URL definiëren verdere specificaties.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 25ecd1a753cb9a401408f7ed6605d53e5310df2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075667"
---
# <a name="web-search-endpoint"></a>Webzoekopdrachten-eind punt

> [!WARNING]
> Bing Zoeken-API's van Cognitive Services naar Bing Search-Services verplaatsen. Vanaf **30 oktober 2020** moeten nieuwe exemplaren van Bing Search worden ingericht volgens het proces dat [hier](https://aka.ms/cogsvcs/bingmove)wordt beschreven.
> Bing Zoeken-API's ingericht met Cognitive Services wordt voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst gebeurt.
> Zie [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)voor migratie-instructies.

De **webzoekopdrachten-API** retourneert webpagina's, nieuws, afbeeldingen, Video's en [entiteiten](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Entiteiten hebben samenvattende informatie over een persoon, plaats of onderwerp.

## <a name="endpoint"></a>Eindpunt

Als u zoek resultaten wilt ophalen met behulp van de Bing API, verzendt `GET` u een aanvraag naar het volgende eind punt. De para meters headers en URL definiëren verdere specificaties.

**Eind punt** : retourneert Webresultaten die relevant zijn voor de zoek query van de gebruiker die is gedefinieerd door `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Eind punt: Zie de naslag informatie voor [Bing Web API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) voor meer informatie over kopteksten, para meters, markt codes, reactie objecten, fouten en meer.

## <a name="response-json"></a>JSON van antwoord

Het antwoord op een aanvraag voor zoeken op Internet bevat alle resultaten als JSON-objecten. Voor het parseren van het resultaat zijn procedures vereist die de elementen van elk type verwerken. Raadpleeg de [zelf studie](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) en [bron code](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) voor voor beelden.

## <a name="next-steps"></a>Volgende stappen

De **Bing** api's ondersteunen zoek acties die resultaten retour neren op basis van hun type. Alle zoek eindpunten retour neren resultaten als JSON-antwoord objecten.  Alle eind punten ondersteunen query's waarmee een specifieke taal en locatie worden geretourneerd door de lengte graad, de breedte graad en de RADIUS van de zoek opdracht.

Zie de naslag pagina's voor elk type voor volledige informatie over de para meters die door elk eind punt worden ondersteund.
Zie voor voor beelden van basis aanvragen met de Web Search-API [zoeken op Internet snel starten](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
