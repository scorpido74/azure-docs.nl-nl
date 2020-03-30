---
title: Facetfilters voor zoeknavigatie in apps
titleSuffix: Azure Cognitive Search
description: Filter criteria op gebruikersbeveiligingsidentiteit, geolocatie of numerieke waarden om zoekresultaten op query's in Azure Cognitive Search, een gehoste cloudzoekservice op Microsoft Azure, te verminderen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792899"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Een facetfilter bouwen in Azure Cognitive Search 

Gefacetteerde navigatie wordt gebruikt voor zelfsturende filtering op queryresultaten in een zoek-app, waar uw toepassing ui-besturingselementen biedt voor het zoeken naar groepen documenten (bijvoorbeeld categorieën of merken) en Azure Cognitive Search biedt de gegevensstructuur om de ervaring te onderdeinen. Bekijk in dit artikel snel de basisstappen voor het maken van een gefacetteerde navigatiestructuur die de zoekervaring ondersteunt die u wilt bieden. 

> [!div class="checklist"]
> * Velden kiezen voor filteren en faceen
> * Kenmerken instellen op het veld
> * De index- en laadgegevens samenstellen
> * Facetfilters toevoegen aan een query
> * Resultaten verwerken

Facetten zijn dynamisch en worden geretourneerd op een query. Zoekreacties brengen de facetcategorieën met zich mee die worden gebruikt om door de resultaten te navigeren. Als u niet bekend bent met facetten, is het volgende voorbeeld een illustratie van een facetnavigatiestructuur.

  ![](./media/search-filters-facets/facet-nav.png)

