---
title: Taalondersteuning - Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen, landen en regio's die worden ondersteund door de Bing Visual Search API. De Bing Visual Search API ondersteunt meer dan drie dozijn landen/regio's, waarvan er vele met meer dan één taal.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883554"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Ondersteuning voor taal en regio voor de Bing Visual Search API

Bing Visual Search API ondersteunt meer dan drie dozijn landen/regio's, waarvan er vele met meer dan één taal. Elke aanvraag moet het land/de regio en de taal van de gebruiker bevatten. Het kennen van de markt van de gebruiker helpt Bing om de juiste resultaten te retourneren. Als u geen land/regio en taal opgeeft, doet Bing er alles aan om het land/de regio en de taal van de gebruiker te bepalen. Omdat de resultaten koppelingen naar Bing kunnen bevatten, kan het weten van het land/de regio en de taal een voorkeursgelokaliseerde Bing-gebruikerservaring bieden als de gebruiker op de Bing-koppelingen klikt.

Als u het land/de regio `mkt` en de taal wilt opgeven, stelt u de parameter (markt)query in op een code uit de **onderstaande tabel Markten.** De markt specificeert zowel een land/regio als taal. Als de gebruiker liever weergavetekst in een `setLang` andere taal ziet, stelt u de queryparameter in op de juiste taalcode.

U ook het land/de `cc` regio opgeven met behulp van de queryparameter. Als u een land/regio opgeeft, moet u ook `Accept-Language` een of meer taalcodes opgeven met de HTTP-header. De ondersteunde talen verschillen per land/regio; ze worden gegeven voor elk land in de markten tabel.



> [!NOTE]
> De volgende marktbeperkingen zijn van toepassing:
>
> - Annotaties voor beeldherkenning zijn alleen beschikbaar in het Engels.
> - Recept, winkelen en pagina's inclusief inzichten zijn alleen beschikbaar in de en-VS-markt.


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
