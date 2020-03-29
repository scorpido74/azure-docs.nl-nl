---
title: Taalondersteuning - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Ontdek welke landen/regio's en talen worden ondersteund door de Bing Image Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881923"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Ondersteuning voor taal en regio voor de Bing Image Search API

De Bing Image Search API ondersteunt meer dan drie dozijn landen/regio's, waarvan er vele met meer dan één taal. Het opgeven van een land/regio met een query dient voornamelijk om zoekresultaten te verfijnen op basis van interesses in dat land/regio. Bovendien kunnen de resultaten koppelingen naar Bing bevatten en deze koppelingen kunnen de gebruikerservaring van Bing lokaliseren op basis van het opgegeven land/regio's of taal.

Als u het land/de regio `mkt` en de taal wilt opgeven, stelt u de parameter (markt)query in op een code uit de **onderstaande tabel Markten.** De markt specificeert zowel een land/regio als taal. Als de gebruiker liever weergavetekst in een `setLang` andere taal ziet, stelt u de queryparameter in op de juiste taalcode.

U ook het land/de `cc` regio opgeven met behulp van de queryparameter. Als u een land/regio opgeeft, moet u ook `Accept-Language` een of meer taalcodes opgeven met de HTTP-header. De ondersteunde talen verschillen per land/regio; ze worden voor elk land/regio in de tabel Markten vermeld.

> [!NOTE]
> De TRENDING Images API ondersteunt momenteel alleen de volgende markten:
> - nl-VS (Engels, Verenigde Staten)
> - en-CA (Engels, Canada)
> - en-AU (Engels, Australië)
> - zh-CN (Chinees, China)

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
Zie Nieuws [image search API v7 referentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)voor meer informatie over de eindpunten van Bing News Search.
