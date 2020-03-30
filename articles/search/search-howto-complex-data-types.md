---
title: Complexe gegevenstypen modelleren
titleSuffix: Azure Cognitive Search
description: Geneste of hiërarchische gegevensstructuren kunnen worden gemodelleerd in een Azure Cognitive Search-index met behulp van gegevenstypen ComplexType en Verzamelingen.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283054"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Complexe gegevenstypen modelleren in Azure Cognitive Search

Externe gegevenssets die worden gebruikt om een Azure Cognitive Search-index in te vullen, kunnen in vele vormen worden geleverd. Soms bevatten ze hiërarchische of geneste substructuren. Voorbeelden hiervan zijn meerdere adressen voor één klant, meerdere kleuren en formaten voor één SKU, meerdere auteurs van één boek, enzovoort. In modelleringstermen ziet u deze structuren mogelijk *complexe,* *samengestelde,* *samengestelde*of *geaggregeerde* gegevenstypen. De term Azure Cognitive Search gebruikt voor dit concept is **complex type**. In Azure Cognitive Search worden complexe typen gemodelleerd met behulp van **complexe velden.** Een complex veld is een veld dat kinderen (subvelden) bevat die van elk gegevenstype kunnen zijn, inclusief andere complexe typen. Dit werkt op een vergelijkbare manier als gestructureerde gegevenstypen in een programmeertaal.

Complexe velden vertegenwoordigen één object in het document of een array met objecten, afhankelijk van het gegevenstype. Velden van `Edm.ComplexType` tekst vertegenwoordigen afzonderlijke `Collection(Edm.ComplexType)` objecten, terwijl velden van het type arrays van objecten vertegenwoordigen.

Azure Cognitive Search ondersteunt native complexe typen en verzamelingen. Met deze typen u bijna elke JSON-structuur modelleren in een Azure Cognitive Search-index. In eerdere versies van Azure Cognitive Search API's kunnen alleen afgevlakte rijsets worden geïmporteerd. In de nieuwste versie kan uw index nu beter overeenkomen met brongegevens. Met andere woorden, als uw brongegevens complexe typen hebben, kan uw index ook complexe typen hebben.

Om aan de slag te gaan, raden we de [gegevensset Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)aan die u laden in de wizard **Gegevens importeren** in de Azure-portal. De wizard detecteert complexe typen in de bron en stelt een indexschema voor op basis van de gedetecteerde structuren.

> [!Note]
> Ondersteuning voor complexe typen is `api-version=2019-05-06`over het algemeen beschikbaar in . 
>
> Als uw zoekoplossing is gebaseerd op eerdere oplossingen van afgevlakte gegevenssets in een verzameling, moet u uw index wijzigen om complexe typen op te nemen zoals ondersteund in de nieuwste API-versie. Zie [Upgraden naar de nieuwste REST API-versie](search-api-migration.md) of [Upgrade naar de nieuwste .NET SDK-versie voor](search-dotnet-sdk-migration-version-9.md)meer informatie over het upgraden van API-versies.

## <a name="example-of-a-complex-structure"></a>Voorbeeld van een complexe structuur

Het volgende JSON-document bestaat uit eenvoudige velden en complexe velden. Complexe velden, `Address` zoals `Rooms`en , hebben subvelden. `Address`heeft één set waarden voor die subvelden, omdat het één object in het document is. Daarentegen `Rooms` heeft meerdere sets waarden voor de subvelden, één voor elk object in de verzameling.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Complexe velden maken

Zoals bij elke indexdefinitie u de portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) gebruiken om een schema te maken dat complexe typen bevat. 

In het volgende voorbeeld wordt een JSON-indexschema weergegeven met eenvoudige velden, verzamelingen en complexe typen. Merk op dat in een complex type elk subveld een type heeft en mogelijk kenmerken heeft, net zoals velden op het hoogste niveau dat doen. Het schema komt overeen met de bovenstaande voorbeeldgegevens. `Address`is een complex veld dat geen collectie is (een hotel heeft één adres). `Rooms`is een complex verzamelveld (een hotel heeft veel kamers).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Complexe velden bijwerken

