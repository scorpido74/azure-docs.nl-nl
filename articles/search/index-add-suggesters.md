---
title: Typeahead-query's toevoegen aan een index
titleSuffix: Azure Cognitive Search
description: Schakel Type-Ahead query acties in azure Cognitive Search in door Voorst Ellen te maken en aanvragen te formuleren waarmee automatisch aanvullen of automatische suggesties worden aangeroepen.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790104"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Suggesties toevoegen aan een index voor typeahead in azure Cognitive Search

In azure Cognitive Search is de functionaliteit ' Search-as-u-type ' of typeahead gebaseerd op een **suggestie voor suggesties** die u toevoegt aan een [zoek index](search-what-is-an-index.md). Het is een lijst met een of meer velden waarvoor u wilt dat typeahead is ingeschakeld.

Een suggestie biedt ondersteuning voor twee typeahead varianten: *automatisch aanvullen*, waarmee de door u getypte term of woord groep wordt voltooid en *suggesties* die een korte lijst van overeenkomende documenten retour neren.  

De volgende scherm afbeelding, van de [eerste app maken in C# voor](tutorial-csharp-type-ahead-and-suggestions.md) beeld, illustreert typeahead. Automatisch aanvullen is van plan wat de gebruiker kan typen in het zoekvak. De werkelijke invoer is ' TW ', die automatisch wordt voltooid met ' in ', waarbij een ' dubbele ' wordt opgelost als de zoek term voor potentiële klanten. Suggesties worden weer gegeven in de vervolg keuzelijst. Voor suggesties kunt u elk deel van een document belichten dat het beste het resultaat beschrijft. In dit voor beeld zijn de suggesties namen van hotels. 

![Visuele vergelijking van automatisch aanvullen en voorgestelde query's](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuele vergelijking van automatisch aanvullen en voorgestelde query's")

Als u dit gedrag wilt implementeren in azure Cognitive Search, is er een index-en query onderdeel. 

+ Voeg in de index een suggestie toe aan een index. U kunt de portal, [rest API](https://docs.microsoft.com/rest/api/searchservice/create-index)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)gebruiken. De rest van dit artikel is gericht op het maken van een suggestie. 

+ Roep in de query aanvraag een van de [hieronder weer gegeven api's](#how-to-use-a-suggester)aan.

De ondersteuning voor zoeken naar het type wordt per veld ingeschakeld. U kunt zowel typeahead-gedrag in dezelfde Zoek oplossing implementeren als u een vergelijk bare ervaring wilt hebben als de functie die wordt aangegeven in de scherm opname. Beide aanvragen zijn gericht op de verzameling *documenten* van een specifieke index en reacties worden geretourneerd nadat een gebruiker ten minste een invoer teken reeks van drie tekens heeft opgegeven.

## <a name="create-a-suggester"></a>Een suggester maken

Hoewel een suggestie meerdere eigenschappen heeft, is het voornamelijk een verzameling velden waarvoor u een typeahead-ervaring inschakelt. Een reis-app kan bijvoorbeeld typeahead Search inschakelen op bestemmingen, steden en attractions. Als zodanig worden alle drie de velden in de verzameling velden.

Als u een suggestie wilt maken, voegt u er een toe aan een index schema. U kunt één suggestie in een index hebben (met name één suggestie in de verzameling suggesties). 

### <a name="when-to-create-a-suggester"></a>Wanneer maakt u een suggestie

Het beste moment om een suggestie te maken, is wanneer u ook de veld definitie zelf maakt.

Als u probeert een suggestie te maken met behulp van bestaande velden, wordt deze niet door de API toegestaan. Als gedeeltelijke voor waarden in twee of meer teken combinaties worden getokend op basis van hele voor waarden, wordt er tijdens het indexeren een typeahead-tekst gemaakt. Gezien dat bestaande velden al zijn getokend, moet u de index opnieuw samen stellen als u deze wilt toevoegen aan een suggestie. Zie [een Azure Cognitive search-index opnieuw samen stellen](search-howto-reindex.md)voor meer informatie over het opnieuw indexeren.

### <a name="create-using-the-rest-api"></a>Maken met behulp van de REST API

Voeg in de REST API suggesties toe via [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) of [update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>Maken met behulp van de .NET SDK

In C#definieert u een [suggestie object](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` is een verzameling, maar er kan slechts één item worden toegepast. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="property-reference"></a>Eigenschappen referentie

|Eigenschap      |Beschrijving      |
|--------------|-----------------|
|`name`        |De naam van de suggestie.|
|`searchMode`  |De strategie die wordt gebruikt om te zoeken naar kandidaten zinsdelen. De enige modus die momenteel wordt ondersteund, is `analyzingInfixMatching`, waarmee u in het begin of in het midden van zinnen flexibele overeenkomende woord groepen uitvoert.|
|`sourceFields`|Een lijst met een of meer velden die de bron van de inhoud voor suggesties zijn. Velden moeten van het type `Edm.String` en `Collection(Edm.String)`zijn. Als er een analyse programma is opgegeven in het veld, moet dit een named Analyzer van [deze lijst](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) zijn (geen aangepaste analyse functie).<p/>Geef als best practice alleen de velden op die aan een verwacht en passend antwoord worden uitgeleend, of het nu gaat om een voltooide teken reeks in een zoek balk of vervolg keuzelijst.<p/>De naam van een hotel is een goede kandidaat omdat deze precisie heeft. Uitgebreide velden, zoals beschrijvingen en opmerkingen, zijn te dicht bij. Zo zijn herhalende velden, zoals categorieën en tags, minder effectief. In de voor beelden bevatten we ' categorie ' om aan te tonen dat u meerdere velden kunt bevatten. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Analyse beperkingen voor sourceFields in een suggestie

De inhoud van het veld wordt door Azure Cognitive Search geanalyseerd zodat er query's kunnen worden uitgevoerd op afzonderlijke voor waarden. Voorst Ellen moeten voor voegsels worden geïndexeerd naast de volledige voor waarden. dit vereist extra analyse over de bron velden. Aangepaste analyse configuraties kunnen elk van de verschillende tokenizers en filters combi neren, vaak op manieren die het maken van de Voorst Ellen van de voor voegsels zouden veroorzaken. Daarom voor komt Azure Cognitive Search dat velden met aangepaste analyse functies in een suggestie worden opgenomen.

> [!NOTE] 
>  Als u de bovenstaande beperking wilt omzeilen, gebruikt u twee afzonderlijke velden voor dezelfde inhoud. Hiermee kan een van de velden een suggestie bieden, terwijl de andere kan worden ingesteld met een aangepaste analyse configuratie.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Een suggestie gebruiken in een query

Nadat een suggestie is gemaakt, roept u de juiste API aan in de query logica om de functie aan te roepen. 

+ [Suggesties REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [REST API automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Methode SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Methode AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Het API-gebruik wordt geïllustreerd in de volgende aanroep van de REST API voor automatisch aanvullen. Er zijn twee Takeaways uit dit voor beeld. Ten eerste, net als bij alle query's, wordt de bewerking vergeleken met de documenten verzameling van een index. Ten tweede kunt u query parameters toevoegen. Hoewel veel query parameters gebruikelijk zijn voor beide Api's, is de lijst voor elk van beide niet hetzelfde.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Als een suggestie niet in de index is gedefinieerd, mislukt de aanroep van automatisch aanvullen of suggesties.

## <a name="sample-code"></a>Voorbeeldcode

+ [Uw eerste app maken in C# het voor](tutorial-csharp-type-ahead-and-suggestions.md) beeld laat zien hoe u een suggestie kunt doen over een nieuwe constructie, aanbevolen query's, automatisch aanvullen en facet navigatie. Dit code voorbeeld wordt uitgevoerd op een sandbox Azure Cognitive Search-service en maakt gebruik van een vooraf geladen Hotels-index. u hoeft alleen op F5 te drukken om de toepassing uit te voeren. Er is geen abonnement of aanmelding vereist.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) is een ouder voor beeld dat C# zowel als Java-code bevat. Er wordt ook gedemonstreerd hoe u een suggestie maakt, suggesties voor query's, automatisch aanvullen en facet navigatie. Dit code voorbeeld maakt gebruik van de gehoste [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) -voorbeeld gegevens. 


## <a name="next-steps"></a>Volgende stappen

Het volgende voor beeld wordt aangeraden om te zien hoe de aanvragen worden geformuleerd.

> [!div class="nextstepaction"]
> [Suggesties en voor beelden van automatisch aanvullen](search-autocomplete-tutorial.md) 
