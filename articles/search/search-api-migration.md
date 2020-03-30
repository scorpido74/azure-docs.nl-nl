---
title: Rest API-versies upgraden
titleSuffix: Azure Cognitive Search
description: Bekijk verschillen in API-versies en ontdek welke acties nodig zijn om bestaande code te migreren naar de nieuwste Azure Cognitive Search-service REST API-versie.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112164"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Upgraden naar de nieuwste AZURE Cognitive Search-service REST API-versie

Als u een vorige versie van de [Search REST API](https://docs.microsoft.com/rest/api/searchservice/)gebruikt, helpt dit artikel u uw toepassing te upgraden naar de nieuwste algemeen beschikbare API-versie, 2019-05-06.

Versie 2019-05-06 van de REST API bevat enkele wijzigingen ten opzichte van eerdere versies. Deze zijn meestal achterwaarts compatibel, dus het wijzigen van uw code moet slechts minimale inspanning vereisen, afhankelijk van welke versie u eerder gebruikte. [Stappen om te upgraden](#UpgradeSteps) schetst de codewijzigingen die nodig zijn voor het gebruik van nieuwe functies.

> [!NOTE]
> Een Azure Cognitive Search-serviceinstantie ondersteunt een reeks REST API-versies, waaronder eerdere versies. U deze API-versies blijven gebruiken, maar we raden u aan uw code te migreren naar de nieuwste versie, zodat u toegang hebt tot nieuwe mogelijkheden.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Nieuw in versie 2019-05-06
Versie 2019-05-06 is de nieuwste algemeen beschikbare release van de REST API. Functies die zijn overgegaan op algemeen beschikbare status in deze API-versie:

* [Automatisch aanvullen](index-add-suggesters.md) is een functie typeahead die een gedeeltelijk opgegeven terminvoer voltooit.

* [Complexe typen](search-howto-complex-data-types.md) bieden native ondersteuning voor gestructureerde objectgegevens in zoekindex.

* [JsonLines parsing modes](search-howto-index-json-blobs.md), onderdeel van Azure Blob indexing, maakt één zoekdocument per JSON entiteit die wordt gescheiden door een nieuwe regel.

* [AI-verrijking](cognitive-search-concept-intro.md) biedt indexering die gebruikmaakt van de AI-verrijkingsengines van Cognitive Services.

Verschillende preview-functiereleases vallen samen met deze algemeen beschikbare update. Zie [Zoek REST-api-versie 2019-05-06-Preview](search-api-preview.md)voor het bekijken van de lijst met nieuwe preview-functies.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

Bestaande code met de volgende functionaliteit wordt afgebroken op api-versie=2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexer voor Azure Cosmos DB - datasource is nu "type": "cosmosdb"

Als u een [Cosmos DB-indexer gebruikt,](search-howto-index-cosmosdb.md )moet u overschakelen `"type": "documentdb"` naar `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Fouten in het uitvoeringsresultaat van indexeren hebben geen status meer

De foutstructuur voor indexeruitvoering `status` had eerder een element. Dit element is verwijderd omdat het geen nuttige informatie verstrekte.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Indexergegevensbron API retourneert niet langer verbindingstekenreeksen

Vanaf API-versies 2019-05-06 en 2019-05-06-Preview retourneert de API voor gegevensbronnen niet langer verbindingstekenreeksen in de reactie op een REST-bewerking. In eerdere API-versies, voor gegevensbronnen die zijn gemaakt met POST, heeft Azure Cognitive Search **201** geretourneerd, gevolgd door de OData-respons, die de verbindingstekenreeks in platte tekst bevatte.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Benoemde entiteit erkenning cognitieve vaardigheid is nu stopgezet

Als u [de vaardigheid van naamentiteitherkenning](cognitive-search-skill-named-entity-recognition.md) in uw code aanroept, mislukt het gesprek. Vervangende functionaliteit is [entiteitsherkenning](cognitive-search-skill-entity-recognition.md). U moet in staat zijn om de vaardigheid referentie te vervangen door geen andere wijzigingen. De API-handtekening is voor beide versies hetzelfde. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen om te upgraden
Als u een upgrade uitvoert ten opzichte van een vorige GA-versie, 2017-11-11 of 2016-09-01, hoeft u waarschijnlijk geen wijzigingen aan te brengen in uw code, behalve om het versienummer te wijzigen. De enige situaties waarin u de code moet wijzigen, zijn wanneer:

* Uw code mislukt wanneer niet-herkende eigenschappen worden geretourneerd in een API-antwoord. Standaard moet uw toepassing eigenschappen negeren die niet worden begrepen.

* Uw code blijft bestaan API-aanvragen en probeert deze opnieuw te verzenden naar de nieuwe API-versie. Dit kan bijvoorbeeld gebeuren als uw toepassing vervolgtokens blijft gebruiken die zijn `@search.nextPageParameters` geretourneerd uit de Search API (zoek voor meer informatie in de [zoek-API-verwijzing).](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

Als een van deze situaties op u van toepassing is, moet u mogelijk uw code dienovereenkomstig wijzigen. Anders zijn er geen wijzigingen nodig, tenzij u de [nieuwe functies](#WhatsNew) van versie 2019-05-06 wilt gaan gebruiken.

Als u een upgrade uitvoert van een preview-API-versie, is het bovenstaande ook van toepassing, maar u moet zich er ook van bewust zijn dat sommige preview-functies niet beschikbaar zijn in versie 2019-05-06:

* ["Meer als dit" query's](search-more-like-this.md)
* [Blob-indexering met CSV](search-howto-index-csv-blobs.md)
* [MongoDB API-ondersteuning voor Cosmos DB-indexers](search-howto-index-cosmosdb.md)

Als uw code deze functies gebruikt, u niet upgraden naar API-versie 2019-05-06 zonder uw gebruik ervan te verwijderen.

> [!IMPORTANT]
> Preview-API's zijn bedoeld voor testen en evaluatie en mogen niet worden gebruikt in productieomgevingen.
> 

### <a name="upgrading-complex-types"></a>Complexe typen upgraden

Als uw code complexe typen gebruikt met de oudere preview-API-versies 2017-11-11-Preview of 2016-09-01-Preview, zijn er enkele nieuwe en gewijzigde limieten in versie 2019-05-06 waarvan u op de hoogte moet zijn:

+ De limieten voor de diepte van de deelvelden en het aantal complexe verzamelingen per index zijn verlaagd. Als u indexen hebt gemaakt die deze limieten overschrijden met behulp van de preview-api-versies, mislukt elke poging om deze bij te werken of opnieuw te maken met API-versie 2019-05-06. Als dit op u van toepassing is, moet u uw schema opnieuw ontwerpen om binnen de nieuwe limieten te passen en vervolgens uw index opnieuw op te bouwen.

+ Er is een nieuwe limiet in api-versie 2019-05-06 voor het aantal elementen van complexe verzamelingen per document. Als u indexen hebt gemaakt met documenten die deze limieten overschrijden met behulp van de preview-api-versies, mislukt elke poging om die gegevens opnieuw te indexeren met api-versie 2019-05-06. Als dit op u van toepassing is, moet u het aantal complexe verzamelingselementen per document verminderen voordat u uw gegevens opnieuw indexeert.

Zie [Servicelimieten voor Azure Cognitive Search voor](search-limits-quotas-capacity.md)meer informatie.

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Een oude complexe typestructuur upgraden

Als uw code complexe typen gebruikt met een van de oudere preview-API-versies, gebruikt u mogelijk een indexdefinitieindeling die er als volgt uitziet:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

In API-versie 2017-11-11-Preview is een nieuwere boomachtige indeling geïntroduceerd voor het definiëren van indexvelden. In de nieuwe indeling heeft elk complex veld een veldenverzameling waarin de subvelden worden gedefinieerd. In API-versie 2019-05-06 wordt deze nieuwe indeling uitsluitend gebruikt en wordt een poging om een index te maken of bij te werken met behulp van de oude indeling mislukt. Als u indexen hebt gemaakt met behulp van de oude indeling, moet u API-versie 2017-11-11-Preview gebruiken om deze bij te werken naar de nieuwe indeling voordat ze kunnen worden beheerd met API-versie 2019-05-06.

U 'platte' indexen bijwerken naar de nieuwe indeling met de volgende stappen met API-versie 2017-11-11-Preview:

1. Voer een GET-verzoek uit om uw index op te halen. Als het al in het nieuwe formaat is, bent u klaar.

2. Vertaal de index van de 'platte' notatie naar de nieuwe indeling. Je moet code schrijven voor dit omdat er geen voorbeeldcode beschikbaar op het moment van dit schrijven.

3. Voer een PUT-verzoek uit om de index bij te werken naar de nieuwe indeling. Zorg ervoor dat u geen andere details van de index wijzigt, zoals de doorzoekbaarheid/filterbaarheid van velden, omdat dit niet is toegestaan door de Update Index API.

> [!NOTE]
> Het is niet mogelijk om indexen te beheren die zijn gemaakt met de oude 'platte' indeling van de Azure-portal. Upgrade uw indexen van de "platte" vertegenwoordiging naar de "boom" vertegenwoordiging bij uw vroegste gemak.

## <a name="next-steps"></a>Volgende stappen

Bekijk de referentiedocumentatie voor zoekREST API. Als u problemen ondervindt, vraag ons dan om hulp op [StackOverflow](https://stackoverflow.com/) of [neem contact op met ondersteuning.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Zoekservice REST API-verwijzing](https://docs.microsoft.com/rest/api/searchservice/)

