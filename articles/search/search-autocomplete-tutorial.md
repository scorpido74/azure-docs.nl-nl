---
title: Suggesties toevoegen en automatisch aanvullen in een zoekvak
titleSuffix: Azure Cognitive Search
description: Schakel typeahead queryacties in Azure Cognitive Search in door suggesties te maken en aanvragen te formuleren die een zoekvak vullen met voltooide termen of zinnen.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72792526"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Suggesties toevoegen of automatisch aanvullen aan uw Azure Cognitive Search-toepassing

In dit artikel leert u hoe u [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions) en [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) gebruikt om een krachtig zoekvak te bouwen dat zoek-as-you-type gedrag ondersteunt.

+ *Suggesties* worden voorgestelde resultaten gegenereerd tijdens het typen, waarbij elke suggestie één resultaat is van de index die overeenkomt met wat u tot nu toe hebt getypt. 

+ *Automatisch aanvullen* "eindigt" het woord of de zin die een gebruiker momenteel typt. In plaats van resultaten terug te sturen, wordt een query voltooid, die u vervolgens uitvoeren om resultaten te retourneren. Net als bij suggesties is een voltooid woord of woordgroep in een query gebaseerd op een overeenkomst in de index. De service biedt geen query's die nul resultaten in de index retourneren.

