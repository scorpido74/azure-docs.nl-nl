---
title: Filteren op zoekresultaten
titleSuffix: Azure Cognitive Search
description: Filter op gebruikersbeveiligingsidentiteit, taal, geolocatie of numerieke waarden om zoekresultaten op query's in Azure Cognitive Search, een gehoste cloudzoekservice op Microsoft Azure, te verminderen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191005"
---
# <a name="filters-in-azure-cognitive-search"></a>Filters in Azure Cognitive Search 

Een *filter* bevat criteria voor het selecteren van documenten die worden gebruikt in een Azure Cognitive Search-query. Ongefilterd zoeken bevat alle documenten in de index. Een filter scopet een zoekopdracht naar een subset van documenten. Een filter kan bijvoorbeeld zoeken in volledige tekst beperken tot alleen die producten met een specifiek merk of kleur, tegen prijspunten boven een bepaalde drempelwaarde.

Sommige zoekervaringen leggen filtervereisten op als onderdeel van de implementatie, maar u filters gebruiken wanneer u zoeken wilt beperken met behulp van *op waarde gebaseerde* criteria (zoeken naar producttype boeken voor categorie non-fictie gepubliceerd door Simon & Schuster).

Als in plaats daarvan uw doel is gericht zoeken op specifieke *gegevensstructuren* (scoping zoeken naar een klant-reviews veld), zijn er alternatieve methoden, hieronder beschreven.

## <a name="when-to-use-a-filter"></a>Wanneer een filter gebruiken

Filters zijn fundamenteel voor verschillende zoekervaringen, waaronder 'vind mij bij de buurt', gefacetteerde navigatie en beveiligingsfilters die alleen die documenten weergeven die een gebruiker mag zien. Als u een van deze ervaringen implementeert, is een filter vereist. Het is het filter dat is gekoppeld aan de zoekopdracht dat de geolocatiecoördinaten, de facetcategorie die door de gebruiker is geselecteerd of de beveiligings-id van de aanvrager biedt.

Voorbeelden van scenario's zijn de volgende:

1. Gebruik een filter om uw index te segmenteren op basis van gegevenswaarden in de index. Als u een schema met stad, woningtype en voorzieningen krijgt, u een filter maken om expliciet documenten te selecteren die aan uw criteria voldoen (in Seattle, condos, waterkant). 

   Zoeken in volledige tekst met dezelfde ingangen levert vaak vergelijkbare resultaten op, maar een filter is nauwkeuriger omdat het een exacte overeenkomst van de filterterm vereist met inhoud in uw index. 

2. Gebruik een filter als de zoekervaring wordt geleverd met een filtervereiste:

   * [Gefacetteerde navigatie](search-faceted-navigation.md) maakt gebruik van een filter om de door de gebruiker geselecteerde facetcategorie terug te geven.
   * Geo-search maakt gebruik van een filter om coördinaten van de huidige locatie door te geven in 'zoek bij mij in de buurt'-apps. 
   * Beveiligingsfilters passeren beveiligings-id's als filtercriteria, waarbij een overeenkomst in de index dient als proxy voor toegangsrechten voor het document.

3. Gebruik een filter als u zoekcriteria wilt op een numeriek veld. 

   Numerieke velden kunnen worden opgehaald in het document en kunnen afzonderlijk worden weergegeven in zoekresultaten, maar ze zijn niet afzonderlijk doorzoekbaar (onder voorbehoud van zoeken in volledige tekst). Als u selectiecriteria nodig hebt op basis van numerieke gegevens, gebruikt u een filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatieve methoden om het toepassingsgebied te beperken

Als u een beperkend effect in uw zoekresultaten wilt, zijn filters niet uw enige keuze. Deze alternatieven kunnen beter passen, afhankelijk van uw doelstelling:

 + `searchFields`queryparameterpinnen zoeken naar specifieke velden. Als uw index bijvoorbeeld afzonderlijke velden voor Engelse en Spaanse beschrijvingen bevat, u zoekvelden gebruiken om te targeten welke velden u moet gebruiken voor zoeken in volledige tekst. 

+ `$select`parameter wordt gebruikt om op te geven welke velden in een resultaatset moeten worden opgenomen, waarbij het antwoord effectief wordt bijgeknipt voordat u het naar de aanroepende toepassing verzendt. Deze parameter verfijnt de query niet of vermindert de documentverzameling niet, maar als een kleiner antwoord uw doel is, is deze parameter een optie om rekening mee te houden. 

