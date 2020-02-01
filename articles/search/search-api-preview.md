---
title: REST API versie 2019-05-06-preview
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API versie 2019-05-06-Preview bevat experimentele functies zoals Knowledge Store en Indexeer functie cache voor incrementele verrijking..
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 71c6879f467823ab01f4c60ac4d9f26cffcd4eea
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896119"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>REST API voor Azure Cognitive Search Service-versie 2019-05-06-preview

In dit artikel wordt de `api-version=2019-05-06-Preview` versie van de zoek service REST API beschreven en worden experimentele functies aangeboden die nog niet algemeen beschikbaar zijn.

> [!NOTE]
> Preview-functies zijn beschikbaar voor testen en experimenteren met het doel van het verzamelen van feedback en kunnen worden gewijzigd. We raden u ten zeerste aan gebruik te maken van preview-Api's in productie toepassingen.

## <a name="features-in-2019-05-06-preview"></a>Functies in 2019-05-06-preview

Deze sectie bevat een lijst met functies die de preview-status hebben. De meeste zijn toegevoegd aan de huidige API-preview-versie van 2019-05-06, maar sommige `moreLikeThis` zijn van eerdere Preview-versies die in de nieuwste preview-API zijn geïmplementeerd. 

Zodra een preview-functie algemeen beschikbaar wordt, wordt deze verwijderd uit deze lijst. U kunt [service-updates](https://azure.microsoft.com/updates/?product=search) controleren of [Wat is er nieuw](whats-new.md) voor aankondigingen over algemene Beschik baarheid.

+ [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt caching toe aan een verrijkings pijplijn, zodat u de bestaande uitvoer opnieuw kunt gebruiken als een gerichte wijziging, zoals een update van een vakkennisset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die worden geproduceerd door een vaardig heden.

+ [Cosmos DB Indexeer functie](search-howto-index-cosmosdb.md) ondersteunt MongoDb API (preview), GREMLIN-API (preview) en Cassandra-API (preview).

+ [Azure data Lake Storage Gen2 Indexeer functie (preview)](search-howto-index-azure-data-lake-storage.md) kan inhoud en meta gegevens indexeren vanuit data Lake Storage Gen2.

+ Het [kennis archief](knowledge-store-concept-intro.md) is een nieuwe bestemming van een AI-gebaseerde verrijkings pijplijn. De fysieke gegevens structuur bevindt zich in Azure Blob Storage en Azure Table Storage, en wordt gemaakt en ingevuld wanneer u een Indexeer functie uitvoert met een bijgevoegde cognitieve vaardigheids. De definitie van een kennis archief zelf is opgegeven in een definitie van een vakkennisset. In de definitie van het kennis archief beheert u de fysieke structuur van uw gegevens door middel van *projectie* -elementen die bepalen hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob-opslag en of er meerdere weer gaven zijn.

+ [moreLikeThis-query parameter](search-more-like-this.md) zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. 

## <a name="earlier-preview-features"></a>Eerdere preview-functies

Functies die zijn aangekondigd in eerdere voor beelden, als ze niet zijn overgegaan op algemene Beschik baarheid, zijn nog steeds beschikbaar in de open bare preview. Als u een API aanroept met een eerdere preview API-versie, kunt u deze versie blijven gebruiken of overschakelen naar `2019-05-06-Preview` zonder wijzigingen in het verwachte gedrag.

## <a name="how-to-call-a-preview-api"></a>Een preview-API aanroepen

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code `api-version=2016-09-01-Preview` of `api-version=2017-11-11-Preview`aanroept, zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen. 

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

De Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over zoek REST API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie voor de Search-service REST API](https://docs.microsoft.com/rest/api/searchservice/)