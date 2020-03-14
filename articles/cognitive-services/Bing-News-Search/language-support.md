---
title: Taal ondersteuning-Bing Nieuws zoeken-API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen, landen en regio's die worden ondersteund door de Bing Nieuws zoeken-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220191"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Ondersteuning van talen en regio's voor de Bing Nieuws zoeken-API

Het Bing Nieuws zoeken-API ondersteunt talrijke landen/regio's, veel met meer dan één taal. Het opgeven van een land/regio met een query is voornamelijk bedoeld voor het verfijnen van de zoek resultaten op basis van interesses in dat land/deze regio. Daarnaast kunnen de resultaten koppelingen naar Bing bevatten en kunnen deze koppelingen de Bing-gebruikers ervaring lokaliseren volgens het opgegeven land/regio of taal.

U kunt een land/regio opgeven met behulp van de `cc` query parameter. Als u een land/regio opgeeft, moet u ook een of meer taal codes opgeven met behulp van de `Accept-Language` HTTP-header. De ondersteunde talen variëren per teleenheid/regio. ze worden vermeld voor elk land/elke regio in de tabel markten.

U kunt ook de markt opgeven met behulp van de `mkt` query parameter en een code uit de tabel **markten** . Als u een markt opgeeft, geeft u een land/regio en een voorkeurs taal op. In dit geval kan de query parameter `setLang` worden ingesteld op een taal code. doorgaans is dit dezelfde taal die wordt opgegeven door `mkt` tenzij de gebruiker de voor keur geeft om Bing in een andere taal te zien.

## <a name="supported-markets-for-news-search-endpoint"></a>Ondersteunde markten voor het zoeken naar nieuws-eind punten

Voor het `/news/search`-eind punt bevat de volgende tabel de waarden van de markt code die u kunt gebruiken om de `mkt` query-para meter op te geven. Bing retourneert inhoud alleen voor deze markten. De lijst is onderhevig aan wijzigingen.  

