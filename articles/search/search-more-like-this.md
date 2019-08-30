---
title: moreLikeThis in Azure Search (preview)-Azure Search
description: Voorlopige documentatie voor de functie moreLikeThis (preview), die wordt weer gegeven in de Azure Search REST API.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182326"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis in Azure Search

> [!Note]
> moreLikeThis is een preview-versie en is niet bedoeld voor productie gebruik. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.

`moreLikeThis=[key]`is een query parameter in de [Search Documents-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) waarmee documenten worden gevonden die vergelijkbaar zijn met het document dat is opgegeven door de document sleutel. Wanneer een zoek opdracht wordt gemaakt met `moreLikeThis`, wordt er een query gegenereerd met zoek termen die zijn geëxtraheerd uit het opgegeven document, waarin het document het beste wordt beschreven. De gegenereerde query wordt vervolgens gebruikt voor het maken van de zoek opdracht. Standaard wordt de inhoud van alle Doorzoek bare velden beschouwd als minus alle beperkte velden die u hebt opgegeven met behulp `searchFields` van de para meter. De `moreLikeThis` para meter kan niet worden gebruikt met de zoek `search=[string]`parameter,.

Standaard wordt de inhoud van alle Doorzoek bare velden op het hoogste niveau beschouwd. Als u in plaats daarvan bepaalde velden wilt opgeven, kunt u de `searchFields` para meter gebruiken. 

U kunt moreLikeThis niet gebruiken voor Doorzoek bare subvelden in een [complex type](search-howto-complex-data-types.md).

## <a name="examples"></a>Voorbeelden 

Hieronder ziet u een voor beeld van een moreLikeThis-query. De query vindt documenten waarvan de beschrijvings velden het meest overeenkomen met het veld van het bron document, `moreLikeThis` zoals opgegeven door de para meter.

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
> [Azure Search REST-Api's verkennen met behulp van postman](search-get-started-postman.md)