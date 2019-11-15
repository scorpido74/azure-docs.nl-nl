---
title: C#zelf studie over de paginering van zoek resultaten
titleSuffix: Azure Cognitive Search
description: In deze zelf studie wordt gedemonstreerd hoe de zoek resultaten worden gepagineerd. Het wordt gebouwd op basis van een bestaand project in hotels, met de knoppen voor de eerste, de volgende, de vorige, de laatste en de genummerde knop. Een tweede wissel systeem gebruikt oneindig schuiven, geactiveerd door een verticale schuif balk te verplaatsen naar de ondergrens.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 04f8229a86fbd8fbd5404997926412e760e74973
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113764"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>C#zelf studie: paginering van zoek resultaten-Azure Cognitive Search

Meer informatie over het implementeren van twee verschillende wissel systemen, het eerst op basis van pagina nummers en de tweede op oneindig schuiven. Beide systemen van paging worden veel gebruikt en het selecteren van de juiste versie is afhankelijk van de gebruikers ervaring die u wilt gebruiken met de resultaten. In deze zelf studie worden de paginerings systemen gebouwd in het project dat in de [ C# zelf studie is gemaakt: uw eerste app-Azure-Cognitive Search](tutorial-csharp-create-first-app.md) zelf studie maken.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Uw app uitbreiden met genummerde paginering
> * Uw app uitbreiden met oneindig schuiven

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

U hebt de [ C# zelf studie: Maak uw eerste app-Azure-Cognitive Search](tutorial-csharp-create-first-app.md) project actief. Dit project kan uw eigen versie zijn of installeren vanaf GitHub: [Maak eerst een app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Uw app uitbreiden met genummerde paginering

Genummerd wissel geheugen is het wissel systeem van de belangrijkste zoek machines voor Internet en de meeste andere Zoek websites. Genummerde wissels bevatten doorgaans een optie ' volgende ' en ' vorige ' naast een bereik van de pagina nummers. Ook de optie ' eerste pagina ' en ' laatste pagina ' zijn mogelijk ook beschikbaar. Deze opties geven een gebruiker de controle over het navigeren door middel van resultaten op basis van pagina's.

We voegen een systeem toe met de opties eerste, vorige, volgende en laatste, samen met pagina nummers die niet vanaf 1 worden gestart, maar in plaats daarvan de huidige pagina waarop de gebruiker zich bevindt (bijvoorbeeld als de gebruiker op pagina 10 zoekt), misschien pagina nummers 8 , 9, 10, 11 en 12 worden weer gegeven).

Het systeem is flexibel genoeg zodat het aantal zicht bare pagina nummers in een globale variabele kan worden ingesteld.

Het systeem behandelt de meest linkse en meest rechtse pagina nummer knoppen als speciaal, wat betekent dat ze het wijzigen van het bereik van de weer gegeven pagina nummers kunnen activeren. Als bijvoorbeeld pagina nummers 8, 9, 10, 11 en 12 worden weer gegeven en de gebruiker op 8 klikt, wordt het bereik van de pagina nummers gewijzigd in 6, 7, 8, 9 en 10. En er is een vergelijk bare verschuiving naar rechts als deze 12 is geselecteerd.

### <a name="add-paging-fields-to-the-model"></a>Wissel velden toevoegen aan het model

Laat de algemene zoek pagina-oplossing open.

1. Open het SearchData.cs-model bestand.

2. Voeg eerst enkele globale variabelen toe. In MVC worden globale variabelen in hun eigen statische klasse gedeclareerd. **ResultsPerPage** Hiermee stelt u het aantal resultaten per pagina in. **MaxPageRange** bepaalt het aantal zicht bare pagina nummers in de weer gave. **PageRangeDelta** bepaalt hoeveel pagina's links of rechts het pagina bereik moet worden verplaatst wanneer het meest linkse of het meest rechtse pagina nummer is geselecteerd. Dit laatste nummer is doorgaans ongeveer de helft van **MaxPageRange**. Voeg de volgende code toe aan de naam ruimte.

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
    >Als u dit project uitvoert op een apparaat met een kleiner scherm, zoals een laptop, kunt u **ResultsPerPage** wijzigen in 2.

3. Eigenschappen van paginering toevoegen aan de klasse **SearchData** , zeggen, na de eigenschap **brons** .

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Een tabel met wissel opties toevoegen aan de weer gave

1. Open het bestand index. cshtml en voeg de volgende code toe vóór de &lt;&gt;/body-tag voor sluiten. Deze nieuwe code bevat een tabel met wissel opties: eerste, vorige, 1, 2, 3, 4, 5, volgende, laatste.

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

    We gebruiken een HTML-tabel om dingen netjes af te stemmen. Alle acties zijn echter afkomstig uit de @Html.ActionLink-instructies, elke aanroep van de controller met een **Nieuw** model dat is gemaakt met verschillende vermeldingen voor de **paginerings** eigenschap die u eerder hebt toegevoegd.

    De opties voor de eerste en laatste pagina verzenden geen teken reeksen zoals ' First ' en ' last ', maar worden in plaats daarvan de juiste pagina nummers verzonden.

2. Voeg enkele paginerings klassen toe aan de lijst met HTML-stijlen in het bestand Hotels. CSS. De **pageSelected** -klasse is er om de pagina te identificeren die de gebruiker momenteel bekijkt (door het getal vet in te scha kelen) in de lijst met pagina nummers.

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

### <a name="add-a-page-action-to-the-controller"></a>Een pagina actie toevoegen aan de controller

1. Open het HomeController.cs-bestand en voeg de **pagina** actie toe. Deze actie reageert op een van de geselecteerde pagina opties.

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

    In de methode **RunQueryAsync** wordt nu een syntaxis fout weer gegeven, vanwege de derde para meter, die we in een bit zullen beleven.

    > [!Note]
    > Met de **TempData** -aanroepen wordt een waarde (een **object**) opgeslagen in de tijdelijke opslag, hoewel deze opslag _slechts_ voor één aanroep wordt bewaard. Als er iets wordt opgeslagen in tijdelijke gegevens, is het beschikbaar voor de volgende aanroep van een controller actie, maar wordt het het meest zonder enige weg door de oproep. Als gevolg van deze korte levens duur slaan we de Zoek tekst en paginerings eigenschappen terug in de tijdelijke opslag elke en op elke aanroep van de **pagina**.

2. De **index-actie (model)** moet zijn bijgewerkt om de tijdelijke variabelen op te slaan, en om de meest linkse pagina parameter toe te voegen aan de **RunQueryAsync** -aanroep.

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

3. De methode **RunQueryAsync** moet aanzienlijk worden bijgewerkt. We gebruiken de velden **overs Laan**, **boven**en **IncludeTotalResultCount** van de klasse **SearchParameters** om slechts één pagina aan resultaten aan te vragen, beginnend bij de instelling **overs Laan** . We moeten ook de paginerings variabelen voor onze weer gave berekenen. Vervang de volledige-methode door de volgende code.

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

4. Ten slotte moeten we een kleine wijziging aanbrengen in de weer gave. De variabele **resultsList. results. Count** bevat nu het aantal resultaten dat op één pagina wordt geretourneerd (3 in ons voor beeld), niet het totale aantal. Omdat we de **IncludeTotalResultCount** instellen op True, bevat de variabele **resultsList. Count** nu het totale aantal resultaten. Zoek naar waar het aantal resultaten wordt weer gegeven in de weer gave en wijzig deze in de volgende code.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Er is een prestatie treffer, maar niet veel van één, door **IncludeTotalResultCount** in te stellen op True, omdat dit totaal moet worden berekend door Azure Cognitive Search. Met complexe gegevens sets is er een waarschuwing dat de geretourneerde waarde een _benadering_is. Voor onze Hotel gegevens is het nauw keurig.

### <a name="compile-and-run-the-app"></a>De app compileren en uitvoeren

Selecteer nu **starten zonder fout opsporing** (of druk op de toets F5).

1. Zoek naar tekst die veel resultaten oplevert (zoals ' WiFi '). Kunt u de resultaten van de pagina overzichtelijk door lopen?

    ![Genummerde paginering via groeps resultaten](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Klik op de meest rechtse en latere, meest linkse pagina nummers. Worden de pagina nummers op de juiste wijze aangepast om de pagina te centreren?

3. Zijn de opties ' First ' en ' last ' nuttig? Sommige populaire webzoekopdrachten gebruiken deze opties en andere niet.

4. Ga naar de laatste pagina met resultaten. De laatste pagina is de enige pagina die minder dan **ResultsPerPage** resultaten kan bevatten.

    ![De laatste pagina van ' WiFi ' wordt onderzocht](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Typ ' stad ' en klik op zoeken. Er worden geen paginerings opties weer gegeven als er minder dan één pagina de resultaten heeft.

    ![Zoeken naar "stad"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Sla dit project nu op en we proberen een alternatief te maken voor dit soort paginering.

## <a name="extend-your-app-with-infinite-scrolling"></a>Uw app uitbreiden met oneindig schuiven

Oneindig schuiven wordt geactiveerd wanneer een gebruiker een verticale schuif balk schuift naar de laatste van de weer gegeven resultaten. Bij deze gebeurtenis wordt een aanroep naar de server gemaakt voor de volgende pagina met resultaten. Als er geen resultaten meer zijn, wordt er niets geretourneerd en verandert de verticale schuif balk niet. Als er meer resultaten zijn, worden deze toegevoegd aan de huidige pagina en wordt de schuif balk gewijzigd om aan te geven dat er meer resultaten beschikbaar zijn.

Het belang rijk punt is dat de pagina die wordt weer gegeven, niet wordt vervangen, maar wordt toegevoegd aan met de nieuwe resultaten. Een gebruiker kan altijd een back-up maken naar de eerste resultaten van de zoek opdracht.

Als u oneindig schuiven wilt implementeren, begint u met het project voordat een van de schuif balken van het pagina nummer is toegevoegd. Als dat zo is, maakt u een kopie van de basis pagina voor zoeken vanuit GitHub: [Maak eerst een app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Wissel velden toevoegen aan het model

1. Voeg eerst een **paginerings** eigenschap toe aan de klasse **SearchData** (in het model bestand SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Deze variabele is een teken reeks die ' volgende ' bevat als de volgende pagina met resultaten moet worden verzonden of null is voor de eerste pagina van een zoek opdracht.

2. Voeg in hetzelfde bestand, en binnen de naam ruimte, een globale variabele klasse met één eigenschap toe. In MVC worden globale variabelen in hun eigen statische klasse gedeclareerd. **ResultsPerPage** Hiermee stelt u het aantal resultaten per pagina in. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Een verticale schuif balk aan de weer gave toevoegen

1. Zoek de sectie van het bestand index. cshtml waarin de resultaten worden weer gegeven (deze beginnen met de **@if (model! = null)** ).

2. Vervang de sectie door de onderstaande code. De nieuwe sectie **&lt;div-&gt;** is rond het gebied dat schuifbaar moet worden, en voegt zowel een **overflow-y-** kenmerk als een aanroep naar een **onscrolle** -functie met de naam ' scrolled () ' toe, zoals u dat wilt.

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

3. Voeg direct onder de lus, na de &lt;/div&gt; tag, de functie **scrolled** toe.

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

    De **if** -instructie in het script hierboven wordt getest om te controleren of de gebruiker naar de onderkant van de verticale schuif balk is geschoven. Als dat het geval is, wordt er een aanroep naar de **Start** controller gemaakt met de naam **volgende**. Er is geen andere informatie nodig voor de controller, de volgende pagina met gegevens wordt geretourneerd. Deze gegevens worden vervolgens opgemaakt met identieke HTML-stijlen als de oorspronkelijke pagina. Als er geen resultaten worden geretourneerd, wordt er niets toegevoegd en blijven de dingen ongewijzigd.

### <a name="handle-the-next-action"></a>De volgende actie afhandelen

Er zijn slechts drie acties die moeten worden verzonden naar de controller: de eerste keer dat de app wordt uitgevoerd, waarmee **index ()** wordt aangeroepen, de eerste zoek opdracht van de gebruiker, die **index (model)** aanroept en vervolgens de volgende aanroepen voor meer resultaten via **volgende (model)** .

1. Open het bestand van de start controller en verwijder de methode **RunQueryAsync** uit de oorspronkelijke zelf studie.

2. Vervang de actie **index (model)** door de volgende code. Nu wordt het veld voor het **wissel bestand** verwerkt wanneer het null is of is ingesteld op ' volgende ', en wordt de aanroep naar Azure Cognitive Search verwerkt.

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

    Net als bij de genummerde wissel methode gebruiken we de Zoek instellingen voor **overs Laan** en **bovenaan** om alleen de gegevens op te vragen die we nodig hebben.

3. Voeg de **volgende** actie toe aan de start controller. U ziet dat er een lijst wordt geretourneerd, waarbij elk Hotel twee elementen aan de lijst toevoegt: de naam van een hotel en een beschrijving van het hotel. Deze indeling is zo ingesteld dat deze overeenkomt met **de functie van de** geretourneerde gegevens in de weer gave.

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

4. Als er een syntaxis fout wordt **weer geven in de lijst&lt;teken reeks&gt;** , voegt u de volgende instructie toe **met behulp** van de instructies in de kop van het controller bestand.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Uw project compileren en uitvoeren

Selecteer nu **starten zonder fout opsporing** (of druk op de toets F5).

1. Voer een term in die veel resultaten levert (zoals ' pool ') en test vervolgens de verticale schuif balk. Wordt een nieuwe pagina met resultaten geactiveerd?

    ![Oneindig schuiven door groeps resultaten](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Om ervoor te zorgen dat er op de eerste pagina een schuif balk wordt weer gegeven, moet de eerste pagina met de resultaten iets groter zijn dan de hoogte van het gebied waarin ze worden weer gegeven. In ons voor beeld **. box1** heeft een hoogte van 30 pixels, **. box2** heeft een hoogte van 100 pixels _en_ een ondermarge van 24 pixels. Elk item gebruikt dus 154 pixels. Drie vermeldingen worden 3 x 154 = 462 pixels. Om ervoor te zorgen dat een verticale schuif balk wordt weer gegeven, moet er een hoogte aan het weergave gebied worden ingesteld die kleiner is dan 462 pixels, zelfs 461 werkt. Dit probleem doet zich alleen voor op de eerste pagina, nadat u zeker weet dat er een schuif balk wordt weer gegeven. De regel die moet worden bijgewerkt is: **&lt;div id = "myDiv" Style = "width: 800px; Height: 450px; overflow-y: Scroll;" onscroll = "scrolled ()"&gt;** .

2. Schuif omlaag naar de onderkant van de resultaten. U ziet nu alle informatie op de pagina met één weer gave. U kunt helemaal naar boven schuiven zonder server aanroepen te activeren.

Meer geavanceerde Infinite-schuif systemen kunnen gebruikmaken van het muis wiel of een soortgelijk ander mechanisme om het laden van een nieuwe pagina met resultaten te activeren. Deze zelf studies worden niet doorlopend herhaald, maar er is een zekere Charm, maar het voor komt dat er extra muis klikken worden gevermijdd. u kunt ook andere opties verder onderzoeken.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende Takeaways van dit project:

* Genummerd pagineren is geschikt voor Zoek opdrachten waarbij de volg orde van de resultaten enigszins wille keurig is, wat inhoudt dat er iets van belang is voor uw gebruikers op de latere pagina's.
* Oneindig schuiven is handig wanneer de volg orde van de resultaten bijzonder belang rijk is. Bijvoorbeeld, als de resultaten worden besteld op de afstand van het midden van een bestemmings plaats.
* Genummerde paginering biedt een betere navigatie. Een gebruiker kan bijvoorbeeld onthouden dat er een interessant resultaat was op pagina 6, terwijl er geen eenvoudige referentie bestaat in oneindig schuiven.
* Oneindig schuiven heeft een eenvoudig uiterlijk, Schuif omhoog en omlaag zonder fussy pagina nummers om op te klikken.
* Een belang rijke functie van oneindig schuiven is dat er resultaten worden toegevoegd aan een bestaande pagina, waardoor die pagina niet wordt vervangen. Dit is efficiënt.
* Tijdelijke opslag blijft behouden voor slechts één aanroep en moet opnieuw worden ingesteld zodat er nog meer aanroepen kunnen worden doorgevoerd.


## <a name="next-steps"></a>Volgende stappen

Paginering is fundamenteel voor Zoek opdrachten op internet. De volgende stap is het optimaliseren van de gebruikers ervaring door type-ahead Zoek opdrachten toe te voegen.

> [!div class="nextstepaction"]
> [C#Zelf studie: automatisch aanvullen en suggesties toevoegen-Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
