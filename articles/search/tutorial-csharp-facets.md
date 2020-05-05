---
title: C#-zelf studie over het gebruik van facetten om navigatie te helpen
titleSuffix: Azure Cognitive Search
description: Door gaan van ' paginerings resultaten ' om facet navigatie toe te voegen. Meer informatie over hoe facetten kunnen worden gebruikt om eenvoudig een zoek opdracht te verfijnen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: bc25ba6d95fe58a80c90881c6ad60cdb8801cce4
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780586"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Zelf studie: facet navigatie toevoegen met behulp van de .NET SDK

Facetten worden gebruikt om navigatie te helpen door de gebruiker een set koppelingen te bieden die u kunt gebruiken om de zoek opdracht te richten. Facetten zijn kenmerken van de gegevens (zoals de categorie of een specifieke functie van een hotel in de voorbeeld gegevens).

Deze zelf studie bouwt voort op het paginerings project dat in de [C#-zelf studie is gemaakt: Zoek resultaten pagineren-Azure Cognitive Search-](tutorial-csharp-paging.md) zelf studie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Model eigenschappen instellen als _IsFacetable_
> * Facet navigatie toevoegen aan uw app

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

De [C# zelf studie: Zoek resultaten pagineren-Azure Cognitive Search](tutorial-csharp-paging.md) project actief. Dit project kan uw eigen versie zijn of installeren vanaf GitHub: [Maak eerst een app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Model eigenschappen instellen als IsFacetable

Een model eigenschap kan alleen in een facet zoekactie worden gevonden als deze is gelabeld met **IsFacetable**.

1. Controleer de klasse **Hotel** . **Categorie** en **Tags**zijn bijvoorbeeld gelabeld als **IsFacetable**, maar de naam van de **Hotels** en de **Beschrijving** is niet. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Er worden geen tags gewijzigd als onderdeel van deze zelf studie. Sluit daarom het hotel.cs-bestand ongewijzigd.

    > [!Note]
    > Bij het zoeken naar facetten wordt een fout gegenereerd als een veld dat in de zoek opdracht is aangevraagd, niet op de juiste wijze is gelabeld.


## <a name="add-facet-navigation-to-your-app"></a>Facet navigatie toevoegen aan uw app

In dit voor beeld gaan we de gebruiker in staat stellen om één categorie Hotel of één Amenity te selecteren, van lijsten met koppelingen die links van de resultaten worden weer gegeven. De gebruiker begint met het invoeren van bepaalde Zoek tekst, vervolgens kan de resultaten van de zoek opdracht worden beperkt door een categorie te selecteren en kan de resultaten verder beperken door een Amenity te selecteren, of ze kunnen de Amenity eerst selecteren (de volg orde is niet belang rijk).

De controller moet de lijst met facetten door geven aan de weer gave. De gebruikers selecties moeten worden behouden als de zoek actie wordt uitgevoerd en er worden opnieuw tijdelijke opslag gebruikt als mechanisme voor het bewaren van gegevens.

![Facet navigatie gebruiken om een zoek opdracht van ' pool ' te beperken](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Filter teken reeksen toevoegen aan het SearchData-model

1. Open het SearchData.cs-bestand en voeg teken reeks eigenschappen toe aan de klasse **SearchData** om de facet filter teken reeksen op te slaan.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>De facet actie methode toevoegen

De start controller heeft één nieuwe actie, **facet**en updates nodig voor de bestaande **index** -en **pagina** acties, evenals de updates voor de methode **RunQueryAsync** .

1. Open het bestand van de start controller en voeg de instructie **using** toe om de **teken&lt;reeks&gt; constructie lijst** in te scha kelen.

    ```cs
    using System.Collections.Generic;
    ```

2. Vervang de actie methode **index (SearchData model)** .

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Vervang de actie methode **pagina (SearchData-model)** .

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
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

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Een actie methode voor een **facet (SearchData-model)** toevoegen die moet worden geactiveerd wanneer de gebruiker op een facet koppeling klikt. Het model bevat een categorie-zoek filter of een Amenity-zoek filter. Mogelijk moet u deze na de **pagina** actie toevoegen.

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Het zoek filter instellen

Wanneer een gebruiker een bepaald facet selecteert, bijvoorbeeld door te klikken op de categorie **redmiddel en beveiligd-wachtwoord** verificatie, moeten alleen hotels die zijn opgegeven als deze categorie, worden geretourneerd in de resultaten. Als u een zoek opdracht op deze manier wilt beperken, moet u een _filter_instellen.

1. Vervang de methode **RunQueryAsync** door de volgende code. In eerste instantie neemt het een categorie filter reeks en een Amenity-filter teken reeks op en stelt de **filter** parameter van de **SearchParameters**in.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

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
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    De eigenschappen **categorie** en **Tags** zijn toegevoegd aan de lijst met **geselecteerde** items die moeten worden geretourneerd. Deze toevoeging is niet vereist voor het werken met facet navigatie, maar we gebruiken deze informatie om te controleren of er correct wordt gefilterd.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lijsten met facet koppelingen toevoegen aan de weer gave

Er zijn enkele belang rijke wijzigingen vereist voor de weer gave. 

1. Begin met het openen van het bestand Hotels. CSS (in de map wwwroot/CSS) en voeg de volgende klassen toe.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. Voor de weer gave wordt de uitvoer in een tabel ingedeeld, zodat de facet lijsten op een overzichtelijke manier worden uitgelijnd op de linkerkant en de resultaten aan de rechter kant. Open het bestand index. cshtml. Vervang de volledige inhoud van de HTML &lt;-&gt; hoofdtekst Tags, met de volgende code.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
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

                                <td class="tdPage">
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
                                    <td class="tdPage">
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

                                <td class="tdPage">
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

                                <td class="tdPage">
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
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Let op het gebruik van de **HTML. ActionLink-** aanroep. Met deze aanroep worden geldige filter teken reeksen naar de controller gecommuniceerd wanneer de gebruiker op een facet koppeling klikt. 

### <a name="run-and-test-the-app"></a>De app uitvoeren en testen

Het voor deel van facet navigatie naar de gebruiker is dat de zoek opdracht met één klik kan worden beperkt, wat in de volgende volg orde kan worden weer gegeven.

1. Voer de app uit, typ "lucht haven" als Zoek tekst. Controleer of de lijst met facetten netjes aan de linkerkant wordt weer gegeven. Deze facetten zijn allemaal van toepassing op Hotels met ' lucht haven ' in hun tekst gegevens, met een telling van hoe vaak ze optreden.

    ![Facet navigatie gebruiken om een zoek opdracht van ' lucht haven ' te beperken](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klik op de categorie **redmiddel en beveiligd-wachtwoord** verificatie. Controleer of alle resultaten in deze categorie zijn.

    ![De zoek opdracht beperken tot "redmiddel en beveiligd-wachtwoord verificatie"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klik op het **continentale ontbijt** Amenity. Controleer of alle resultaten zich nog in de categorie "redmiddel" en "Spa" bevinden, met de geselecteerde Amenity.

    ![De zoek opdracht beperken tot "continent ontbijt"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Selecteer een andere categorie, vervolgens één Amenity en Bekijk de beperkende resultaten. Ga vervolgens op een andere manier te ronden, één Amenity en vervolgens één categorie.

    >[!Note]
    > Wanneer een selectie wordt gemaakt in een facet lijst (bijvoorbeeld categorie), wordt een eerdere selectie in de categorie lijst overschreven.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende Takeaways van dit project:

* Het is essentieel dat elke eigenschap als **IsFacetable**wordt gemarkeerd als deze moeten worden opgenomen in de facet navigatie.
* Facet navigatie biedt een gebruiker een gemakkelijke en intuïtieve manier om een zoek opdracht te verfijnen.
* Facet navigatie is het meest geschikt voor secties (categorieën Hotel, voorzieningen van een hotel, prijs bereik, classificatie bereik enz.), elke sectie met een passende kop.

## <a name="next-steps"></a>Volgende stappen

In de volgende zelf studie kijken we naar de volg orde van resultaten. Tot nu toe worden de resultaten geordend in de volg orde waarin ze zich bevinden in de-data base.

> [!div class="nextstepaction"]
> [C#-zelf studie: de resultaten best Ellen-Azure Cognitive Search](tutorial-csharp-orders.md)
