---
title: C# zelfstudie over pagination met zoekresultaten
titleSuffix: Azure Cognitive Search
description: Deze tutorial toont paging van zoekresultaten. Het bouwt voort op een bestaand hotels project, met paging door de eerste, volgende, vorige, laatste en genummerde knoppen. Een tweede pagingsysteem maakt gebruik van oneindig scrollen, geactiveerd door een verticale schuifbalk naar de ondergrens te verplaatsen.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121517"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C# zelfstudie: paginatie met zoekresultaten - Azure Cognitive Search

Leer hoe u twee verschillende pagingsystemen implementeert, de eerste op basis van paginanummers en de tweede op oneindig scrollen. Beide systemen van paging worden op grote schaal gebruikt, en het selecteren van de juiste is afhankelijk van de gebruikerservaring die u zou willen met de resultaten. In deze zelfstudie worden de paging-systemen gebouwd in het project dat is gemaakt in de [C#-zelfstudie: Uw eerste app maken -](tutorial-csharp-create-first-app.md) Azure Cognitive Search-zelfstudie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Uw app uitbreiden met genummerde paging
> * Uw app uitbreiden met oneindig scrollen

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

Laat de [C#-zelfstudie: maak uw eerste app - Azure Cognitive Search-project](tutorial-csharp-create-first-app.md) operationeel. Dit project kan uw eigen versie zijn of het installeren vanuit GitHub: [Eerste app maken.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="extend-your-app-with-numbered-paging"></a>Uw app uitbreiden met genummerde paging

Genummerde paging is de paging systeem van keuze van de belangrijkste internet zoekmachines en de meeste andere zoekwebsites. Genummerde paging bevat meestal een "volgende" en "vorige" optie in aanvulling op een bereik van de werkelijke paginanummers. Ook een "eerste pagina" en "laatste pagina" optie kan ook beschikbaar zijn. Deze opties geven een gebruiker zeker controle over het navigeren door pagina-gebaseerde resultaten.

We voegen een systeem toe dat de eerste, vorige, volgende en laatste opties bevat, samen met paginanummers die niet vanaf 1 beginnen, maar in plaats daarvan de huidige pagina omringen waarop de gebruiker zich bevindt (dus als de gebruiker bijvoorbeeld naar pagina 10 kijkt, worden paginanummers 8, 9, 10, 11 en 12 weergegeven).

Het systeem is flexibel genoeg om het aantal zichtbare paginanummers in een globale variabele in te stellen.

Het systeem behandelt de links-meeste en meest rechtse paginanummerknoppen als speciaal, wat betekent dat ze het wijzigen van het bereik van de weergegeven paginanummers activeren. Als bijvoorbeeld paginanummers 8, 9, 10, 11 en 12 worden weergegeven en de gebruiker op 8 klikt, wordt het bereik van paginanummers gewijzigd in 6, 7, 8, 9 en 10. En er is een gelijkaardige verschuiving aan het recht als zij 12 selecteerden.

### <a name="add-paging-fields-to-the-model"></a>Pagingvelden toevoegen aan het model

Laat de basisoplossing voor de zoekpagina openen.

1. Open het SearchData.cs-modelbestand.

2. Voeg eerst enkele globale variabelen toe. In MVC worden globale variabelen gedeclareerd in hun eigen statische klasse. **ResultsPerPage** stelt het aantal resultaten per pagina in. **MaxPageRange** bepaalt het aantal zichtbare paginanummers in de weergave. **PageRangeDelta** bepaalt hoeveel pagina's links of rechts het paginabereik moet worden verschoven, wanneer het meest-linkse of het meest rechtse paginanummer is geselecteerd. Typisch dit laatste nummer is ongeveer de helft van **MaxPageRange**. Voeg de volgende code toe aan de naamruimte.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Als u dit project uitvoert op een apparaat met een kleiner scherm, zoals een laptop, u overwegen **ResultsPerPage** in 2 te wijzigen.

3. Voeg bijvoorbeeld paging-eigenschappen toe aan de klasse **SearchData** na de eigenschap **searchText.**

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Een tabel met pagingopties toevoegen aan de weergave

1. Open het bestand index.cshtml en voeg de &lt;volgende&gt; code toe vlak voor de afsluitende /bodytag. Deze nieuwe code presenteert een tabel met paging-opties: eerste, vorige, 1, 2, 3, 4, 5, volgende, laatste.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">|&lt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage > 0)
                {
                    <p class="pageButton">
                        @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&lt;</p>
                }
            </td>

            @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
            {
                <td>
                    @if (Model.currentPage == pn)
                    {
                        // Convert displayed page numbers to 1-based and not 0-based.
                        <p class="pageSelected">@(pn + 1)</p>
                    }
                    else
                    {
                        <p class="pageButton">
                            @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                        </p>
                    }
                </td>
            }

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;</p>
                }
            </td>

            <td>
                @if (Model.currentPage < Model.pageCount - 1)
                {
                    <p class="pageButton">
                        @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                    </p>
                }
                else
                {
                    <p class="pageButtonDisabled">&gt;|</p>
                }
            </td>
        </tr>
    </table>
    }
    ```

    We gebruiken een HTML-tabel om dingen netjes uit te lijnen. Maar alle actie komt @Html.ActionLink uit de verklaringen, elke aanroepen van de controller met een **nieuw** model gemaakt met verschillende vermeldingen aan de **paging** eigenschap die we eerder toegevoegd.

    De opties voor de eerste en laatste pagina verzenden geen tekenreeksen zoals 'eerste' en 'laatste', maar sturen in plaats daarvan de juiste paginanummers.

2. Voeg enkele pagingklassen toe aan de lijst met HTML-stijlen in het bestand hotels.css. De klasse **PageSelected** is er om de pagina te identificeren die de gebruiker momenteel bekijkt (door het getal vet te draaien) in de lijst met paginanummers.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Een actie Pagina toevoegen aan de controller

1. Open het HomeController.cs-bestand en voeg de **actie Pagina** toe. Deze actie reageert op een van de geselecteerde paginaopties.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    De **RunQueryAsync-methode** toont nu een syntaxisfout vanwege de derde parameter, die we zo ver zullen komen.

    > [!Note]
    > De **TempData-aanroepen** slaan een waarde (een **object)** op in tijdelijke opslag, hoewel deze opslag _slechts_ één gesprek blijft bestaan. Als we iets op te slaan in tijdelijke gegevens, zal het beschikbaar zijn voor de volgende oproep tot een controller actie, maar zal zeker worden gegaan door de oproep na dat! Vanwege deze korte levensduur slaan we de zoektekst en paging-eigenschappen op in tijdelijke opslag elke oproep naar **Pagina.**

2. De actie **Index(model)** moet worden bijgewerkt om de tijdelijke variabelen op te slaan en de parameter de meest linkse pagina toe te voegen aan de **RunQueryAsync-aanroep.**

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. De **RunQueryAsync-methode** moet aanzienlijk worden bijgewerkt. We gebruiken de velden **Overslaan,** **Top**en **IncludeTotalResultCount** van de klasse **SearchParameters** om slechts één pagina met resultaten op te vragen, te beginnen bij de instelling **Overslaan.** We moeten ook de paging variabelen voor onze mening te berekenen. Vervang de hele methode door de volgende code.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
                   SearchMode = SearchMode.All,

                   // Skip past results that have already been returned.
                   Skip = page * GlobalVariables.ResultsPerPage,

                   // Take only the next page worth of results.
                   Top = GlobalVariables.ResultsPerPage,

                   // Include the total number of results.
                   IncludeTotalResultCount = true,
               };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. Tot slot moeten we een kleine wijziging aanbrengen in het standpunt. De variabele **resultsList.Results.Count** bevat nu het aantal resultaten dat op één pagina wordt geretourneerd (3 in ons voorbeeld), niet het totale aantal. Omdat we het **IncludeTotalResultCount** op true hebben ingesteld, bevat de variabele **resultsList.Count** nu het totale aantal resultaten. Zoek dus waar het aantal resultaten in de weergave wordt weergegeven en wijzig deze in de volgende code.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Er is een prestatiehit, maar meestal niet veel van een, door **IncludeTotalResultCount** in te stellen op true, omdat dit totaal moet worden berekend door Azure Cognitive Search. Bij complexe gegevenssets wordt gewaarschuwd dat de geretourneerde waarde een _benadering_is. Voor onze hotelgegevens is deze accuraat.

### <a name="compile-and-run-the-app"></a>De app compileren en uitvoeren

Selecteer nu **Starten zonder foutopsporing** (of druk op de F5-toets).

1. Zoek op een tekst die veel resultaten zal geven (zoals "wifi"). u pagina netjes door de resultaten?

    ![Genummerde paging door "pool" resultaten](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Klik op de meest rechtse en later meest links-meeste paginanummers. Passen de paginanummers zich op de juiste manier aan om de pagina waarop u zich bevindt te centreren?

3. Zijn de "eerste" en "laatste" opties nuttig? Sommige populaire zoekopdrachten op het web gebruiken deze opties, en anderen niet.

4. Ga naar de laatste pagina met resultaten. De laatste pagina is de enige pagina die mogelijk minder dan **ResultsPerPage-resultaten** bevat.

    ![Onderzoek laatste pagina van "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Typ 'stad' en klik op zoeken. Er worden geen paging-opties weergegeven als er minder dan één pagina aan resultaten is.

    ![Op zoek naar "stad"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Nu op te slaan dit project en laten we proberen een alternatief voor deze vorm van paging.

## <a name="extend-your-app-with-infinite-scrolling"></a>Uw app uitbreiden met oneindig scrollen

Oneindig scrollen wordt geactiveerd wanneer een gebruiker een verticale schuifbalk naar de laatste van de weergegeven resultaten schuift. In dit geval wordt een oproep naar de server gemaakt voor de volgende pagina met resultaten. Als er geen resultaten meer zijn, wordt er niets geretourneerd en verandert de verticale schuifbalk niet. Als er meer resultaten zijn, worden deze toegevoegd aan de huidige pagina en wordt de schuifbalk gewijzigd om aan te geven dat er meer resultaten beschikbaar zijn.

Het belangrijkste punt hier is dat de pagina die wordt weergegeven niet wordt vervangen, maar toegevoegd aan de nieuwe resultaten. Een gebruiker kan altijd terugscrollen naar de eerste resultaten van de zoekopdracht.

Als u oneindig scrollen wilt implementeren, beginnen we met het project voordat een van de paginanummerschuifelementen is toegevoegd. Dus, als het nodig is, maak een andere kopie van de basiszoekpagina van GitHub: [Maak de eerste app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Pagingvelden toevoegen aan het model

1. Voeg eerst een **eigenschap paging** toe aan de klasse **SearchData** (in het SearchData.cs-modelbestand).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Deze variabele is een tekenreeks, die "volgende" bevat als de volgende pagina met resultaten moet worden verzonden of null moet zijn voor de eerste pagina van een zoekopdracht.

2. Voeg in hetzelfde bestand en binnen de naamruimte een algemene variabele klasse toe met één eigenschap. In MVC worden globale variabelen gedeclareerd in hun eigen statische klasse. **ResultsPerPage** stelt het aantal resultaten per pagina in. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Een verticale schuifbalk toevoegen aan de weergave

1. Zoek het gedeelte van het bestand index.cshtml met de resultaten (het begint met het ** @if (Model != null).**

2. Vervang de sectie door de onderstaande code. De ** &lt;nieuwe&gt; div** sectie is rond het gebied dat moet worden scrollable, en voegt zowel een **overflow-y** attribuut en een aanroep aan een **onscroll** functie genaamd "scrolled()", zoals zo.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Direct onder de lus, na de &lt;/div-tag,&gt; voegt u de **gescrollde** functie toe.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    De **if-instructie** in het script hierboven wordt getest om te zien of de gebruiker naar de onderkant van de verticale schuifbalk is gebladerd. Als dit het wel is, wordt een oproep gedaan naar de **Home-controller** naar een actie genaamd **Next**. Er is geen andere informatie nodig door de controller, het zal de volgende pagina met gegevens retourneren. Deze gegevens worden vervolgens opgemaakt met identieke HTML-stijlen als de oorspronkelijke pagina. Als er geen resultaten worden geretourneerd, wordt er niets toegevoegd en blijven de dingen zoals ze zijn.

### <a name="handle-the-next-action"></a>De volgende actie afhandelen

Er zijn slechts drie acties die naar de controller moeten worden verzonden: de eerste run van de app, die **Index()** aanroept, de eerste zoekopdracht door de gebruiker, die **Index(model)** aanroept, en vervolgens de daaropvolgende oproepen om meer resultaten via **Next(model)**.

1. Open het thuiscontrollerbestand en verwijder de **runqueryasync-methode** uit de oorspronkelijke zelfstudie.

2. Vervang de actie **Index(model)** door de volgende code. Het behandelt nu het **pagingveld** wanneer het null is, of ingesteld op "volgende", en verwerkt de aanroep naar Azure Cognitive Search.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Net als bij de genummerde paging-methode gebruiken we de **zoekinstellingen Overslaan** en **Top** om alleen de gegevens op te vragen die we nodig hebben, wordt geretourneerd.

3. Voeg de actie **Volgende** toe aan de thuiscontroller. Let op hoe het een lijst retourneert, elk hotel voegt twee elementen toe aan de lijst: een hotelnaam en een hotelbeschrijving. Deze indeling komt overeen met het gebruik van de **geretourneerde** gegevens in de weergave.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Als u een syntaxisfout opdeed op **de&lt;tekenreeks&gt;Lijst,** voegt u de volgende **gebruiksrichtlijn** toe aan het hoofd van het controllerbestand.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Uw project compileren en uitvoeren

Selecteer nu **Starten zonder foutopsporing** (of druk op de F5-toets).

1. Voer een term in die veel resultaten geeft (zoals 'pool') en test vervolgens de verticale schuifbalk. Leidt het tot een nieuwe pagina met resultaten?

    ![Oneindig scrollen door "pool" resultaten](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Om ervoor te zorgen dat er een schuifbalk op de eerste pagina wordt weergegeven, moet de eerste pagina met resultaten de hoogte van het gebied waarin ze worden weergegeven iets overschrijden. In ons voorbeeld **.box1** heeft een hoogte van 30 pixels, **.box2** heeft een hoogte van 100 pixels _en_ een onderste marge van 24 pixels. Dus elk item maakt gebruik van 154 pixels. Drie vermeldingen nemen 3 x 154 = 462 pixels in. Om ervoor te zorgen dat er een verticale schuifbalk verschijnt, moet een hoogte van het weergavegebied worden ingesteld die kleiner is dan 462 pixels, zelfs 461 werkt. Dit probleem vindt alleen plaats op de eerste pagina, waarna een schuifbalk zeker wordt weergegeven. De te updaten regel is: ** &lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y:&gt;scroll;" onscroll="scrolled()"**.

2. Scroll helemaal naar beneden naar de onderkant van de resultaten. Merk op hoe alle informatie nu op de pagina met één weergave staat. U helemaal terug naar de top scrollen zonder serveroproepen te activeren.

Meer geavanceerde oneindige schuifsystemen kunnen het muiswiel of een soortgelijk ander mechanisme gebruiken om het laden van een nieuwe pagina met resultaten te activeren. We zullen niet nemen oneindig scrollen verder in deze tutorials, maar het heeft een bepaalde charme om het als het vermijdt extra muisklikken, en je zou willen om andere opties verder te onderzoeken!

## <a name="takeaways"></a>Opgedane kennis

Denk aan de volgende afhaalmaaltijden van dit project:

* Genummerde paging is goed voor zoekopdrachten waar de volgorde van de resultaten is enigszins willekeurig, wat betekent dat er misschien wel iets van belang voor uw gebruikers op de latere pagina's.
* Oneindig scrollen is goed wanneer de volgorde van de resultaten is bijzonder belangrijk. Bijvoorbeeld als de resultaten worden besteld op de afstand van het centrum van een bestemmingsstad.
* Genummerde paging zorgt voor een betere navigatie. Een gebruiker kan zich bijvoorbeeld herinneren dat er een interessant resultaat op pagina 6 stond, terwijl er geen eenvoudige verwijzing bestaat in oneindig scrollen.
* Oneindig scrollen heeft een gemakkelijke aantrekkingskracht, scrollen op en neer met geen moeilijke pagina nummers om op te klikken.
* Een belangrijk kenmerk van oneindig scrollen is dat de resultaten worden toegevoegd aan een bestaande pagina, niet ter vervanging van die pagina, die efficiënt is.
* Tijdelijke opslag blijft bestaan voor slechts een gesprek, en moet worden gereset om extra gesprekken te overleven.


## <a name="next-steps"></a>Volgende stappen

Paging is van fundamenteel belang voor zoekopdrachten op internet. Met paging goed gedekt, de volgende stap is om de gebruikerservaring verder te verbeteren, door het toevoegen van type-ahead zoekopdrachten.

> [!div class="nextstepaction"]
> [C# Zelfstudie: automatisch aanvullen en suggesties toevoegen - Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
