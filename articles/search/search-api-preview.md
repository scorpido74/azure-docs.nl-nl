---
title: REST API versie 2019-05-06-preview
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API versie 2019-05-06-Preview bevat experimentele functies zoals Knowledge Store en Indexeer functie cache voor incrementele verrijking..
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: 940ada83aeabf4bf8746ad5f90592e0917f7b403
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844442"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>REST API voor Azure Cognitive Search Service-versie 2019-05-06-preview

In dit artikel wordt de `api-version=2019-05-06-Preview` versie van de zoek service REST API beschreven en worden experimentele functies aangeboden die nog niet algemeen beschikbaar zijn.

> [!NOTE]
> Preview-functies zijn beschikbaar voor testen en experimenteren met het doel van het verzamelen van feedback en kunnen worden gewijzigd. We raden u ten zeerste aan gebruik te maken van preview-Api's in productie toepassingen.


## <a name="new-in-2019-05-06-preview"></a>Nieuw in 2019-05-06-preview

+ [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt caching toe aan een verrijkings pijplijn, zodat u de bestaande uitvoer opnieuw kunt gebruiken als een gerichte wijziging, zoals een update van een vakkennisset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die worden geproduceerd door een vaardig heden.

+ [Cosmos DB Indexeer functie](search-howto-index-cosmosdb.md) ondersteunt MongoDb API (preview), GREMLIN-API (preview) en Cassandra-API (preview).

+ [Azure data Lake Storage Gen2 Indexeer functie (preview)](search-howto-index-azure-data-lake-storage.md) kan inhoud en meta gegevens indexeren vanuit data Lake Storage Gen2.

+ Het [kennis archief](knowledge-store-concept-intro.md) is een nieuwe bestemming van een AI-gebaseerde verrijkings pijplijn. De fysieke gegevens structuur bevindt zich in Azure Blob Storage en Azure Table Storage, en wordt gemaakt en ingevuld wanneer u een Indexeer functie uitvoert met een bijgevoegde cognitieve vaardigheids. De definitie van een kennis archief zelf is opgegeven in een definitie van een vakkennisset. In de definitie van het kennis archief beheert u de fysieke structuur van uw gegevens door middel van *projectie* -elementen die bepalen hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob-opslag en of er meerdere weer gaven zijn.

## <a name="earlier-preview-features"></a>Eerdere preview-functies

Functies die worden aangekondigd in eerdere previews zijn nog steeds beschikbaar in de open bare preview. Als u een API aanroept met een eerdere preview API-versie, kunt u deze versie blijven gebruiken of overschakelen naar `2019-05-06-Preview` zonder wijzigingen in het verwachte gedrag.

+ [moreLikeThis-query parameter](search-more-like-this.md) zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. 

+ Het [indexeren van CSV-blobs](search-howto-index-csv-blobs.md) maakt één document per regel, in plaats van één document per tekst-blob.

## <a name="how-to-call-a-preview-api"></a>Een preview-API aanroepen

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code `api-version=2016-09-01-Preview` of `api-version=2017-11-11-Preview`aanroept, zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen. 

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

De Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over zoek REST API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie voor de Search-service REST API](https://docs.microsoft.com/rest/api/searchservice/)