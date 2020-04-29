---
title: Preview-functies in REST API
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API versie 2019-05-06-Preview bevat experimentele functies zoals Knowledge Store en Indexeer functie in de cache voor incrementele verrijking.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162273"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Preview-functies in azure Cognitive Search

Dit artikel bevat een lijst met functies die momenteel als preview-versie beschikbaar zijn. Functies die overschakelen van preview naar algemene Beschik baarheid, worden verwijderd uit deze lijst. U kunt [service-updates](https://azure.microsoft.com/updates/?product=search) controleren of [Wat is er nieuw](whats-new.md) voor aankondigingen over algemene Beschik baarheid.

Hoewel sommige preview-functies beschikbaar zijn in de portal en .NET SDK, bevat de REST API altijd preview-functies.

+ Voor zoek bewerkingen is [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) de huidige preview-versie.
+ Voor beheer bewerkingen is [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) de huidige preview-versie.

> [!IMPORTANT]
> De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Functies voor AI-verrijking

Bekijk de nieuwste verbeteringen voor AI-verrijking met de [Preview-API voor zoeken](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Zoek vaardigheid aangepaste entiteit (preview-versie)](cognitive-search-skill-custom-entity-lookup.md ) | Een cognitieve vaardigheid die zoekt naar tekst van een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Met deze lijst worden alle documenten met alle overeenkomende entiteiten labeld. De vaardigheid ondersteunt ook een mate van fuzzy matching die kan worden toegepast om overeenkomsten te vinden die vergelijkbaar zijn, maar niet helemaal precies. | 
| [PII-detectie vaardigheid (preview-versie)](cognitive-search-skill-pii-detection.md) | Een cognitieve vaardigheid die wordt gebruikt tijdens de indexering die persoons gegevens ophaalt uit een invoer tekst en u de mogelijkheid biedt om deze op verschillende manieren te maskeren in de tekst.| 
| [Incrementele verrijking (preview-versie)](cognitive-search-incremental-indexing-conceptual.md) | Voegt caching toe aan een verrijkings pijplijn, zodat u de bestaande uitvoer opnieuw kunt gebruiken als een gerichte wijziging, zoals een update van een vakkennisset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die worden geproduceerd door een vaardig heden.| 
| [Knowledge Store (preview)](knowledge-store-concept-intro.md) | Een nieuwe bestemming van een op AI gebaseerde verrijkings pijplijn. De fysieke gegevens structuur bevindt zich in Azure Blob Storage en Azure Table Storage, en wordt gemaakt en ingevuld wanneer u een Indexeer functie uitvoert met een bijgevoegde cognitieve vaardigheids. De definitie van een kennis archief zelf is opgegeven in een definitie van een vakkennisset. In de definitie van het kennis archief beheert u de fysieke structuur van uw gegevens door middel van *projectie* -elementen die bepalen hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob-opslag en of er meerdere weer gaven zijn.| 

## <a name="indexing-and-query-features"></a>Indexerings-en query functies

Preview-functies voor Indexeer functie zijn beschikbaar in de preview-API voor zoeken. 

|||
|-|-|
| [Indexeerfunctie voor Cosmos DB](search-howto-index-cosmosdb.md) | Ondersteuning voor de MongoDB-API (preview), de Gremlin-API (preview) en de API-typen Cassandra-API (preview). | 
|  [Azure Data Lake Storage Gen2 Indexeer functie (preview-versie)](search-howto-index-azure-data-lake-storage.md) | Inhoud en meta gegevens indexeren van Data Lake Storage Gen2.| 
| [moreLikeThis-query parameter (preview-versie)](search-more-like-this.md) | Zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. | 

## <a name="management-features"></a>Beheer functies

|||
|-|-|
| [Ondersteuning voor privé-eind punten](service-create-private-endpoint.md) | U kunt een virtueel netwerk maken met een beveiligde client (zoals een virtuele machine) en vervolgens een zoek service maken die gebruikmaakt van een persoonlijk eind punt. |
| Beperking van IP-toegang | Met [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) behulp van de beheer rest API kunt u een service maken met beperkingen voor de toegang tot IP-adressen. |

## <a name="earlier-preview-features"></a>Eerdere preview-functies

Functies die zijn aangekondigd in eerdere voor beelden, als ze niet zijn overgegaan op algemene Beschik baarheid, zijn nog steeds beschikbaar in de open bare preview. Als u een API aanroept met een eerdere preview API-versie, kunt u deze versie blijven gebruiken of overschakelen naar `2019-05-06-Preview` zonder wijzigingen in de verwachte werking.

## <a name="how-to-call-a-preview-api"></a>Een preview-API aanroepen

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code `api-version=2016-09-01-Preview` aanroept `api-version=2017-11-11-Preview`of, zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen. 

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

De Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over zoek REST API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie voor de Search-service REST API](https://docs.microsoft.com/rest/api/searchservice/)