Zie [land codes](#countrycodes)voor een lijst met land-/regiocodes die u kunt opgeven in de `cc` query parameter.  

|Land/regio|Taal|Markt code|  
|---------------------|--------------|-----------------|
|Denemarken|Deens|da-DK|
|Oostenrijk|Duits|de-AT|
|Zwitserland|Duits|de CH|
|Duitsland|Duits|de-DE|
|Australië|Nederlands|en-AU|
|Canada|Nederlands|NL-CA|
|Verenigd Koninkrijk|Nederlands|en-GB|
|Indonesië|Nederlands|en-ID|
|Ierland|Nederlands|NL-Internet Explorer|
|India|Nederlands|NL-IN|
|Maleisië|Nederlands|en-mijn|
|Nieuw-Zeeland|Nederlands|NL-NZ|
|Republiek der Filippijnen|Nederlands|en-PH|
|Singapore|Nederlands|en-SG|
|Verenigde Staten|Nederlands|nl-NL|
|Nederlands|Algemene|en-WW|
|Nederlands|Algemene|en-XA|
|Zuid-Afrika|Nederlands|en-ZA|
|Argentinië|Spaans|es-AR|
|Chili|Spaans|es-CL|
|Spanje|Spaans|es-ES|
|Mexico|Spaans|es-MX|
|Verenigde Staten|Spaans|es-US|
|Spaans|Algemene|es-XL|
|Finland|Fins|fi-FI|  
|Frankrijk|Frans|fr-worden|
|Canada|Frans|fr-CA|
|België|Nederlands|nl-BE|
|Zwitserland|Frans|FR-h|
|Frankrijk|Frans|fr-FR|  
|Italië|Italiaans|IT-IT|
|Hongkong SAR|Traditioneel Chinees|zh-HK|  
|Taiwan|Traditioneel Chinees|zh-TW|
|Japan|Japans|ja-JP|  
|Korea|Koreaans|ko-KR|  
|Nederland|Nederlands|NL-NL|  
|Volksrepubliek China|Chinees|zh-CN|  
|Brazilië|Portugees|pt-BR|
|Rusland|Russisch|ru-RU|  
|Zweden|Zweeds|SV-SE|  
|Turkije|Turks|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Ondersteunde markten voor nieuws eindpunt
Voor het `/news`-eind punt bevat de volgende tabel de waarden van de markt code die u kunt gebruiken om de `mkt` query-para meter op te geven. Bing retourneert inhoud alleen voor deze markten. De lijst is onderhevig aan wijzigingen.  

Zie [land codes](#countrycodes)voor een lijst met land-/regiocodes die u kunt opgeven in de `cc` query parameter.  

|Land/regio|Taal|Markt code|  
|---------------------|--------------|-----------------|
|Denemarken|Deens|da-DK|
|Duitsland|Duits|de-DE|
|Australië|Nederlands|en-AU|
|Verenigd Koninkrijk|Nederlands|en-GB|
|Verenigde Staten|Nederlands|nl-NL|
|Nederlands|Algemene|en-WW|
|Chili|Spaans|es-CL|
|Mexico|Spaans|es-MX|
|Verenigde Staten|Spaans|es-US|
|Finland|Fins|fi-FI|  
|Canada|Frans|fr-CA|
|Frankrijk|Frans|fr-FR|  
|Italië|Italiaans|IT-IT|
|Brazilië|Portugees|pt-BR|
|Volksrepubliek China|Chinees|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Ondersteunde markten voor het verloop van nieuws-eind punten
Voor het `/news/trendingtopics`-eind punt bevat de volgende tabel de waarden van de markt code die u kunt gebruiken om de `mkt` query-para meter op te geven. Bing retourneert inhoud alleen voor deze markten. De lijst is onderhevig aan wijzigingen.  

Zie [land codes](#countrycodes)voor een lijst met land-/regiocodes die u kunt opgeven in de `cc` query parameter.  

|Land/regio|Taal|Markt code|  
|---------------------|--------------|-----------------|
|Duitsland|Duits|de-DE|
|Australië|Nederlands|en-AU|
|Verenigd Koninkrijk|Nederlands|en-GB|
|Verenigde Staten|Nederlands|nl-NL|
|Canada|Nederlands|NL-CA|
|India|Nederlands|NL-IN|
|Frankrijk|Frans|fr-FR|
|Canada|Frans|fr-CA|
|Brazilië|Portugees|pt-BR|
|Volksrepubliek China|Chinees|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Land codes  

Hier volgen de land/regio codes die u kunt opgeven in de `cc` query parameter. De lijst is onderhevig aan wijzigingen.  

|Land/regio|Landcode|  
|---------------------|------------------|  
|Argentinië|AR|  
|Australië|AU|  
|Oostenrijk|AT|  
|België|BE|  
|Brazilië|BR|  
|Canada|CA|  
|Chili|CL|  
|Denemarken|DK|  
|Finland|FI|  
|Frankrijk|FR|  
|Duitsland|DE|  
|Hongkong SAR|HK|  
|India|IN|  
|Indonesië|Id|  
|Italië|IT|  
|Japan|JP|  
|Korea|KR|  
|Maleisië|MY|  
|Mexico|MX|  
|Nederland|NL|  
|Nieuw-Zeeland|NZ|  
|Noorwegen|NO|  
|Volksrepubliek China|CN|  
|Polen|PL|  
|Portugal|PT|  
|Republiek der Filippijnen|PH|  
|Rusland|RU|  
|Saoedi-Arabië|SA|  
|Zuid-Afrika|ZA|  
|Spanje|ES|  
|Zweden|SE|  
|Zwitserland|CH|  
|Taiwan|TW|  
|Turkije|TR|  
|Verenigd Koninkrijk|GB|  
|Verenigde Staten|VS|

## <a name="next-steps"></a>Volgende stappen
Zie [Nieuws zoeken API V7-referentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)voor meer informatie over de Bing News Search-eind punten.
