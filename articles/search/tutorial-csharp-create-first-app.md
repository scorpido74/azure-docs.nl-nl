---
title: C# zelfstudie om uw eerste app te maken
titleSuffix: Azure Cognitive Search
description: Lees stap voor stap hoe u uw eerste C#-zoek-app maken. De zelfstudie biedt zowel een link naar een werkende app op GitHub als het volledige proces om de app helemaal opnieuw te bouwen. Meer informatie over de essentiële onderdelen van Azure Cognitive Search.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121589"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>C# zelfstudie: Uw eerste app maken - Azure Cognitive Search

Meer informatie over het maken van een webinterface voor het opvragen en presenteren van zoekresultaten uit een index met Azure Cognitive Search. Deze zelfstudie begint met een bestaande, gehoste index, zodat u zich concentreren op het maken van een zoekpagina. De index bevat fictieve hotelgegevens. Zodra u een basispagina hebt, u deze in volgende lessen verbeteren om paging, facetten en een type-ahead-ervaring op te nemen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een ontwikkelomgeving instellen
> * Modelgegevensstructuren
> * Een webpagina maken
> * Methoden definiëren
> * De app testen

U leert ook hoe eenvoudig een zoekopdracht is. De belangrijkste verklaringen in de code die u zult ontwikkelen zijn ingekapseld in de volgende paar regels.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Deze ene aanroep initieert een zoekopdracht naar Azure-gegevens en retourneert de resultaten.

