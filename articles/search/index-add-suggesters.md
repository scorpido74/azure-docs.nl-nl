---
title: Tekstvoorquery's toevoegen aan een index
titleSuffix: Azure Cognitive Search
description: Schakel vervolgquery-acties in Azure Cognitive Search in door suggesties te maken en aanvragen te formuleren die automatisch volledige of automatisch voorgestelde querytermen aanroepen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790104"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Suggesties toevoegen aan een index voor typeahead in Azure Cognitive Search

In Azure Cognitive Search is de 'search-as-you-type' of typeahead-functionaliteit gebaseerd op een **suggestieconstructie** die u toevoegt aan een [zoekindex.](search-what-is-an-index.md) Het is een lijst met een of meer velden waarvoor u moet typen.

Een suggestieondersteunt twee typeahead-varianten: *automatisch aanvullen*, waarmee de term of woordgroep die u typt, en suggesties die een korte lijst met overeenkomende documenten *retourneren,* worden voltooid.  

De volgende schermafbeelding, uit het [voorbeeld Uw eerste app maken in C#](tutorial-csharp-type-ahead-and-suggestions.md) , illustreert typeahead. Automatisch aanvullen anticipeert op wat de gebruiker in het zoekvak kan typen. Werkelijke input is "tw", die automatisch aanvullen eindigt met "in", het oplossen als "twin" als de potentiële zoekterm. Suggesties worden gevisualiseerd in de vervolgkeuzelijst. Voor suggesties u elk deel van een document weergeven dat het resultaat het beste beschrijft. In dit voorbeeld zijn de suggesties hotelnamen. 

![Visuele vergelijking van automatisch aanvullen en voorgestelde query's](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuele vergelijking van automatisch aanvullen en voorgestelde query's")

Om dit gedrag in Azure Cognitive Search te implementeren, is er een index- en querycomponent. 

+ Voeg in de index een suggestie toe aan een index. U de portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)gebruiken. De rest van dit artikel is gericht op het maken van een suggester. 

+ Bel in het queryverzoek een van de [onderstaande API's](#how-to-use-a-suggester).

Ondersteuning voor search-as-you-type is per veld ingeschakeld. U beide typeahead gedrag binnen dezelfde zoekoplossing implementeren als u een ervaring wilt die vergelijkbaar is met die welke in de schermafbeelding wordt aangegeven. Beide aanvragen zijn gericht op het verzamelen van *documenten* van specifieke index en antwoorden worden geretourneerd nadat een gebruiker ten minste een invoertekenreeks met drie tekens heeft opgegeven.

## <a name="create-a-suggester"></a>Een suggester maken

Hoewel een suggester verschillende eigenschappen heeft, is het in de eerste plaats een verzameling velden waarvoor u een typeahead-ervaring inschakelt. Een reis-app kan bijvoorbeeld zoeken naar typen op bestemmingen, steden en attracties inschakelen. Als zodanig zouden alle drie de velden in de velden collectie gaan.

Als u een suggestie wilt maken, voegt u er een toe aan een indexschema. U een suggester in een index (in het bijzonder, een suggester in de suggesters collectie). 

### <a name="when-to-create-a-suggester"></a>Wanneer een suggestie maken

De beste tijd om een suggestie te maken is wanneer u ook de velddefinitie zelf maakt.

Als u een suggestie probeert te maken met bestaande velden, wordt deze niet door de API geweigerd. Teksttekst wordt gemaakt tijdens het indexeren, wanneer gedeeltelijke termen in twee of meer tekencombinaties naast hele termen worden tokenized. Aangezien bestaande velden al zijn tokenized, moet u de index opnieuw opbouwen als u ze wilt toevoegen aan een suggestie. Zie [Een Azure Cognitive Search-index opnieuw opbouwen](search-howto-reindex.md)voor meer informatie over opnieuw indexen.

### <a name="create-using-the-rest-api"></a>Maken met de REST API

Voeg in de REST-API suggesties toe via [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) of [Index bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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


### <a name="create-using-the-net-sdk"></a>Maken met de .NET SDK

Definieer in C#een [object Suggester](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`is een verzameling, maar het kan slechts een item. 

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

### <a name="property-reference"></a>Eigenschappenreferentie

|Eigenschap      |Beschrijving      |
|--------------|-----------------|
|`name`        |De naam van de suggester.|
|`searchMode`  |De strategie gebruikt om te zoeken naar kandidaat zinnen. De enige modus die `analyzingInfixMatching`momenteel wordt ondersteund, is , die flexibele matching van zinnen aan het begin of in het midden van zinnen uitvoert.|
|`sourceFields`|Een lijst met een of meer velden die de bron zijn van de inhoud voor suggesties. Velden moeten van `Edm.String` `Collection(Edm.String)`type zijn en . Als een analyzer is opgegeven op het veld, moet deze een benoemde analyzer uit [deze lijst](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) zijn (geen aangepaste analyzer).<p/>Geef als aanbevolen praktijk alleen de velden op die zich lenen voor een verwacht en passend antwoord, of het nu gaat om een voltooide tekenreeks in een zoekbalk of een vervolgkeuzelijst.<p/>Een hotel naam is een goede kandidaat omdat het precisie heeft. Verbose velden zoals beschrijvingen en opmerkingen zijn te dicht. Ook repetitieve velden, zoals categorieën en tags, zijn minder effectief. In de voorbeelden nemen we toch 'categorie' op om aan te tonen dat u meerdere velden opnemen. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Analysebeperkingen voor sourceFields in een suggestie

Azure Cognitive Search analyseert de veldinhoud om query's op individuele voorwaarden mogelijk te maken. Suggesters vereisen dat voorvoegsels worden geïndexeerd naast volledige termen, wat extra analyse over de bronvelden vereist. Aangepaste analysersconfiguraties kunnen een van de verschillende tokenizers en filters combineren, vaak op manieren die het produceren van de voorvoegsels die nodig zijn voor suggesties onmogelijk maken. Azure Cognitive Search voorkomt daarom dat velden met aangepaste analysatoren worden opgenomen in een suggestie.

> [!NOTE] 
>  Als u de bovenstaande beperking wilt omzeilen, gebruikt u twee afzonderlijke velden voor dezelfde inhoud. Hierdoor kan een van de velden een suggestie hebben, terwijl de andere kan worden ingesteld met een aangepaste analyzer-configuratie.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Een suggestie in een query gebruiken

Nadat een suggestie is gemaakt, roept u de juiste API in uw querylogica aan om de functie aan te roepen. 

+ [Suggesties REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API VOOR automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Automatisch aanvullenMethttpMessagesAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API-gebruik wordt geïllustreerd in de volgende aanroep naar de Autocomplete REST API. Er zijn twee afhaalmaaltijden uit dit voorbeeld. Ten eerste, zoals bij alle query's, is de bewerking in strijd met het verzamelen van documenten van een index. Ten tweede u queryparameters toevoegen. Hoewel veel queryparameters gemeenschappelijk zijn voor beide API's, is de lijst voor elke lijst verschillend.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Als een suggestieniet is gedefinieerd in de index, mislukt een aanroep naar automatisch aanvullen of worden suggesties weergegeven.

## <a name="sample-code"></a>Voorbeeldcode

+ [Maak uw eerste app in C#](tutorial-csharp-type-ahead-and-suggestions.md) voorbeeld toont een suggester constructie, voorgestelde query's, automatisch aanvullen en gefacetteerde navigatie. Dit codevoorbeeld wordt uitgevoerd op een sandbox Azure Cognitive Search-service en maakt gebruik van een vooraf geladen Hotels-index, zodat u alleen maar op F5 hoeft te drukken om de toepassing uit te voeren. Een abonnement of aanmelding is niet nodig.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) is een ouder voorbeeld met zowel C# als Java-code. Het toont ook een suggester constructie, voorgestelde query's, autocomplete, en gefacetteerde navigatie. In dit codevoorbeeld worden de gehoste [NYCJobs-voorbeeldgegevens](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) gebruikt. 


## <a name="next-steps"></a>Volgende stappen

We raden het volgende voorbeeld aan om te zien hoe de aanvragen worden geformuleerd.

> [!div class="nextstepaction"]
> [Suggesties en voorbeelden automatisch aanvullen](search-autocomplete-tutorial.md) 