Zie [Zoekdocumenten > queryparameters > aanvragen](/rest/api/searchservice/search-documents#query-parameters)voor meer informatie over een van beide parameters.


## <a name="how-filters-are-executed"></a>Hoe filters worden uitgevoerd

Bij querytijd accepteert een filterparser criteria als invoer, zet de expressie om in atomaire Booleaanse expressies die als een structuur worden weergegeven en evalueert vervolgens de filterstructuur over filterbare velden in een index.

Filteren gebeurt in combinatie met zoeken, in aanmerking komen welke documenten op te nemen in downstream verwerking voor het ophalen van documenten en relevantie scoren. In combinatie met een zoektekenreeks vermindert het filter effectief de terugroepset van de volgende zoekbewerking. Bij alleen gebruik (bijvoorbeeld wanneer de querytekenreeks leeg is), `search=*`is de filtercriteria de enige invoer. 

## <a name="defining-filters"></a>Filters definiëren
Filters zijn OData-expressies, gelede met behulp van een [subset van OData V4-syntaxis die wordt ondersteund in Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

U één filter opgeven voor elke **zoekbewerking,** maar het filter zelf kan meerdere velden, meerdere criteria en als u een **ismatchfunctie** gebruikt, meerdere zoekexpressies met volledige tekst bevatten. In een meerdelige filterexpressie u predicaten in elke volgorde opgeven (onder voorbehoud van de regels van operatorprioriteit). Er is geen merkbare winst in prestaties als u probeert om predicaten in een bepaalde opeenvolging te herschikken.

Een van de limieten voor een filterexpressie is de maximale groottelimiet van de aanvraag. Het volledige verzoek, inclusief het filter, kan maximaal 16 MB zijn voor POST, of 8 KB voor GET. Er is ook een limiet aan het aantal clausules in uw filterexpressie. Een goede vuistregel is dat als je honderden clausules hebt, je het risico loopt om tegen de limiet aan te lopen. We raden u aan uw toepassing zo te ontwerpen dat deze geen filters van onbegrensde grootte genereert.

De volgende voorbeelden vertegenwoordigen prototypische filterdefinities in verschillende API's.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Gebruikspatronen filteren

In de volgende voorbeelden worden verschillende gebruikspatronen voor filterscenario's geïllustreerd. Zie [OData-expressiesyntaxis > Voorbeelden voor](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)meer ideeën.

+ Zelfstandige **$filter**, zonder querytekenreeks, handig wanneer de filterexpressie documenten van belang volledig kan kwalificeren. Zonder querystring is er geen lexicale of taalkundige analyse, geen score en geen rangschikking. Let op de zoektekenreeks is slechts een sterretje, wat betekent "overeenkomen met alle documenten".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Combinatie van querytekenreeks en **$filter**, waarbij het filter de subset maakt en de querytekenreeks de termingangen voor volledige tekstzoeken over de gefilterde subset biedt. De toevoeging van termen (walking distance theaters) introduceert zoekscores in de resultaten, waar documenten die het beste overeenkomen met de voorwaarden hoger zijn gerangschikt. Het gebruik van een filter met een querytekenreeks is het meest voorkomende gebruikspatroon.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Samengestelde query's, gescheiden door "of", elk met zijn eigen filtercriteria (bijvoorbeeld 'beagles' in 'hond' of 'siamese' in 'kat'). Expressies `or` in combinatie met worden individueel geëvalueerd, waarbij de vereniging van documenten overeenkomt met elke expressie die in het antwoord wordt teruggestuurd. Dit gebruikspatroon wordt `search.ismatchscoring` bereikt door de functie. U ook de niet-scorende versie gebruiken, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Het is ook mogelijk om full-text zoeken via `search.ismatchscoring` te combineren met filters met `and` behulp van in plaats van `or`, maar dit is functioneel gelijk aan het gebruik van de `search` en `$filter` parameters in een zoekverzoek. De volgende twee query's leveren bijvoorbeeld hetzelfde resultaat op:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Follow-up met deze artikelen voor uitgebreide richtlijnen over specifieke use cases:

+ [Facetfilters](search-filters-facets.md)
+ [Taalfilters](search-filters-language.md)
+ [Beveiligingsbeperkingen](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Veldvereisten voor filteren

In de REST API is filterbaar standaard *ingeschakeld* voor eenvoudige velden. Filterbare velden vergroten de indexgrootte; zorg ervoor `"filterable": false` dat u instelt voor velden die u niet van plan bent om daadwerkelijk te gebruiken in een filter. Zie [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over instellingen voor velddefinities.

In de .NET SDK is de filterbare standaard *uitgeschakeld.* U een veld filterbaar maken door de [eigenschap IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) van het bijbehorende [veldobject](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) in te stellen op `true`. U dit ook declaratief doen met het [kenmerk IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In het onderstaande voorbeeld wordt `BaseRate` het kenmerk ingesteld op de eigenschap van een modelklasse die wordt toegewezen aan de indexdefinitie.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Een bestaand veld filterbaar maken

U bestaande velden niet wijzigen om ze filterbaar te maken. In plaats daarvan moet u een nieuw veld toevoegen of de index opnieuw opbouwen. Zie [Een Azure Cognitive Search-index opnieuw opbouwen](search-howto-reindex.md)voor meer informatie over het opnieuw opbouwen van een index of het opnieuw bevolken van velden.

## <a name="text-filter-fundamentals"></a>Fundamenten van tekstfilter

Tekstfilters koppelen tekenreeksvelden aan de letterlijke tekenreeksen die u in het filter opgeeft. In tegenstelling tot full-text zoeken, is er geen lexicale analyse of woordbrekend voor tekstfilters, dus vergelijkingen zijn alleen voor exacte overeenkomsten. Stel dat een veld *f* 'zonnige `$filter=f eq 'Sunny'` dag' bevat, niet overeenkomt, maar `$filter=f eq 'sunny day'` wel. 

Teksttekenreeksen zijn hoofdlettergevoelig. Er is geen onderhuls van hoofdletters: `$filter=f eq 'Sunny day'` zal niet vinden "zonnige dag".

### <a name="approaches-for-filtering-on-text"></a>Benaderingen voor het filteren op tekst

| Methode | Beschrijving | Wanneer gebruikt u dit? |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Een functie die overeenkomt met een veld met een afgebakende lijst met tekenreeksen. | Aanbevolen voor [beveiligingsfilters](search-security-trimming-for-azure-search.md) en voor filters waarbij veel ruwe tekstwaarden moeten worden gekoppeld aan een tekenreeksveld. De **search.in** functie is ontworpen voor snelheid en is veel sneller `eq` dan `or`het expliciet vergelijken van het veld met behulp van en . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Een functie waarmee u zoekbewerkingen met volledige tekst mengen met strikt Booleaanse filterbewerkingen in dezelfde filterexpressie. | Gebruik **search.ismatch** (of het scoreequivalent, **search.ismatchscoring)** wanneer u meerdere combinaties van zoekfiltercombinaties in één aanvraag wilt. U het ook gebruiken voor een *bevatfilter* om te filteren op een gedeeltelijke tekenreeks in een grotere tekenreeks. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Een door de gebruiker gedefinieerde expressie die bestaat uit velden, operatoren en waarden. | Gebruik dit wanneer u exacte overeenkomsten wilt vinden tussen een tekenreeksveld en een tekenreekswaarde. |

## <a name="numeric-filter-fundamentals"></a>Numerieke filterfundamenten

Numerieke velden `searchable` zijn niet in de context van full text search. Alleen tekenreeksen zijn onderworpen aan full text search. Als u bijvoorbeeld 99,99 als zoekterm invoert, krijgt u geen artikelen terug die zijn geprijsd op $ 99,99. In plaats daarvan ziet u items met het getal 99 in tekenreeksvelden van het document. Als u dus numerieke gegevens hebt, gaat u ervan uit dat u ze gebruikt voor filters, waaronder bereiken, facetten, groepen enzovoort. 

Documenten die numerieke velden (prijs, grootte, SKU, ID) bevatten, geven `retrievable`deze waarden op in de zoekresultaten als het veld is gemarkeerd . Het punt hier is dat full text search zelf niet van toepassing is op numerieke veldtypen.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst **Zoekverkenner** in de portal om query's met **$filter** parameters in te dienen. De [index voor het vastgoed-voorbeeld](search-get-started-portal.md) biedt interessante resultaten voor de volgende gefilterde query's wanneer u deze in de zoekbalk plakt:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Zie Syntaxis van [OData-filterexpressie > Voorbeelden](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)voor meer voorbeelden.

## <a name="see-also"></a>Zie ook

+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API voor documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Vereenvoudigde querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxis van Lucene query](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