Alle [regels voor herindexering](search-howto-reindex.md) die van toepassing zijn op velden in het algemeen, zijn nog steeds van toepassing op complexe velden. Het opnieuw invoeren van een paar van de belangrijkste regels hier, het toevoegen van een veld vereist geen index opnieuw op te bouwen, maar de meeste wijzigingen doen.

### <a name="structural-updates-to-the-definition"></a>Structurele updates van de definitie

U op elk gewenst moment nieuwe subvelden aan een complex veld toevoegen zonder dat een index opnieuw hoeft te worden opgebouwd. Het toevoegen van 'Postcode' `Address` aan of 'Voorzieningen' is `Rooms` bijvoorbeeld toegestaan, net als het toevoegen van een veld op het hoogste niveau aan een index. Bestaande documenten hebben een null-waarde voor nieuwe velden totdat u deze velden expliciet vult door uw gegevens bij te werken.

Merk op dat in een complex type elk subveld een type heeft en mogelijk kenmerken heeft, net zoals velden op het hoogste niveau dat doen

### <a name="data-updates"></a>Gegevensupdates

Het bijwerken van bestaande `upload` documenten in een index met de actie werkt op dezelfde manier voor complexe en eenvoudige velden - alle velden worden vervangen. Echter, `merge` (of `mergeOrUpload` wanneer toegepast op een bestaand document) werkt niet hetzelfde in alle velden. Biedt `merge` in het bijzonder geen ondersteuning voor het samenvoegen van elementen binnen een verzameling. Deze beperking bestaat voor verzamelingen van primitieve typen en complexe verzamelingen. Als u een verzameling wilt bijwerken, moet u de volledige verzamelingswaarde ophalen, wijzigingen aanbrengen en vervolgens de nieuwe verzameling opnemen in de API-aanvraag voor index.

## <a name="searching-complex-fields"></a>Zoeken naar complexe velden

Zoekexpressies in vrije vorm werken zoals verwacht met complexe typen. Als een doorzoekbaar veld of subveld ergens in een document overeenkomt, is het document zelf een overeenkomst.

Query's worden genuanceerder wanneer u meerdere termen en operatoren hebt en in sommige termen veldnamen zijn opgegeven, zoals mogelijk is met de [syntaxis van Lucene](query-lucene-syntax.md). Met deze query wordt bijvoorbeeld geprobeerd twee termen, 'Portland' en 'OF', af te koppelen aan twee subvelden van het veld Adres:

    search=Address/City:Portland AND Address/State:OR

Query's als deze zijn *niet gecorreleerd* voor full-text zoeken, in tegenstelling tot filters. In filters worden query's over subvelden van een complexe [ `any` verzameling `all` ](search-query-odata-collection-operators.md)gecorreleerd met behulp van bereikvariabelen in of . De Lucene query hierboven retourneert documenten met zowel "Portland, Maine" en "Portland, Oregon", samen met andere steden in Oregon. Dit gebeurt omdat elke component van toepassing is op alle waarden van het veld in het hele document, dus er is geen concept van een "huidig subdocument". Zie [OData-verzamelingsfilters in Azure Cognitive Search](search-query-understand-collection-filters.md)begrijpen voor meer informatie hierover.

## <a name="selecting-complex-fields"></a>Complexe velden selecteren

De `$select` parameter wordt gebruikt om te kiezen welke velden worden geretourneerd in de zoekresultaten. Als u deze parameter wilt gebruiken om specifieke subvelden van een complex veld te`/`selecteren, neemt u het bovenliggende veld en het subveld op dat wordt gescheiden door een slash ( ).

    $select=HotelName, Address/City, Rooms/BaseRate

Velden moeten worden gemarkeerd als Opvraagbaar in de index als u ze in de zoekresultaten wilt opnemen. Alleen velden die als Opvraagbaar `$select` zijn gemarkeerd, kunnen in een instructie worden gebruikt.

