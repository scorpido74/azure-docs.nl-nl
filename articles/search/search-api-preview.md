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
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720025"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>REST API voor Azure Cognitive Search Service-versie 2019-05-06-preview

In dit artikel wordt de `api-version=2019-05-06-Preview` versie van de zoek service REST API beschreven en worden experimentele functies aangeboden die nog niet algemeen beschikbaar zijn.

> [!NOTE]
> Preview-functies zijn beschikbaar voor testen en experimenteren met het doel van het verzamelen van feedback en kunnen worden gewijzigd. We raden u ten zeerste aan gebruik te maken van preview-Api's in productie toepassingen.


## <a name="new-in-2019-05-06-preview"></a>Nieuw in 2019-05-06-preview

+ [Incrementeel indexeren](cognitive-search-incremental-indexing-conceptual.md) is een nieuwe modus voor het indexeren waarmee de status en de cache worden toegevoegd aan een vaardig heden, zodat u de bestaande uitvoer opnieuw kunt gebruiken als de bron gegevens, de indexer en de definities van vaardig heden ongewijzigd blijven. Deze functie is alleen van toepassing op verrijkingen die een cognitieve vaardigheidset hebben gedefinieerd.

+ [Cosmos DB Indexeer functie](search-howto-index-cosmosdb.md) ondersteunt MongoDb API, Gremlin api en Cassandra-API.

+ [Azure data Lake Storage Gen2 Indexeer functie](search-howto-index-azure-data-lake-storage.md) kan inhoud en meta gegevens indexeren vanuit data Lake Storage Gen2.

+ [Document extractie (preview)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren, waarmee u de inhoud van een bestand kunt extra heren uit een vakkennisset. Het kraken van documenten is eerder voorgekomen voordat de vaardig heden werden uitgevoerd. Als u deze vaardigheid hebt toegevoegd, kunt u deze bewerking ook uitvoeren binnen de vaardig heden-uitvoering.

+ [Tekst omzetting (preview)](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren waarmee tekst wordt geëvalueerd en, voor elke record, de tekst wordt omgezet in de opgegeven doel taal.

+ Het [kennis archief](knowledge-store-concept-intro.md) is een nieuwe bestemming van een AI-gebaseerde verrijkings pijplijn. De fysieke gegevens structuur bevindt zich in Azure Blob Storage en Azure Table Storage, en wordt gemaakt en ingevuld wanneer u een Indexeer functie uitvoert met een bijgevoegde cognitieve vaardigheids. De definitie van een kennis archief zelf is opgegeven in een definitie van een vakkennisset. In de definitie van het kennis archief beheert u de fysieke structuur van uw gegevens door middel van *projectie* -elementen die bepalen hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob-opslag en of er meerdere weer gaven zijn.

+ Door de [klant beheerde versleutelings sleutels](search-security-manage-encryption-keys.md) voor versleuteling aan de service zijde zijn ook een nieuwe preview-functie. Naast de ingebouwde versleutelings-op-rest die door micro soft wordt beheerd, kunt u een extra laag versleuteling Toep assen waarbij u de enige eigenaar van de sleutels bent.

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