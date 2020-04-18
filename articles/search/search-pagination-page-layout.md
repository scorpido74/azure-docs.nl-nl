---
title: Werken met zoekresultaten
titleSuffix: Azure Cognitive Search
description: Structureer en sorteer zoekresultaten, ontvang een documenttelling en voeg inhoudsnavigatie toe aan zoekresultaten in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f815003449f0600bce1cb8927b92b85b51b09a1
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641622"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Werken met zoekresultaten in Azure Cognitive Search

In dit artikel wordt uitgelegd hoe u een queryantwoord krijgen dat terugkomt met een totaal aantal overeenkomende documenten, paginaresultaten, gesorteerde resultaten en termen met op treffers gemarkeerde termen.

De structuur van een antwoord wordt bepaald door parameters in de query: [Zoekdocument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) in de REST API of [documentsearchresultklasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) in de .NET SDK.

## <a name="result-composition"></a>Resultaatsamenstelling

Hoewel een zoekdocument uit een groot aantal velden kan bestaan, zijn er meestal slechts een paar nodig om elk document in de resultaatset weer te geven. Als u in een `$select=<field list>` queryverzoek aangeeft welke velden in het antwoord worden weergegeven. Een veld moet worden toegeschreven **als Opvraagbaar** in de index om in een resultaat te worden opgenomen. 

