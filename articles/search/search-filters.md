---
title: Filters voor het bereik van zoek resultaten in een index-Azure Search
description: Filteren op beveiligings identiteit van gebruiker, taal, geo-locatie of numerieke waarden om Zoek resultaten te verminderen voor query's in Azure Search, een gehoste service voor zoeken in de Cloud op Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 49af6f1f535df098aa45cccd7e2d629ff6ccef50
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649854"
---
# <a name="filters-in-azure-search"></a>Filters in Azure Search 

Een *filter* biedt criteria voor het selecteren van documenten die worden gebruikt in een Azure search query. Niet-gefilterde Zoek opdrachten bevatten alle documenten in de index. Een filter bereikt een zoek query op een subset van documenten. Een filter kan bijvoorbeeld de zoek opdracht in volledige tekst beperken tot alleen die producten met een specifiek merk of dezelfde kleur, tegen prijs punten boven een bepaalde drempel waarde.

Bij sommige zoek functies worden filter vereisten opgelegd als onderdeel van de implementatie, maar u kunt filters op elk gewenst moment gebruiken om de zoek actie te beperken met behulp van *op waarden gebaseerde* criteria (met bereik zoeken naar product type "boeken" voor categorie "niet-fictief" gepubliceerd door " Simon & Schuster ").

Als uw doel in plaats daarvan gericht is op specifieke gegevens *structuren* (bereik zoeken naar een klant-beoordelingen veld), zijn er alternatieve methoden die hieronder worden beschreven.

## <a name="when-to-use-a-filter"></a>Wanneer u een filter wilt gebruiken

Filters zijn basis voor verschillende zoek functies, waaronder ' zoeken in de buurt ', facet navigatie en beveiligings filters die alleen de documenten tonen die een gebruiker mag zien. Als u een van deze functies implementeert, is een filter vereist. Het is het filter dat is gekoppeld aan de zoek query die de geolocatie coördinaten levert, de facet categorie die door de gebruiker is geselecteerd of de beveiligings-ID van de aanvrager.

Voorbeeld scenario's zijn onder andere:

1. Gebruik een filter om uw index te segmenteren op basis van gegevens waarden in de index. Op basis van een schema met City, Type behuizing en voorzieningen kunt u een filter maken om expliciet documenten te selecteren die voldoen aan uw criteria (in Seattle, condos, afgebakend). 

   Zoek opdracht in volledige tekst met dezelfde invoer levert vaak vergelijk bare resultaten op, maar een filter is nauw keuriger in dat een exacte overeenkomst van de filter term met inhoud in uw index vereist. 

2. Gebruik een filter als de zoek ervaring een filter vereiste heeft:

   * [Facet navigatie](search-faceted-navigation.md) maakt gebruik van een filter om de facet categorie die door de gebruiker is geselecteerd, terug te geven.
   * Geo-search maakt gebruik van een filter om de coördinaten van de huidige locatie door te geven in apps die dichtbij zoeken. 
   * Beveiligings filters geven beveiligings-id's door als filter criteria, waarbij een overeenkomst in de index fungeert als een proxy voor toegangs rechten voor het document.

3. Gebruik een filter als u zoek criteria wilt gebruiken voor een numeriek veld. 

   Numerieke velden kunnen worden opgehaald in het document en worden weer gegeven in de zoek resultaten, maar ze worden niet doorzoekbaar (onderhevig aan Zoek opdrachten in volledige tekst). Als u selectie criteria op basis van numerieke gegevens nodig hebt, gebruikt u een filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatieve methoden voor het verkleinen van het bereik

Als u een beperkend effect wilt hebben in de zoek resultaten, zijn de filters niet uw enige keuze. Deze alternatieven kunnen beter passen, afhankelijk van uw doel stelling:

 + `searchFields`query parameter Pegs zoek naar specifieke velden. Als uw index bijvoorbeeld afzonderlijke velden voor Engelse en Spaanse beschrijvingen bevat, kunt u searchFields gebruiken om te richten welke velden u moet gebruiken voor het zoeken in volledige tekst. 

+ `$select`para meter wordt gebruikt om op te geven welke velden in een resultatenset moeten worden meegenomen, waardoor het antwoord effectief wordt afgekapt voordat het naar de aanroepende toepassing wordt verzonden. Met deze para meter wordt de query niet verfijnd of wordt de document verzameling niet verminderd, maar als een kleiner antwoord uw doel is, is deze para meter een optie om rekening mee te houden. 

