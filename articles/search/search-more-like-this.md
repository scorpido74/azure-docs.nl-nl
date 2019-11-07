---
title: query functie moreLikeThis (preview)
titleSuffix: Azure Cognitive Search
description: Beschrijft de functie moreLikeThis (preview), die beschikbaar is in Preview-versies van de Azure Cognitive Search REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721733"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (preview) in azure Cognitive Search

> [!IMPORTANT] 
> Deze functie is momenteel beschikbaar als openbare preview-versie. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

`moreLikeThis=[key]` is een query parameter in de [Search Documents-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) waarmee documenten worden gevonden die vergelijkbaar zijn met het document dat is opgegeven door de document sleutel. Wanneer een zoek opdracht wordt gemaakt met `moreLikeThis`, wordt er een query gegenereerd met zoek termen die zijn geëxtraheerd uit het opgegeven document waarin het document het beste wordt beschreven. De gegenereerde query wordt vervolgens gebruikt voor het maken van de zoek opdracht. Standaard wordt de inhoud van alle Doorzoek bare velden beschouwd als minus alle beperkte velden die u hebt opgegeven met behulp van de para meter `searchFields`. De para meter `moreLikeThis` kan niet worden gebruikt met de zoek parameter `search=[string]`.

Standaard wordt de inhoud van alle Doorzoek bare velden op het hoogste niveau beschouwd. Als u in plaats daarvan bepaalde velden wilt opgeven, kunt u de para meter `searchFields` gebruiken. 

U kunt moreLikeThis niet gebruiken voor Doorzoek bare subvelden in een [complex type](search-howto-complex-data-types.md).

## <a name="examples"></a>Voorbeelden 

Hieronder ziet u een voor beeld van een moreLikeThis-query. De query vindt documenten waarvan de beschrijvings velden het meest overeenkomen met het veld van het bron document, zoals opgegeven door de para meter `moreLikeThis`.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Volgende stappen

U kunt elk hulp programma voor webtesting gebruiken om te experimenteren met deze functie.  U kunt het beste postman gebruiken voor deze oefening.

> [!div class="nextstepaction"]
> [Azure Cognitive Search REST-Api's verkennen met behulp van postman](search-get-started-postman.md)