## <a name="filter-facet-and-sort-complex-fields"></a>Complexe velden filteren, faceten en sorteren

Dezelfde [OData-padsyntaxis](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor filtering en veldzoekopdrachten, kan ook worden gebruikt voor het facenen, sorteren en selecteren van velden in een zoekaanvraag. Voor complexe typen gelden regels die bepalen welke subvelden kunnen worden gemarkeerd als sorteerbaar of facetable. Zie de [referentie voor indexAPI's maken](/rest/api/searchservice/create-index)voor meer informatie over deze regels.

### <a name="faceting-sub-fields"></a>Subvelden voor het faceeren

Elk subveld kan als facetable worden gemarkeerd, tenzij het van type `Edm.GeographyPoint` is of `Collection(Edm.GeographyPoint)`.

De documenttellingen die in de facetresultaten worden geretourneerd, worden berekend voor het bovenliggende document (een hotel), niet voor de subdocumenten in een complexe verzameling (kamers). Stel dat een hotel 20 kamers van type "suite" heeft. Gezien deze facet parameter `facet=Rooms/Type`, zal het facet tellen één voor het hotel, niet 20 voor de kamers.

### <a name="sorting-complex-fields"></a>Complexe velden sorteren

Sorteerbewerkingen zijn van toepassing op documenten (Hotels) en niet op subdocumenten (Kamers). Wanneer u een complexe typecollectie hebt, zoals Kamers, is het belangrijk om te beseffen dat u helemaal niet op kamers sorteren. In feite kun je niet sorteren op een collectie.

Sorteerbewerkingen werken wanneer velden één waarde per document hebben, of het veld nu een eenvoudig veld is of een subveld in een complex type. Het `Address/City` is bijvoorbeeld toegestaan om te worden gesorteerd omdat er slechts `$orderby=Address/City` één adres per hotel is, dus zal hotels sorteren op stad.

### <a name="filtering-on-complex-fields"></a>Filteren op complexe velden

U verwijzen naar subvelden van een complex veld in een filterexpressie. Gebruik gewoon dezelfde [OData-padsyntaxis](query-odata-filter-orderby-syntax.md) die wordt gebruikt voor het maken, sorteren en selecteren van velden. Het volgende filter retourneert bijvoorbeeld alle hotels in Canada:

    $filter=Address/Country eq 'Canada'

Als u wilt filteren op een complex verzamelveld, u een **lambda-expressie** gebruiken met de [ `any` operatoren en `all` de operatoren.](search-query-odata-collection-operators.md) In dat geval is de **bereikvariabele** van de lambda-expressie een object met subvelden. U verwijzen naar die subvelden met de standaard syntaxis van het OData-pad. Het volgende filter retourneert bijvoorbeeld alle hotels met ten minste één luxe kamer en alle rookvrije kamers:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Net als bij eenvoudige velden op het hoogste niveau kunnen eenvoudige subvelden van complexe velden `true` alleen in filters worden opgenomen als ze het **filterbare** kenmerk hebben ingesteld op in de indexdefinitie. Zie de referentie [voor indexAPI maken voor](/rest/api/searchservice/create-index)meer informatie .

## <a name="next-steps"></a>Volgende stappen

Probeer de [gegevensset Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) in de wizard **Gegevens importeren.** U hebt de Cosmos DB-verbindingsgegevens in het leesthema nodig om toegang te krijgen tot de gegevens.

Met die informatie in de hand, uw eerste stap in de wizard is het maken van een nieuwe Azure Cosmos DB-gegevensbron. Verderop in de wizard ziet u een index met complexe typen wanneer u op de doelindexpagina komt. Maak en laad deze index en voer query's uit om de nieuwe structuur te begrijpen.

> [!div class="nextstepaction"]
> [Snelstart: wizard portal voor importeren, indexeren en query's](search-get-started-portal.md)
