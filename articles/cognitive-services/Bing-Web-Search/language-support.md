---
title: Taal ondersteuning-Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Een lijst met natuurlijke talen, landen en regio's die worden ondersteund door de Bing Nieuws zoeken-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882675"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Ondersteuning van talen en regio's voor de Bing Webzoekopdrachten-API

Het Bing Webzoekopdrachten-API ondersteunt meer dan drie dozijn landen of regio's, veel met meer dan één taal. Het opgeven van een land of regio met een query helpt de zoek resultaten te verfijnen op basis van de interesses van die landen of regio's. De resultaten kunnen koppelingen naar Bing bevatten en deze koppelingen kunnen de Bing-gebruikers ervaring lokaliseren op basis van de opgegeven land/regio of taal.

U kunt een land of regio opgeven met behulp van de `cc` query parameter. Wanneer u een land of regio hebt opgegeven, moet u een of meer taal codes met de [ `Accept-Language` header](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)opgeven. Gebruik de [tabel Markets](#markets) voor een lijst met talen die op elke markt worden ondersteund.

U kunt ook de-markt met de `mkt` query parameter en een code uit de tabel **Markets** opgeven. Als u een markt opgeeft, geeft u een land of regio en een voorkeurs taal op. U kunt de taal expliciet instellen met de `setLang` query parameter.

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

## <a name="markets"></a>Landen

|Land/regio|Taal|Markt code|
|-------|--------|-----------|
|Argentinië|Spaans|es-AR|
|Australië|Engels|en-AU|
|Oostenrijk|Duits|de-AT|
|België|Nederlands|nl-worden|
|België|Frans|fr-worden|
|Brazilië|Portugees|pt-BR|
|Canada|Engels|en-CA|
|Canada|Frans|FR-CA|
|Chili|Spaans|es-LC|
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
|Maleisië|Engels|en-mijn|
|Mexico|Spaans|es-MX|
|Nederland|Nederlands|nl-NL|
|Nieuw-Zeeland|Engels|en-NZ|
|Noorwegen|Norwegian|Nee-Nee|
|China|Chinees|zh-CN|
|Polen|Pools|pl-PL|
|Portugal|Portugees|pt-PT|
|Filipijnen|Engels|en-PH|
|Rusland|Russisch|ru-RU|
|Saoedi-Arabië|Arabisch|ar-SA|
|Zuid-Afrika|Engels|en-ZA|
|Spanje|Spaans|es-ES|
|Zweden|Zweeds|sv-SE|
|Zwitserland|Frans|FR-CH|
|Zwitserland|Duits|Dech|
|Taiwan|Traditioneel Chinees|zh-TW|
|Turkije|Turks|tr-TR|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|nl-NL|
|Verenigde Staten|Spaans|es-Verenigde Staten|

## <a name="next-steps"></a>Volgende stappen

* [Naslag voor Bing Afbeeldingen zoeken-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
