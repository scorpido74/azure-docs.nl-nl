---
title: Automatisch aanvullen en suggesties
titleSuffix: Azure Cognitive Search
description: Deze zelfstudie toont automatisch aanvullen en suggesties als een manier om invoer van zoektermen van gebruikers te verzamelen met behulp van vervolgkeuzelijst. Het bouwt voort op een bestaand hotelsproject.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 6b74c3bbb811c122950fd969a8797e87f8f77f86
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641078"
---
# <a name="c-tutorial-add-autocomplete-and-suggestions---azure-cognitive-search"></a>C# zelfstudie: Automatisch aanvullen en suggesties toevoegen - Azure Cognitive Search

Meer informatie over het implementeren van automatisch aanvullen (voortypen en voorgestelde documenten) wanneer een gebruiker begint te typen in een zoekvak. In deze zelfstudie tonen we automatisch voltooide query's en suggestieresultaten afzonderlijk en vervolgens samen. Een gebruiker hoeft mogelijk slechts twee of drie tekens te typen om alle beschikbare resultaten te vinden.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Suggesties toevoegen
> * Markering toevoegen aan de suggesties
> * Automatisch aanvullen toevoegen
> * Automatische voltooiing en suggesties combineren

## <a name="prerequisites"></a>Vereisten

Deze zelfstudie maakt deel uit van een reeks en bouwt voort op het paging-project dat is gemaakt in de [C# Tutorial: Search results pagetion - Azure Cognitive Search](tutorial-csharp-paging.md).

U ook de oplossing voor deze specifieke zelfstudie downloaden en uitvoeren: [3-add-typeahead.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)

## <a name="add-suggestions"></a>Suggesties toevoegen

Laten we beginnen met het eenvoudigste geval van het aanbieden van alternatieven voor de gebruiker: een vervolgkeuzelijst met suggesties.

1. Wijzig in het bestand index.cshtml de instructie `@id` **TextBoxFor** in **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Ga na deze instructie na het sluiten ** &lt;/div&gt;** in dit script in. Dit script maakt gebruik van de [widget Automatisch aanvullen](https://api.jqueryui.com/autocomplete/) uit de open-source-ui-bibliotheek om de vervolgkeuzelijst met voorgestelde resultaten weer te geven. 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    De ID "azureautosuggest" verbindt het bovenstaande script met het zoekvak. De bronoptie van de widget is ingesteld op een methode Stel voor die de API Voorstellen aanroept met twee queryparameters: **hooglichten** en **vaag,** beide ingesteld op false in dit geval. Ook is er minimaal twee tekens nodig om de zoekopdracht te activeren.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Verwijzingen naar jQuery-scripts toevoegen aan de weergave

1. Als u toegang wilt krijgen &lt;&gt; tot de jQuery-bibliotheek, wijzigt u het hoofdgedeelte van het weergavebestand in de volgende code:

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Omdat we een nieuwe jQuery-verwijzing introduceren, moeten we ook de standaardverwijzing van jQuery in het bestand _Layout.cshtml (in de map **Weergaven/Gedeelde bestanden)** verwijderen of erop reageren. Zoek de volgende regels en becommentarieer de eerste scriptregel zoals weergegeven. Deze wijziging voorkomt botsende verwijzingen naar jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Nu kunnen we de vooraf gedefinieerde Autocomplete jQuery-functies gebruiken.

### <a name="add-the-suggest-action-to-the-controller"></a>De actie Voorstellen toevoegen aan de controller

1. Voeg in de thuiscontroller de actie **Stel voor** (bijvoorbeeld na de **actie Pagina).**

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    De parameter **Top** geeft aan hoeveel resultaten moeten worden retournerd (als deze niet is opgegeven, is de standaardwaarde 5). Er wordt een _suggestie_ opgegeven in de Azure-index, die wordt uitgevoerd wanneer de gegevens zijn ingesteld, en niet door een client-app zoals deze zelfstudie. In dit geval wordt de suggester "sg" genoemd en zoekt hij het veld **HotelName** - niets anders. 

    Fuzzy matching maakt het mogelijk "near misses" in de uitvoer, tot één bewerkingsafstand. Als de parameter markeert de parameter **True** is ingesteld, worden vetgedrukte HTML-tags aan de uitvoer toegevoegd. We zullen deze twee parameters in de volgende sectie op true zetten.

2. Mogelijk worden er enkele syntaxisfouten. Voeg in dat verband de volgende twee **met behulp van** instructies toe aan de bovenkant van het bestand.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Voer de app uit. Krijgt u een scala aan opties wanneer u "po" invoert, bijvoorbeeld? Probeer nu "pa".

    ![Het typen van "po" onthult twee suggesties](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Merk op dat de letters die u invoert een woord _moeten_ starten en niet alleen in het woord moeten worden opgenomen.

4. Stel in het weergavescript **&vaag** in op true en voer de app opnieuw uit. Voer nu "po" in. Merk op dat de zoekopdracht ervan uitgaat dat je een brief verkeerd!
 
    ![Typen "pa" met fuzzy ingesteld op true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Als u geïnteresseerd bent, beschrijft de [syntaxis van Lucene query in Azure Cognitive Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) de logica die wordt gebruikt in vage zoekopdrachten in detail.

## <a name="add-highlighting-to-the-suggestions"></a>Markering toevoegen aan de suggesties

We kunnen het uiterlijk van de suggesties voor de gebruiker verbeteren door de parameter **markeren** op true in te stellen. We moeten echter eerst wat code toevoegen aan de weergave om de vetgedrukte tekst weer te geven.

1. Voeg in de weergave (index.cshtml) het volgende script toe nadat het **azureautosuggest-script** dat u hierboven hebt ingevoerd.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. Wijzig nu de ID van het tekstvak zodat het als volgt wordt gelezen.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Voer de app opnieuw uit en u moet de ingevoerde tekst vet in de suggesties zien. Probeer 'pa' te typen.
 
    ![Typen "pa" met markering](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. De logica die wordt gebruikt in het bovenstaande markeringsscript is niet waterdicht. Als u een term invoert die twee keer in dezelfde naam wordt weergegeven, zijn de vetgedrukte resultaten niet helemaal wat u zou willen. Probeer "mo" te typen.

    Een van de vragen die een ontwikkelaar moet beantwoorden is, wanneer werkt een script "goed genoeg", en wanneer moet de eigenaardigheden worden aangepakt. We zullen niet verder markeren in deze tutorial, maar het vinden van een nauwkeurig algoritme is iets om te overwegen als markeren niet effectief is voor uw gegevens. Zie [Hithighlighting voor](search-pagination-page-layout.md#hit-highlighting)meer informatie.

## <a name="add-autocomplete"></a>Automatisch aanvullen toevoegen

Een andere variant, die iets verschilt van suggesties, is autocompletion (ook wel "type-ahead") die een queryterm voltooit. Nogmaals, we beginnen met de eenvoudigste implementatie, voordat we de gebruikerservaring verbeteren.

1. Voer het volgende script in de weergave in, volgens de vorige scripts.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. Wijzig nu de ID van het tekstvak, zodat het als volgt wordt gelezen.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. In de home controller moeten we de **actie Autocomplete** invoeren, bijvoorbeeld onder de **actie Stel voor.**

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    Merk op dat we dezelfde *suggester* functie gebruiken, genaamd "sg", in de autocomplete zoeken zoals we deden voor suggesties (dus we zijn alleen proberen om autocomplete de hotelnamen).

    Er zijn een reeks instellingen voor **AutocompleteMode** en we gebruiken **OneTermWithContext.** Raadpleeg [de API Voor Automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) voor een beschrijving van extra opties.

4. Voer de app uit. Merk op hoe het scala aan opties in de vervolgkeuzelijst enkele woorden zijn. Probeer woorden te typen die beginnen met "re". Merk op hoe het aantal opties vermindert naarmate er meer letters worden getypt.

    ![Typen met basisautomatisch aanvullen](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Zoals het er nu uitziet, de suggesties script dat u eerder uitgevoerd is waarschijnlijk nuttiger dan deze autocompletion script. Om autocompletion gebruiksvriendelijker te maken, u het beste worden toegevoegd aan de suggestiezoekopdracht.

## <a name="combine-autocompletion-and-suggestions"></a>Automatische voltooiing en suggesties combineren

Het combineren van autocompletion en suggesties is de meest complexe van onze opties, en biedt waarschijnlijk de beste gebruikerservaring. Wat we willen is om weer te geven, in lijn met de tekst die wordt getypt, is de eerste keuze van Azure Cognitive Search voor het automatisch invullen van de tekst. Ook willen we een reeks suggesties als een vervolgkeuzelijst.

Er zijn bibliotheken die deze functionaliteit bieden - vaak "inline autocompletion" of een soortgelijke naam genoemd. We gaan deze functie echter native implementeren, zodat u zien wat er aan de hand is. We gaan in dit voorbeeld eerst aan de controller werken.

1. We moeten een actie toevoegen aan de controller die slechts één automatisch voltooiingsresultaat retourneert, samen met een opgegeven aantal suggesties. We zullen deze actie **AutocompleteAndSuggest**noemen. Voeg in de thuiscontroller de volgende actie toe, na uw andere nieuwe acties.

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    Een optie voor automatisch aanvullen wordt bovenaan de **resultatenlijst** geretourneerd, gevolgd door alle suggesties.

2. In de weergave implementeren we eerst een truc, zodat een lichtgrijs automatisch aanvullend woord wordt weergegeven onder veter tekst die door de gebruiker wordt ingevoerd. HTML bevat hiervoor relatieve positionering. Wijzig de **TextBoxFor-instructie** &lt;(en de omringende div-instructies)&gt; in het volgende, waarbij u opvalt dat een tweede zoekvak dat als **hieronder** is geïdentificeerd, direct onder ons normale zoekvak ligt, door dit zoekvak 39 pixels van de standaardlocatie te trekken!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Let op: we veranderen de ID opnieuw, in dit geval **azureautocomplete.**

3. Voer ook in de weergave het volgende script in, na alle scripts die u tot nu toe hebt ingevoerd. Er is heel veel aan.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Let op het slimme gebruik van de **intervalfunctie** om zowel de onderliggende tekst te wissen wanneer deze niet meer overeenkomt met wat de gebruiker typt, als ook om dezelfde behuizing (boven- of onder) in te stellen terwijl de gebruiker typt (zoals "pa" overeenkomt met "PA", "pA", "Pa" bij het zoeken), zodat de bedekte tekst netjes is.

    Lees de opmerkingen in het script om een vollediger begrip te krijgen.

4. Tot slot moeten we een kleine aanpassing aanbrengen in twee HTML-klasse om ze transparant te maken. Voeg de volgende regel toe aan de klassen **searchBoxForm** en **searchBox** in het bestand hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Voer nu de app uit. Voer 'pa' in het zoekvak in. Krijg je "palace" als de autocomplete suggestie, samen met twee hotels die "pa" bevatten?

    ![Typen met inline automatisch aanvullen en suggesties](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Probeer tabben om de suggestie automatisch aanvullen te accepteren en probeer suggesties te selecteren met de pijltoetsen en de tabtoets, en probeer het opnieuw met de muis en een enkele klik. Controleer of het script al deze situaties netjes afhandelt.

    U besluiten dat het eenvoudiger is om te laden in een bibliotheek die deze functie voor u biedt, maar nu weet u ten minste een manier om inline autocompletion te laten werken!

## <a name="takeaways"></a>Opgedane kennis

Denk aan de volgende afhaalmaaltijden van dit project:

* Autocompletion (ook bekend als "type-ahead") en suggesties kunnen de gebruiker in staat stellen om slechts een paar toetsen te typen om precies te vinden wat ze willen.
* Automatisch aanvullen en suggesties die samenwerken kunnen een rijke gebruikerservaring bieden.
* Test altijd automatische voltooiingsfuncties met alle vormen van invoer.
* Het gebruik van de functie **setInterval** kan handig zijn bij het verifiëren en corrigeren van gebruikersinterface-elementen.

## <a name="next-steps"></a>Volgende stappen

In de volgende tutorial, hebben we een kijkje op een andere manier van het verbeteren van de gebruikerservaring, met behulp van facetten om zoekopdrachten te beperken met een enkele klik.

> [!div class="nextstepaction"]
> [C# Zelfstudie: Gebruik facetten om navigatie te ondersteunen - Azure Cognitive Search](tutorial-csharp-facets.md)


