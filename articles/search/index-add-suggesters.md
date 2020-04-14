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
ms.openlocfilehash: d40d4cfe1b86448f1e8df307013905d69f203dcd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261054"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Een suggestie maken om automatisch aanvullen en voorgestelde resultaten in een query in te schakelen

In Azure Cognitive Search is 'search-as-you-type' ingeschakeld via een **suggestieconstructie** die is toegevoegd aan een [zoekindex.](search-what-is-an-index.md) Een suggestie ondersteunt twee ervaringen: *automatisch aanvullen*, die de term of zin voltooit, en suggesties die een korte lijst met overeenkomende documenten *retourneren.*  

De volgende schermafbeelding van [Uw eerste app maken in C#](tutorial-csharp-type-ahead-and-suggestions.md) illustreert beide. Autocomplete anticipeert op een potentiële term, afwerking "tw" met "in". Suggesties zijn mini-zoekresultaten, waarbij een veld als hotelnaam een overeenkomend hotelzoekdocument uit de index vertegenwoordigt. Voor suggesties u elk veld weergeven dat beschrijvende informatie biedt.

![Visuele vergelijking van automatisch aanvullen en voorgestelde query's](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuele vergelijking van automatisch aanvullen en voorgestelde query's")

U deze functies afzonderlijk of samen gebruiken. Om dit gedrag in Azure Cognitive Search te implementeren, is er een index- en querycomponent. 

+ Voeg in de index een suggestie toe aan een index. U de portal, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)of [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)gebruiken. De rest van dit artikel is gericht op het maken van een suggester.

+ Bel in het queryverzoek een van de [onderstaande API's](#how-to-use-a-suggester).

Ondersteuning voor zoek-naar-type is ingeschakeld per veld voor tekenreeksvelden. U beide typeahead gedrag binnen dezelfde zoekoplossing implementeren als u een ervaring wilt die vergelijkbaar is met die welke in de schermafbeelding wordt aangegeven. Beide aanvragen zijn gericht op het verzamelen van *documenten* van specifieke index en antwoorden worden geretourneerd nadat een gebruiker ten minste een invoertekenreeks met drie tekens heeft opgegeven.

## <a name="what-is-a-suggester"></a>Wat is een suggestie?

Een suggestieis een gegevensstructuur die zoek-as-you-type gedrag ondersteunt door voorvoegsels op te slaan voor matching op gedeeltelijke query's. Net als bij tokenized termen worden voorvoegsels opgeslagen in omgekeerde indexen, één voor elk veld dat is opgegeven in een verzameling suggestervelden.

Bij het maken van voorvoegsels heeft een suggestiezijn eigen analyseketen, vergelijkbaar met die welke wordt gebruikt voor zoeken naar volledige tekst. Echter, in tegenstelling tot analyse in full text search, kan een suggester alleen werken over velden die gebruik maken van de standaard Lucene analyzer (standaard) of een [taalanalyzer.](index-add-language-analyzers.md) Velden die [aangepaste analysers](index-add-custom-analyzers.md) of [vooraf gedefinieerde analysatoren](index-add-custom-analyzers.md#predefined-analyzers-reference) gebruiken (met uitzondering van standaard Lucene) zijn expliciet niet toegestaan om slechte resultaten te voorkomen.

> [!NOTE]
> Als u de beperking van de analyzer wilt omzeilen, gebruikt u twee afzonderlijke velden voor dezelfde inhoud. Hierdoor kan een van de velden een suggestie hebben, terwijl de andere kan worden ingesteld met een aangepaste analyzer-configuratie.

## <a name="define-a-suggester"></a>Een suggestie definiëren

Hoewel een suggestie meerdere eigenschappen heeft, is het in de eerste plaats een verzameling velden waarvoor u een zoek-as-you-type-ervaring inschakelt. Een reis-app kan bijvoorbeeld automatisch aanvullen op bestemmingen, steden en attracties inschakelen. Als zodanig zouden alle drie de velden in de velden collectie gaan.

Als u een suggestie wilt maken, voegt u er een toe aan een indexschema. U een suggester in een index (in het bijzonder, een suggester in de suggesters collectie). Een suggestiemaakt een lijst met velden. 

+ Kies velden die het beste één resultaat vertegenwoordigen voor suggesties. Namen, titels of andere unieke velden die onderscheid maken tussen documenten werken het beste. Als velden uit vergelijkbare of identieke waarden bestaan, worden de suggesties samengesteld uit identieke resultaten en weet een gebruiker niet op welke waarden hij moet klikken.

+ Zorg ervoor dat elk `sourceFields` veld in de lijst met deelnemers`"analyzer": null`de standaard Lucene analyzer `"analyzer": "en.Microsoft"`() of een [taalanalyzer](index-add-language-analyzers.md) (bijvoorbeeld) gebruikt. 

  Uw keuze van een analyzer bepaalt hoe velden worden tokenized en vervolgens vooraf worden vastgesteld. Voor een geafbreekbare tekenreeks als 'contextgevoelig' resulteert het gebruik van een taalanalyzer bijvoorbeeld in deze tokencombinaties: 'context', 'gevoelig', 'contextgevoelig'. Als je de standaard Lucene analyzer had gebruikt, zou de afgebroken string niet bestaan.

> [!TIP]
> Overweeg de [API Tekst analyseren](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) te gebruiken voor inzicht in hoe termen worden tokenized en vervolgens vooraf zijn vastgelegd. Zodra u een index hebt gemaakt, u verschillende analysators op een tekenreeks proberen om de tokens te bekijken die het uitzendt.

### <a name="when-to-create-a-suggester"></a>Wanneer een suggestie maken

De beste tijd om een suggestie te maken is wanneer u ook de velddefinitie zelf maakt.

Als u een suggestie probeert te maken met bestaande velden, wordt deze niet door de API geweigerd. Voorvoegsels worden gegenereerd tijdens het indexeren, wanneer gedeeltelijke termen in twee of meer tekencombinaties naast hele termen worden tokenized. Aangezien bestaande velden al zijn tokenized, moet u de index opnieuw opbouwen als u ze wilt toevoegen aan een suggestie. Zie [Een Azure Cognitive Search-index voor](search-howto-reindex.md)meer informatie.

### <a name="create-using-the-rest-api"></a>Maken met de REST API

Voeg in de REST-API suggesties toe via [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) of [Index bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
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
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
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
|`sourceFields`|Een lijst met een of meer velden die de bron zijn van de inhoud voor suggesties. Velden moeten van `Edm.String` `Collection(Edm.String)`type zijn en . Als een analyzer is opgegeven op het veld, moet deze een benoemde analyzer uit [deze lijst](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) zijn (geen aangepaste analyzer).<p/> Geef als aanbevolen praktijk alleen de velden op die zich lenen voor een verwacht en passend antwoord, of het nu gaat om een voltooide tekenreeks in een zoekbalk of een vervolgkeuzelijst.<p/>Een hotel naam is een goede kandidaat omdat het precisie heeft. Verbose velden zoals beschrijvingen en opmerkingen zijn te dicht. Ook repetitieve velden, zoals categorieën en tags, zijn minder effectief. In de voorbeelden nemen we toch 'categorie' op om aan te tonen dat u meerdere velden opnemen. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Een suggestie gebruiken

Een suggestie wordt gebruikt in een query. Nadat een suggestie is gemaakt, roept u de juiste API in uw querylogica aan om de functie aan te roepen. 

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
