---
title: Een suggester maken
titleSuffix: Azure Cognitive Search
description: Schakel vervolgquery-acties in Azure Cognitive Search in door suggesties te maken en aanvragen te formuleren die automatisch volledige of automatisch voorgestelde querytermen aanroepen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1e2a837acef976b6b872c2d4002ee49d662ad594
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641328"
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

Als u een suggestie wilt maken, voegt u er een toe aan een [indexschema](https://docs.microsoft.com/rest/api/searchservice/create-index) en [stelt u elke eigenschap in.](#property-reference) In de index u één suggestiehebben (specifiek één suggestiever in de suggestersverzameling). De beste tijd om een suggestie te maken is wanneer u ook het veld definieert dat het zal gebruiken.

### <a name="choose-fields"></a>Velden kiezen

Hoewel een suggestie meerdere eigenschappen heeft, is het in de eerste plaats een verzameling velden waarvoor u een zoek-as-you-type-ervaring inschakelt. Kies velden die het beste één resultaat vertegenwoordigen voor suggesties. Namen, titels of andere unieke velden die onderscheid maken tussen meerdere overeenkomsten werken het beste. Als velden bestaan uit repetitieve waarden, bestaan de suggesties uit identieke resultaten en weet een gebruiker niet op welke basis waarde hij moet klikken.

Zorg ervoor dat elk veld een analyzer gebruikt die lexicale analyse uitvoert tijdens het indexeren. U de standaard Lucene analyzer ()`"analyzer": null`of een [taalanalyzer](index-add-language-analyzers.md) (bijvoorbeeld) `"analyzer": "en.Microsoft"`gebruiken. 

Uw keuze van een analyzer bepaalt hoe velden worden tokenized en vervolgens vooraf worden vastgesteld. Voor een geafbreekbare tekenreeks als 'contextgevoelig' resulteert het gebruik van een taalanalyzer bijvoorbeeld in deze tokencombinaties: 'context', 'gevoelig', 'contextgevoelig'. Als je de standaard Lucene analyzer had gebruikt, zou de afgebroken string niet bestaan.

> [!TIP]
> Overweeg de [API Tekst analyseren](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) te gebruiken voor inzicht in hoe termen worden tokenized en vervolgens vooraf zijn vastgelegd. Zodra u een index hebt gemaakt, u verschillende analysators op een tekenreeks proberen om de tokens te bekijken die het uitzendt.

### <a name="when-to-create-a-suggester"></a>Wanneer een suggestie maken

De beste tijd om een suggestie te maken is wanneer u ook de velddefinitie zelf maakt.

Als u een suggestie probeert te maken met bestaande velden, wordt deze niet door de API geweigerd. Voorvoegsels worden gegenereerd tijdens het indexeren, wanneer gedeeltelijke termen in twee of meer tekencombinaties naast hele termen worden tokenized. Aangezien bestaande velden al zijn tokenized, moet u de index opnieuw opbouwen als u ze wilt toevoegen aan een suggestie. Zie [Een Azure Cognitive Search-index voor](search-howto-reindex.md)meer informatie.

## <a name="create-using-rest"></a>Maken met REST

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

## <a name="create-using-net"></a>Maken met .NET

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

## <a name="property-reference"></a>Eigenschappenreferentie

|Eigenschap      |Beschrijving      |
|--------------|-----------------|
|`name`        |De naam van de suggester.|
|`searchMode`  |De strategie gebruikt om te zoeken naar kandidaat zinnen. De enige modus die `analyzingInfixMatching`momenteel wordt ondersteund, is , die momenteel overeenkomt met het begin van een term.|
|`sourceFields`|Een lijst met een of meer velden die de bron zijn van de inhoud voor suggesties. Velden moeten van `Edm.String` `Collection(Edm.String)`type zijn en . Als een analyzer is opgegeven op het veld, moet deze een benoemde analyzer uit [deze lijst](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) zijn (geen aangepaste analyzer).<p/> Geef als aanbevolen praktijk alleen de velden op die zich lenen voor een verwacht en passend antwoord, of het nu gaat om een voltooide tekenreeks in een zoekbalk of een vervolgkeuzelijst.<p/>Een hotel naam is een goede kandidaat omdat het precisie heeft. Verbose velden zoals beschrijvingen en opmerkingen zijn te dicht. Ook repetitieve velden, zoals categorieën en tags, zijn minder effectief. In de voorbeelden nemen we toch 'categorie' op om aan te tonen dat u meerdere velden opnemen. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Een suggestie gebruiken

Een suggestie wordt gebruikt in een query. Nadat een suggestie is gemaakt, belt u een van de volgende API's voor een zoek-naar-het-type-ervaring:

+ [Suggesties REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API VOOR automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Automatisch aanvullenMethttpMessagesAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

In een zoektoepassing moet clientcode een bibliotheek zoals [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) gebruiken om de gedeeltelijke query te verzamelen en de overeenkomst te verstrekken. Zie Automatisch aanvullen of [voorgestelde resultaten toevoegen aan clientcode voor](search-autocomplete-tutorial.md)meer informatie over deze taak.

API-gebruik wordt geïllustreerd in de volgende aanroep naar de Autocomplete REST API. Er zijn twee afhaalmaaltijden uit dit voorbeeld. Ten eerste, zoals bij alle query's, is de bewerking tegen het verzamelen van documenten van een index en bevat de query een **zoekparameter,** die in dit geval de gedeeltelijke query bevat. Ten tweede moet u **suggesterName** aan het verzoek toevoegen. Als een suggestieniet is gedefinieerd in de index, mislukt een aanroep naar automatisch aanvullen of worden suggesties weergegeven.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Voorbeeldcode

+ [Maak uw eerste app in C# (les 3 - Zoek-als-u-type toevoegen)](tutorial-csharp-type-ahead-and-suggestions.md) voorbeeld toont een suggesterconstructie, voorgestelde query's, automatisch aanvullen en gefacetteerde navigatie. Dit codevoorbeeld wordt uitgevoerd op een sandbox Azure Cognitive Search-service en maakt gebruik van een vooraf geladen Hotels-index, zodat u alleen maar op F5 hoeft te drukken om de toepassing uit te voeren. Een abonnement of aanmelding is niet nodig.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) is een ouder voorbeeld met zowel C# als Java-code. Het toont ook een suggester constructie, voorgestelde query's, autocomplete, en gefacetteerde navigatie. In dit codevoorbeeld worden de gehoste [NYCJobs-voorbeeldgegevens](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) gebruikt. 

## <a name="next-steps"></a>Volgende stappen

We raden het volgende voorbeeld aan om te zien hoe de aanvragen worden geformuleerd.

> [!div class="nextstepaction"]
> [Automatisch aanvullen en suggesties toevoegen aan clientcode](search-autocomplete-tutorial.md) 