Velden die het beste werken, zijn velden die contrasteren en onderscheid maken tussen documenten en voldoende informatie bieden om een klikreactie van de gebruiker uit te nodigen. Op een e-commercesite kan het een productnaam, beschrijving, merk, kleur, grootte, prijs en beoordeling zijn. Voor de ingebouwde steekproef steekproef hotels-sample-index zijn dit mogelijk velden in het volgende voorbeeld:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Als u afbeeldingsbestanden wilt opnemen in een resultaat, zoals een productfoto of -logo, slaat u deze op buiten Azure Cognitive Search, maar neemt u een veld op in uw index om naar de afbeeldings-URL in het zoekdocument te verwijzen. Voorbeeldindexen die afbeeldingen in de resultaten ondersteunen, zijn onder andere de **demo van realestate-sample-us,** te zien in deze [quickstart](search-create-app-portal.md)en de [demo-app van New York City Jobs](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Resultaten pagineren

Standaard retourneert de zoekmachine tot de eerste 50 overeenkomsten, zoals bepaald door de zoekscore als de query full text search is, of in een willekeurige volgorde voor exacte overeenkomende query's.

Als u een ander aantal `$top` overeenkomende documenten wilt retourneren, voegt u en `$skip` parameters toe aan de queryaanvraag. In de volgende lijst wordt de logica uitgelegd.

+ Toevoegen `$count=true` om een aantal van het totale aantal overeenkomende documenten binnen een index te krijgen.

+ Retourneer de eerste set van 15 overeenkomende documenten plus een aantal totale overeenkomsten:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Return de tweede set, het overslaan van de `$top=15&$skip=15`eerste 15 naar de volgende 15 te krijgen: . Doe hetzelfde voor de derde set van 15:`$top=15&$skip=30`

De resultaten van paginated queries zijn niet gegarandeerd stabiel als de onderliggende index verandert. Paging wijzigt `$skip` de waarde van elke pagina, maar elke query is onafhankelijk en werkt op de huidige weergave van de gegevens zoals deze bestaat in de index op querytijd (met andere woorden, er is geen caching of momentopname van de resultaten, zoals die gevonden in een database voor algemeen gebruik).
 
Hieronder volgt een voorbeeld van hoe u duplicaten krijgen. Ga uit van een index met vier documenten:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Stel nu dat u resultaten twee tegelijk wilt terugkrijgen, geordend op beoordeling. U voert deze query uit om de `$top=2&$skip=0&$orderby=rating desc`eerste pagina met resultaten te krijgen: , de volgende resultaten produceren:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
Ga er bij de service van uit dat er `{ "id": "5", "rating": 4 }`een vijfde document wordt toegevoegd aan de index tussen queryoproepen: .  Kort daarna voert u een query uit `$top=2&$skip=2&$orderby=rating desc`om de tweede pagina op te halen: , en de volgende resultaten te krijgen:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Merk op dat document 2 twee keer wordt opgehaald. Dit komt omdat het nieuwe document 5 een grotere waarde heeft voor beoordeling, dus het sorteert vóór document 2 en landt op de eerste pagina. Hoewel dit gedrag onverwacht kan zijn, is het typerend voor hoe een zoekmachine zich gedraagt.

## <a name="ordering-results"></a>Resultaten ordenen

Voor zoekopdrachten in volledige tekst worden de resultaten automatisch gerangschikt op basis van een zoekscore, berekend op basis van de termfrequentie en nabijheid in een document, waarbij hogere scores naar documenten gaan die meer of sterkere overeenkomsten hebben op een zoekterm. 

Zoekscores geven een algemeen gevoel van relevantie over, dat de sterkte van de overeenkomst weerspiegelt in vergelijking met andere documenten in dezelfde resultatenset. Scores zijn niet altijd consistent van de ene query naar de volgende, dus als u met query's werkt, u kleine verschillen opmerken in de manier waarop zoekdocumenten worden geordend. Er zijn verschillende verklaringen waarom dit zou kunnen gebeuren.

| Oorzaak | Beschrijving |
|-----------|-------------|
| Datavolatiliteit | Indexinhoud varieert wanneer u documenten toevoegt, wijzigt of verwijdert. Termfrequenties worden gewijzigd naarmate indexupdates in de loop van de tijd worden verwerkt, wat van invloed is op de zoekscores van overeenkomende documenten. |
| Meerdere replica's | Voor services met meerdere replica's worden query's parallel aan elke replica uitgegeven. De indexstatistieken die worden gebruikt om een zoekscore te berekenen, worden berekend op basis van een replica, waarbij de resultaten worden samengevoegd en geordend in het queryantwoord. Replica's zijn meestal spiegels van elkaar, maar statistieken kunnen verschillen als gevolg van kleine verschillen in staat. Een replica kan bijvoorbeeld documenten hebben verwijderd die bijdragen aan hun statistieken, die zijn samengevoegd uit andere replica's. Doorgaans zijn verschillen in statistieken per replica meer merkbaar in kleinere indexen. |
| Identieke scores | Als meerdere documenten dezelfde score hebben, kan elk van deze documenten eerst worden weergegeven.  |

### <a name="consistent-ordering"></a>Consistente bestelling

Gezien de flex in het bestellen van resultaten, wilt u misschien andere opties verkennen als consistentie een toepassingsvereiste is. De eenvoudigste benadering is sorteren op een veldwaarde, zoals beoordeling of datum. Voor scenario's waarin u wilt sorteren op een specifiek veld, zoals [ `$orderby` ](query-odata-filter-orderby-syntax.md)een classificatie of datum, u expliciet een expressie definiëren, die kan worden toegepast op elk veld dat is geïndexeerd als **Sorteerbaar**.

Een andere optie is het gebruik van een [aangepast scoreprofiel](index-add-scoring-profiles.md). Scoreprofielen geven u meer controle over de rangschikking van items in zoekresultaten, met de mogelijkheid om overeenkomsten in specifieke velden te promoten. De aanvullende scorelogica kan helpen bij het overschrijven van kleine verschillen tussen replica's, omdat de zoekscores voor elk document verder uit elkaar liggen. Wij raden het [ranking algoritme](index-ranking-similarity.md) voor deze aanpak.

## <a name="hit-highlighting"></a>Markeren

Hithighlighting verwijst naar tekstopmaak (zoals vetgedrukte of gele hooglichten) die is toegepast op overeenkomende termen in een resultaat, waardoor de overeenkomst gemakkelijk kan worden gevonden. Instructies voor het markeren van treffers worden op de [queryaanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents)gegeven. De zoekmachine omsluit de overeenkomende `highlightPreTag` `highlightPostTag`term in tags en , en uw code verwerkt het antwoord (bijvoorbeeld het toepassen van een vet lettertype).

Opmaak wordt toegepast op hele term query's. In het volgende voorbeeld worden de termen "zanderig", "zand", "stranden", "strand" gevonden in het veld Beschrijving, gemarkeerd voor markeringen. Query's die query-uitbreiding in de engine activeren, zoals fuzzy en wildcard search, hebben beperkte ondersteuning voor hithighlighting.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nieuw gedrag (vanaf 15 juli)

Diensten die na 15 juli 2020 zijn gemaakt, bieden een andere aandachtservaring. Services die vóór die datum zijn gemaakt, veranderen niet in hun markeringsgedrag. 

Met het nieuwe gedrag:

* Alleen zinnen die overeenkomen met de volledige woordgroepquery worden geretourneerd. De query "super bowl" zal hoogtepunten als volgt retourneren:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Merk op dat de term *kom chips* geen highlighting heeft omdat deze niet overeenkomt met de volledige zin.
  
* Het is mogelijk om de fragmentgrootte aan te geven die is geretourneerd voor de markering. De grootte van het fragment is opgegeven als aantal tekens (maximaal 1000 tekens).

Wanneer u klantcode schrijft die hithighlighting implementeert, moet u zich bewust zijn van deze wijziging. Houd er rekening mee dat dit geen invloed op u heeft, tenzij u een volledig nieuwe zoekservice maakt.

## <a name="next-steps"></a>Volgende stappen

Als u snel een zoekpagina voor uw client wilt genereren, u de volgende opties overwegen:

+ [Application Generator,](search-create-app-portal.md)in de portal, maakt een HTML-pagina met een zoekbalk, gefacetteerde navigatie en resultaten gebied dat afbeeldingen bevat.
+ [Maak uw eerste app in C#](tutorial-csharp-create-first-app.md) is een zelfstudie die een functionele client bouwt. Voorbeeldcode toont pagina's met pagina's, markeringen en sorteren.

Verschillende code voorbeelden omvatten een web front-end interface, die u hier vinden: [New York City Jobs demo app,](https://aka.ms/azjobsdemo) [JavaScript sample code met een live demo site,](https://github.com/liamca/azure-search-javascript-samples)en [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
