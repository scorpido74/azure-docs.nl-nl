---
title: Preview-functies in REST API
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search-service REST API Version 2019-05-06-Preview bevat experimentele functies zoals knowledge store en indexercaching voor incrementele verrijking.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162273"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Voorbeeld van functies in Azure Cognitive Search

In dit artikel worden functies weergegeven die momenteel in een voorbeeld worden weergegeven. Functies die overgaan van voorbeeld naar algemene beschikbaarheid, worden uit deze lijst verwijderd. U [Service-updates](https://azure.microsoft.com/updates/?product=search) of [Nieuw voor](whats-new.md) aankondigingen met betrekking tot algemene beschikbaarheid controleren.

Hoewel sommige preview-functies mogelijk beschikbaar zijn in de portal en .NET SDK, heeft de REST API altijd preview-functies.

+ Voor zoekbewerkingen [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) is dit de huidige voorbeeldversie.
+ Voor beheerbewerkingen [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) is dit de huidige voorbeeldversie.

> [!IMPORTANT]
> Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="ai-enrichment-features"></a>AI-verrijkingsfuncties

Ontdek de nieuwste verbeteringen voor AI-verrijking via de [preview Search API.](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)

|||
|-|-|
| [Aangepaste entiteit opzoekvaardigheid (voorbeeld)](cognitive-search-skill-custom-entity-lookup.md ) | Een cognitieve vaardigheid die zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinnen. Met behulp van deze lijst worden alle documenten labels met alle overeenkomende entiteiten. De vaardigheid ondersteunt ook een mate van fuzzy matching die kan worden toegepast om overeenkomsten te vinden die vergelijkbaar zijn, maar niet helemaal exact. | 
| [PII-detectievaardigheid (voorbeeld)](cognitive-search-skill-pii-detection.md) | Een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren die persoonlijk identificeerbare informatie uit een invoertekst haalt en u de mogelijkheid geeft om deze op verschillende manieren uit die tekst te maskeren.| 
| [Incrementele verrijking (voorbeeld)](cognitive-search-incremental-indexing-conceptual.md) | Hiermee voegt u caching toe aan een verrijkingspijplijn, zodat u bestaande uitvoer opnieuw gebruiken als een gerichte wijziging, zoals een update voor een skillset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die door een skillset zijn geproduceerd.| 
| [Knowledge Store (preview)](knowledge-store-concept-intro.md) | Een nieuwe bestemming van een AI-gebaseerde verrijkingspijplijn. De fysieke gegevensstructuur bestaat in Azure Blob-opslag en Azure Table-opslag en wordt gemaakt en ingevuld wanneer u een indexer uitvoert met een gekoppelde cognitieve vaardigheden. De definitie van een kenniswinkel zelf wordt gespecificeerd binnen een skillset-definitie. Binnen de definitie van het kennisarchief beheert u de fysieke structuren van uw gegevens door middel van *projectie-elementen* die bepalen hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabelopslag of Blob-opslag en of er meerdere weergaven zijn.| 

## <a name="indexing-and-query-features"></a>Indexering en queryfuncties

De preview-functies van indexeren zijn beschikbaar in de preview Search API. 

|||
|-|-|
| [Indexeerfunctie voor Cosmos DB](search-howto-index-cosmosdb.md) | Ondersteuning voor MongoDB API (preview), Gremlin API (preview) en Cassandra API (preview) API-typen. | 
|  [Azure Data Lake Storage Gen2-indexeerder (voorbeeld)](search-howto-index-azure-data-lake-storage.md) | Indexerinhoud en metadata van Data Lake Storage Gen2.| 
| [moreLikeDeze queryparameter (voorbeeld)](search-more-like-this.md) | Hiermee worden documenten gevonden die relevant zijn voor een specifiek document. Deze functie is in eerdere previews. | 

## <a name="management-features"></a>Beheerfuncties

|||
|-|-|
| [Ondersteuning voor privéeindpunt](service-create-private-endpoint.md) | U een virtueel netwerk maken met een beveiligde client (zoals een virtuele machine) en vervolgens een zoekservice maken die privéeindpunt gebruikt. |
| IP-toegangsbeperking | Met [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) behulp van de API Beheer REST u een service maken met beperkingen waarop IP-adressen toegang hebben. |

## <a name="earlier-preview-features"></a>Eerdere preview-functies

Functies die in eerdere previews zijn aangekondigd, als ze niet zijn overgestapt naar algemene beschikbaarheid, zijn nog steeds in openbare preview. Als u een API aanroept met een eerdere api-versie van een voorbeeldvoorbeeld, u die versie blijven gebruiken of naar schakelen `2019-05-06-Preview` zonder wijzigingen in het verwachte gedrag.

## <a name="how-to-call-a-preview-api"></a>Een voorbeeld-API aanroepen

Oudere previews zijn nog steeds operationeel, maar worden na verloop van tijd verouderd. Als uw `api-version=2016-09-01-Preview` code `api-version=2017-11-11-Preview`aanroept of als deze oproepen nog steeds geldig zijn. Echter, alleen de nieuwste preview-versie wordt vernieuwd met verbeteringen. 

In de volgende syntaxis van het voorbeeld wordt een aanroep naar de preview-API-versie geïllustreerd.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

Bekijk de referentiedocumentatie voor zoekREST API. Als u problemen ondervindt, vraag ons dan om hulp op [StackOverflow](https://stackoverflow.com/) of [neem contact op met ondersteuning.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Zoekservice REST API-verwijzing](https://docs.microsoft.com/rest/api/searchservice/)