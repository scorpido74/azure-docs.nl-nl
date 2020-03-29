---
title: Upgrade Bing News Search API v5 naar v7
titleSuffix: Azure Cognitive Services
description: Hiermee worden de onderdelen van uw toepassing geïdentificeerd die u moet bijwerken om versie 7 te kunnen gebruiken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: bad0ef849af7c94e63f1dfbebda7f47caef9947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294370"
---
# <a name="news-search-api-upgrade-guide"></a>Upgradegids voor nieuwszoeken API

Deze upgradehandleiding identificeert de wijzigingen tussen versie 5 en versie 7 van de Bing News Search API. Gebruik deze handleiding om u te helpen de onderdelen van uw toepassing te identificeren die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versienummer van het eindpunt is gewijzigd van v5 naar v7. Bijvoorbeeld `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

### <a name="error-response-objects-and-error-codes"></a>Foutreactieobjecten en foutcodes

- Alle mislukte aanvragen moeten `ErrorResponse` nu een object in de antwoordinstantie bevatten.

- De volgende velden `Error` aan het object toegevoegd.  
  - `subCode`&mdash;Partities van de foutcode in discrete buckets, indien mogelijk
  - `moreDetails`&mdash;Aanvullende informatie over de `message` fout die in het veld is beschreven

- Verving de v5-foutcodes `code` door `subCode` de volgende mogelijke en waarden.

|Code|Subcode|Beschrijving
|-|-|-
|ServerFout|Onverwachts<br/>ResourceFout<br/>Niet geïmplementeerd|Bing retourneert ServerError wanneer een van de subcodevoorwaarden zich voordoet. Het antwoord bevat deze fouten als de HTTP-statuscode 500 is.
|Ongeldig verzoek|ParameterMissing<br/>ParameterOngeldigWaarde<br/>HttpNotAtoegestaan<br/>Geblokkeerd|Bing retourneert Ongeldige aanvraag wanneer een deel van de aanvraag niet geldig is. Een vereiste parameter ontbreekt bijvoorbeeld of een parameterwaarde is ongeldig.<br/><br/>Als de fout ParameterMissing of ParameterInvalidValue is, is de HTTP-status400.<br/><br/>Als de fout httpNota is toegestaan, wordt de HTTP-statuscode 410 weergegeven.
|RateLimitoverschreden||Bing retourneert RateLimitExceeded wanneer u uw query's per seconde (QPS) of query's per maand (QPM) quotum overschrijdt.<br/><br/>Bing retourneert HTTP-statuscode 429 als u QPS en 403 hebt overschreden als u QPM hebt overschreden.
|Ongeldig autorisatie|Autorisatieontbreekt<br/>AutorisatieRedundantie|Bing retourneert InvalidAuthorization wanneer Bing de beller niet kan verifiëren. De `Ocp-Apim-Subscription-Key` koptekst ontbreekt bijvoorbeeld of de abonnementssleutel is ongeldig.<br/><br/>Redundantie treedt op als u meer dan één verificatiemethode opgeeft.<br/><br/>Als de fout Ongeldig autorisatie is, is de HTTP-status401.
|OnvoldoendeAutorisatie|AutorisatieUitgeschakeld<br/>Autorisatie verlopen|Bing retourneert OnvoldoendeAutorisatie wanneer de beller geen machtigingen heeft om toegang te krijgen tot de bron. Dit kan gebeuren als de abonnementssleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout Onvoldoende autorisatie is, is de HTTP-statuscode 403.

- De volgende geeft de vorige foutcodes een overzicht van de nieuwe codes. Als u afhankelijk bent van v5-foutcodes, werkt u uw code dienovereenkomstig bij.

|Versie 5-code|Versie 7 code.subCode
|-|-
|AanvraagParametermissing|InvalidRequest.ParameterMissing
AanvraagParameterInvalidValue|Ongeldige waarde voor ongeldige aanvragen.parameterongeldig
ResourceAccess geweigerd|OnvoldoendeAutorisatie
OverschredenVolume|RateLimitoverschreden
Limiet overschredenQpsLimit|RateLimitoverschreden
Uitgeschakeld|OnvoldoendeAutorisatie.AutorisatieUitgeschakeld
Onverwachts|ServerError.UnexpectedError
Gegevensbronfouten|ServerError.ResourceError
Autorisatieontbreekt|InvalidAuthorization.AuthorizationMissing InvalidAuthorization.AuthorizationMissing InvalidAuthorization.AuthorizationMissing InvalidAuthorization
HttpNotAtoegestaan|InvalidRequest.HttpNotA toegestaan
UserAgentMissing|InvalidRequest.ParameterMissing
Niet geïmplementeerd|ServerFout.Niet geïmplementeerd
Ongeldig autorisatie|Ongeldig autorisatie
Ongeldige autorisatiemethode|Ongeldig autorisatie
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
VerlopenAutorisatieToken|OnvoldoendeAutorisatie.Autorisatieverlopen
OnvoldoendeScope|OnvoldoendeAutorisatie
Geblokkeerd|Ongeldig verzoek.geblokkeerd

### <a name="object-changes"></a>Objectwijzigingen

- Het `contractualRules` veld toegevoegd aan het object [NewsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) Het `contractualRules` veld bevat een lijst met regels die u moet volgen (bijvoorbeeld toeschrijving van artikelen). U moet de inschrijving toepassen in `contractualRules` plaats van het gebruik van `provider`. Het artikel `contractualRules` bevat alleen wanneer het antwoord van de [Web Search API](../bing-web-search/search-the-web.md) een nieuwsantwoord bevat.

## <a name="non-breaking-changes"></a>Niet-brekende wijzigingen

### <a name="query-parameters"></a>Queryparameters

- Producten toegevoegd als mogelijke waarde waarop u de [parameter categoriequery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) instellen. Zie [categorieën op markten](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Added the [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) query parameter, which returns trending topics sorted by date with the most recent first.

- Toegevoegd de [Parameter Sinds](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) query, die trending onderwerpen die werden ontdekt door Bing op of na de opgegeven Unix tijdperk tijdstempel retourneert.

### <a name="object-changes"></a>Objectwijzigingen

- Het `mentions` veld toegevoegd aan het object [NewsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) Het `mentions` veld bevat een lijst met entiteiten (personen of plaatsen) die in het artikel zijn gevonden.

- Het `video` veld toegevoegd aan het object [NewsArticle.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) Het `video` veld bevat een video die gerelateerd is aan het nieuwsartikel. De video is \<een\> iframe dat u insluiten of een bewegingsminiatuur.

- Het `sort` veld toegevoegd aan het object [Nieuws.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) Het `sort` veld toont de sorteervolgorde van de artikelen. De artikelen worden bijvoorbeeld gesorteerd op relevantie (standaard) of datum.

- Het object [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) toevoegen, dat een sorteervolgorde definieert. Het `isSelected` veld geeft aan of het antwoord de sorteervolgorde heeft gebruikt. Als **dit waar is,** heeft het antwoord de sorteervolgorde gebruikt. Als `isSelected` **dit niet waar**is, `url` kunt u de URL in het veld gebruiken om een andere sorteervolgorde aan te vragen.
