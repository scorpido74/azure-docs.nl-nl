---
title: C# tutorial over het gebruik van facetten om navigatie te helpen
titleSuffix: Azure Cognitive Search
description: Deze zelfstudie bouwt voort op het project 'Zoekresultaten - Azure Cognitive Search' om facetnavigatie toe te voegen. Ontdek hoe facetten kunnen worden gebruikt om een zoekopdracht eenvoudig te beperken.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121569"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>C# zelfstudie: facetten gebruiken om navigatie te ondersteunen - Azure Cognitive Search

Facetten worden gebruikt om navigatie te helpen, door de gebruiker te voorzien van een set van links te gebruiken om hun zoekopdracht te concentreren. Facetten zijn kenmerken van de gegevens (zoals de categorie, of een specifieke functie, van een hotel in onze voorbeeldgegevens).

Deze zelfstudie bouwt voort op het paging-project dat is gemaakt in de [zelfstudie C# Zelfstudie: Zoekresultaten - Azure Cognitive Search.](tutorial-csharp-paging.md)

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Modeleigenschappen instellen als _IsFacetable_
> * Facet-navigatie toevoegen aan uw app

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

Laat de [C# Tutorial: Search results pagination - Azure Cognitive Search](tutorial-csharp-paging.md) project up and running. Dit project kan uw eigen versie zijn of het installeren vanuit GitHub: [Eerste app maken.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="set-model-properties-as-isfacetable"></a>Modeleigenschappen instellen als IsFacetable

Als u een modeleigenschap in een facetzoekopdracht wilt bevinden, moet deze worden getagd met **IsFacetable**.

1. Bestudeer de **hotelklas.** **Categorie** en **tags**worden bijvoorbeeld getagd als **IsFacetable,** maar **HotelName** en **Beschrijving** niet. 

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

2. We zullen geen tags wijzigen als onderdeel van deze zelfstudie, dus sluit het hotel.cs bestand ongewijzigd.

    > [!Note]
    > Een facetzoekopdracht brengt een fout met zich mee als een veld dat in de zoekopdracht wordt aangevraagd, niet op de juiste manier is getagd.


## <a name="add-facet-navigation-to-your-app"></a>Facet-navigatie toevoegen aan uw app

For this example, we are going to enable the user to select one category of hotel, or one amenity, from lists of links shown to the left of the results. De gebruiker begint met het invoeren van een zoektekst, kan vervolgens de resultaten van de zoekopdracht verkleinen door een categorie te selecteren en kan de resultaten verder beperken door een voorziening te selecteren, of ze kunnen eerst de voorziening selecteren (de volgorde is niet belangrijk).

We hebben de controller nodig om de lijsten met facetten door te geven aan het uitzicht. We moeten de gebruikersselecties behouden naarmate de zoekopdracht vordert, en nogmaals, we gebruiken tijdelijke opslag als het mechanisme voor het bewaren van gegevens.

![Facet-navigatie gebruiken om een zoekopdracht naar "pool" te verkleinen](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Filtertekenreeksen toevoegen aan het SearchData-model

1. Open het SearchData.cs bestand en voeg tekenreekseigenschappen toe aan de klasse **SearchData** om de tekenreeksen van het facetfilter vast te houden.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>De actiemethode Facet toevoegen

De thuiscontroller heeft één nieuwe actie nodig, **Facet**en updates van de bestaande **index-** en **paginaacties,** evenals updates van de **RunQueryAsync-methode.**

1. Open het thuiscontrollerbestand en voeg de **instructie met behulp** toe om de tekenreeksconstructie van de **lijst&lt;in&gt; ** te schakelen.

    ```cs
    using System.Collections.Generic;
    ```

2. Vervang de actiemethode **Index(SearchData-model).**

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

3. Vervang de **actiemethode Pagina(SearchData-model).**

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

4. Voeg een **Facet(SearchData-model)** actiemethode toe, die moet worden geactiveerd wanneer de gebruiker op een facet-koppeling klikt. Het model bevat een zoekfilter voor categorieën of een zoekfilter voor voorzieningen. Misschien toevoegen na de **actie Pagina.**

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

### <a name="set-up-the-search-filter"></a>Het zoekfilter instellen

Wanneer een gebruiker bijvoorbeeld een bepaald facet selecteert, klikt hij op de categorie **Resort en Spa,** dan mogen alleen hotels die als deze categorie zijn opgegeven, worden geretourneerd in de resultaten. Om een zoekopdracht op deze manier te beperken, moeten we een _filter_instellen.

1. Vervang de **methode RunQueryAsync** door de volgende code. In de eerste plaats is er een categoriefiltertekenreeks en een tekenreeks van het amenheidsfilter nodig en wordt de parameter **Filter** van de **parameter SearchParameters ingesteld.**

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

    We hebben de eigenschappen **Categorie** en **Tags** toegevoegd aan de lijst **met Objecten selecteren** om terug te keren. Deze toevoeging is geen vereiste voor facet navigatie om te werken, maar we gebruiken deze informatie om te controleren of we correct filteren.

### <a name="add-lists-of-facet-links-to-the-view"></a>Lijsten met facetkoppelingen toevoegen aan de weergave

Het uitzicht zal een aantal belangrijke veranderingen vereisen. 

1. Begin met het openen van het bestand hotels.css (in de map wwwroot/css) en voeg de volgende klassen toe.

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

2. Voor de weergave organiseren we de uitvoer in een tabel, om de facetlijsten aan de linkerkant en de resultaten aan de rechterkant netjes uit te lijnen. Open het bestand index.cshtml. Vervang de volledige inhoud &lt;&gt; van de HTML-hoofdtags door de volgende code.

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

    Let op het gebruik van de **Html.ActionLink-aanroep.** Deze aanroep communiceert geldige filtertekenreeksen naar de controller, wanneer de gebruiker op een facetkoppeling klikt. 

### <a name="run-and-test-the-app"></a>De app uitvoeren en testen

Het voordeel van facet navigatie aan de gebruiker is dat ze zoekopdrachten kunnen beperken met een enkele klik, die we kunnen laten zien in de volgende volgorde.

1. Voer de app uit en typ 'luchthaven' als zoektekst. Controleer of de lijst met facetten netjes naar links wordt weergegeven. Deze facetten zijn allemaal van toepassing op hotels die "luchthaven" in hun tekst gegevens, met een telling van hoe vaak ze voorkomen.

    ![Facet-navigatie gebruiken om een zoekopdracht op "luchthaven" te beperken](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klik op de categorie **Resort en Spa.** Controleer of alle resultaten in deze categorie vallen.

    ![Vernauwing van de zoektocht naar "Resort en Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klik op de **continentale ontbijtvoorziening.** Controleer of alle resultaten nog steeds in de categorie "Resort and Spa" vallen, met de geselecteerde voorzieningen.

    ![Vernauwing van de zoektocht naar "continentaal ontbijt"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Probeer een andere categorie te selecteren, vervolgens een voorziening, en bekijk de vernauwende resultaten. Probeer dan andersom, een voorziening, dan een categorie.

    >[!Note]
    > Wanneer één selectie wordt gemaakt in een facetlijst (zoals categorie), wordt elke eerdere selectie in de categorielijst overschreven.

## <a name="takeaways"></a>Opgedane kennis

Denk aan de volgende afhaalmaaltijden van dit project:

* Het is noodzakelijk om elke eigenschap te markeren als **IsFacetable,** als ze moeten worden opgenomen in facet navigatie.
* Facet-navigatie biedt een gebruiker een eenvoudige en intuïtieve manier om een zoekopdracht te verkleinen.
* Facet navigatie is het best onderverdeeld in secties (categorieën van het hotel, voorzieningen van een hotel, prijsklassen, rating bereiken, enz.), elke sectie met een geschikte rubriek.

## <a name="next-steps"></a>Volgende stappen

In de volgende tutorial kijken we naar het bestellen van resultaten. Tot op dit punt worden de resultaten eenvoudig geordend in de volgorde waarin ze zich in de database bevinden.

> [!div class="nextstepaction"]
> [C# zelfstudie: De resultaten bestellen- Azure Cognitive Search](tutorial-csharp-orders.md)
