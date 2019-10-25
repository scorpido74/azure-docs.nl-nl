---
title: REST API versie 2019-05-06-preview
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API versie 2019-05-06-Preview bevat experimentele functies zoals Knowledge Store en door de klant beheerde versleutelings sleutels.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a4daaac6abababcedb5d6dd7eb2122ef29846ef4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792534"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>REST API voor Azure Cognitive Search Service-versie 2019-05-06-preview

In dit artikel wordt de `api-version=2019-05-06-Preview` versie van de zoek service REST API beschreven en worden experimentele functies aangeboden die nog niet algemeen beschikbaar zijn.

> [!NOTE]
> Preview-functies zijn beschikbaar voor testen en experimenteren met het doel van het verzamelen van feedback en kunnen worden gewijzigd. We raden u ten zeerste aan gebruik te maken van preview-Api's in productie toepassingen.


## <a name="new-in-2019-05-06-preview"></a>Nieuw in 2019-05-06-preview

Het [**kennis archief**](knowledge-store-concept-intro.md) is een nieuwe bestemming van een AI-gebaseerde verrijkings pijplijn. Naast een index kunt u nu gevulde gegevens structuren behouden die zijn gemaakt tijdens het indexeren in azure Storage. U beheert de fysieke structuren van uw gegevens door middel van elementen in een vaardig heden, met inbegrip van hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob Storage en of er meerdere weer gaven zijn.

Door de [**klant beheerde versleutelings sleutels**](search-security-manage-encryption-keys.md) voor versleuteling aan de service zijde zijn ook een nieuwe preview-functie. Naast de ingebouwde versleutelings-op-rest die door micro soft wordt beheerd, kunt u een extra laag versleuteling Toep assen waarbij u de enige eigenaar van de sleutels bent.

## <a name="other-preview-features"></a>Andere preview-functies

Functies die worden aangekondigd in eerdere previews zijn nog steeds beschikbaar in de open bare preview. Als u een API aanroept met een eerdere preview API-versie, kunt u deze versie blijven gebruiken of overschakelen naar `2019-05-06-Preview` zonder wijzigingen in het verwachte gedrag.

+ [moreLikeThis-query parameter](search-more-like-this.md) zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. 
* Het [indexeren van CSV-blobs](search-howto-index-csv-blobs.md) maakt één document per regel, in plaats van één document per tekst-blob.
* [MONGODB API-ondersteuning voor Cosmos DB Indexeer functies](search-howto-index-cosmosdb.md) is beschikbaar in de preview-versie.


## <a name="how-to-call-a-preview-api"></a>Een preview-API aanroepen

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code `api-version=2016-09-01-Preview` of `api-version=2017-11-11-Preview`aanroept, zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen. 

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

De Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over zoek REST API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie voor de Search-service REST API](https://docs.microsoft.com/rest/api/searchservice/)