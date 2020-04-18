---
title: Automatisch aanvullen en suggesties toevoegen in een zoekvak
titleSuffix: Azure Cognitive Search
description: Schakel queryacties in Azure Cognitive Search in door suggesties te maken en aanvragen te formuleren die een zoekvak automatisch aanvullen met voltooide termen of woordgroepen. U ook voorgestelde overeenkomsten retourneren.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1d8085c6056cb0d2541999c3e9c249cde3da8834
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641256"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Automatisch aanvullen en suggesties toevoegen aan client-apps

Search-as-you-type is een veelgebruikte techniek voor het verbeteren van de productiviteit van door gebruikers geïnitieerde query's. In Azure Cognitive Search wordt deze ervaring ondersteund door *middel van automatisch aanvullen*, waarbij een term of woordgroep wordt voltooid op basis van gedeeltelijke invoer (het voltooien van 'micro' met 'microsoft'). Een ander formulier is *suggesties:* een korte lijst van overeenkomende documenten (boektitels retourneren met een ID, zodat u linken naar een detailpagina). Zowel automatisch aanvullen als suggesties zijn gebaseerd op een overeenkomst in de index. De service biedt geen query's die nul resultaten opleveren.

Als u deze ervaringen wilt implementeren in Azure Cognitive Search, hebt u het volgende nodig:

+ Een *suggester* op de achterkant.
+ Een *query* met automatische aan-/ smeerinformatie of de API voor suggesties op de aanvraag.
+ Een *ui-besturingselement* voor het afhandelen van zoek-naar-je-type interacties in uw client-app. We raden u aan hiervoor een bestaande JavaScript-bibliotheek te gebruiken.

In Azure Cognitive Search worden automatisch voltooide query's en voorgestelde resultaten opgehaald uit de zoekindex, uit geselecteerde velden die u hebt geregistreerd bij een suggestie. Een suggestiemaakt deel uit van de index en geeft aan welke velden inhoud bieden die een query voltooit, een resultaat suggereert of beide doet. Wanneer de index wordt gemaakt en geladen, wordt intern een suggestiestructuur gemaakt om voorvoegsels op te slaan die worden gebruikt voor het afstemmen op gedeeltelijke query's. Voor suggesties is het kiezen van geschikte velden die uniek zijn, of in ieder geval niet repetitief, essentieel voor de ervaring. Zie [Een suggestie maken voor](index-add-suggesters.md)meer informatie.

De rest van dit artikel is gericht op query's en clientcode. Het maakt gebruik van JavaScript en C # om belangrijke punten te illustreren. REST API-voorbeelden worden gebruikt om elke bewerking beknopt te presenteren. Zie [Volgende stappen](#next-steps)voor koppelingen naar end-to-end codevoorbeelden.

## <a name="set-up-a-request"></a>Een aanvraag instellen

