---
title: Suggesties en automatisch aanvullen toevoegen in een zoekvak-Azure Search
description: Schakel typeahead-query acties in Azure Search in door Voorst Ellen te maken en aanvragen te formuleren waarmee een zoekvak met volledige voor waarden of zinsdelen wordt ingevuld.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183286"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Suggesties of automatisch aanvullen toevoegen aan uw Azure Search-toepassing

In dit artikel leert u hoe u [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions) en [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) kunt gebruiken om een krachtig zoekvak te maken dat ondersteuning biedt voor zoek gedrag.

+ *Suggesties* zijn voorgestelde resultaten die zijn gegenereerd tijdens het typen, waarbij elke suggestie een enkel resultaat is van de index die overeenkomt met wat u tot nu toe hebt getypt. 

+ *Automatisch aanvullen* ' voltooit ' het woord of de woord groep die momenteel door een gebruiker wordt getypt. In plaats van resultaten te retour neren, wordt een query voltooid, die u vervolgens kunt uitvoeren om resultaten te retour neren. Net als bij suggesties wordt een voltooid woord of zinsdeel in een query voorgesteld op een overeenkomst in de index. De service biedt geen query's die nul resultaten retour neren in de index.

U kunt de voorbeeld code in **DotNetHowToAutocomplete** downloaden en uitvoeren om deze functies te evalueren. De voorbeeld code is gericht op een vooraf gemaakte index die is gevuld met [NYCJobs-voorbeeld gegevens](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). De NYCJobs-index bevat een [suggestie voor suggesties](index-add-suggesters.md), een vereiste voor het gebruik van suggesties of automatisch aanvullen. U kunt de voor bereide index gebruiken die wordt gehost in een sandbox-service of [uw eigen index vullen](#configure-app) met behulp van een gegevens lader in de NYCJobs-voorbeeld oplossing. 

Het **DotNetHowToAutocomplete** -voor beeld demonstreert zowel suggesties als automatisch aanvullen C# in de taal versies van Java script. C#Ontwikkel aars kunnen stappen door lopen van een ASP.NET MVC-toepassing die gebruikmaakt van de [Azure Search .NET SDK](https://aka.ms/search-sdk). De logica voor het maken van automatisch aanvullen en voorgestelde query aanroepen vindt u in het HomeController.cs-bestand. Java script-ontwikkel aars vinden vergelijk bare query logica in IndexJavaScript. cshtml, waaronder rechtstreekse aanroepen naar de [Azure Search rest API](https://docs.microsoft.com/rest/api/searchservice/). 

Voor beide taal versies is de gebruikers ervaring van de front-end gebaseerd op de [jQuery-gebruikers interface](https://jqueryui.com/autocomplete/) en de [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) -bibliotheken. We gebruiken deze bibliotheken om het zoekvak te bouwen dat zowel suggesties als automatisch aanvullen ondersteunt. Invoer die in het zoekvak wordt verzameld, wordt gekoppeld aan suggesties en acties voor automatisch aanvullen, zoals gedefinieerd in HomeController.cs of IndexJavaScript. cshtml.

In deze oefening wordt u begeleid bij de volgende taken:

> [!div class="checklist"]
> * Een zoekvak voor zoeken implementeren in Java script en aanvragen verzenden voor voorgestelde overeenkomsten of automatisch aanvullende voor waarden
> * In C#worden suggesties en acties voor automatisch aanvullen in HomeController.cs gedefinieerd.
> * Roep in Java script de REST Api's rechtstreeks aan om dezelfde functionaliteit te bieden

## <a name="prerequisites"></a>Vereisten

Een Azure Search-service is optioneel voor deze oefening omdat de oplossing gebruikmaakt van een live sandbox-service die als host fungeert voor een voor bereide NYCJobs-demo-index. Zie [NYC-taken index configureren](#configure-app) voor instructies als u dit voor beeld wilt uitvoeren in uw eigen zoek service.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), elke editie. Voor beelden van code en instructies zijn getest in de gratis Community-editie.

* Down load het DotNetHowToAutoComplete-voor [beeld](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Het voor beeld is uitgebreid met suggesties, automatisch aanvullen, facet navigatie en caching aan de client zijde. Lees het Leesmij-bestand en de opmerkingen voor een volledige beschrijving van de voor beelden.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Open **AutocompleteTutorial. SLN** in Visual Studio. De oplossing bevat een ASP.NET MVC-project met een verbinding met de NYC-taken demo-index.

2. Druk op F5 om het project uit te voeren en de pagina in de door u gewenste browser te laden.

Bovenaan ziet u een optie voor het selecteren van C# of JavaScript. Met C# de optie wordt de HomeController vanuit de browser aangeroepen en wordt de Azure Search .NET SDK gebruikt om de resultaten op te halen. 

Met de JavaScript-optie wordt de Azure Search REST-API rechtstreeks vanuit de browser aangeroepen. Met deze optie worden doorgaans opmerkelijk betere prestaties gehaald, omdat de controller ertussenuit wordt gehaald. U kunt de optie kiezen die bij uw behoeften en taalvoorkeuren past. Er bevinden zich diverse voorbeelden voor automatisch aanvullen op de pagina, met enkele richtlijnen voor elk voorbeeld. Elk voorbeeld heeft een aanbevolen voorbeeldtekst die u kunt proberen.  

Typ een paar letters in elk zoekvak om te zien wat er gebeurt.

## <a name="search-box"></a>Zoekvak

Voor zowel C# als Java script-versies is de implementatie van het zoekvak precies hetzelfde. 

Open het bestand **index. cshtml** in de map \Views\Home om de code te bekijken:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Dit voor beeld is een eenvoudig invoer tekstvak met een klasse voor opmaak, een ID waarnaar wordt verwezen door Java script en tekst van tijdelijke aanduiding.  Het Magic bevindt zich in de Inge sloten java script.

In C# het taal voorbeeld wordt Java script in index. cshtml gebruikt om gebruik te maken van de bibliotheek voor automatisch aanvullen van de [jQuery-gebruikers interface](https://jqueryui.com/autocomplete/) Deze bibliotheek voegt de ervaring automatisch aanvullen toe aan het zoekvak door asynchrone aanroepen naar de MVC-controller te maken om suggesties op te halen. De taal versie van Java script bevindt zich in IndexJavaScript. cshtml. Het bevat het onderstaande script voor de zoek balk, evenals REST API aanroepen naar Azure Search.

Laten we eens kijken naar de Java script-code voor het eerste voor beeld, waarmee de functie automatisch aanvullen van de gebruikers interface van jQuery wordt aangeroepen in een aanvraag voor suggesties:

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

De bovenstaande code wordt uitgevoerd in de browser op pagina laden om de automatisch aanvullen van de jQuery-gebruikers interface te configureren voor het invoervak ' example1a '.  `minLength: 3` zorgt ervoor dat aanbevelingen alleen worden weergegeven wanneer er zich ten minste drie tekens in het zoekvak bevinden.  De bronwaarde is belangrijk:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

De bovenstaande regel vertelt de functie automatisch aanvullen van de jQuery-gebruikers interface waar u de lijst met items kunt ophalen die in het zoekvak worden weer gegeven. Aangezien dit project een MVC-project is, wordt de functie suggereren aangeroepen in HomeController.cs die de logica bevat voor het retour neren van query suggesties (meer informatie over suggesties in de volgende sectie). Deze functie geeft ook enkele para meters door aan het beheren van hooglichten, fuzzy matching en term. Door de JavaScript-API voor automatisch aanvullen wordt de parameter 'term' toegevoegd.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Het voorbeeld uitbreiden om ondersteuning te bieden voor zoeken bij benadering

Zoeken bij benadering zorgt ervoor dat u resultaten krijgt te zien op basis van treffers bij benadering, zelfs als de gebruiker een woord in het zoekvak onjuist heeft gespeld. Hoewel dit niet vereist is, wordt de robuustheid van een typeahead-ervaring aanzienlijk verbeterd. U gaat dit uitproberen door de bronregel zo te wijzigen dat zoeken bij benadering mogelijk is.

Wijzig de volgende regel van dit:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

in dit:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Start de toepassing door op F5 te drukken.

Typ een woord als 'exclasief' en u ziet hoe resultaten worden weergegeven voor 'exclusief', zelfs als deze niet exact overeenkomen met de letters die u hebt getypt.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>automatisch aanvullen van jQuery wordt ondersteund door Azure Search automatisch aanvullen

Tot nu toe is de zoek UX-code gecentreerd op de suggesties. In het volgende code blok wordt de functie automatisch aanvullen van de jQuery-gebruikers interface weer gegeven (regel 91 in index. cshtml), waarbij een aanvraag voor Azure Search automatisch aanvullen wordt door gegeven:

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

## <a name="c-example"></a>C#Hierbij

Nu we de Java script-code voor de webpagina hebben gecontroleerd, gaan we kijken naar de C# controller code aan server zijde waarmee de voorgestelde overeenkomsten worden opgehaald met behulp van de Azure Search .NET SDK.

Open het **HomeController.cs** -bestand in de map controllers. 

Het eerste wat u mogelijk ziet is een methode boven aan de klasse `InitSearch`. Met deze methode maakt u een geverifieerde HTTP-index client voor de Azure Search-service. Zie [Azure Search gebruiken vanuit een .NET-toepassing](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)voor meer informatie.

Bekijk op regel 41 de functie Voorst Ellen. Deze is gebaseerd op de [methode DocumentsOperationsExtensions. suggereren](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

De functie Voorstellen maakt gebruik van twee parameters die bepalen of er bij het invoeren van de zoekterm markeringen voor treffers worden geretourneerd of zoeken bij benadering wordt gebruikt. De methode maakt een [SuggestParameters-object](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), dat vervolgens wordt door gegeven aan de API Voorst Ellen. Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

Op regel 69 ziet u de functie automatisch aanvullen. Het is gebaseerd op de [methode DocumentsOperationsExtensions. autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

De functie automatisch aanvullen neemt de invoer van de zoek term. De methode maakt een [AutoCompleteParameters-object](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

Voeg een onderbrekingspunt toe aan het begin van de functie Voorstellen en doorloop de code. (optioneel) Let op het antwoord dat door de SDK is geretourneerd en hoe het wordt geconverteerd naar het resultaat dat wordt geretourneerd door de-methode.

De andere voor beelden op de pagina volgen hetzelfde patroon om treffer markeringen en facetten toe te voegen om de caching aan client zijde van de resultaten van AutoAanvullen te ondersteunen. Loop door elk van deze voorbeelden heen zodat u begrijpt hoe ze werken en hoe u ze kunt gebruiken in uw zoekervaring.

## <a name="javascript-example"></a>Java script-voor beeld

Een Java script-implementatie van automatisch aanvullen en suggesties roept de REST API, met behulp van een URI als bron om de index en bewerking op te geven. 

Als u de Java script-implementatie wilt bekijken, opent u **IndexJavaScript. cshtml**. U ziet dat de functie automatisch aanvullen van de jQuery-gebruikers interface ook wordt gebruikt voor het zoekvak, het verzamelen van zoek termen en het maken van asynchrone aanroepen naar Azure Search om voorgestelde overeenkomsten of voltooide voor waarden op te halen. 

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

Als u dit voor beeld vergelijkt met het bovenstaande voor beeld dat de start controller aanroept, zult u merken dat er verschillende overeenkomsten zijn.  De configuratie voor automatisch aanvullen voor `minLength` en `position` zijn identiek. 

De belangrijke wijziging hier is de bron. In plaats van de methode suggereren in de start controller aan te roepen, wordt een REST-aanvraag in een Java script-functie gemaakt en uitgevoerd met behulp van Ajax. De reactie wordt vervolgens verwerkt in 'geslaagd' en als bron gebruikt.

Bij REST-aanroepen worden Uri's gebruikt om op te geven of een [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) of [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions) van een API-oproep wordt gedaan. De volgende Uri's bevinden zich respectievelijk op regel 9 en 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

Op regel 148 kunt u een script vinden waarmee de `autocompleteUri`wordt aangeroepen. De eerste aanroep `suggestUri` van is op regel 39.

> [!Note]
> Het maken van REST-aanroepen naar de service in Java script wordt hier aangeboden als een handige demonstratie van de REST API, maar mag niet worden beschouwd als een best practice of aanbeveling. Het opnemen van een API-sleutel en het eind punt in een script opent uw service tot denial of service-aanvallen voor iedereen die deze waarden van het script kan lezen. Hoewel het veilig is Java script te gebruiken voor leer doeleinden, bijvoorbeeld op indicers die worden gehost op de gratis service, C# kunt u het beste Java gebruiken of voor indexering en query bewerkingen in productie code. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>NYCJobs configureren om uit te voeren op uw service

Tot nu toe hebt u de gehoste NYCJobs-demo-index gebruikt. Als u volledig inzicht wilt in alle code, inclusief de index, volgt u deze instructies voor het maken en laden van de index in uw eigen zoek service.

1. [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor dit voor beeld een gratis service gebruiken. 

   > [!Note]
   > Als u de gratis Azure Search-service gebruikt, bent u beperkt tot drie indexen. De gegevenslader NYCJobs maakt twee indexen. Zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe indexen.

1. Voorbeeld code voor [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) downloaden.

1. Open in de map DataLoader van de NYCJobs-voorbeeld code **DataLoader. SLN** in Visual Studio.

1. Voeg de verbindings gegevens voor uw Azure Search-service toe. Open App.config in het project DataLoader en wijzig de app-instellingen TargetSearchServiceName en TargetSearchServiceApiKey zodat deze verwijzen naar uw Azure Search-service en Azure Search Service API-sleutel. Deze informatie vindt u in de Azure Portal.

1. Druk op F5 om de toepassing te starten, twee indexen te maken en de voorbeeld gegevens van de NYCJob te importeren.

1. Open **AutocompleteTutorial. SLN** en bewerk het bestand Web. config in het **AutocompleteTutorial** -project. Wijzig de waarden voor SearchServiceName en SearchServiceApiKey in waarden die geldig zijn voor uw zoek service.

1. Druk op F5 om de toepassing uit te voeren. De voor beeld-web-app wordt geopend in de standaard browser. De ervaring is identiek aan de sandbox-versie, maar alleen de index en gegevens worden gehost op uw service.

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld worden de basis stappen beschreven voor het maken van een zoekvak dat automatisch aanvullen en suggesties ondersteunt. U hebt gezien hoe u een ASP.NET MVC-toepassing kunt bouwen en de Azure Search .NET SDK of REST API kunt gebruiken om suggesties op te halen.

Als volgende stap probeert u de integratie van suggesties en automatisch aanvullen in uw zoek ervaring. De volgende referentie artikelen moeten helpen.

> [!div class="nextstepaction"]
> [Autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Suggestions REST API ](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Facets index attribute on a Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) (Indexattribuut Facetten in een Create Index REST API)

