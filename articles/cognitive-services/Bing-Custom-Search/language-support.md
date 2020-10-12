---
title: Taal ondersteuning-Bing Custom Search-API
titleSuffix: Azure Cognitive Services
description: Een lijst met ondersteunde talen en regio's voor de Bing Custom Search-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 004bd973651d5903db4254a8883be2c8a83d9b38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84310577"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Ondersteuning van talen en regio's voor de Bing Custom Search-API

Het Bing Custom Search-API ondersteunt meer dan drie dozijn landen/regio's, veel met meer dan één taal.

Hoewel dit optioneel is, moet de aanvraag de para meter [MKT](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) query opgeven, waarmee de markt wordt geïdentificeerd waarvan u wilt dat de resultaten afkomstig zijn. Zie [query parameters](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) voor een lijst met optionele query parameters

U kunt een land/regio opgeven met behulp van de `cc` query parameter. Als u een land/regio opgeeft, moet u ook een of meer taal codes opgeven met behulp van de `Accept-Language` header. De ondersteunde talen variëren per land/regio. ze worden vermeld voor elk land/elke regio in de tabel **markten** .

De `Accept-Language` header en de `setLang` query parameter sluiten elkaar wederzijds uit. Geef beide niet op. Zie [accepteren-taal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)voor meer informatie.

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
|Canada|Frans|fr-CA|
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
|Noorwegen|Noors|Nee-Nee|
|China|Chinees|zh-CN|
|Polen|Pools|pl-PL|
|Portugal|Portugees|pt-PT|
|Filipijnen|Engels|en-PH|
|Rusland|Russisch|ru-RU|
|Saoedi-Arabië|Arabisch|ar-SA|
|Zuid-Afrika|Engels|en-ZA|
|Spanje|Spaans|es-ES|
|Zweden|Zweeds|sv-SE|
|Zwitserland|Frans|fr-CH|
|Zwitserland|Duits|de-CH|
|Taiwan|Traditioneel Chinees|zh-TW|
|Turkije|Turks|tr-TR|
|Verenigd Koninkrijk|Engels|en-GB|
|Verenigde Staten|Engels|nl-NL|
|Verenigde Staten|Spaans|es-Verenigde Staten|
