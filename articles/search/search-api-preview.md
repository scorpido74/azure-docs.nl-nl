---
title: Preview-functies in REST API
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API versie 2019-05-06-Preview bevat experimentele functies zoals Knowledge Store en Indexeer functie in de cache voor incrementele verrijking.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 9985e7ac70c5851699839a95d1e23af4dcca35e7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935104"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Preview-functies in azure Cognitive Search

Dit artikel bevat een lijst met functies die momenteel als preview-versie beschikbaar zijn. Functies die overschakelen van preview naar algemene Beschik baarheid, worden verwijderd uit deze lijst. U kunt [service-updates](https://azure.microsoft.com/updates/?product=search) controleren of [Wat is er nieuw](whats-new.md) voor aankondigingen over algemene Beschik baarheid.

Hoewel sommige preview-functies beschikbaar zijn in de portal en .NET SDK, bevat de REST API altijd preview-functies. De huidige preview API-versie is `2019-05-06-Preview`.

> [!IMPORTANT]
> De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="features-in-public-preview"></a>Functies in open bare preview

+ [Zoek opdracht voor aangepaste entiteiten (preview)](cognitive-search-skill-custom-entity-lookup.md ) zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Met deze lijst worden alle documenten met alle overeenkomende entiteiten labeld. De vaardigheid ondersteunt ook een mate van fuzzy matching die kan worden toegepast om overeenkomsten te vinden die vergelijkbaar zijn, maar niet helemaal precies. 

+ [PII-detectie (preview)](cognitive-search-skill-pii-detection.md) is een cognitieve vaardigheid die wordt gebruikt tijdens de indexering die persoonlijke gegevens ophaalt uit een invoer tekst en die u de mogelijkheid biedt om de tekst op verschillende manieren te maskeren.

+ [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt caching toe aan een verrijkings pijplijn, zodat u de bestaande uitvoer opnieuw kunt gebruiken als een gerichte wijziging, zoals een update van een vakkennisset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die worden geproduceerd door een vaardig heden.

+ [Cosmos DB Indexeer functie](search-howto-index-cosmosdb.md) ondersteunt MongoDb API (preview), GREMLIN-API (preview) en Cassandra-API (preview).

+ [Azure data Lake Storage Gen2 Indexeer functie (preview)](search-howto-index-azure-data-lake-storage.md) kan inhoud en meta gegevens indexeren vanuit data Lake Storage Gen2.

+ [Knowledge Store (preview)](knowledge-store-concept-intro.md) is een nieuwe bestemming voor een op AI gebaseerde verrijkings pijplijn. De fysieke gegevens structuur bevindt zich in Azure Blob Storage en Azure Table Storage, en wordt gemaakt en ingevuld wanneer u een Indexeer functie uitvoert met een bijgevoegde cognitieve vaardigheids. De definitie van een kennis archief zelf is opgegeven in een definitie van een vakkennisset. In de definitie van het kennis archief beheert u de fysieke structuur van uw gegevens door middel van *projectie* -elementen die bepalen hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob-opslag en of er meerdere weer gaven zijn.

+ [moreLikeThis query para meter (preview)](search-more-like-this.md) vindt u documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. 

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