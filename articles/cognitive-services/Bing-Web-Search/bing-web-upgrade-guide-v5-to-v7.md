---
title: Upgrade van API v5 naar v7 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bepaal welke onderdelen van uw toepassing updates nodig hebben om de Bing Web Search v7 API's te gebruiken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 2133cd59c524112ae8a77c0a20cbce1d1336a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881308"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Upgrade van Bing Web Search API v5 naar v7

Deze upgradehandleiding identificeert de wijzigingen tussen versie 5 en versie 7 van de Bing Web Search API. Gebruik deze handleiding om u te helpen de onderdelen van uw toepassing te identificeren die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versienummer van het eindpunt is gewijzigd van v5 naar v7. Bijvoorbeeld https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

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
|OnvoldoendeAutorisatie|AutorisatieUitgeschakeld<br/>Autorisatie verlopen|Bing retourneert OnvoldoendeAutorisatie wanneer de beller geen machtigingen heeft om toegang te krijgen tot de bron. Deze fout kan optreden als de abonnementssleutel is uitgeschakeld of is verlopen. <br/><br/>Als de fout Onvoldoende autorisatie is, is de HTTP-statuscode 403.

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


## <a name="non-breaking-changes"></a>Niet-brekende wijzigingen  

### <a name="headers"></a>Headers

- Toegevoegd de optionele [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) aanvraag header. Bing retourneert standaard cache-inhoud, indien beschikbaar. Om te voorkomen dat Bing inhoud uit de cache retourneert, stelt u de Pragma-header in op no-cache (bijvoorbeeld: Pragma: no-cache).

### <a name="query-parameters"></a>Queryparameters

- De [parameter answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) query toegevoegd. Gebruik deze parameter om het aantal antwoorden op te geven dat het antwoord moet opnemen. De antwoorden worden gekozen op basis van ranking. Als u deze parameter bijvoorbeeld instelt op drie (3), bevat het antwoord de top drie gerangschikte antwoorden.  

- De parameter [promotiequery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) toegevoegd. Gebruik deze parameter `answerCount` samen met om expliciet een of meer antwoordtypen op te nemen, ongeacht hun rangschikking. Als u bijvoorbeeld video's en afbeeldingen wilt promoten in het antwoord, stelt u promotie in op *video's, afbeeldingen.* De lijst met antwoorden die u wilt `answerCount` promoten, telt niet mee voor de limiet. Als u `answerCount` bijvoorbeeld 2 `promote` is en is ingesteld op *video's, afbeeldingen,* kan het antwoord webpagina's, nieuws, video's en afbeeldingen bevatten.

### <a name="object-changes"></a>Objectwijzigingen

- Het `someResultsRemoved` veld toegevoegd aan het object [WebAnswer.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) Het veld bevat een Booleaanse waarde die aangeeft of het antwoord bepaalde resultaten van het webantwoord heeft uitgesloten.  
