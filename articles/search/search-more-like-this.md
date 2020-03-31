---
title: moreLikeDeze queryfunctie (voorbeeld)
titleSuffix: Azure Cognitive Search
description: Beschrijft de functie moreLikeThis (preview), die beschikbaar is in previewversies van de Azure Cognitive Search REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873808"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (preview) in Azure Cognitive Search

> [!IMPORTANT] 
> Deze functie is momenteel beschikbaar als openbare preview-versie. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. De [REST API versie 2019-05-06-Preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal of .NET SDK-ondersteuning.

`moreLikeThis=[key]`is een queryparameter in de [API voor zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents) waarin documenten worden gevonden die vergelijkbaar zijn met het document dat door de documentsleutel is opgegeven. Wanneer een zoekaanvraag `moreLikeThis`wordt gedaan met , wordt een query gegenereerd met zoektermen die zijn geëxtraheerd uit het opgegeven document dat dat document het beste beschrijft. De gegenereerde query wordt vervolgens gebruikt om de zoekaanvraag te maken. Standaard wordt de inhoud van alle doorzoekbare velden in aanmerking `searchFields` genomen, verminderd met de beperkte velden die u met de parameter hebt opgegeven. De `moreLikeThis` parameter kan niet worden `search=[string]`gebruikt met de zoekparameter.

Standaard wordt de inhoud van alle doorzoekbare velden op het hoogste niveau in aanmerking genomen. Als u bepaalde velden wilt opgeven, `searchFields` kunt u de parameter gebruiken. 

U kunt `MoreLikeThis` geen doorzoekbare subvelden in een [complex type gebruiken.](search-howto-complex-data-types.md)

## <a name="examples"></a>Voorbeelden

Alle volgende voorbeelden gebruiken het voorbeeld van De hotels van [Quickstart: Maak een zoekindex in de Azure-portal.](search-get-started-portal.md)

### <a name="simple-query"></a>Eenvoudige query

In de volgende query worden documenten gevonden waarvan de beschrijvingsvelden `moreLikeThis` het meest lijken op het veld van het brondocument zoals opgegeven door de parameter:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

In dit voorbeeld zoekt het verzoek naar hotels `HotelId` die vergelijkbaar zijn met die met 29.
In plaats van HTTP GET `MoreLikeThis` te gebruiken, u ook een beroep doen op HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Filters toepassen

`MoreLikeThis`kan worden gecombineerd met andere `$filter`algemene queryparameters zoals . De query kan bijvoorbeeld worden beperkt tot alleen hotels waarvan de categorie 'Budget' is en waar de beoordeling hoger is dan 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Velden selecteren en resultaten beperken

De `$top` kiezer kan worden gebruikt om te beperken `MoreLikeThis` hoeveel resultaten moeten worden geretourneerd in een query. Ook kunnen velden worden `$select`geselecteerd met . Hier worden de top drie hotels geselecteerd, samen met hun ID, Naam en Waardering: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Volgende stappen

U elk hulpprogramma voor webtests gebruiken om met deze functie te experimenteren.  We raden aan om Postman te gebruiken voor deze oefening.

> [!div class="nextstepaction"]
> [Azure Cognitive Search REST API's verkennen met behulp van Postbode](search-get-started-postman.md)