U de voorbeeldcode downloaden en uitvoeren in **DotNetHowToAutocomplete** om deze functies te evalueren. De voorbeeldcode is gericht op een vooraf gebouwde index die is gevuld met [DEMOgegevens van NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) De NYCJobs-index bevat een [Suggester-constructie,](index-add-suggesters.md)wat een vereiste is voor het gebruik van suggesties of automatisch aanvullen. U de voorbereide index gebruiken die wordt gehost in een sandbox-service of [uw eigen index vullen](#configure-app) met behulp van een gegevenslader in de NYCJobs-voorbeeldoplossing. 

Het **voorbeeld van DotNetHowToAutocomplete** toont zowel suggesties als automatisch aanvullen, zowel in C#- als JavaScript-taalversies. C#-ontwikkelaars kunnen een ASP.NET MVC-toepassing doorlopen die gebruikmaakt van de [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). De logica voor het automatisch aanvullen en voorgestelde queryoproepen is te vinden in het HomeController.cs-bestand. JavaScript-ontwikkelaars vinden gelijkwaardige querylogica in IndexJavaScript.cshtml, waaronder directe aanroepen naar de [Azure Cognitive Search REST API.](https://docs.microsoft.com/rest/api/searchservice/) 

Voor beide taalversies is de front-end gebruikerservaring gebaseerd op de [jQuery UI-](https://jqueryui.com/autocomplete/) en [XDSoft-bibliotheken.](https://xdsoft.net/jqplugins/autocomplete/) We gebruiken deze bibliotheken om het zoekvak te bouwen dat zowel suggesties als automatisch aanvullen ondersteunt. Invoer die in het zoekvak wordt verzameld, worden gekoppeld aan suggesties en acties voor automatisch aanvullen, zoals gedefinieerd in HomeController.cs of IndexJavaScript.cshtml.

Deze oefening leidt u door de volgende taken:

> [!div class="checklist"]
> * Een zoekinvoervak implementeren in JavaScript en aanvragen voor voorgestelde overeenkomsten of automatisch voltooide termen aanvragen
> * Definieer in C#, definieer suggesties en acties voor automatisch aanvullen in HomeController.cs
> * Bel in JavaScript de API's van de REST rechtstreeks om dezelfde functionaliteit te bieden

## <a name="prerequisites"></a>Vereisten

Een Azure Cognitive Search-service is optioneel voor deze oefening omdat de oplossing een live sandbox-service gebruikt die een voorbereide NYCJobs-demo-index host. Zie [NYC-banenindex configureren](#configure-app) voor instructies als u dit voorbeeld op uw eigen zoekservice wilt uitvoeren.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), elke editie. Voorbeeldcode en instructies werden getest op de gratis communautaire editie.

* Download het [voorbeeld van DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Het voorbeeld is uitgebreid, met suggesties, automatisch aanvullen, gefacetteerde navigatie en caching aan de clientzijde. Bekijk de readme en opmerkingen voor een volledige beschrijving van wat het voorbeeld biedt.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Open **AutocompleteTutorial.sln** in Visual Studio. De oplossing bevat een ASP.NET MVC-project met een verbinding met de NYC Jobs demo-index.

2. Druk op F5 om het project uit te voeren en de pagina in de door u gewenste browser te laden.

Bovenaan ziet u een optie voor het selecteren van C# of JavaScript. De optie C# roept vanuit de browser naar de HomeController en gebruikt de Azure Cognitive Search .NET SDK om resultaten op te halen. 

De optie JavaScript roept de Azure Cognitive Search REST API rechtstreeks vanuit de browser aan. Met deze optie worden doorgaans opmerkelijk betere prestaties gehaald, omdat de controller ertussenuit wordt gehaald. U kunt de optie kiezen die bij uw behoeften en taalvoorkeuren past. Er bevinden zich diverse voorbeelden voor automatisch aanvullen op de pagina, met enkele richtlijnen voor elk voorbeeld. Elk voorbeeld heeft een aanbevolen voorbeeldtekst die u kunt proberen.  

Typ een paar letters in elk zoekvak om te zien wat er gebeurt.

## <a name="search-box"></a>Zoekvak

Voor zowel C# als JavaScript-versies is de implementatie van het zoekvak precies hetzelfde. 

Open het bestand **Index.cshtml** onder de map \Views\Home om de code weer te geven:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Dit voorbeeld is een eenvoudig invoertekstvak met een klasse voor styling, een ID waarnaar wordt verwezen door JavaScript en tijdelijke aanduidingstekst.  De magie zit in het ingesloten JavaScript.

Het voorbeeld van de C#-taal gebruikt JavaScript in Index.cshtml om de [jQuery UI Autocomplete-bibliotheek te](https://jqueryui.com/autocomplete/)gebruiken. Deze bibliotheek voegt de ervaring voor automatisch aanvullen toe aan het zoekvak door asynchrone aanroepen naar de MVC-controller om suggesties op te halen. De JavaScript-taalversie bevindt zich in IndexJavaScript.cshtml. Het bevat het onderstaande script voor de zoekbalk, evenals REST API-aanroepen naar Azure Cognitive Search.

Laten we eens kijken naar de JavaScript-code voor het eerste voorbeeld, die jQuery UI Autocomplete functie aanroept, het doorgeven van een verzoek om suggesties:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

De bovenstaande code wordt uitgevoerd in de browser op paginabelasting om jQuery UI automatisch aanvullen te configureren voor het invoervak 'example1a'.  `minLength: 3` zorgt ervoor dat aanbevelingen alleen worden weergegeven wanneer er zich ten minste drie tekens in het zoekvak bevinden.  De bronwaarde is belangrijk:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

De bovenstaande regel vertelt de functie jQuery UI Autocomplete waar u de lijst met items onder het zoekvak moet weergeven. Aangezien dit project een MVC-project is, wordt de functie Voorstellen in HomeController.cs aanroept die de logica bevat voor het retourneren van querysuggesties (meer informatie over Voorstellen in de volgende sectie). Deze functie passeert ook een paar parameters om hoogtepunten, fuzzy matching en term te beheren. Door de JavaScript-API voor automatisch aanvullen wordt de parameter 'term' toegevoegd.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Het voorbeeld uitbreiden om ondersteuning te bieden voor zoeken bij benadering

Zoeken bij benadering zorgt ervoor dat u resultaten krijgt te zien op basis van treffers bij benadering, zelfs als de gebruiker een woord in het zoekvak onjuist heeft gespeld. Hoewel het niet nodig is, verbetert het de robuustheid van een typeahead-ervaring aanzienlijk. U gaat dit uitproberen door de bronregel zo te wijzigen dat zoeken bij benadering mogelijk is.

Wijzig de volgende regel van dit:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

in deze:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Start de toepassing door op F5 te drukken.

Typ een woord als 'exclasief' en u ziet hoe resultaten worden weergegeven voor 'exclusief', zelfs als deze niet exact overeenkomen met de letters die u hebt getypt.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>jQuery Automatisch aanvullen ondersteund door Azure Cognitive Search automatisch aanvullen

Tot nu toe is de zoek UX-code gecentreerd op de suggesties. Het volgende codeblok toont de functie jQuery UI Autocomplete (regel 91 in index.cshtml), waarbij een aanvraag voor Azure Cognitive Search automatisch wordt voltooid:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# voorbeeld

Nu we de JavaScript-code voor de webpagina hebben bekeken, laten we eens kijken naar de C# server-side controller code die daadwerkelijk de voorgestelde overeenkomsten ophaalt met behulp van de Azure Cognitive Search .NET SDK.

Open het **HomeController.cs** bestand onder de map Controllers. 

Het eerste wat je zou kunnen merken is `InitSearch`een methode aan de bovenkant van de klasse genaamd . Met deze methode wordt een geverifieerde HTTP-indexclient gemaakt voor de Azure Cognitive Search-service. Zie [Azure Cognitive Search gebruiken vanuit een .NET-toepassing](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)voor meer informatie.

Let op regel 41 op de functie Voorstellen. Het is gebaseerd op de [methode DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

De functie Voorstellen maakt gebruik van twee parameters die bepalen of er bij het invoeren van de zoekterm markeringen voor treffers worden geretourneerd of zoeken bij benadering wordt gebruikt. De methode maakt een [object SuggestParameters,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)dat vervolgens wordt doorgegeven aan de API Voorstellen. Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

Let op regel 69 op de functie Automatisch aanvullen. Het is gebaseerd op de [methode DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

De functie Automatisch aanvullen neemt de invoer van de zoekterm. De methode maakt een [object AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

Voeg een onderbrekingspunt toe aan het begin van de functie Voorstellen en doorloop de code. (optioneel) Let op het antwoord dat door de SDK wordt geretourneerd en hoe deze wordt omgezet in het resultaat dat wordt geretourneerd met de methode.

De andere voorbeelden op de pagina volgen hetzelfde patroon om hithighlighting en facetten toe te voegen om client-side caching van de automatisch aanvullende resultaten te ondersteunen. Loop door elk van deze voorbeelden heen zodat u begrijpt hoe ze werken en hoe u ze kunt gebruiken in uw zoekervaring.

## <a name="javascript-example"></a>JavaScript-voorbeeld

Een Javascript-implementatie van automatisch aanvullen en suggesties roept de REST API aan, waarbij een URI als bron wordt gebruikt om de index en bewerking op te geven. 

Als u de JavaScript-implementatie wilt controleren, opent u **IndexJavaScript.cshtml**. Merk op dat de functie jQuery UI Autocomplete ook wordt gebruikt voor het zoekvak, het verzamelen van invoer van zoektermen en het voeren van asynchrone aanroepen naar Azure Cognitive Search om voorgestelde overeenkomsten of voltooide termen op te halen. 

Kijk eens naar de JavaScript-code uit het eerste voorbeeld:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Als u dit voorbeeld vergelijkt met het bovenstaande voorbeeld dat de Home-controller aanroept, ziet u verschillende overeenkomsten.  De configuratie voor automatisch aanvullen voor `minLength` en `position` zijn identiek. 

De belangrijke wijziging hier is de bron. In plaats van de Methode Voorstellen in de thuiscontroller aan te roepen, wordt een REST-aanvraag gemaakt in een JavaScript-functie en uitgevoerd met Ajax. De reactie wordt vervolgens verwerkt in 'geslaagd' en als bron gebruikt.

REST-aanroepen gebruiken URI's om aan te geven of er een [API-aanroep](https://docs.microsoft.com/rest/api/searchservice/autocomplete) voor automatisch aanvullen of [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions) wordt uitgevoerd. De volgende URI's bevinden zich respectievelijk op de lijnen 9 en 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Op lijn 148 vindt u een `autocompleteUri`script dat de aanroepen van de . De eerste `suggestUri` oproep is op lijn 39.

> [!Note]
> Het maken van REST-gesprekken naar de service in JavaScript wordt hier aangeboden als een handige demonstratie van de REST API, maar moet niet worden opgevat als een best practice of aanbeveling. De opname van een API-sleutel en eindpunt in een script opent uw service tot denial of service-aanvallen voor iedereen die deze waarden buiten het script kan lezen. Hoewel het veilig is om JavaScript te gebruiken voor leerdoeleinden, misschien op indexen die op de gratis service worden gehost, raden we aan Java of C# te gebruiken voor indexering en querybewerkingen in productiecode. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>NYCJobs configureren om op uw service uit te voeren

Tot nu toe heb je gebruik gemaakt van de gehoste NYCJobs demo-index. Als u volledig inzicht wilt in alle code, inclusief de index, volgt u deze instructies om de index in uw eigen zoekservice te maken en te laden.

1. [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis dienst voor dit voorbeeld. 

   > [!Note]
   > Als u de gratis Azure Cognitive Search-service gebruikt, bent u beperkt tot drie indexen. De gegevenslader NYCJobs maakt twee indexen. Zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe indexen.

1. Download [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) voorbeeldcode.

1. Open **DataLoader.sln** in Visual Studio in de map DataLoader van de NYCJobs-voorbeeldcode.

1. Voeg de verbindingsgegevens voor uw Azure Cognitive Search-service toe. Open de App.config binnen het DataLoader-project en wijzig de AppSettings TargetSearchServiceName en TargetSearchServiceApiKey om uw Azure Cognitive Search-service en Azure Cognitive Search-service API Key weer te geven. Deze informatie is te vinden in de Azure-portal.

1. Druk op F5 om de toepassing te starten, maak twee indexen en importeer de NYCJob-voorbeeldgegevens.

1. Open **AutocompleteTutorial.sln** en bewerk de Web.config in het project **AutocompleteTutorial.** Wijzig de waarden SearchServiceName en SearchServiceApiKey in waarden die geldig zijn voor uw zoekservice.

1. Druk op F5 om de toepassing uit te voeren. De voorbeeldweb-app wordt geopend in de standaardbrowser. De ervaring is identiek aan de sandbox-versie, alleen de index en gegevens worden gehost op uw service.

## <a name="next-steps"></a>Volgende stappen

In dit voorbeeld worden de basisstappen getoond voor het bouwen van een zoekvak dat automatisch aanvullen en suggesties ondersteunt. U zag hoe u een ASP.NET MVC-toepassing bouwen en de Azure Cognitive Search .NET SDK of REST API gebruiken om suggesties op te halen.

Als volgende stap probeert u suggesties te integreren en automatisch in uw zoekervaring te voltooien. De volgende referentieartikelen moeten helpen.

> [!div class="nextstepaction"]
> [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggesties REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Facets index attribuut op een Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)