Nieuw bij gefacetteerde navigatie en wil je meer details? Zie [Hoe u gefacetteerde navigatie implementeert in Azure Cognitive Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Velden kiezen

Facetten kunnen worden berekend over velden met één waarde en verzamelingen. Velden die het beste werken in gefacetteerde navigatie hebben een lage kardinaliteit: een klein aantal verschillende waarden die zich herhalen in documenten in uw zoekcorpus (bijvoorbeeld een lijst met kleuren, landen/regio's of merknamen). 

Faceting is per veld ingeschakeld wanneer u de index maakt `facetable` door `true`het kenmerk in te stellen op . U moet het `filterable` kenmerk over `true` het algemeen ook instellen op dergelijke velden, zodat uw zoektoepassing op die velden kan filteren op basis van facetten die de eindgebruiker selecteert. 

Bij het maken van een index met de REST API wordt elk [veldtype](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) dat mogelijk kan worden gebruikt in gefacetteerde navigatie standaard gemarkeerd als: `facetable`

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numerieke `Edm.Int32`veldtypen: `Edm.Int64`,`Edm.Double`
+ Verzamelingen van de bovenstaande `Collection(Edm.String)` typen `Collection(Edm.Double)`(bijvoorbeeld of )

U kunt `Edm.GeographyPoint` `Collection(Edm.GeographyPoint)` geen gebruik maken of velden gebruiken in gefacetteerde navigatie. Facetten werken het beste op velden met een lage kardinaliteit. Vanwege de resolutie van geocoördinaten is het zeldzaam dat twee sets coördinaten gelijk zijn in een bepaalde gegevensset. Als zodanig worden facetten niet ondersteund voor geocoördinaten. Je zou een stad of regio veld nodig om facet op locatie.

## <a name="set-attributes"></a>Kenmerken instellen

Indexkenmerken die bepalen hoe een veld wordt gebruikt, worden toegevoegd aan afzonderlijke velddefinities in de index. In het volgende voorbeeld, velden met een lage kardinaliteit, nuttig voor faceting, bestaan uit: `category` (hotel, motel, hostel), `tags`en `rating`. Deze velden `filterable` hebben `facetable` de en kenmerken expliciet ingesteld in het volgende voorbeeld voor illustratieve doeleinden. 

> [!Tip]
> Als een best practice voor prestaties en opslag optimalisatie, zet faceting uit voor velden die nooit mogen worden gebruikt als een facet. In het bijzonder moeten tekenreeksvelden voor unieke waarden, zoals een `"facetable": false` id of productnaam, worden ingesteld om te voorkomen dat ze per ongeluk (en ineffectief) worden gebruikt in gefacetteerde navigatie.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Deze indexdefinitie wordt gekopieerd uit [Een Azure Cognitive Search-index maken met behulp van de REST API.](https://docs.microsoft.com/azure/search/search-create-index-rest-api) Het is identiek behalve oppervlakkige verschillen in de gebiedsdefinities. De `filterable` `facetable` en kenmerken worden expliciet `category` `tags`toegevoegd `parkingIncluded` `smokingAllowed`aan `rating` , , , en velden. In de `filterable` `facetable` praktijk, en zou standaard worden ingeschakeld op deze velden bij het gebruik van de REST API. Bij het gebruik van de .NET SDK moeten deze kenmerken expliciet worden ingeschakeld.

## <a name="build-and-load-an-index"></a>Een index bouwen en laden

Een tussenliggende (en misschien voor de hand liggende) stap is dat u de index moet [opbouwen en invullen](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) voordat u een query formuleert. We vermelden deze stap hier voor volledigheid. Een manier om te bepalen of de index beschikbaar is, is door de indexenlijst in het [portaal](https://portal.azure.com)te controleren .

## <a name="add-facet-filters-to-a-query"></a>Facetfilters toevoegen aan een query

Maak in toepassingscode een query die alle onderdelen van een geldige query opgeeft, inclusief zoekexpressies, facetten, filters, scoreprofielen, alles wat wordt gebruikt om een aanvraag te formuleren. In het volgende voorbeeld wordt een aanvraag gebouwd die facetnavigatie maakt op basis van het type accommodatie, beoordeling en andere voorzieningen.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Gefilterde resultaten retourneren op klikgebeurtenissen

Wanneer de eindgebruiker op een facetwaarde klikt, moet de handler voor de klikgebeurtenis een filterexpressie gebruiken om de intentie van de gebruiker te realiseren. Gezien `category` een facet, wordt het klikken op de `$filter` categorie "motel" uitgevoerd met een uitdrukking die accommodaties van dat type selecteert. Wanneer een gebruiker op 'motel' klikt om aan te geven dat `$filter=category eq 'motel'`alleen motels moeten worden weergegeven, bevat de volgende query die de toepassing verzendt .

In het volgende codefragment wordt categorie aan het filter toegevoegd als een gebruiker een waarde uit de categorie facet selecteert.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Als de gebruiker op een facetwaarde `tags`klikt voor een verzamelingsveld zoals bijvoorbeeld de waarde 'groep', moet uw toepassing de volgende filtersyntaxis gebruiken:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tips en oplossingen

### <a name="initialize-a-page-with-facets-in-place"></a>Een pagina met facetten initialiseren

Als u een pagina met facetten wilt initialiseren, u een query verzenden als onderdeel van de initialisatie van pagina's om de pagina met een eerste facetstructuur te zaaien.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Een facetnavigatiestructuur asynchroon van gefilterde resultaten behouden

Een van de uitdagingen met facet navigatie in Azure Cognitive Search is dat facetten bestaan alleen voor de huidige resultaten. In de praktijk is het gebruikelijk om een statische set facetten te behouden, zodat de gebruiker in omgekeerde volgorde kan navigeren, stappen kan volgen om alternatieve paden te verkennen via zoekinhoud. 

Hoewel dit een veel voorkomende use case is, is het niet iets wat de facet-navigatiestructuur momenteel out-of-the-box biedt. Ontwikkelaars die statische facetten willen, werken meestal rond de beperking door twee gefilterde query's uit te geven: de ene wordt gebruikt voor het maken van een statische lijst met facetten voor navigatiedoeleinden.

## <a name="see-also"></a>Zie ook

+ [Filters in Azure Cognitive Search](search-filters.md)
+ [Api voor indexrest maken](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [REST API voor documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents)
