---
title: Taalondersteuning - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen, landen en regio's die worden ondersteund door de Bing News Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882675"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Ondersteuning voor taal en regio voor de Bing Web Search API

De Bing Web Search API ondersteunt meer dan drie dozijn landen of regio's, waarvan er vele met meer dan één taal. Als u een land of regio opgeeft met een query, u zoekresultaten verfijnen op basis van de interesses van dat land of dat gebied. De resultaten kunnen koppelingen naar Bing bevatten en deze koppelingen kunnen de gebruikerservaring van Bing lokaliseren op basis van het opgegeven land/regio of taal.

U een land of `cc` gebied opgeven met behulp van de queryparameter. Wanneer een land of regio is opgegeven, moet u een of meer taalcodes opgeven met de [ `Accept-Language` koptekst](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Gebruik de [tabel Markten](#markets) voor een lijst met talen die in elke markt worden ondersteund.

U ook de markt `mkt` opgeven met de queryparameter en een code uit de tabel **Markten.** Als u een markt opgeeft, geeft u tegelijkertijd een land of regio en een voorkeurstaal op. U de taal expliciet `setLang` instellen met de queryparameter.

## <a name="countriesregions"></a>Landen/regio's

|Land/regio|Code|
|-------|----|
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
|China|CN|
|Polen|PL|
|Portugal|PT|
|Filipijnen|PH|
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

## <a name="markets"></a>Markten

|Land/regio|Taal|Marktcode|
|-------|--------|-----------|
|Argentinië|Spaans|es-AR|
|Australië|Engels|nl-AU|
|Oostenrijk|Duits|de-AT|
|België|Nederlands|nl-BE|
|België|Frans|fr-BE|
|Brazilië|Portugees|pt-BR|
|Canada|Engels|nl-CA|
|Canada|Frans|fr-CA|
|Chili|Spaans|es-CL|
|Denemarken|Deens|da-DK|
|Finland|Fins|fi-FI|
|Frankrijk|Frans|fr-FR|
|Duitsland|Duits|de-DE|
|Hongkong SAR|Traditioneel Chinees|zh-HK|
|India|Engels|en-IN|
|Indonesië|Engels|en-ID|
|Italië|Italiaans|it-IT|
|Japan|Japans|ja-JP|
|Korea|Koreaans|ko-KR|
|Maleisië|Engels|en-MY|
|Mexico|Spaans|es-MX|
|Nederland|Nederlands|nl-NL|
|Nieuw-Zeeland|Engels|nl-NZ|
|Noorwegen|Norwegian|No-no|
|China|Chinees|zh-CN|
|Polen|Pools|pl-PL|
|Portugal|Portugees|pt-PT|
|Filipijnen|Engels|en-PH|
|Rusland|Russisch|ru-RU|
|Saoedi-Arabië|Arabisch|ar-SA|
|Zuid-Afrika|Engels|nl-ZA|
|Spanje|Spaans|es-ES|
|Zweden|Zweeds|sv-SE|
|Zwitserland|Frans|fr-CH|
|Zwitserland|Duits|de-CH|
|Taiwan|Traditioneel Chinees|zh-TW|
|Turkije|Turks|tr-TR|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|nl-NL|
|Verenigde Staten|Spaans|es-VS|

## <a name="next-steps"></a>Volgende stappen

* [Naslag voor Bing Afbeeldingen zoeken-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
