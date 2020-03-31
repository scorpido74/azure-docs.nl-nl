---
title: Werken met zoekresultaten
titleSuffix: Azure Cognitive Search
description: Structureer en sorteer zoekresultaten, ontvang een documenttelling en voeg inhoudsnavigatie toe aan zoekresultaten in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 124f1ce3d30ce87d5e9d8fa027e5a7d6c0b3cb17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481599"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Werken met zoekresultaten in Azure Cognitive Search
In dit artikel vindt u richtlijnen voor het implementeren van standaardelementen van een pagina met zoekresultaten, zoals totale tellingen, het ophalen van documenten, sorteerorders en navigatie. Paginagerelateerde opties die gegevens of gegevens bijdragen aan uw zoekresultaten, worden opgegeven via de [zoekdocumentaanvragen](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) die naar uw Azure Cognitive Search-service zijn verzonden. 

In de REST-API bevatten aanvragen een GET-opdracht,pad en queryparameters die de service informeren over wat er wordt aangevraagd en hoe u het antwoord formuleren. In de .NET SDK is de gelijkwaardige API [de klasse DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Als u snel een zoekpagina voor uw klant wilt genereren, onderzoekt u de volgende opties:

+ Gebruik de [toepassingsgenerator](search-create-app-portal.md) in de portal om een HTML-pagina te maken met een zoekbalk, gefacetteerde navigatie en resultaatgebied.
+ Volg de [zelfstudie Van uw eerste app maken in C#](tutorial-csharp-create-first-app.md) om een functionele client te maken.

Verschillende code voorbeelden omvatten een web front-end interface, die u hier vinden: [New York City Jobs demo app,](https://aka.ms/azjobsdemo) [JavaScript sample code met een live demo site,](https://github.com/liamca/azure-search-javascript-samples)en [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Een geldige aanvraag bevat een aantal elementen, zoals een service-URL en pad, HTTP-werkwoord, `api-version`enzovoort. Voor beknoptheid hebben we de voorbeelden bijgesneden om alleen de syntaxis te markeren die relevant is voor paginatie. Zie AZURE Cognitive Search [REST API's](https://docs.microsoft.com/rest/api/searchservice)voor meer informatie over de syntaxis van de aanvraag .
>

## <a name="total-hits-and-page-counts"></a>Totaal aantal treffers en pagina’s

Het totale aantal resultaten dat van een query wordt geretourneerd en deze resultaten vervolgens in kleinere segmenten retourneren, is van fundamenteel belang voor vrijwel alle zoekpagina's.

![][1]

In Azure Cognitive Search `$count`gebruikt `$top`u `$skip` de , en parameters om deze waarden te retourneren. In het volgende voorbeeld wordt een voorbeeldaanvraag weergegeven voor het `@odata.count`totale aantal treffers in een index die "online-catalogus" wordt genoemd, geretourneerd als:

    GET /indexes/online-catalog/docs?$count=true

Documenten ophalen in groepen van 15 en ook het totale aantal hits weergeven, te beginnen op de eerste pagina:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Voor paginating-resultaten `$skip`moeten `$top` beide `$top` en , waar wordt `$skip` aangegeven hoeveel artikelen in een batch moeten worden geretourneerd, en wordt aangegeven hoeveel items u moet overslaan. In het volgende voorbeeld toont elke pagina de volgende 15 `$skip` items, aangegeven door de incrementele sprongen in de parameter.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Indeling

Op een pagina met zoekresultaten u een miniatuurafbeelding, een subset van velden en een koppeling naar een volledige productpagina weergeven.

 ![][2]

In Azure Cognitive Search `$select` gebruikt u en een [Search API-verzoek](https://docs.microsoft.com/rest/api/searchservice/search-documents) om deze ervaring te implementeren.

Ga als lid van een subset van velden voor een tegelindeling:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Afbeeldingen en mediabestanden zijn niet direct doorzoekbaar en moeten worden opgeslagen in een ander opslagplatform, zoals Azure Blob-opslag, om de kosten te verlagen. Definieer in de index en documenten een veld dat het URL-adres van de externe inhoud opslaat. U het veld vervolgens gebruiken als referentie voor afbeeldingen. De URL naar de afbeelding moet in het document staan.

Als u een productbeschrijvingspagina wilt ophalen voor een **onClick-gebeurtenis,** gebruikt u [Opzoekdocument](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) om de sleutel van het document door te geven om op te halen. Het gegevenstype van `Edm.String`de sleutel is . In dit voorbeeld is het *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sorteren op relevantie, beoordeling of prijs

Sorteer orders vaak standaard voor relevantie, maar het is gebruikelijk om alternatieve sorteerorders direct beschikbaar te maken, zodat klanten bestaande resultaten snel kunnen herschikken naar een andere rangorder.

 ![][3]

In Azure Cognitive Search is sorteren `$orderby` gebaseerd op de expressie, `"Sortable": true.` voor `$orderby` alle velden die zijn geïndexeerd als Een clausule is een OData-expressie. Zie [OData-expressiesyntaxis voor filters en order-door-clausules voor](query-odata-filter-orderby-syntax.md)informatie over syntaxis .

Relevantie wordt sterk geassocieerd met scoreprofielen. U de standaardscore gebruiken, die afhankelijk is van tekstanalyse en statistieken om alle resultaten te rangschikken, waarbij hogere scores naar documenten gaan met meer of sterkere overeenkomsten op een zoekterm.

Alternatieve sorteerorders worden meestal gekoppeld aan **onClick-gebeurtenissen** die terugbellen naar een methode die de sorteervolgorde bouwt. Bijvoorbeeld, gezien dit pagina-element:

 ![][4]

U maakt een methode die de geselecteerde sorteeroptie als invoer accepteert en retourneert een geordende lijst voor de criteria die aan die optie zijn gekoppeld.

 ![][5]

> [!NOTE]
> Hoewel de standaardscore voldoende is voor veel scenario's, raden we u aan de relevantie te baseren op een aangepast scoreprofiel. Een aangepast scoreprofiel geeft je een manier om items te promoten die beter zijn voor je bedrijf. Zie [Scoreprofielen toevoegen](index-add-scoring-profiles.md) voor meer informatie.
>

## <a name="hit-highlighting"></a>Markeren

U opmaak toepassen op overeenkomende termen in zoekresultaten, waardoor u de overeenkomst eenvoudig herkennen. Instructies voor het markeren van treffers worden op de [queryaanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents)gegeven. 

Opmaak wordt toegepast op hele term query's. Query's op gedeeltelijke voorwaarden, zoals fuzzy search of wildcard search die resulteren in query uitbreiding in de engine, kan geen gebruik maken van hit highlighting.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```

> [!IMPORTANT]
> Diensten die na 15 juli 2020 zijn gemaakt, bieden een andere aandachtservaring. Services die vóór die datum zijn gemaakt, veranderen niet in hun markeringsgedrag. Met deze wijziging worden alleen zinnen geretourneerd die overeenkomen met de volledige woordgroepquery. Ook is het mogelijk om de fragmentgrootte op te geven die is geretourneerd voor de markering.
>
> Wanneer u klantcode schrijft die hithighlighting implementeert, moet u zich bewust zijn van deze wijziging. Houd er rekening mee dat dit geen invloed op u heeft, tenzij u een volledig nieuwe zoekservice maakt.

## <a name="faceted-navigation"></a>Facetnavigatie

Zoeknavigatie is gebruikelijk op een resultatenpagina, vaak aan de zijkant of bovenkant van een pagina. In Azure Cognitive Search biedt gefacetteerde navigatie zelfgerichte zoekopdrachten op basis van vooraf gedefinieerde filters. Zie [Gefacetteerde navigatie in Azure Cognitive Search](search-faceted-navigation.md) voor meer informatie.

## <a name="filters-at-the-page-level"></a>Filters op paginaniveau

Als uw oplossingsontwerp speciale zoekpagina's voor specifieke typen inhoud heeft opgenomen (bijvoorbeeld een online retailtoepassing met afdelingen boven aan de pagina), u een [filterexpressie](search-filters.md) naast een **onClick-gebeurtenis** invoegen om een pagina in een vooraf gefilterde status te openen.

U een filter verzenden met of zonder zoekexpressie. In het volgende verzoek wordt bijvoorbeeld de merknaam gefilterd en worden alleen de documenten die overeenkomen met deze documenten teruggegeven.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Zie [Search Documents (Azure Cognitive Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) voor meer informatie over `$filter` expressies.

## <a name="see-also"></a>Zie ook

- [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice)
- [Indexbewerkingen](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Documentbewerkingen](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Gefacetteerde navigatie in Azure Cognitive Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
