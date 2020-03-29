---
title: Upgrade van Bing Image Search API v5 naar v7
titleSuffix: Azure Cognitive Services
description: In deze upgradehandleiding worden wijzigingen tussen versie 5 en versie 7 van de Bing Image Search API beschreven. Gebruik deze handleiding om u te helpen de onderdelen van uw toepassing te identificeren die u moet bijwerken om versie 7 te gebruiken.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883492"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Upgradegids bing Image Search API v7

Deze upgradehandleiding identificeert de wijzigingen tussen versie 5 en versie 7 van de Bing Image Search API. Gebruik deze handleiding om u te helpen de onderdelen van uw toepassing te identificeren die u moet bijwerken om versie 7 te gebruiken.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

### <a name="endpoints"></a>Eindpunten

- Het versienummer van het eindpunt is gewijzigd van v5 naar v7. Bijvoorbeeld https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

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

- De queryparameter hernoemd naar `modulesRequested` [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Hernoemd de annotaties naar Tags. Zie de parameter [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) query naar Tags.  

- Wijzigde de lijst met ondersteunde markten van de filterwaarde ShoppingSources alleen in en-VS. Zie [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Beeldinzichten verandert

- De naam `annotations` van het `imageTags`veld [ImagesInsights hernoemd](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) naar .  

- De naam `AnnotationModule` van het object gewijzigd in [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- De naam `Annotation` van het object `confidence` gewijzigd in [Tag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)en het veld verwijderd.  

- De naam `insightsSourcesSummary` van het veld `insightsMetadata`van het object [Afbeelding](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) gewijzigd in .  

- De naam `InsightsSourcesSummary` van het object gewijzigd in [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Het `https://api.cognitive.microsoft.com/bing/v7.0/images/details` eindpunt toegevoegd. Gebruik dit eindpunt om afbeeldingsinzichten op te vragen in plaats van het eindpunt /images/search. Zie [Image Insights](./image-insights.md).

- De volgende queryparameters zijn nu `/images/details` alleen geldig met het eindpunt.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [Cabine](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [Ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- De naam `ImageInsightsResponse` van het object gewijzigd in [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- De gegevenstypen van de volgende velden in het object [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) zijn gewijzigd.  

    -   Wijzig het type `relatedCollections` veld `ImageGallery[]` van [relatedcollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Wijzig het type `pagesIncluding` veld `Image[]` van [AfbeeldingenModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Wijzighet type `relatedSearches` veld `Query[]` van [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Wijzighet type `recipes` veld `Recipe[]` van [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Wijzig het type `visuallySimilarImages` veld `Image[]` van [AfbeeldingenModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Wijzig het type `visuallySimilarProducts` veld `ProductSummaryImage[]` van [AfbeeldingenModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Het `ProductSummaryImage` object verwijderd en de productgerelateerde velden naar het [beeldobject](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) verplaatst. Het `Image` object bevat de productgerelateerde velden alleen wanneer de afbeelding is opgenomen als onderdeel van visueel vergelijkbare producten in een beeldinzichtsreactie.  

    -   Wijzighet type `recognizedEntityGroups` veld `RecognizedEntityGroup[]` van [RecognizedEnModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   De naam `categoryClassification` van het `annotations`veld [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) wijzigen `AnnotationsModule`en het type gewijzigd in .  

### <a name="images-answer"></a>Afbeeldingen antwoord

-   Verwijderde de velden DisplayShoppingSourcesBadges en displayRecipeSourcesBadges uit [Afbeeldingen](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   De naam `nextOffsetAddCount` van het `nextOffset`veld [Afbeeldingen](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) hernoemd naar . De manier waarop u de offset gebruikt, is ook gewijzigd. Voorheen stelde u de parameter `nextOffsetAddCount` [offsetquery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) in op de waarde plus de vorige verschuivingswaarde plus het aantal afbeeldingen in het resultaat. Nu, je `offset` ingesteld `nextOffset` op de waarde.  


## <a name="non-breaking-changes"></a>Niet-brekende wijzigingen

### <a name="query-parameters"></a>Queryparameters

- Transparant toegevoegd als mogelijke [imageType-filterwaarde.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) Het filter Transparant retourneert alleen afbeeldingen met een transparante achtergrond.

- Toegevoegd de Any als een mogelijke [licentie](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) filter waarde. Het filter Elke retourneert alleen afbeeldingen die onder licentie vallen.

- Toegevoegd de [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) en [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) query parameters. Gebruik deze filters om afbeeldingen binnen een bereik van bestandsgroottes terug te sturen.  

- Toegevoegd de [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) query parameters. Gebruik deze filters om afbeeldingen binnen een bereik van hoogtes en breedtes terug te sturen.  

### <a name="object-changes"></a>Objectwijzigingen

- De `description` velden `lastUpdated` en velden aan het object [Aanbieding](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) toevoegen.  

- Het `name` veld toegevoegd aan het object [ImageGallery.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery)  

- Toegevoegd `similarTerms` aan het object [Afbeeldingen.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) Dit veld bevat een lijst met termen die qua betekenis vergelijkbaar zijn met de querytekenreeks van de gebruiker.  
