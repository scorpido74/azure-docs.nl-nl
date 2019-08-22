---
title: Preview-REST API voor Azure Search 2019-05-06-preview-Azure Search
description: Azure Search service REST API versie 2019-05-06-Preview bevat experimentele functies zoals Knowledge Store en door de klant beheerde versleutelings sleutels.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649987"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>REST API van Azure Search-Service-versie 2019-05-06-preview
In dit artikel wordt `api-version=2019-05-06-Preview` de versie van Azure Search service rest API beschreven en worden experimentele functies aangeboden die nog niet algemeen beschikbaar zijn.

> [!NOTE]
> Preview-functies zijn beschikbaar voor testen en experimenteren met het doel van het verzamelen van feedback en kunnen worden gewijzigd. We raden u ten zeerste aan gebruik te maken van preview-Api's in productie toepassingen.


## <a name="new-in-2019-05-06-preview"></a>Nieuw in 2019-05-06-preview

Het [**kennis archief**](knowledge-store-concept-intro.md) is een nieuwe bestemming van een AI-gebaseerde verrijkings pijplijn. Naast een index kunt u nu gevulde gegevens structuren behouden die zijn gemaakt tijdens het indexeren in azure Storage. U beheert de fysieke structuren van uw gegevens door middel van elementen in een vaardig heden, met inbegrip van hoe gegevens worden gevormd, of gegevens worden opgeslagen in tabel opslag of Blob Storage en of er meerdere weer gaven zijn.

Door de [**klant beheerde versleutelings sleutels**](search-security-manage-encryption-keys.md) voor versleuteling aan de service zijde zijn ook een nieuwe preview-functie. Naast de ingebouwde versleutelings-op-rest die door micro soft wordt beheerd, kunt u een extra laag versleuteling Toep assen waarbij u de enige eigenaar van de sleutels bent.

## <a name="other-preview-features"></a>Andere preview-functies

Functies die worden aangekondigd in eerdere previews zijn nog steeds beschikbaar in de open bare preview. Als u een API aanroept met een eerdere preview API-versie, kunt u deze versie blijven gebruiken of overschakelen naar `2019-05-06-Preview` zonder wijzigingen in de verwachte werking.

+ [moreLikeThis-query parameter](search-more-like-this.md) zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. 
* Het [indexeren van CSV](search-howto-index-csv-blobs.md) -blobs maakt één document per regel, in plaats van één document per tekst-blob.
* [MONGODB API-ondersteuning voor Cosmos DB Indexeer functies](search-howto-index-cosmosdb.md) is beschikbaar in de preview-versie.


## <a name="how-to-call-a-preview-api"></a>Een preview-API aanroepen

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code `api-version=2016-09-01-Preview` aanroept `api-version=2017-11-11-Preview`of, zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen. 

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over de Azure Search-service REST API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie voor de Search-service REST API](https://docs.microsoft.com/rest/api/searchservice/)