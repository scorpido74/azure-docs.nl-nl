---
title: Taalondersteuning - Bing Custom Search API
titleSuffix: Azure Cognitive Services
description: Een lijst met ondersteunde talen en regio's voor de Bing Custom Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66388566"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Ondersteuning voor taal en regio voor de Bing Custom Search API

De Bing Custom Search API ondersteunt meer dan drie dozijn landen/regio's, waarvan er vele met meer dan één taal.

Hoewel de aanvraag optioneel is, moet de queryparameter [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) worden opgegeven, waarmee de markt wordt aangegeven waar u de resultaten vandaan wilt halen. Zie [Queryparameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) voor een lijst met optionele queryparameters

U een land/regio `cc` opgeven met behulp van de queryparameter. Als u een land/regio opgeeft, moet u ook `Accept-Language` een of meer taalcodes opgeven met de koptekst. De ondersteunde talen verschillen per land/regio; ze worden voor elk land/regio in de **tabel Markten** vermeld.

De `Accept-Language` koptekst `setLang` en de queryparameter sluiten elkaar uit en geven beide niet op. Zie [Accepteren-taal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)voor meer informatie.

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
|Hongkong, SAR|Traditioneel Chinees|zh-HK|
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