![Op zoek naar "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

[Installeer Visual Studio](https://visualstudio.microsoft.com/) om te gebruiken als de IDE.

### <a name="install-and-run-the-project-from-github"></a>Het project installeren en uitvoeren vanaf GitHub

1. Zoek het voorbeeld op GitHub: [Eerste app maken](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecteer **Kloon of download** en maak uw privé-lokale kopie van het project.
1. Navigeer visual studio en open de oplossing voor de basiszoekpagina en selecteer **Start zonder foutopsporing** (of druk op F5).
1. Typ enkele woorden in (bijvoorbeeld 'wifi', 'view', 'bar', 'parking') en bekijk de resultaten!

    ![Op zoek naar "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Hopelijk loopt dit project soepel en wordt de Azure-app uitgevoerd. Veel van de essentiële onderdelen voor meer geavanceerde zoekopdrachten zijn opgenomen in deze ene app, dus het is een goed idee om er doorheen te gaan en het stap voor stap opnieuw te maken.

Als u dit project helemaal opnieuw wilt maken en zo de onderdelen van Azure Cognitive Search in uw hoofd wilt versterken, gaat u door de volgende stappen.

## <a name="set-up-a-development-environment"></a>Een ontwikkelomgeving instellen

1. Selecteer in Visual Studio 2017 of hoger **Nieuw/Project** en **ASP.NET Core-webtoepassing**. Geef het project een naam zoals "FirstAzureSearchApp".

    ![Een cloudproject maken](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Nadat u op **OK** hebt geklikt voor dit projecttype, krijgt u een tweede set opties die van toepassing zijn op dit project. Selecteer **Webtoepassing (Model-View-Controller)**.

    ![Een MVC-project maken](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Selecteer vervolgens in het menu **Extra** **NuGet Package Manager** en beheer **nuget-pakketten voor oplossing...**. Er is één pakket dat we moeten installeren. Selecteer het tabblad **Bladeren** en typ 'Azure Cognitive Search' in het zoekvak. Installeer **Microsoft.Azure.Search** wanneer deze in de lijst wordt weergegeven (versie 9.0.1 of hoger). U moet door een paar extra dialoogvensters klikken om de installatie te voltooien.

    ![NuGet gebruiken om Azure-bibliotheken toe te voegen](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search initialiseren

Voor dit voorbeeld gebruiken we openbaar beschikbare hotelgegevens. Deze gegevens zijn een willekeurige verzameling van 50 fictieve hotelnamen en beschrijvingen, die uitsluitend zijn gemaakt met het oog op het verstrekken van demogegevens. Om toegang te krijgen tot deze gegevens, moet u er een naam en sleutel voor opgeven.

1. Open het appsettings.json-bestand in uw nieuwe project en vervang de standaardregels door de volgende naam en sleutel. De API-sleutel die hier wordt weergegeven is geen voorbeeld van een sleutel, het is _precies_ de sleutel die u nodig hebt om toegang te krijgen tot de hotelgegevens. Uw appsettings.json-bestand moet er nu zo uitzien.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. We zijn nog niet klaar met dit bestand, selecteer de eigenschappen voor dit bestand en wijzigde de instelling **Kopiëren naar uitvoermap** in **Kopiëren als nieuwer**.

    ![De app-instellingen naar de uitvoer kopiëren](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modelgegevensstructuren

Modellen (C#-klassen) worden gebruikt om gegevens te communiceren tussen de client (de weergave), de server (de controller) en ook de Azure-cloud met behulp van de MVC-architectuur (model, weergave, controller). Deze modellen weerspiegelen doorgaans de structuur van de gegevens die worden geopend. Ook hebben we een model nodig om de communicatie tussen weergave en controller te verwerken.

1. Open de map **Modellen** van uw project met Solution Explorer en u ziet daar één standaardmodel: **ErrorViewModel.cs**.

2. Klik met de rechtermuisknop op de map **Modellen** en selecteer **Toevoegen** en **nieuw item**. Selecteer vervolgens in het dialoogvenster dat wordt weergegeven **ASP.NET Core** vervolgens de eerste **optieklasse**. Wijzig de naam van het .cs-bestand in Hotel.cs en klik op **Toevoegen**. Vervang alle inhoud van Hotel.cs door de volgende code. Let op het **adres** en **de kamer** leden van de klas, deze velden zijn klassen zelf, dus we zullen modellen nodig hebben voor hen ook.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Volg hetzelfde proces voor het maken van een model voor de klasse **Adres,** met uitzondering van de naam van het bestand Address.cs. Vervang de inhoud door het volgende.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. En nogmaals, volg hetzelfde proces om de klasse **Ruimte** te maken, waarbij het bestand wordt Room.cs. Vervang nogmaals de inhoud door het volgende.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. De set **van hotel-,** **adres-** en **kamerklassen** zijn wat in Azure bekend staat als [_complexe typen,_](search-howto-complex-data-types.md)een belangrijk kenmerk van Azure Cognitive Search. Complexe typen kunnen veel niveaus diep van klassen en subklassen, en in staat stellen veel meer complexe gegevensstructuren worden vertegenwoordigd dan met behulp van _eenvoudige typen_ (een klasse die alleen primitieve leden). We hebben nog een model nodig, dus ga opnieuw door het proces van het maken van een nieuwe modelklasse, hoewel deze keer de klasse SearchData.cs en de standaardcode te vervangen door het volgende.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Deze klasse bevat de invoer van de gebruiker **(searchText)** en de uitvoer van de zoekopdracht **(resultList).** Het type van de uitvoer is van cruciaal belang, **DocumentSearchResult&lt;Hotel&gt;**, omdat dit type precies overeenkomt met de resultaten van de zoekopdracht, en we moeten deze verwijzing doorgeven aan de weergave.



## <a name="create-a-web-page"></a>Een webpagina maken

Het project dat u hebt gemaakt, maakt standaard een aantal clientweergaven. De exacte weergaven zijn afhankelijk van de versie van Core .NET die u gebruikt (we gebruiken 2.1 in dit voorbeeld). Ze staan allemaal in de map **Weergaven** van het project. U hoeft alleen het bestand Index.cshtml (in de map **Weergaven/Start)** te wijzigen.

Verwijder de inhoud van Index.cshtml in zijn geheel en herbouw het bestand in de volgende stappen.

1. We gebruiken twee kleine afbeeldingen in het uitzicht. U uw eigen afbeeldingen gebruiken of kopiëren over de afbeeldingen van het GitHub-project: azure-logo.png en search.png. Deze twee afbeeldingen moeten in de **map wwwroot/images** worden geplaatst.

2. De eerste regel van Index.cshtml moet verwijzen naar het model dat we zullen gebruiken om gegevens te communiceren tussen de client (de weergave) en de server (de controller), dat is het **SearchData-model** dat we hebben gemaakt. Voeg deze regel toe aan het bestand Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Het is gebruikelijk om een titel in te voeren voor de weergave, dus de volgende regels moeten zijn:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Als u de titel volgt, voert u een verwijzing in naar een HTML-stijlblad, die we binnenkort zullen maken.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nu naar het vlees van het uitzicht. Een belangrijk ding om te onthouden is dat het uitzicht heeft om twee situaties te behandelen. Ten eerste moet het scherm worden verwerkt wanneer de app voor het eerst wordt gestart en heeft de gebruiker nog geen zoektekst ingevoerd. Ten tweede moet het de weergave van de resultaten, naast het zoektekstvak, verwerken voor herhaald gebruik door de gebruiker. Om deze twee situaties aan te pakken, moeten we controleren of het model dat aan de weergave wordt verstrekt, nietig is of niet. Een null-model geeft aan dat we in de eerste van de twee situaties (de eerste werking van de app). Voeg het volgende toe aan het bestand Index.cshtml en lees de opmerkingen door.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Tot slot voegen we de stylesheet toe. Selecteer in Visual Studio **in het** menu Bestand de optie **Nieuw/Bestand** vervolgens **Stijlblad** (met **algemeen** gemarkeerd). Vervang de standaardcode door het volgende. We zullen niet in gaan op dit bestand in meer detail, de stijlen zijn standaard HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Sla het stylesheet-bestand op als hotels.css, in de map wwwroot/css, naast het standaardbestand site.css.

Dat maakt ons beeld compleet. We boeken goede vooruitgang. De modellen en weergaven zijn voltooid, alleen de controller blijft over om alles aan elkaar te knopen.

## <a name="define-methods"></a>Methoden definiëren

We moeten aanpassen aan de inhoud van de ene controller (**Home Controller**), die standaard is gemaakt.

1. Open het HomeController.cs bestand en vervang de **instructies voor het gebruik** door het volgende.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Indexmethoden toevoegen

We hebben twee **indexmethoden** nodig, waarvan er één geen parameters neemt (voor het geval wanneer de app voor het eerst wordt geopend) en één die een model als parameter neemt (voor wanneer de gebruiker zoektekst heeft ingevoerd). De eerste van deze methoden wordt standaard gemaakt. 

1. Voeg de volgende methode toe, na de methode standaard **Index().**

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Let op de **async-verklaring** van de methode en de **wachtoproep** naar **RunQueryAsync**. Deze zoekwoorden zorgen voor het maken van onze gesprekken asynchroon, en dus voorkomen dat het blokkeren van discussies op de server.

    Het **vangblok** maakt standaard gebruik van het foutmodel dat voor ons is gemaakt.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Let op de foutafhandeling en andere standaardweergaven en -methoden

Afhankelijk van de versie van .NET Core die u gebruikt, wordt standaard een iets andere set standaardweergaven gemaakt. Voor .NET Core 2.1 zijn de standaardweergaven Index, Over, Contact, Privacy en Fout. Voor .NET Core 2.2 zijn de standaardweergaven bijvoorbeeld Index, Privacy en Error. In beide gevallen u deze standaardpagina's bekijken wanneer u de app uitvoert en onderzoeken hoe deze in de controller worden behandeld.

We zullen het testen van de fout weergave later in deze tutorial.

In het GitHub-voorbeeld hebben we de ongebruikte weergaven en de bijbehorende acties verwijderd.

### <a name="add-the-runqueryasync-method"></a>De methode RunQueryAsync toevoegen

De Azure Cognitive Search-aanroep is ingekapseld in onze **RunQueryAsync-methode.**

1. Voeg eerst enkele statische variabelen toe om de Azure-service in te stellen en een oproep om deze te starten.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Voeg nu de **RunQueryAsync-methode** zelf toe.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    Bij deze methode zorgen we er eerst voor dat onze Azure-configuratie wordt gestart en stellen we vervolgens enkele zoekparameters in. De namen van de velden in de parameter **Selecteren** komen exact overeen met de eigenschapsnamen in de **hotelklasse.** Het is mogelijk om de parameter **Select** weg te laten, in welk geval alle eigenschappen worden geretourneerd. Het instellen van geen **Select-parameters** is echter inefficiënt als we alleen geïnteresseerd zijn in een subset van de gegevens. Door de eigenschappen op te geven waarin we geïnteresseerd zijn, worden alleen deze eigenschappen geretourneerd.

    De asynchrone call to search **(model.resultList =&lt;wachten&gt;_indexClient.Documents.SearchAsync Hotel (model.searchText, parameters);**) is wat deze tutorial en app zijn allemaal over. De klasse **DocumentSearchResult** is een interessante, en een goed idee (wanneer de app wordt uitgevoerd) is om hier een breekpunt in te stellen en een foutopsporing te gebruiken om de inhoud van **model.resultList**te onderzoeken. Je moet vinden dat het intuïtief is, waardoor u met de gegevens die u gevraagd, en niet veel anders.

Nu voor het moment van de waarheid.

### <a name="test-the-app"></a>De app testen

Laten we nu controleren of de app correct wordt uitgevoerd.

1. Selecteer **Foutopsporing/Starten zonder foutopsporing** of druk op de F5-toets. Als u dingen correct hebt gecodeerd, krijgt u de eerste indexweergave.

     ![De app openen](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Voer tekst in zoals 'strand' (of een tekst die in je opkomt) en klik op het zoekpictogram. Je zou wat resultaten moeten krijgen.

     ![Op zoek naar "strand"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Probeer het invoeren van "vijf sterren". Merk op hoe je geen resultaten. Een meer geavanceerde zoekopdracht zou behandelen "vijf sterren" als een synoniem voor "luxe" en terug die resultaten. Het gebruik van synoniemen is beschikbaar in Azure Cognitive Search, hoewel we het niet zullen behandelen in de eerste tutorials.
 
4. Probeer 'hot' in te voeren als zoektekst. Het is _niet_ inzendingen met het woord "hotel" in hen terugkeren. Onze zoektocht is alleen het lokaliseren van hele woorden, hoewel een paar resultaten worden geretourneerd.

5. Probeer andere woorden: "zwembad", "zonneschijn", "uitzicht", en wat dan ook. U ziet Azure Cognitive Search werken op zijn eenvoudigste, maar nog steeds overtuigende niveau.

## <a name="test-edge-conditions-and-errors"></a>Randcondities en -fouten testen

Het is belangrijk om te controleren of onze functies voor foutafhandeling werken zoals ze zouden moeten, zelfs als dingen perfect werken. 

1. Voer in de methode **Index** na het **proberen {** aanroep de regel Gooi **nieuwe uitzondering()** in. Deze uitzondering zal een fout forceren wanneer we zoeken op tekst.

2. Voer de app uit, voer 'balk' in als zoektekst en klik op het zoekpictogram. De uitzondering moet resulteren in de foutweergave.

     ![Een fout forceren](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Het wordt beschouwd als een beveiligingsrisico om interne foutnummers in foutpagina's terug te sturen. Als uw app is bedoeld voor algemeen gebruik, doet u een onderzoek naar veilige en best practices van wat u moet retourneren wanneer er een fout optreedt.

3. **Gooi nieuwe uitzondering() verwijderen** wanneer u tevreden bent dat de foutafhandeling naar behoren werkt.

## <a name="takeaways"></a>Opgedane kennis

Denk aan de volgende afhaalmaaltijden van dit project:

* Een Azure Cognitive Search-aanroep is beknopt en het is eenvoudig om de resultaten te interpreteren.
* Asynchrone gesprekken voegen een kleine hoeveelheid complexiteit toe aan de controller, maar zijn de beste praktijken als u van plan bent om kwaliteitsapps te ontwikkelen.
* Deze app voerde een eenvoudige tekst zoeken, gedefinieerd door wat is ingesteld in **searchParameters**. Deze ene klasse kan echter worden gevuld met veel leden die verfijning toevoegen aan een zoekopdracht. Er is niet veel extra werk nodig om deze app aanzienlijk krachtiger te maken.

## <a name="next-steps"></a>Volgende stappen

Om de beste gebruikerservaring te bieden met Azure Cognitive Search, moeten we meer functies toevoegen, met name paging (met behulp van paginanummers of oneindig scrollen) en automatisch invullen/suggesties. We moeten ook rekening houden met geavanceerdere zoekparameters (bijvoorbeeld geografische zoekopdrachten in hotels binnen een bepaalde straal van een bepaald punt en volgorde van zoekresultaten).

Deze volgende stappen worden behandeld in een reeks tutorials. Laten we beginnen met paging.

> [!div class="nextstepaction"]
> [C# Zelfstudie: paginatie met zoekresultaten - Azure Cognitive Search](tutorial-csharp-paging.md)


