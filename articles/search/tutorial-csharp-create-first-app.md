---
title: C#-zelfstudie voor het maken van uw eerste app
titleSuffix: Azure Cognitive Search
description: Meer informatie over het stapsgewijs maken van uw eerste C#-zoek-app. De zelfstudie biedt zowel een koppeling naar een werkende app op GitHub als het volledige proces om de app helemaal zelf te bouwen. Meer informatie over de essentiële onderdelen van Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 49dc551039e0fd82ddb4374713c59fca2f493b62
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397943"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Zelfstudie: Uw eerste zoek-app maken met behulp van de .NET SDK

Meer informatie over het maken van een webinterface voor het opvragen en presenteren van zoekresultaten uit een index met Azure Cognitive Search. Deze zelfstudie begint met een bestaande, gehoste index, zodat u zich kunt richten op het bouwen van een zoekpagina. De index bevat fictieve hotelgegevens. Zodra u een basispagina hebt, kunt u deze in de volgende lessen uitbreiden met paginering, facetten en automatisch aanvullen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een ontwikkelomgeving instellen
> * Gegevensstructuren modelleren
> * Een webpagina maken
> * Methoden definiëren
> * De app testen

U leert ook hoe eenvoudig een zoekaanroep is. De belangrijkste instructies in de code die u ontwikkelt, zijn in de volgende paar regels ingekapseld.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Met deze aanroep initieert u een zoekopdracht naar Azure-gegevens en worden de resultaten opgehaald.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Zoeken naar '*pool*'" border="false":::


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

