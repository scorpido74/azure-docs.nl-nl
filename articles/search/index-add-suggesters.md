---
title: Typeahead-query's toevoegen aan een index-Azure Search
description: Schakel Type-Ahead query acties in Azure Search in door Voorst Ellen te maken en aanvragen te formuleren waarmee automatisch aanvullen of automatische suggestieve query termen worden aangeroepen.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648846"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Suggesties toevoegen aan een index voor typeahead in Azure Search

Een **suggestie** is een constructie in een [Azure search index](search-what-is-an-index.md) die ondersteuning biedt voor de functie voor zoeken naar een type. Het bevat een lijst met velden waarvoor u typeahead query-invoer wilt inschakelen. Binnen een index ondersteunt dezelfde Voorst Ellen een of beide van deze twee typeahead varianten: automatisch *aanvullen* voltooit de term of de woord groep die u typt. *suggesties* bieden een korte lijst met resultaten. 

De volgende scherm afbeelding illustreert beide typeahead-functies. In deze Xbox-zoek pagina gaat u naar een nieuwe pagina met zoek resultaten voor deze query. de suggesties zijn de werkelijke resultaten waarmee u naar een pagina voor dat specifieke spel gaat. U kunt de functie voor automatisch aanvullen beperken tot één item in een zoek balk of een lijst opgeven zoals hier wordt weer gegeven. Voor suggesties kunt u elk deel van een document belichten dat het beste het resultaat beschrijft.

