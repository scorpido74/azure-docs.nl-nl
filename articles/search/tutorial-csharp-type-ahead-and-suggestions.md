---
title: C#zelf studie over automatisch aanvullen en suggesties
titleSuffix: Azure Cognitive Search
description: In deze zelf studie ziet u hoe u automatisch aanvullen en suggesties kunt gebruiken voor het verzamelen van de invoer van zoek termen van gebruikers met een vervolg keuzelijst. Het is gebaseerd op een bestaand Hotels-project.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b542476ac1c9b6d4368d97eb4db76518eb2dba03
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114560"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C#zelf studie: automatisch aanvullen en suggesties toevoegen-Azure Cognitive Search

Meer informatie over het implementeren van AutoAanvullen (type vooruit en suggesties) wanneer een gebruiker begint met typen in het zoekvak. In deze zelf studie worden de type vooruit en suggesties resultaten afzonderlijk weer gegeven. vervolgens wordt een methode voor het combi neren van deze resultaten weer gegeven om een uitgebreide gebruikers ervaring te creëren. Een gebruiker hoeft alleen twee of drie sleutels te typen om alle beschik bare resultaten te vinden. Deze zelf studie is gebaseerd op het paginerings project dat in de [ C# zelf studie is gemaakt: Zoek resultaten pagineren-Azure Cognitive Search-](tutorial-csharp-paging.md) zelf studie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Suggesties toevoegen
> * Markering toevoegen aan de suggesties
> * Automatisch aanvullen toevoegen
> * Automatisch aanvullen en suggesties combi neren

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

U hebt de [ C# zelf studie: Zoek resultaten pagineren-Azure Cognitive Search](tutorial-csharp-paging.md) project actief. Dit project kan uw eigen versie zijn, die u in de vorige zelf studie hebt voltooid, of u installeert deze vanuit GitHub: [Maak eerst een app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Suggesties toevoegen

Laten we beginnen met het eenvoudigste aantal alternatieven voor de gebruiker: een vervolg keuzelijst met suggesties.

1. Wijzig in het bestand index. cshtml de instructie **TextBoxFor** in het volgende.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    Hier is de ID van het zoekvak ingesteld op **azureautosuggest**.

2. Nadat u deze instructie hebt opgegeven, voert u na het sluiten van **&lt;/div&gt;** dit script in.

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

    Dit script is via dezelfde ID verbonden met het zoekvak. Er is ook mini maal twee tekens nodig om de zoek opdracht te activeren en we noemen de **suggestie** actie in de start controller met twee query parameters: **hooglichten** en **fuzzy**, beide ingesteld op ONWAAR in dit exemplaar.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Verwijzingen naar jQuery-scripts toevoegen aan de weer gave

De functie voor automatisch aanvullen, die wordt aangeroepen in het bovenstaande script, is niet iets waar we zelf kunnen schrijven wanneer deze beschikbaar zijn in de jQuery-bibliotheek. 

1. Om toegang te krijgen tot de jQuery-bibliotheek, wijzigt u de sectie &lt;Head&gt; van het weergave bestand in de volgende code.

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

2. We moeten ook een regel verwijderen, of een opmerking uit nemen, die verwijst naar jQuery in het bestand _Layout. cshtml (in de **weer gaven/gedeelde** map). Zoek de volgende regels en check de eerste script regel uit zoals weer gegeven. Met deze wijziging wordt het conflicteren van verwijzingen naar jQuery voor komen.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Nu kunnen we de vooraf gedefinieerde jQuery-functies voor automatisch aanvullen gebruiken.

### <a name="add-the-suggest-action-to-the-controller"></a>De actie suggereren toevoegen aan de controller

1. Voeg in de start controller de actie **suggesties** toe (bijvoorbeeld na de **pagina** actie).

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

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    De **bovenste** para meter geeft aan hoeveel resultaten moeten worden geretourneerd (als deze niet zijn opgegeven, is de standaard waarde 5). Er is een _suggestie_ opgegeven voor de Azure-index, die wordt uitgevoerd wanneer de gegevens worden ingesteld, en niet door een client-app, zoals deze zelf studie. In dit geval wordt het Voorst Ellen ' AG ' genoemd en wordt het veld naam van de **Hotel** zoek opdracht niets anders. 

    Bij fuzzy matching kan ' bijna missers ' worden opgenomen in de uitvoer. Als de **Marks** -para meter is ingesteld op True, worden er vette HTML-tags aan de uitvoer toegevoegd. Deze twee para meters worden in de volgende sectie ingesteld op True.

2. Mogelijk worden er enkele syntaxis fouten weer geven. Als dit het geval is, voegt **u de volgende twee instructies** toe aan de bovenkant van het bestand.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Voer de app uit. Krijgt u een reeks opties wanneer u "io" opgeeft, bijvoorbeeld? Probeer nu ' pa '.

    ![Als u ' io ' typt, worden twee suggesties onthuld](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    U ziet dat de letters die u invoert, _moeten_ beginnen met een woord en niet gewoon in het woord worden opgenomen.

4. Stel in het script weer geven **& fuzzy** in op True en voer de app opnieuw uit. Voer nu ' io ' in. De zoek opdracht gaat ervan uit dat u een verkeerde letter hebt!
 
    ![Typ ' pa ' waarbij fuzzy is ingesteld op True](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Als u geïnteresseerd bent, wordt in de [lucene-query syntaxis in Azure Cognitive Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) de logica beschreven die in fuzzy searchs wordt gebruikt.

## <a name="add-highlighting-to-the-suggestions"></a>Markering toevoegen aan de suggesties

We kunnen het uiterlijk van de suggesties voor de gebruiker verbeteren door de **markeringen** para meter in te stellen op True. Eerst moeten we echter code toevoegen aan de weer gave om de vette tekst weer te geven.

1. Voeg in de weer gave (index. cshtml) het volgende script toe na het **azureautosuggest** -script dat u hierboven hebt ingevoerd.

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

2. Wijzig nu de ID van het tekstvak zodat dit als volgt wordt gelezen.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Voer de app opnieuw uit en u ziet dat de ingevoerde tekst in de suggesties wordt weer gegeven. Stel, typ ' pa '.
 
    ![Typ ' pa ' met markering](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. De logica die in het markerings script hierboven wordt gebruikt, is niet Foolproof. Als u een term opgeeft die twee keer in dezelfde naam wordt weer gegeven, zijn de vetgedrukte resultaten niet helemaal wat u wilt. Typ "mo".

    Een van de vragen die een ontwikkelaar moet beantwoorden is, wanneer het een script is dat ' goed genoeg ' is, en wanneer het quirks moet worden opgelost. Het is niet meer mogelijk om in deze zelf studie te markeren, maar u kunt ook een nauw keurig algoritme zoeken als u rekening moet houden als u nog meer markering wilt maken.

## <a name="add-autocompletion"></a>Automatisch aanvullen toevoegen

Een andere variatie, die iets anders is dan suggesties, is automatisch aanvullen (ook wel ' type vooruit ' genoemd). Ook hier gaan we beginnen met de eenvoudigste implementatie voordat u overstapt op het verbeteren van de gebruikers ervaring.

1. Voer het volgende script in de weer gave in, volgens uw vorige scripts.

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

2. Wijzig nu de ID van het tekstvak, zodat dit als volgt wordt gelezen.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. In de start controller moet u de actie **AutoAanvullen** invoeren, onder de actie Voorst **Ellen** .

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

    U ziet dat we dezelfde functie voor *suggesties* , genaamd ' AG ', in de zoek opdracht AutoAanvullen gebruiken om suggesties te vinden (zodat we de namen van hotels alleen automatisch kunnen volt ooien).

    Er zijn verschillende **AutocompleteMode** -instellingen en we gebruiken **OneTermWithContext**. Raadpleeg [Azure automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) voor een beschrijving van het bereik van de opties.

4. Voer de app uit. U ziet hoe het bereik van opties die worden weer gegeven in de vervolg keuzelijst enkele woorden zijn. Typ woorden die beginnen met ' re '. U ziet hoe het aantal opties vermindert naarmate er meer letters worden getypt.

    ![Typen met eenvoudige automatisch aanvullen](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Zoals het het geval is, is het script dat u eerder hebt uitgevoerd waarschijnlijk handiger dan dit script voor automatisch aanvullen. Als u automatisch aanvullen meer gebruikers vriendelijk wilt maken, kunt u het beste toevoegen aan de suggestie voor suggesties.

## <a name="combine-autocompletion-and-suggestions"></a>Automatisch aanvullen en suggesties combi neren

Het combi neren van AutoAanvullen en suggesties is het meest complexe van onze opties en biedt waarschijnlijk de beste gebruikers ervaring. Wat we willen laten zien, wordt inline weer gegeven met de tekst die wordt getypt, de eerste keuze van Azure Cognitive Search voor het Autovullen van de tekst. We willen ook een reeks suggesties als een vervolg keuzelijst.

Er zijn bibliotheken die deze functionaliteit bieden. dit wordt vaak ' inline-automatisch aanvullen ' of een vergelijk bare naam genoemd. We zullen deze functie echter zelf implementeren, zodat u kunt zien wat er gebeurt. In dit voor beeld gaat u eerst aan de slag gaan met de controller.

1. We moeten een actie toevoegen aan de controller die slechts één automatisch aanvullens resultaat retourneert, samen met een opgegeven aantal suggesties. Deze actie **AutocompleteAndSuggest**wordt aangeroepen. Voeg in de start controller de volgende actie toe, volgens uw andere nieuwe acties.

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

    Boven aan de lijst met **resultaten** wordt één optie voor automatisch aanvullen weer gegeven, gevolgd door alle suggesties.

2. In de weer gave implementeren we eerst een truc, zodat een lichtgrijs woord voor automatisch aanvullen wordt weer gegeven onder vetgedrukte tekst die door de gebruiker wordt ingevoerd. HTML bevat relatieve positionering voor dit doel. Wijzig de **TextBoxFor** -instructie (en de bijbehorende omliggende &lt;div&gt;-instructies) naar het volgende. Als u dit zoekvak 39 pixels uit de standaard locatie haalt **, wordt het** tweede zoekvak direct onder ons normale Zoek venster vermeld.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Opmerking: we wijzigen de ID opnieuw in **azureautocomplete** in dit geval.

3. Voer in de weer gave ook het volgende script in, na alle scripts die u tot nu toe hebt ingevoerd. Er is heel veel.

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

    Let op het slimme gebruik van de **interval** functie voor het wissen van de onderliggende tekst wanneer deze niet meer overeenkomt met wat de gebruiker typt en ook om dezelfde case (boven of onder elkaar) in te stellen als de gebruiker typt ("PA" komt overeen met "PA", "PA", "PA" bij het zoeken), zodat de overlappende tekst er netjes uitziet.

    Lees de opmerkingen in het script om een uitgebreidere uitleg te krijgen.

4. Ten slotte moeten we een kleine aanpassing van twee HTML-klasse maken om ze transparant te maken. Voeg de volgende regel toe aan de klassen **searchBoxForm** en **searchBox** in het bestand Hotels. CSS.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Voer nu de app uit. Geef pa op in het zoekvak. Krijgt u "Palace" als voor stel voor automatisch aanvullen, samen met twee hotels die "PA" bevatten?

    ![Typen met inline automatisch aanvullen en suggesties](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Probeer met de tabtoets te gaan en selecteer suggesties met de pijl toetsen en de tab-toets en probeer het opnieuw met de muis en één klik. Controleer of het script al deze situaties netjes verwerkt.

    U kunt ervoor kiezen om te laden in een bibliotheek die deze functie voor u biedt, maar u weet nu ten minste één manier om inline automatisch aanvullen te laten werken.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende Takeaways van dit project:

* Automatisch aanvullen (ook wel ' type vooruit ' genoemd) en suggesties kunnen de gebruiker in staat stellen om slechts enkele sleutels te typen om precies te vinden wat ze willen.
* Automatisch aanvullen en suggesties die samen werken, kunnen een uitgebreide gebruikers ervaring bieden.
* De functie voor automatisch aanvullen altijd testen met alle vormen van invoer.
* Het gebruik van de functie **setInterval** kan nuttig zijn bij het controleren en corrigeren van UI-elementen.

## <a name="next-steps"></a>Volgende stappen

In de volgende zelf studie ziet u een andere manier om de gebruikers ervaring te verbeteren, met behulp van facetten om Zoek opdrachten te beperken met één klik.

> [!div class="nextstepaction"]
> [C#Zelf studie: facetten gebruiken om de navigatie te helpen-Azure Cognitive Search](tutorial-csharp-facets.md)