Zie [zoeken naar documenten > aanvragen > query parameters](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)voor meer informatie over para meters.


## <a name="how-filters-are-executed"></a>Hoe filters worden uitgevoerd

Bij het uitvoeren van query's accepteert een filter-parser criteria als invoer, zet de expressie om in atomische Boole-expressies die worden weer gegeven als een structuur en evalueert vervolgens de filter structuur over filter bare velden in een index.

Filteren vindt in combi natie met zoeken plaats in de documenten die in de downstream-verwerking moeten worden meegenomen voor het ophalen van documenten en relevantie punten. Bij het koppelen met een zoek reeks, vermindert het filter de ingetrokken set van de volgende zoek bewerking effectief. Wanneer u alleen gebruikt (bijvoorbeeld wanneer de query reeks leeg `search=*`is), is de filter criteria de enige invoer. 

## <a name="defining-filters"></a>Filters definiëren

Filters zijn OData-expressies, die worden gegeleeerd met behulp [van een subset van Odata v4-syntaxis die wordt ondersteund in azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

U kunt voor elke **Zoek** bewerking één filter opgeven, maar het filter zelf kan meerdere velden bevatten, meerdere criteria en als u een functie **ismatch** gebruikt, meerdere zoek expressies in volledige tekst. In een filter expressie met meerdere delen kunt u predikaten in een wille keurige volg orde opgeven (onderhevig aan de voor waarden van de operator prioriteit). Er is geen merk bare prestatie verbetering als u predikaten probeert te rangschikken in een bepaalde volg orde.

Een van de limieten voor een filter expressie is de maximale grootte van de aanvraag. De volledige aanvraag, inclusief het filter, kan Maxi maal 16 MB aan POST of 8 KB voor GET zijn. Er is ook een limiet voor het aantal componenten in uw filter expressie. Als u honderden componenten hebt, is het een goed idee om de limiet uit te voeren. We raden u aan uw toepassing zodanig te ontwerpen dat er geen filters van een ongebonden grootte worden gegenereerd.

De volgende voor beelden vertegenwoordigen prototypen filter definities in verschillende Api's.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Gebruiks patronen filteren

De volgende voor beelden illustreren verschillende gebruiks patronen voor filter scenario's. Zie [OData-expressie syntaxis > voor beelden](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)voor meer ideeën.

+ Zelfstandige **$filter**, zonder query teken reeks, handig wanneer de filter expressie volledig kan kwalificeren op interessante documenten. Zonder een query reeks is er geen lexicale of linguïstische analyse, geen scoreing en geen classificatie. U ziet dat de zoek teken reeks slechts een sterretje is, wat betekent dat alle documenten overeenkomen.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combi natie van query teken reeks en **$filter**, waarbij het filter de subset maakt en de query reeks voorziet in de term invoer voor zoeken in volledige tekst via de gefilterde subset. Het gebruik van een filter met een query reeks is het meest voorkomende gebruiks patroon.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Samengestelde query's, gescheiden door "of", elk met zijn eigen filter criteria (bijvoorbeeld ' Beagles ' in ' hond ' of ' Siamese ' in ' kat '). Expressies die worden `or` gecombineerd met worden afzonderlijk geëvalueerd, waarbij de samen voeging van documenten die overeenkomen met elke expressie terug in het antwoord wordt verzonden. Dit gebruiks patroon wordt bereikt met behulp van de `search.ismatchscoring` functie. U kunt ook de niet-Score versie `search.ismatch`gebruiken.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Het is ook mogelijk om zoeken `search.ismatchscoring` in volledige tekst te combi neren met filters met behulp `and` van in plaats van `or`, maar dit is functioneel `search` gelijk aan het gebruik van de para meters en `$filter` in een zoek opdracht. De volgende twee query's produceren bijvoorbeeld hetzelfde resultaat:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Volg deze artikelen voor uitgebreide informatie over specifieke gebruiks voorbeelden:

+ [Facetfilters](search-filters-facets.md)
+ [Taalfilters](search-filters-language.md)
+ [Beveiligingsbeperkingen](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Veld vereisten voor filteren

In de REST API is filteren standaard *ingeschakeld* voor eenvoudige velden. Filter bare velden verg Roten index grootte; Zorg ervoor dat u `"filterable": false` velden instelt die u niet wilt gebruiken in een filter. Zie [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over de instellingen voor veld definities.

In de .NET SDK is het filterable standaard *uitgeschakeld* . U kunt een veld filterbaar maken door de [eigenschap IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) van het bijbehorende [veld](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) object in te `true`stellen op. U kunt dit ook declaratief doen met behulp van het [kenmerk IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). In het onderstaande voor beeld is het kenmerk ingesteld op de `BaseRate` eigenschap van een model klasse die is toegewezen aan de index definitie.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Het maken van een bestaand veld filteren

U kunt bestaande velden niet wijzigen om ze te kunnen filteren. In plaats daarvan moet u een nieuw veld toevoegen of de index opnieuw samen stellen. Zie [een Azure search index opnieuw samen stellen](search-howto-reindex.md)voor meer informatie over het opnieuw opbouwen van een index of het opnieuw invullen van velden.

## <a name="text-filter-fundamentals"></a>Grond beginselen van tekst filters

Tekst filters komen overeen met teken reeks velden op basis van letterlijke teken reeksen die u in het filter opgeeft. In tegens telling tot zoeken in volledige tekst is er geen lexicale analyse of woord afbreking voor tekst filters, zodat vergelijkingen alleen voor exacte overeenkomsten gelden. Stel bijvoorbeeld dat een veld *f* de waarde ' zonnige dag ' `$filter=f eq 'Sunny'` bevat, niet overeenkomt `$filter=f eq 'sunny day'` met, maar wel. 

Teken reeksen zijn hoofdletter gevoelig. Er bevindt zich geen kleine letters in hoofd letters: `$filter=f eq 'Sunny day'` er wordt geen ' zonnige dag ' gevonden.

### <a name="approaches-for-filtering-on-text"></a>Benaderingen voor het filteren op tekst

| Methode | Description | Wanneer gebruikt u dit? |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Een functie die overeenkomt met een veld in een gescheiden lijst met teken reeksen. | Aanbevolen voor [beveiligings filters](search-security-trimming-for-azure-search.md) en voor filters waarbij veel onbewerkte tekst waarden moeten overeenkomen met een teken reeks veld. De functie **Search.in** is ontworpen voor snelheid en is veel sneller dan expliciet het veld vergelijken met de teken reeks `eq` met `or`behulp van en. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Een functie waarmee u Zoek opdrachten in volledige tekst kunt combi neren met strikt Booleaanse filter bewerkingen in dezelfde filter-expressie. | Gebruik **Search. ismatch** (of het equivalente Score-item **Search. ismatchscoring**) als u meerdere zoek filter combinaties wilt gebruiken in één aanvraag. U kunt deze ook voor een *contains* -filter gebruiken om te filteren op een gedeeltelijke teken reeks binnen een grotere teken reeks. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Een door de gebruiker gedefinieerde expressie die bestaat uit velden, Opera tors en waarden. | Gebruik deze instelling als u exacte overeenkomsten wilt zoeken tussen een teken reeks veld en een teken reeks waarde. |

## <a name="numeric-filter-fundamentals"></a>Basis beginselen van numerieke filters

Numerieke velden bevinden `searchable` zich niet in de context van zoeken in volledige tekst. Alleen teken reeksen zijn onderhevig aan Zoek opdrachten in volledige tekst. Als u bijvoorbeeld 99,99 als zoek term invoert, ontvangt u geen back-upitems van $99,99. In plaats daarvan ziet u items met het nummer 99 in de teken reeks velden van het document. Als u numerieke gegevens hebt, moet u er dus voor zorgen dat u deze gebruikt voor filters, met inbegrip van bereiken, facetten, groepen enzovoort. 

Documenten die numerieke velden (prijs, grootte, SKU, ID) bevatten, bieden deze waarden in Zoek resultaten als het veld is `retrievable`gemarkeerd. Het punt hier is dat zoeken in volledige tekst zelf niet van toepassing is op numerieke veld typen.

## <a name="next-steps"></a>Volgende stappen

Probeer eerst de **Verkenner** in de portal te doorzoeken om query's met **$filter** -para meters te verzenden. De voor [beeld-index van onroerend](search-get-started-portal.md) goed levert interessante resultaten voor de volgende gefilterde query's wanneer u ze in de zoek balk plakt:

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

Als u meer voor beelden wilt gebruiken, raadpleegt u de syntaxis van de [OData-filter expressie > voor beelden](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Zie ook

+ [De manier waarop zoeken in volledige tekst werkt in Azure Search](search-lucene-query-architecture.md)
+ [Zoeken naar documenten REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Vereenvoudigde querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