[Visual Studio installeren](https://visualstudio.microsoft.com/) om te gebruiken als IDE.

### <a name="install-and-run-the-project-from-github"></a>Het project installeren en uitvoeren vanuit GitHub

1. Zoek het voorbeeld op GitHub: [Eerste app maken](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecteer **Clone or download** en maak uw persoonlijke lokale kopie van het project.
1. Navigeer met Visual Studio naar en open de oplossing voor de eenvoudige zoekpagina en selecteer **Starten zonder foutopsporing** (of druk op F5).
1. Typ enkele woorden (bijvoorbeeld 'wifi', 'view', 'bar', 'parking') en bekijk de resultaten.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Zoeken naar '*pool*'" border="false":::

Hopelijk wordt dit project soepel uitgevoerd en wordt er een Azure-app uitgevoerd. Veel van de essentiële onderdelen voor complexere zoekopdrachten zijn opgenomen in deze ene app. Daarom is het een goed idee om deze te doorlopen en stap voor stap opnieuw te maken.

Als u dit project helemaal zelf wilt maken, en daarmee de onderdelen van Azure Cognitive Search die u in gedachten hebt, doorloopt u de volgende stappen.

## <a name="set-up-a-development-environment"></a>Een ontwikkelomgeving instellen

1. Selecteer in Visual Studio 2017 of hoger **Nieuw/Project** en **ASP.NET Core-webtoepassing**. Geef het project een naam zoals 'FirstAzureSearchApp'.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Zoeken naar '*pool*'" border="false":::

2. Nadat u op **OK** hebt geklikt voor dit projecttype, krijgt u een tweede set opties die van toepassing zijn op dit project. Selecteer **Web Application (Model-View-Controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Zoeken naar '*pool*'":::

3. Selecteer nu in het menu **Extra** de optie **NuGet Package Manager** en vervolgens **NuGet-pakketten voor oplossing beheren**. Er is één pakket dat moet worden geïnstalleerd. Selecteer het tabblad **Bladeren** en typ vervolgens 'Azure Cognitive Search' in het zoekvak. Installeer **Microsoft.Azure.Search** wanneer dit wordt weergegeven in de lijst (versie 9.0.1 of hoger). U moet door een paar extra dialoogvensters klikken om de installatie te voltooien.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Zoeken naar '*pool*'" border="false":::

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search initialiseren

Voor dit voorbeeld gebruiken we openbaar beschikbare hotelgegevens. Deze gegevens zijn een willekeurige verzameling van 50 fictieve hotelnamen en beschrijvingen die alleen zijn gemaakt voor het leveren van demogegevens. Voor toegang tot deze gegevens moet u er een naam en een sleutel voor opgeven.

1. Open het bestand appsettings.json in het nieuwe project en vervang de standaardregels door de volgende naam en sleutel. De API-sleutel die hier wordt weergegeven, is geen voorbeeld van een sleutel. Dit is _exact_ de sleutel die u nodig hebt voor toegang tot de hotelgegevens. Het bestand appsettings.json moet er nu als volgt uitzien.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. We zijn nog niet klaar met dit bestand. Selecteer de eigenschappen voor dit bestand en wijzig de instelling **Naar uitvoermap kopiëren** in **Kopiëren indien nieuwer**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Zoeken naar '*pool*'" border="false":::

## <a name="model-data-structures"></a>Gegevensstructuren modelleren

Modellen (C#-klassen) worden gebruikt om gegevens te communiceren tussen de client (de weergave), de server (de controller) en ook de Azure-cloud met behulp van de MVC-architectuur (model, view, controller). Normaal gesproken wordt in deze modellen de structuur weergegeven van de gegevens die worden geopend. We hebben ook een model nodig om de communicatie van de weergave/controller te kunnen afhandelen.

1. Open de map **Models** van uw project met behulp van Solution Explorer. U ziet hier een standaardmodel: **ErrorViewModel.cs**.

2. Klik met de rechtermuisknop op de map **Modellen** en selecteer **Toevoegen** en vervolgens **Nieuw item**. Selecteer vervolgens in het dialoogvenster dat wordt weergegeven **ASP.NET Core** en vervolgens de eerste optie **Klasse**. Wijzig de naam van het .cs-bestand in Hotel.cs en klik op **Toevoegen**. Vervang alle inhoud van Hotel.cs door de volgende code. Let op de leden **Address** en **Room** van de klasse. Deze velden zijn zelf klassen, dus hier hebben we ook modellen voor nodig.

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

3. Volg hetzelfde proces voor het maken van een model voor de klasse **Address**, maar geef het bestand de naam Address.cs. Vervang de inhoud door het volgende.

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

4. Volg opnieuw hetzelfde proces om de klasse **Room** te maken, en geef het bestand de naam Room.cs. Vervang de inhoud opnieuw door het volgende.

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

5. De set klassen **Hotel**, **Address** en **Room** zijn wat bekend staat in Azure als [_complexe typen_](search-howto-complex-data-types.md), een belangrijke functie van Azure Cognitive Search. Complexe typen kunnen bestaan uit een groot aantal klassen en subklassen, en veel complexere gegevensstructuren weergeven dan _eenvoudige typen_ (een klasse met alleen primitieve leden). We hebben nog één model nodig, dus doorlopen we het proces van het maken van een nieuwe modelklasse opnieuw, maar deze keer wordt de klasse SearchData.cs aangeroepen en de standaardcode vervangen door het volgende.

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

    Deze klasse bevat de gebruikersinvoer (**searchText**) en de uitvoer van de zoekopdracht (**resultList**). Het type uitvoer is kritiek, **DocumentSearchResult&lt;Hotel&gt;** , omdat dit type precies overeenkomt met de resultaten van de zoekopdracht, en we deze verwijzing moeten door geven aan de weergave.



## <a name="create-a-web-page"></a>Een webpagina maken

Het project dat u hebt gemaakt, maakt standaard een aantal clientweergaven. De exacte weergaven zijn afhankelijk van de versie van Core .NET die u gebruikt (in dit voorbeeld gebruiken we 2.1). Ze bevinden zich allemaal in de map **Views** van het project. U hoeft alleen het bestand Index.cshtml te wijzigen (in de map **Views/Home**).

Verwijder de inhoud van Index.cshtml in zijn geheel en bouw het bestand opnieuw op in de volgende stappen.

1. We gebruiken twee kleine afbeeldingen in de weergave. U kunt uw eigen afbeelding gebruiken of de afbeeldingen vanuit het project GitHub-project kopiëren: azure-logo.png en search.png. Deze twee afbeeldingen moeten worden geplaatst in de map **wwwroot/images**.

2. De eerste regel van index. cshtml moet verwijzen naar het model dat we gebruiken om gegevens te communiceren tussen de client (de weergave) en de server (de controller). Dit is het **SearchData-** model dat we hebben gemaakt. Voeg deze regel toe aan het bestand Index.cshtml file.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Het is gebruikelijk om een titel voor de weergave in te voeren. De volgende regels moeten dus als volgt zijn:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Na de titel voert u een verwijzing in naar een HTML-opmaakmodel, dat we binnenkort gaan maken.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nu naar de essentie van de weergave. Iets belangrijks waarmee u rekening moet houden, is dat de weergave twee situaties moet afhandelen. Ten eerste moet de weergave worden afgehandeld wanneer de app voor het eerst wordt gestart en er nog geen zoektekst is door de gebruiker. Ten tweede moet de weergave van resultaten, naast het zoekvak, worden afgehandeld voor herhaald gebruik door de gebruiker. Voor het afhandelen van deze twee situaties moeten we controleren of het model dat aan de weergave is verstrekt wel of niet null is. Een model van null geeft aan dat we ons in de eerste van de twee situaties bevinden (de eerste keer dat de app wordt uitgevoerd). Voeg het volgende toe aan het bestand Index.cshtml en lees de opmerkingen.

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

6. Ten slotte voegen we het opmaakmodel toe. Selecteer in Visual Studio in het menu **Bestand** de optie **Nieuw/Bestand** en vervolgens **Opmaakmodel** (met **Algemeen** gemarkeerd). Vervang de code door het volgende. We gaan niet meer uitgebreid in op dit bestand. De stijlen zijn standaard-HTML.

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

7. Sla het opmaakmodelbestand op als hotels.css, in de map wwwroot/css, naast het standaardbestand site.css.

Hiermee is de weergave voltooid. We maken goede voortgang. De modellen en weergaven zijn voltooid. Nu is er alleen nog de controller die alles moet koppelen.

## <a name="define-methods"></a>Methoden definiëren

We moeten wijzigingen aanbrengen in de inhoud van de ene controller (**Home Controller**) die standaard wordt gemaakt.

1. Open het HomeController.cs-bestand en vervang de **using**-instructies door het volgende.

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

### <a name="add-index-methods"></a>Index-methoden toevoegen

We hebben twee **Index**-methoden nodig, één waarbij geen parameters worden opgegeven (voor het geval dat de app voor het eerst wordt geopend) en één waarbij een model als parameter wordt genomen (voor het geval dat de gebruiker zoektekst heeft ingevoerd). De eerste van deze methoden wordt standaard gemaakt. 

1. Voeg de volgende methode toe na de standaard **Index()** -methode.

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

    Let op de **async**-declaratie van de methode en de **await**-aanroep naar **RunQueryAsync**. Deze trefwoorden zorgen dat onze aanroepen asynchroon worden en voorkomen zo dat threads op de server worden geblokkeerd.

    Het **catch**-blok maakt gebruik van het foutmodel dat standaard voor ons is gemaakt.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>De foutafhandeling en andere standaardweergaven en -methoden bekijken

Afhankelijk van welke versie van .NET core u gebruikt, wordt standaard een iets andere set standaardweergaven gemaakt. Voor .NET Core 2.1 zijn de standaard weergaven Index, About, Contact, Privacy en Error. Voor .NET Core 2.2 zijn de standaardweergaven bijvoorbeeld Index, Privacy en Error. In beide gevallen kunt u deze standaardpagina's weergeven tijdens het uitvoeren van de app en onderzoeken hoe deze worden verwerkt in de controller.

De weergave Error wordt verderop in deze zelfstudie getest.

In het GitHub-voorbeeld zijn de niet-gebruikte weergaven en de bijbehorende acties verwijderd.

### <a name="add-the-runqueryasync-method"></a>De methode RunQueryAsync toevoegen

De aanroep van Azure Cognitive Search is ingekapseld in onze **RunQueryAsync**-methode.

1. Voeg eerst enkele statische variabelen toe om de Azure-service in te stellen en een aanroep om deze te initiëren.

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

2. Voeg nu de **RunQueryAsync**-methode zelf toe.

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

    Met deze methode zorgen we er eerst voor dat de Azure-configuratie wordt gestart en stellen we vervolgens een aantal zoekparameters in. De namen van de velden in de **Select**-parameter die exact overeenkomen met de namen van de eigenschappen in de klasse **hotel**. Het is mogelijk om de **Select**-parameter weg te laten. In dat geval worden alle eigenschappen geretourneerd. Het niet instellen van **Select**-parameters is echter inefficiënt als we alleen geïnteresseerd zijn in een subset van de gegevens. Door de eigenschappen op te geven waarin we geïnteresseerd zijn, worden alleen deze eigenschappen geretourneerd.

    De asynchrone aanroep voor zoeken (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);** ) is waar deze zelfstudie en app over gaan. De **DocumentSearchResult**-klasse is een interessante, en het is een goed idee (wanneer de app wordt uitgevoerd) om hier een onderbrekingspunt in te stellen en een foutopsporingsprogramma te gebruiken om de inhoud van **model. resultList** te controleren. U ziet dat dit intuïtief is, en u de gegevens krijgt die u hebt gevraagd en niet veel meer.

En dan nu het moment van de waarheid.

### <a name="test-the-app"></a>De app testen

We gaan nu controleren of de app correct wordt uitgevoerd.

1. Selecteer **Fouten opsporen/Starten zonder foutopsporing** (of druk op de toets F5). Als u alles correct hebt gecodeerd, krijgt u de initiële indexweergave.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Zoeken naar '*pool*'" border="false":::

2. Voer tekst in, bijvoorbeeld 'beach' (of andere tekst die u te binnen schiet), en klik op het zoekpictogram. U moet enkele resultaten krijgen.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Zoeken naar '*pool*'" border="false":::

3. Probeer 'five star' in te voeren. U ziet dat u geen resultaten krijgt. Een geavanceerdere zoekopdracht zou 'five star' behandelen als synoniem voor 'luxury' en deze resultaten retourneren. Het gebruik van synoniemen is beschikbaar in Azure Cognitive Search, maar we behandelen dit niet in de eerste zelfstudies.
 
4. Voer 'hot' in als zoektekst. Er worden _geen_ vermeldingen met het woord 'hotel' opgehaald. Onze zoekopdracht zoekt alleen naar hele woorden, maar er worden wel enkele resultaten opgehaald.

5. Probeer andere woorden: 'pool','sunshine', 'view', enzovoort. U ziet dat Azure Cognitive Search heel eenvoudig werkt, maar wel overtuigend is.

## <a name="test-edge-conditions-and-errors"></a>Voorwaarden en fouten aan de rand testen

Het is belangrijk om te controleren of de functies voor foutafhandeling werken zoals zou moeten, zelfs wanneer alles perfect werkt. 

1. Voer in de **Index**-methode, na de aanroep **try {** de regel **Throw new Exception()** in. Deze uitzondering dwingt een fout af wanneer in tekst wordt gezocht.

2. Voer de app uit, voer 'bar' in als zoektekst en klik op het zoekpictogram. De uitzondering moet de foutweergave tot gevolg hebben.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Zoeken naar '*pool*'" border="false":::

    > [!Important]
    > Het wordt beschouwd als een beveiligingsrisico om interne foutnummers op foutpagina's te retourneren. Doe als uw app bedoeld is voor algemeen gebruik onderzoek naar veilige en beste procedures voor wat er moet worden geretourneerd als er een fout optreedt.

3. Verwijder **Throw new Exception()** wanneer u tevreden bent over de foutafhandeling.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Een Azure Cognitive Search-aanroep is beknopt en het is eenvoudig om de resultaten te interpreteren.
* Asynchrone aanroepen voegen enige complexiteit toe aan de controller, maar zijn de best practice als u van plan bent om kwaliteits-apps te ontwikkelen.
* Deze app heeft een eenvoudige zoekopdracht naar tekst uitgevoerd, die is gedefinieerd door wat is ingesteld in **searchParameters**. Deze ene klasse kan echter worden gevuld met veel leden die verfijning toevoegen aan een zoekopdracht. Er is niet veel extra werk nodig om deze app aanzienlijk krachtiger te maken.

## <a name="next-steps"></a>Volgende stappen

Om de beste gebruikerservaring met Azure Cognitive Search te bieden, moeten we meer functies toevoegen, met name paginering (met behulp van paginanummers of oneindig schuiven) en automatisch aanvullen/suggesties. We moeten ook complexere zoekparameters overwegen (bijvoorbeeld geografische zoekacties op hotels binnen een opgegeven straal van een bepaald punt en de volgorde van de zoekresultaten).

Deze volgende stappen worden behandeld in een reeks zelfstudies. Laten we beginnen met paginering.

> [!div class="nextstepaction"]
> [C#-zelfstudie: Zoekresultaten pagineren: Azure Cognitive Search](tutorial-csharp-paging.md)


