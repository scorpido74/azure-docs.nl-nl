---
title: Werken met zoek resultaten
titleSuffix: Azure Cognitive Search
description: Structuur en sorteer de zoek resultaten, haal een document telling op en voeg inhouds navigatie toe aan Zoek resultaten in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c32e58a43b5409fd9f8ede536167d185270c6a22
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721571"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Werken met zoek resultaten in azure Cognitive Search
Dit artikel bevat richt lijnen voor het implementeren van standaard elementen van een pagina met zoek resultaten, zoals het totale aantal, het ophalen van documenten, het sorteren van orders en navigatie. Pagina opties die gegevens of informatie aan uw zoek resultaten bijdragen, worden opgegeven via de [Zoek documenten](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aanvragen die naar uw Azure Cognitive Search-service worden verzonden. 

In de REST API bestaan aanvragen uit een GET-opdracht, een pad en een query parameter die de service op de hoogte stellen van wat er wordt gevraagd en hoe u het antwoord kunt formuleren. In de .NET SDK is de equivalente API de [DocumentSearchResult-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Als u snel een zoek pagina voor uw client wilt genereren, bekijkt u de volgende opties:

+ Gebruik de [toepassings Generator](search-create-app-portal.md) in de portal om een HTML-pagina te maken met een zoek balk, facet navigatie en resultaten.
+ Volg de zelf studie [uw eerste app C# maken](tutorial-csharp-create-first-app.md) om een functionele client te maken.

Enkele voor beelden van code zijn een web-front-end-interface, die u hier kunt vinden: [New York City Jobs demo app](https://azjobsdemo.azurewebsites.net/), [Java script-voorbeeld code met een live demo site](https://github.com/liamca/azure-search-javascript-samples)en [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Een geldige aanvraag bevat een aantal elementen, zoals een service-URL en pad, HTTP-term `api-version`, enzovoort. Voor het kortings merk zijn de voor beelden verwijderd om alleen de syntaxis te markeren die relevant is voor de paginering. Zie voor meer informatie over de aanvraag syntaxis [Azure COGNITIVE Search rest-api's](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Totaal aantal treffers en pagina aantallen

Het totale aantal resultaten dat door een query wordt geretourneerd, wordt weer gegeven en deze resultaten worden geretourneerd in kleinere segmenten, is fundamenteel voor vrijwel alle zoek pagina's.

![][1]

In azure Cognitive Search gebruikt u de para meters `$count`, `$top`en `$skip` om deze waarden te retour neren. In het volgende voor beeld ziet u een voorbeeld aanvraag voor het totale aantal treffers in een index met de naam ' online-Catalog ', geretourneerd als `@odata.count`:

    GET /indexes/online-catalog/docs?$count=true

Documenten ophalen in groepen van 15 en ook de totale treffers weer geven, beginnend bij de eerste pagina:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Voor Paginating-resultaten zijn zowel `$top` als `$skip`vereist, waarbij `$top` specificeert hoeveel items in een batch moeten worden geretourneerd en `$skip` geeft aan hoeveel items moeten worden overgeslagen. In het volgende voor beeld toont elke pagina de volgende 15 items, aangegeven door de incrementele sprongen in de para meter `$skip`.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Indeling

Op een pagina met zoek resultaten wilt u mogelijk een miniatuur afbeelding, een subset van velden en een koppeling naar een volledige product pagina weer geven.

 ![][2]

In azure Cognitive Search gebruikt u `$select` en een [Search API-aanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents) om deze ervaring te implementeren.

Een subset van velden voor een tegel indeling retour neren:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Afbeeldingen en media bestanden kunnen niet rechtstreeks worden doorzocht en moeten worden opgeslagen in een ander opslag platform, zoals Azure Blob Storage, om de kosten te verlagen. In de index en documenten definieert u een veld waarin het URL-adres van de externe inhoud wordt opgeslagen. U kunt vervolgens het veld gebruiken als verwijzing naar een afbeelding. De URL naar de afbeelding moet in het document staan.

Als u een product beschrijvings pagina voor een **geonclick** gebeurtenis wilt ophalen, gebruikt u [opzoek document](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) voor het door geven van de sleutel van het document dat moet worden opgehaald. Het gegevens type van de sleutel is `Edm.String`. In dit voor beeld is dit *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sorteren op relevantie, waardering of prijs

Sorteer orders vaak standaard voor relevantie, maar het is gebruikelijk om alternatieve Sorteer volgorden beschikbaar te maken zodat klanten snel bestaande resultaten in een andere rang orde kunnen omzetten.

 ![][3]

In azure Cognitive Search is sorteren gebaseerd op de `$orderby`-expressie, voor alle velden die zijn geïndexeerd als `"Sortable": true.` een `$orderby`-component een OData-expressie is. Zie de [syntaxis van de OData-expressie voor filters en order-by-componenten](query-odata-filter-orderby-syntax.md)voor meer informatie over de syntaxis.

Relevantie wordt sterk geassocieerd met Score profielen. U kunt de standaard Score gebruiken, die afhankelijk is van tekst analyse en statistische gegevens om de volg orde van alle resultaten te rangschikken, met hogere scores naar documenten met meer of betere overeenkomsten voor een zoek term.

Alternatieve Sorteer volgorden worden meestal gekoppeld aan **OnClick** -gebeurtenissen die terugkeren naar een methode die de sorteer volgorde bouwt. Op basis van dit pagina-element:

 ![][4]

U maakt een methode die de geselecteerde sorteer optie als invoer accepteert en retourneert een geordende lijst voor de criteria die aan die optie zijn gekoppeld.

 ![][5]

> [!NOTE]
> Hoewel de standaard Score voldoende is voor veel scenario's, raden we u aan om in plaats daarvan relevantie op een aangepast Score profiel te baseren. Een aangepast Score profiel biedt een manier om items te stimuleren die nuttiger zijn voor uw bedrijf. Zie [Score profielen toevoegen](index-add-scoring-profiles.md) voor meer informatie.
>

## <a name="hit-highlighting"></a>Markeren

U kunt opmaak Toep assen op overeenkomende voor waarden in de zoek resultaten, zodat u de overeenkomst eenvoudig herkent. Instructies voor het markeren van treffers worden vermeld in de [query-aanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

De opmaak wordt toegepast op volledige term query's. Query's op gedeeltelijke voor waarden, zoals fuzzy zoeken of joker tekens zoeken die resulteren in het uitbreiden van query's in de engine, kunnen geen treffer markeringen gebruiken.

```http
POST /indexes/hotels/docs/search?api-version=2019-05-06 
    {  
      "search": "something",  
      "highlight": "Description"  
    }
```



## <a name="faceted-navigation"></a>Facetnavigatie

Zoek navigatie is gebruikelijk op een resultaten pagina die zich vaak aan de zijkant of boven aan een pagina bevindt. In azure Cognitive Search biedt facet navigatie een zelfgestuurde zoek functie op basis van vooraf gedefinieerde filters. Zie [facet navigatie in Azure Cognitive Search](search-faceted-navigation.md) voor meer informatie.

## <a name="filters-at-the-page-level"></a>Filters op pagina niveau

Als uw oplossing speciaal speciale zoek pagina's voor specifieke inhouds typen bevat (bijvoorbeeld een online retail-toepassing met afdelingen die boven aan de pagina worden weer gegeven), kunt u een [filter expressie](search-filters.md) invoegen naast een **OnClick** -gebeurtenis om een pagina in een vooraf gefilterde status te openen.

U kunt een filter met of zonder een zoek expressie verzenden. Met de volgende aanvraag wordt bijvoorbeeld op de merk naam gefilterd, waarbij alleen de documenten worden geretourneerd die overeenkomen.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Zie [zoeken naar documenten (Azure Cognitive Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) voor meer informatie over `$filter`-expressies.

## <a name="see-also"></a>Zie ook

- [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice)
- [Index bewerkingen](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Document bewerkingen](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Facet navigatie in azure Cognitive Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