Elementen van een aanvraag zijn de API[(Autocomplete REST](https://docs.microsoft.com/rest/api/searchservice/autocomplete) of [Suggestion REST),](https://docs.microsoft.com/rest/api/searchservice/suggestions)een gedeeltelijke query en een suggestie.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

De **suggesterName** geeft u de suggester-bewuste velden die worden gebruikt om termen of suggesties te voltooien. Met name voor suggesties moet de veldlijst bestaan uit suggesties die duidelijke keuzes bieden tussen overeenkomende resultaten. Op een site die computerspellen verkoopt, kan het veld de titel van het spel zijn.

De **zoekparameter** biedt de gedeeltelijke query, waarbij tekens worden ingevoerd om de queryaanvraag via het besturingselement jQuery Autocomplete. In het bovenstaande voorbeeld, "minecraf" is een statische illustratie van wat de controle zou kunnen zijn verstreken in.

De API's stellen geen minimumlengtevereisten op aan de gedeeltelijke query; het kan zo weinig zijn als één teken. JQuery Autocomplete biedt echter een minimale lengte. Een minimum van twee of drie tekens is typisch.

Overeenkomsten staan aan het begin van een term overal in de invoertekenreeks. Gezien "de snelle bruine vos", zowel autocomplete en suggesties zal overeenkomen met gedeeltelijke versies van "de", "snel", "bruin", of "vos", maar niet op gedeeltelijke infix termen als "rown" of "os". Bovendien bepaalt elke match de ruimte voor downstream-uitbreidingen. Een gedeeltelijke query van "quick br" zal overeenkomen op "quick brown" of "quick bread", maar noch "bruin" of "brood" op zichzelf zou overeenkomen, tenzij "snel" aan hen voorafgaat.

### <a name="apis"></a>API's

Volg deze links voor de referentiepagina's REST en .NET SDK:

+ [Suggesties REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [API VOOR automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Automatisch aanvullenMethttpMessagesAsync-methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Een reactie structureren

Reacties voor automatisch aanvullen en suggesties zijn wat u zou verwachten voor het patroon: [Automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) retourneert een lijst met termen, [Suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) retourneert termen plus een document-id, zodat u het document ophalen (gebruik de API voor het opzoeken [van document](https://docs.microsoft.com/rest/api/searchservice/lookup-document) om het specifieke document voor een detailpagina op te halen).

De antwoorden worden gevormd door de parameters op de aanvraag. Stel voor Automatisch aanvullen [**de modus automatisch aanvullen**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) in om te bepalen of de voltooiing van tekst plaatsvindt op een of twee voorwaarden. Voor Suggesties bepaalt het veld dat u kiest de inhoud van het antwoord.

Als u het antwoord verder wilt verfijnen, neemt u meer parameters op de aanvraag op. De volgende parameters zijn van toepassing op zowel Automatisch aanvullen als Suggesties.

| Parameter | Gebruik |
|-----------|-------|
| **$select** | Als u meerdere **sourceFields hebt,** gebruikt u **$select** om te kiezen welk veld waarden bijdraagt (`$select=GameTitle`). |
| **$filter** | Wedstrijdcriteria toepassen op de`$filter=ActionAdventure`resultaatset ( ). |
| **$top** | Beperk de resultaten tot`$top=5`een specifiek getal ( ).|

## <a name="add-user-interaction-code"></a>Gebruikersinteractiecode toevoegen

Voor het automatisch invullen van een queryterm of het neerzetten van een lijst met overeenkomende koppelingen is gebruikersinteractiecode nodig, meestal JavaScript, die aanvragen van externe bronnen kan verbruiken, zoals automatisch aanvullen of suggesties voor een Azure Search Cognitive-index.

Hoewel u deze code native schrijven, is het veel gemakkelijker om functies uit de bestaande JavaScript-bibliotheek te gebruiken. Dit artikel toont twee, een voor suggesties en een andere voor autocomplete. 

+ [Het object Automatisch aanvullen (jQuery-gebruikersinterface)](https://jqueryui.com/autocomplete/) wordt gebruikt in het voorbeeld suggestie. U een zoekvak maken en er vervolgens naar verwijzen in een JavaScript-functie die de widget Automatisch aanvullen gebruikt. Eigenschappen op de widget stellen de bron in (een functie automatisch aanvullen of suggesties), minimale lengte van invoertekens voordat actie wordt ondernomen en positionering.

+ [XDSoft Autocomplete plug-in](https://xdsoft.net/jqplugins/autocomplete/) wordt gebruikt in het voorbeeld Automatisch aanvullen.

We gebruiken deze bibliotheken om het zoekvak te bouwen dat zowel suggesties als automatisch aanvullen ondersteunt. De invoer die in het zoekvak wordt verzameld, wordt gekoppeld aan suggesties en acties voor automatisch aanvullen.

## <a name="suggestions"></a>Suggesties

In deze sectie vindt u een implementatie van voorgestelde resultaten, te beginnen met de definitie van het zoekvak. Het toont ook hoe en script dat de eerste JavaScript autocomplete bibliotheek waarnaar wordt verwezen in dit artikel aanroept.

### <a name="create-a-search-box"></a>Een zoekvak maken

Ervan uitgaande dat de [bibliotheek voor jQuery-gebruikersinterface automatisch aanvullen](https://jqueryui.com/autocomplete/) en een MVC-project in C#, u het zoekvak definiëren met JavaScript in het bestand **Index.cshtml.** De bibliotheek voegt de interactie tussen zoek en u toe toe aan het zoekvak door asynchrone aanroepen naar de MVC-controller om suggesties op te halen.

In **Index.cshtml** onder de map \Views\Home ziet een regel om een zoekvak te maken als volgt:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Dit voorbeeld is een eenvoudig invoertekstvak met een klasse voor styling, een ID waarnaar wordt verwezen door JavaScript en tijdelijke aanduidingstekst.  

Sluit JavaScript in dat verwijst naar het zoekvak in hetzelfde bestand. De volgende functie roept de API Voorstellen aan, die voorgestelde overeenkomende documenten opvraagt op basis van gedeeltelijke term-invoer:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

De `source` vertelt de jQuery UI Autocomplete functie waar de lijst met items te krijgen om weer te geven onder het zoekvak. Aangezien dit project een MVC-project is, wordt de functie **Voorstellen** in **HomeController.cs** aanroepen die de logica bevat voor het retourneren van querysuggesties. Deze functie passeert ook een paar parameters om hoogtepunten, fuzzy matching en term te beheren. Door de JavaScript-API voor automatisch aanvullen wordt de parameter 'term' toegevoegd.

Het `minLength: 3` zorgt ervoor dat aanbevelingen alleen worden weergegeven als er ten minste drie tekens in het zoekvak staan.

### <a name="enable-fuzzy-matching"></a>Fuzzy matching inschakelen

Zoeken bij benadering zorgt ervoor dat u resultaten krijgt te zien op basis van treffers bij benadering, zelfs als de gebruiker een woord in het zoekvak onjuist heeft gespeld. De bewerkingsafstand is 1, wat betekent dat er een maximale discrepantie van één teken kan zijn tussen de gebruikersinvoer en een overeenkomst. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Markeringen inschakelen

Met markering wordt de lettertypestijl toepast op de tekens in het resultaat die overeenkomen met de invoer. Als de gedeeltelijke invoer bijvoorbeeld 'micro' is, wordt het resultaat weergegeven als **microzacht,** **microbereik**enzovoort. Markeren is gebaseerd op de parameters HighlightPreTag en HighlightPostTag, gedefinieerd in lijn met de functie Suggestie.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Stel voor, functie

Als u C# en een MVC-toepassing gebruikt, is **HomeController.cs** bestand onder de map Controllers de plaats waar u een klasse maken voor voorgestelde resultaten. In .NET is een functie Voorstellen gebaseerd op de [methode DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

De `InitSearch` methode maakt een geverifieerde HTTP-indexclient voor de Azure Cognitive Search-service. Zie Azure Cognitive Search gebruiken [vanuit een .NET-toepassing](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)voor meer informatie over de .NET SDK.

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

## <a name="autocomplete"></a>Automatisch aanvullen

Tot nu toe is de zoek UX-code gecentreerd op suggesties. Het volgende codeblok toont automatisch aanvullen met de functie XDSoft jQuery UI Autocomplete, waarbij een aanvraag voor Azure Cognitive Search automatisch wordt voltooid. Net als bij de suggesties, in een C # applicatie, code die gebruikersinteractie ondersteunt gaat in **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Automatisch aanvullen, functie

Automatisch aanvullen is gebaseerd op de [methode DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Net als bij suggesties, zou deze code blok gaan in het **HomeController.cs** bestand.

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

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

De functie Automatisch aanvullen neemt de invoer van de zoekterm. De methode maakt een [object AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Het resultaat wordt vervolgens geconverteerd naar JSON, zodat deze in de client kan worden weergegeven.

## <a name="next-steps"></a>Volgende stappen

Volg deze links voor end-to-end instructies of code die beide zoek-als-je-type ervaringen weergeven. Beide codevoorbeelden omvatten hybride implementaties van suggesties en automatisch aanvullen samen.

+ [Zelfstudie: Uw eerste app maken in C# (les 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C#code voorbeeld: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# en JavaScript met VOORBEELD VAN REST side-by-side code](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)