---
title: Een suggester maken
titleSuffix: Azure Cognitive Search
description: Schakel vervolgquery-acties in Azure Cognitive Search in door suggesties te maken en aanvragen te formuleren die automatisch volledige of automatisch voorgestelde querytermen aanroepen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: a6c4051a5b3d557f9ac2927a62492425e7636c0d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113472"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggestions-in-azure-cognitive-search"></a>Een suggestie maken om automatisch aanvullen en suggesties in Azure Cognitive Search in te schakelen

In Azure Cognitive Search is de 'search-as-you-type' of typeahead-functionaliteit gebaseerd op een **suggestieconstructie** die u toevoegt aan een [zoekindex.](search-what-is-an-index.md) Een suggestieondersteunt twee zoek-naar-je-type varianten: *autocomplete*, die de term of zin die u typt voltooit, en suggesties die een korte lijst met overeenkomende documenten *retourneren.*  

De volgende schermafbeelding, uit het voorbeeld [Uw eerste app maken in C#](tutorial-csharp-type-ahead-and-suggestions.md) illustreert beide ervaringen. Autocomplete anticipeert op wat de gebruiker zou kunnen typen, afwerking "tw" met "in" als de potentiële zoekterm. Suggesties zijn werkelijke zoekresultaten, die elk een overeenkomend document vertegenwoordigen. Voor suggesties u elk deel van een document weergeven dat het resultaat het beste beschrijft. In dit voorbeeld worden de suggesties weergegeven door het veld hotelnaam.

![Visuele vergelijking van automatisch aanvullen en voorgestelde query's](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuele vergelijking van automatisch aanvullen en voorgestelde query's")

Om dit gedrag in Azure Cognitive Search te implementeren, is er een index- en querycomponent. 

+ Voeg in de index een suggestie toe aan een index. U de portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)gebruiken. De rest van dit artikel is gericht op het maken van een suggester.

+ Bel in het queryverzoek een van de [onderstaande API's](#how-to-use-a-suggester).

Ondersteuning voor search-as-you-type is per veld ingeschakeld. U beide typeahead gedrag binnen dezelfde zoekoplossing implementeren als u een ervaring wilt die vergelijkbaar is met die welke in de schermafbeelding wordt aangegeven. Beide aanvragen zijn gericht op het verzamelen van *documenten* van specifieke index en antwoorden worden geretourneerd nadat een gebruiker ten minste een invoertekenreeks met drie tekens heeft opgegeven.

## <a name="what-is-a-suggester"></a>Wat is een suggestie?

Een suggestieis een gegevensstructuur die zoek-as-you-type gedrag ondersteunt door voorvoegsels op te slaan voor matching op gedeeltelijke query's. Net als bij tokenized termen worden voorvoegsels opgeslagen in omgekeerde indexen, één voor elk veld dat is opgegeven in een verzameling suggestervelden.

Bij het maken van voorvoegsels heeft een suggestiezijn eigen analyseketen, vergelijkbaar met die welke wordt gebruikt voor zoeken naar volledige tekst. Echter, in tegenstelling tot analyse in full text search, kan een suggester alleen vooraf gedefinieerde analyzers gebruiken (standaard Lucene, [taalanalysers](index-add-language-analyzers.md)of andere analysators in de [vooraf gedefinieerde analyzer-lijst](index-add-custom-analyzers.md#predefined-analyzers-reference). [Aangepaste analysers](index-add-custom-analyzers.md) en configuraties zijn specifiek niet toegestaan om willekeurige configuraties te vermijden die slechte resultaten opleveren.

> [!NOTE]
> Als u de beperking van de analyzer wilt omzeilen, gebruikt u twee afzonderlijke velden voor dezelfde inhoud. Hierdoor kan een van de velden een suggestie hebben, terwijl de andere kan worden ingesteld met een aangepaste analyzer-configuratie.

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
