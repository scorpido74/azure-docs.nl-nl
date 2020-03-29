---
title: Bing Video Search API v5 upgraden naar v7
titleSuffix: Azure Cognitive Services
description: Hiermee worden de onderdelen van uw toepassing geïdentificeerd die u moet bijwerken om versie 7 te kunnen gebruiken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 5dc4c870ae8dbe9f082456d738836aced1271732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500730"
---
# <a name="video-search-api-upgrade-guide"></a>Upgradehandleiding voor videozoek-API

Deze upgradehandleiding identificeert de wijzigingen tussen versie 5 en versie 7 van de Bing Video Search API. Gebruik deze handleiding om u te helpen de onderdelen van uw toepassing te identificeren die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versienummer van het eindpunt is gewijzigd van v5 naar v7. Bijvoorbeeld `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

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

### <a name="query-parameters"></a>Queryparameters

- De queryparameter hernoemd naar `modulesRequested` [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Objectwijzigingen

- Hernoemd `nextOffsetAddCount` het veld `nextOffset` [van Video's](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) naar . De manier waarop u de offset gebruikt, is ook gewijzigd. Voorheen stelde u de parameter [offsetquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) in op de `nextOffset` waarde plus de vorige verschuivingswaarde plus het aantal video's in het resultaat. Nu stelt u `offset` de queryparameter `nextOffset` eenvoudig in op de waarde.  
  
- Het gegevenstype van `relatedVideos` het `Video[]` veld gewijzigd van [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (zie [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).

