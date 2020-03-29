---
title: Taalondersteuning - Bing News Search API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen, landen en regio's die worden ondersteund door de Bing News Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220191"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Ondersteuning voor taal en regio voor de Bing News Search API

De Bing News Search API ondersteunt tal van landen/regio's, waarvan er vele met meer dan één taal. Het opgeven van een land/regio met een query dient voornamelijk om zoekresultaten te verfijnen op basis van interesses in dat land/regio. Bovendien kunnen de resultaten koppelingen naar Bing bevatten en deze koppelingen kunnen de Gebruikerservaring van Bing lokaliseren op basis van het opgegeven land/regio of taal.

U een land/regio `cc` opgeven met behulp van de queryparameter. Als u een land/regio opgeeft, moet u ook `Accept-Language` een of meer taalcodes opgeven met de HTTP-header. De ondersteunde talen verschillen per telper/regio; ze worden voor elk land/regio in de tabel Markten vermeld.

U ook de markt `mkt` opgeven met behulp van de queryparameter en een code uit de tabel **Markten.** Als u een markt opgeeft, geeft u tegelijkertijd een land/regio en een voorkeurstaal op. De `setLang` queryparameter kan in dit geval worden ingesteld op een taalcode. meestal is dit dezelfde `mkt` taal opgegeven door, tenzij de gebruiker de voorkeur geeft aan Bing te zien in een andere taal.

## <a name="supported-markets-for-news-search-endpoint"></a>Ondersteunde markten voor nieuwszoekeindpunt

Voor `/news/search` het eindpunt worden in de volgende tabel de marktcodewaarden weergegeven die u gebruiken om de `mkt` queryparameter op te geven. Bing retourneert inhoud voor alleen deze markten. De lijst kan worden gewijzigd.  

Zie `cc` [Landcodes](#countrycodes)voor een lijst met land-/regiocodes die u in de queryparameter opgeven.  

|Land/regio|Taal|Marktcode|  
|---------------------|--------------|-----------------|
|Denemarken|Deens|da-DK|
|Oostenrijk|Duits|de-AT|
|Zwitserland|Duits|de-CH|
|Duitsland|Duits|de-DE|
|Australië|Engels|nl-AU|
|Canada|Engels|nl-CA|
|Verenigd Koninkrijk|Engels|en-GB|
|Indonesië|Engels|en-ID|
|Ierland|Engels|en-IE|
|India|Engels|en-IN|
|Maleisië|Engels|en-MY|
|Nieuw-Zeeland|Engels|nl-NZ|
|Republiek der Filippijnen|Engels|en-PH|
|Singapore|Engels|en-SG|
|Verenigde Staten|Engels|nl-NL|
|Engels|algemeen|nl-WW|
|Engels|algemeen|en-XA|
|Zuid-Afrika|Engels|nl-ZA|
|Argentinië|Spaans|es-AR|
|Chili|Spaans|es-CL|
|Spanje|Spaans|es-ES|
|Mexico|Spaans|es-MX|
|Verenigde Staten|Spaans|es-VS|
|Spaans|algemeen|es-XL|
|Finland|Fins|fi-FI|  
|Frankrijk|Frans|fr-BE|
|Canada|Frans|fr-CA|
|België|Nederlands|nl-BE|
|Zwitserland|Frans|fr-CH|
|Frankrijk|Frans|fr-FR|  
|Italië|Italiaans|it-IT|
|Hongkong SAR|Traditioneel Chinees|zh-HK|  
|Taiwan|Traditioneel Chinees|zh-TW|
|Japan|Japans|ja-JP|  
|Korea|Koreaans|ko-KR|  
|Nederland|Nederlands|nl-NL|  
|Volksrepubliek China|Chinees|zh-CN|  
|Brazilië|Portugees|pt-BR|
|Rusland|Russisch|ru-RU|  
|Zweden|Zweeds|sv-SE|  
|Turkije|Turks|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Ondersteunde markten voor nieuwseindpunt
Voor `/news` het eindpunt worden in de volgende tabel de marktcodewaarden weergegeven die u gebruiken om de `mkt` queryparameter op te geven. Bing retourneert inhoud voor alleen deze markten. De lijst kan worden gewijzigd.  

Zie `cc` [Landcodes](#countrycodes)voor een lijst met land-/regiocodes die u in de queryparameter opgeven.  

|Land/regio|Taal|Marktcode|  
|---------------------|--------------|-----------------|
|Denemarken|Deens|da-DK|
|Duitsland|Duits|de-DE|
|Australië|Engels|nl-AU|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|nl-NL|
|Engels|algemeen|nl-WW|
|Chili|Spaans|es-CL|
|Mexico|Spaans|es-MX|
|Verenigde Staten|Spaans|es-VS|
|Finland|Fins|fi-FI|  
|Canada|Frans|fr-CA|
|Frankrijk|Frans|fr-FR|  
|Italië|Italiaans|it-IT|
|Brazilië|Portugees|pt-BR|
|Volksrepubliek China|Chinees|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Ondersteunde markten voor nieuwstrending endpoint
Voor `/news/trendingtopics` het eindpunt worden in de volgende tabel de marktcodewaarden weergegeven die u gebruiken om de `mkt` queryparameter op te geven. Bing retourneert inhoud voor alleen deze markten. De lijst kan worden gewijzigd.  

Zie `cc` [Landcodes](#countrycodes)voor een lijst met land-/regiocodes die u in de queryparameter opgeven.  

|Land/regio|Taal|Marktcode|  
|---------------------|--------------|-----------------|
|Duitsland|Duits|de-DE|
|Australië|Engels|nl-AU|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|nl-NL|
|Canada|Engels|nl-CA|
|India|Engels|en-IN|
|Frankrijk|Frans|fr-FR|
|Canada|Frans|fr-CA|
|Brazilië|Portugees|pt-BR|
|Volksrepubliek China|Chinees|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Landcodes  

Hieronder volgen de land-/regiocodes die `cc` u in de queryparameter opgeven. De lijst kan worden gewijzigd.  

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
Zie Nieuws [zoekAPI v7-verwijzing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)voor meer informatie over de eindpunten van Bing News Search.
