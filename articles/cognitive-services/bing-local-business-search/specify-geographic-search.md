---
title: Geografische grenzen gebruiken om resultaten uit de Bing Local Business Search API te filteren
titleSuffix: Azure Cognitive Services
description: Gebruik dit artikel voor meer informatie over het filteren van zoekresultaten uit de Bing Local Business Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 213457bc583494bbe039269b96b25990f7d0a961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69906250"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>Geografische grenzen gebruiken om resultaten uit de Bing Local Business Search API te filteren

Met de Bing Local Business Search API u grenzen instellen voor het `localCircularView` `localMapView` specifieke geografische gebied dat u wilt zoeken met behulp van de queryparameters of queryparameters. Zorg ervoor dat u slechts één parameter in uw query's gebruikt. 

Als een zoekterm een expliciete geografische locatie bevat, gebruikt de Bing Local Business API deze automatisch om grenzen voor de zoekresultaten in te stellen. Als de zoekterm bijvoorbeeld `sailing in San Diego`is, wordt deze `San Diego` gebruikt als locatie en worden alle andere opgegeven locaties in de queryparameters of gebruikerskoppen genegeerd. 

Als een geografische locatie niet wordt gedetecteerd in de zoekterm en er geen geografische locatie is opgegeven met behulp van `X-Search-ClientIP` de `X-Search-Location` queryparameters, probeert de Bing Local Business Search API de locatie te bepalen op basis van de query of kopteksten. Als geen van beide headers is opgegeven, bepaalt de API de locatie van het client-IP van de aanvraag of GPS-coördinaten voor mobiele apparaten.

## <a name="localcircularview"></a>localCircularView (localCircularView)

De `localCircularView` parameter creëert een cirkelvormig geografisch gebied rond een reeks breedte-/lengtecoördinaten, gedefinieerd door een straal. Bij het gebruik van deze parameter bevatten antwoorden van de Bing Local `localMapView` Business Search API alleen locaties binnen deze cirkel, in tegenstelling tot de parameter die locaties enigszins buiten het zoekgebied kan bevatten.

Als u een cirkelvormig geografisch zoekgebied wilt opgeven, kiest u een breedte- en lengtegraad om te dienen als het midden van de cirkel en een straal in meters. Deze parameter kan vervolgens worden toegevoegd aan een `q=Restaurants&localCircularView=47.6421,-122.13715,5000`querytekenreeks, bijvoorbeeld: .

Volledige query:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

De `localMapView` parameter geeft een rechthoekig geografisch gebied aan dat moet worden doorzocht, met behulp van twee reeksen coördinaten om de hoeken in het zuidoosten en noordwesten op te geven. Bij het gebruik van deze parameter kunnen antwoorden van de Bing Local Business Search `localCircularView` API locaties binnen en net buiten het opgegeven gebied bevatten, in tegenstelling tot de parameter, die alleen locaties binnen het zoekgebied omvat.

Als u een rechthoekig zoekgebied wilt opgeven, kiest u twee reeksen breedte-/lengtecoördinaten om te dienen als de hoeken van het zuidoosten en noordwesten van de grens. Zorg ervoor dat u eerst de zuidoostcoördinaten `localMapView=47.619987,-122.181671,47.6421,-122.13715`definieert, zoals in het volgende voorbeeld: .

Volledige query:

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>Volgende stappen
- [Lokaal zakelijk zoeken Java Quickstart](quickstarts/local-search-java-quickstart.md)
- [Lokaal zakelijk zoeken C# Snelstart](quickstarts/local-quickstart.md)
- [Quickstart voor lokaal zakelijk zoeken](quickstarts/local-search-node-quickstart.md)
- [Lokaal zoeken in Business Python snel gestart](quickstarts/local-search-python-quickstart.md)