![Visuele vergelijking van automatisch aanvullen en voorgestelde query's](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visuele vergelijking van automatisch aanvullen en voorgestelde query's")

Voor het implementeren van deze gedragingen in Azure Search is er een index-en query onderdeel. 

+ Het index onderdeel is een suggestie. U kunt de portal, REST API of .NET SDK gebruiken om een suggestie te maken. 

+ Het query onderdeel is een actie die is opgegeven voor de query aanvraag (een suggestie of een actie voor automatisch volt ooien). 

De ondersteuning voor zoeken naar het type wordt per veld ingeschakeld. U kunt zowel typeahead-gedrag in dezelfde Zoek oplossing implementeren als u een vergelijk bare ervaring wilt hebben als de functie die wordt aangegeven in de scherm opname. Beide aanvragen zijn gericht op de verzameling *documenten* van een specifieke index en reacties worden geretourneerd nadat een gebruiker ten minste een invoer teken reeks van drie tekens heeft opgegeven.

## <a name="create-a-suggester"></a>Een suggester maken

Hoewel een suggestie meerdere eigenschappen heeft, is het voornamelijk een verzameling velden waarvoor u een typeahead-ervaring inschakelt. Een reis-app kan bijvoorbeeld typeahead Search inschakelen op bestemmingen, steden en attractions. Als zodanig worden alle drie de velden in de verzameling velden.

Als u een suggestie wilt maken, voegt u er een toe aan een index schema. U kunt één suggestie in een index hebben (met name één suggestie in de verzameling suggesties). 

### <a name="use-the-rest-api"></a>REST-API gebruiken

In de REST API kunt u Voorst Ellen toevoegen via [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) of [update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
Nadat u een suggestie hebt gemaakt, voegt u [](https://docs.microsoft.com/rest/api/searchservice/suggestions) de API Suggestions of de [api's voor automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) in uw query logica toe om de functie aan te roepen.

### <a name="use-the-net-sdk"></a>De .NET SDK gebruiken

In C#definieert u een [suggestie object](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`is een verzameling, maar er kan slechts één item worden toegepast. 

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

## <a name="property-reference"></a>Eigenschappen referentie

Belang rijke punten voor suggesties zijn dat er een naam (Voorst Ellen wordt verwezen door een naam op een aanvraag), een Search mode (momenteel alleen een ' analyzingInfixMatching ') en de lijst met velden waarvoor typeahead is ingeschakeld. 

Eigenschappen die een voor stel definiëren, zijn onder meer het volgende:

|Eigenschap      |Description      |
|--------------|-----------------|
|`name`        |De naam van de suggestie. U gebruikt de naam van de suggestie bij het aanroepen van de [suggesties rest API](https://docs.microsoft.com/rest/api/searchservice/suggestions) of [automatisch aanvullen rest API](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |De strategie die wordt gebruikt om te zoeken naar kandidaten zinsdelen. De enige modus die momenteel wordt `analyzingInfixMatching`ondersteund, is het uitvoeren van flexibele overeenkomende woord groepen aan het begin of in het midden van de zinnen.|
|`sourceFields`|Een lijst met een of meer velden die de bron van de inhoud voor suggesties zijn. Alleen velden van het `Edm.String` type `Collection(Edm.String)` en kunnen bronnen voor suggesties zijn. Alleen velden waarvoor geen aangepaste taal analyseset is ingesteld, kunnen worden gebruikt.<p/>Geef alleen de velden op die aan een verwacht en passend antwoord worden uitgeleend, of het nu gaat om een voltooide teken reeks in een zoek balk of vervolg keuzelijst.<p/>De naam van een hotel is een goede kandidaat omdat deze precisie heeft. Uitgebreide velden, zoals beschrijvingen en opmerkingen, zijn te dicht bij. Zo zijn herhalende velden, zoals categorieën en tags, minder effectief. In de voor beelden bevatten we ' categorie ' om aan te tonen dat u meerdere velden kunt bevatten. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>Analyse van SourceFields in een suggestie

Azure Search analyseert de veld inhoud om query's op afzonderlijke voor waarden mogelijk te maken. Voorst Ellen moeten voor voegsels worden geïndexeerd naast de volledige voor waarden. dit vereist extra analyse over de bron velden. Aangepaste analyse configuraties kunnen elk van de verschillende tokenizers en filters combi neren, vaak op manieren die het maken van de Voorst Ellen van de voor voegsels zouden veroorzaken. Daarom **kan Azure Search voor komen dat velden met aangepaste analyse functies in een suggestie worden opgenomen**.

> [!NOTE] 
>  De aanbevolen benadering voor het omzeilen van de bovenstaande beperking is het gebruik van twee afzonderlijke velden voor dezelfde inhoud. Hiermee kan een van de velden suggesties geven en kan de andere worden ingesteld met een aangepaste analyse configuratie.

## <a name="when-to-create-a-suggester"></a>Wanneer maakt u een suggestie

Om te voor komen dat een index opnieuw wordt opgebouwd, moeten een suggestie `sourceFields` en de velden die zijn opgegeven in op hetzelfde moment worden gemaakt.

Als u een suggestie toevoegt aan een bestaande index, waarbij bestaande velden zijn opgenomen in, `sourceFields`wordt de veld definitie op basis van wijzigingen en een heropbouw vereist. Zie [een Azure search index opnieuw samen stellen](search-howto-reindex.md)voor meer informatie.

## <a name="how-to-use-a-suggester"></a>Een suggestie gebruiken

Zoals eerder is vermeld, kunt u een suggestie gebruiken voor voorgestelde query's, automatisch aanvullen of beide. 

Er wordt in de aanvraag samen met de bewerking naar een suggestie verwezen. Geef `suggest` bijvoorbeeld in een Get rest-aanroep een of `autocomplete` op in de verzameling documenten. Gebruik voor REST, na het maken van een suggestie, de [suggesties API](https://docs.microsoft.com/rest/api/searchservice/suggestions) of de [API voor automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) in uw query logica.

Voor .NET gebruikt u [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) of [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Voor een voor beeld van beide aanvragen, Zie [voor beeld voor het toevoegen van AutoAanvullen en suggesties in azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Voorbeeldcode

Het [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) -voor beeld C# bevat zowel java-code als een voor stel voor de constructie van een suggestie, aanbevolen query's, automatisch aanvullen en facet navigatie. 

Er wordt gebruikgemaakt van een sandbox Azure Search-service en een vooraf geladen index. u hoeft alleen op F5 te drukken om de app uit te voeren. Geen abonnement of aanmelden nood zakelijk.

## <a name="next-steps"></a>Volgende stappen

Het volgende voor beeld wordt aangeraden om te zien hoe de aanvragen worden geformuleerd.

> [!div class="nextstepaction"]
> [Suggesties en voor beelden van automatisch aanvullen](search-autocomplete-tutorial.md) 
