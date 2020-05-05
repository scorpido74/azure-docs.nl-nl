---
title: C#-zelf studie voor het maken van uw eerste app
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van uw eerste C# Search-app stapsgewijs. De zelf studie biedt een koppeling naar een werkende app op GitHub en het volledige proces voor het helemaal zelf bouwen van de app. Meer informatie over de essentiële onderdelen van Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: a105766b713ce44d800391e2e943f2ac864fa0df
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780620"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Zelf studie: uw eerste Zoek-app maken met behulp van de .NET SDK

Meer informatie over het maken van een webinterface voor het opvragen en presen teren van zoek resultaten van een index met Azure Cognitive Search. Deze zelf studie begint met een bestaande, gehoste index, zodat u zich kunt richten op het bouwen van een zoek pagina. De index bevat fictieve Hotel gegevens. Zodra u een basis pagina hebt, kunt u deze in de volgende lessen uitbreiden met paginering, facetten en een soort ervaring.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een ontwikkel omgeving instellen
> * Model gegevens structuren
> * Een webpagina maken
> * Methoden definiëren
> * De app testen

U leert ook hoe eenvoudig een zoek aanroep is. De belangrijkste instructies in de code die u ontwikkelt, worden in de volgende paar regels ingekapseld.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Met deze aanroep initieert een zoek opdracht naar Azure-gegevens en worden de resultaten geretourneerd.

![Zoeken naar groep](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

[Installeer Visual Studio](https://visualstudio.microsoft.com/) om te gebruiken als IDE.

### <a name="install-and-run-the-project-from-github"></a>Het project installeren en uitvoeren vanuit GitHub

1. Zoek het voor beeld op GitHub: [Maak de eerste app](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecteer **klonen of downloaden** en maak uw persoonlijke lokale kopie van het project.
1. Ga in Visual Studio naar en open de oplossing voor de pagina basis zoeken en selecteer **starten zonder fout opsporing** (of druk op F5).
1. Typ enkele woorden (bijvoorbeeld ' WiFi ', ' weer gave ', ' balk ', ' parkeren ') en Bekijk de resultaten.

    ![Zoeken naar ' WiFi '](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Hopelijk werkt dit project soepel en wordt er een Azure-app uitgevoerd. Veel van de essentiële onderdelen voor complexere Zoek opdrachten zijn opgenomen in deze app. Daarom is het een goed idee om deze te laten door lopen en de stap voor stap opnieuw te maken.

Ga als volgt te werk om dit project helemaal zelf te maken, zodat u de onderdelen van Azure Cognitive Search in uw gedachte kunt versterken.

## <a name="set-up-a-development-environment"></a>Een ontwikkel omgeving instellen

1. In Visual Studio 2017 of hoger selecteert u **Nieuw/project** en vervolgens **ASP.net core webtoepassing**. Geef het project een naam zoals ' FirstAzureSearchApp '.

    ![Een Cloud project maken](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Nadat u op **OK** hebt geklikt voor dit project type, krijgt u een tweede set opties die van toepassing zijn op dit project. Selecteer **Webtoepassing (model-view-controller)**.

    ![Een MVC-project maken](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Klik vervolgens in het menu **extra** op **NuGet package manager** en vervolgens op **NuGet-pakketten beheren voor oplossing...**. Er is één pakket dat moet worden geïnstalleerd. Selecteer het tabblad **Bladeren** en typ ' Azure Cognitive Search ' in het zoekvak. Installeer **micro soft. Azure. Search** wanneer deze wordt weer gegeven in de lijst (versie 9.0.1 of hoger). U moet door een paar extra dialoog vensters klikken om de installatie te volt ooien.

    ![NuGet gebruiken om Azure-bibliotheken toe te voegen](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search initialiseren

Voor dit voor beeld gebruiken we openbaar beschik bare Hotel gegevens. Deze gegevens zijn een wille keurige verzameling van 50 fictieve Hotel namen en beschrijvingen die alleen worden gemaakt voor het leveren van demo gegevens. Voor toegang tot deze gegevens moet u een naam en sleutel voor het bestand opgeven.

1. Open het bestand appSettings. json in het nieuwe project en vervang de standaard regels door de volgende naam en sleutel. De API-sleutel die hier wordt weer gegeven, is geen voor beeld van een sleutel. _Dit is de_ sleutel die u nodig hebt om toegang te krijgen tot de Hotel gegevens. Het bestand appSettings. json moet er nu als volgt uitzien.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. We zijn nog niet klaar met dit bestand, selecteer de eigenschappen voor dit bestand en wijzig de instelling van **kopiëren naar uitvoer Directory** naar **kopiëren indien nieuwer**.

    ![De app-instellingen naar de uitvoer kopiëren](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Model gegevens structuren

Modellen (C#-klassen) worden gebruikt om gegevens te communiceren tussen de client (de weer gave), de server (de controller) en de Azure-Cloud met behulp van de architectuur MVC (model, View, controller). Normaal gesp roken wordt in deze modellen de structuur weer gegeven van de gegevens die worden geopend. We hebben ook een model nodig om de communicatie van de weer gave/controller te kunnen afhandelen.

1. Open de map **modellen** van het project met behulp van Solution Explorer en u ziet een standaard model in er: **ErrorViewModel.cs**.

2. Klik met de rechter muisknop op de map **modellen** en selecteer vervolgens **Nieuw item** **toevoegen** . Selecteer vervolgens in het dialoog venster dat wordt weer gegeven **ASP.net core** de eerste optie **klasse**. Wijzig de naam van het. CS-bestand in Hotel.cs en klik op **toevoegen**. Vervang alle inhoud van Hotel.cs door de volgende code. Let op het **adres** en de **kamer** leden van de klasse, deze velden zijn zelf klassen, zodat we er modellen voor hebben.

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

3. Volg hetzelfde proces voor het maken van een model voor de **adres** klasse, met uitzonde ring van de naam van het bestand address.cs. Vervang de inhoud door het volgende.

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

4. En voer het proces opnieuw uit om de **room** -klasse te maken. noem het bestand room.cs. Vervang opnieuw de inhoud door het volgende.

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

5. De set met **Hotels**, **adres**en **room** -klassen is bekend in azure als [_complexe typen_](search-howto-complex-data-types.md), een belang rijke functie van Azure Cognitive Search. Complexe typen kunnen een groot aantal klassen en subklassen zijn, en veel complexere gegevens structuren kunnen worden weer gegeven dan met behulp van _eenvoudige typen_ (een klasse met alleen primitieve leden). We hebben nog één model nodig, dus ga dan door met het proces van het maken van een nieuwe model klasse, maar deze keer wordt de klasse SearchData.cs aangeroepen en de standaard code vervangen door het volgende.

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

    Deze klasse bevat de invoer van de gebruiker (**brons**) en de uitvoer van de zoek opdracht (**resultList**). Het type uitvoer is kritiek, **DocumentSearchResult&lt;Hotel&gt;**, omdat dit type precies overeenkomt met de resultaten van de zoek opdracht, en we moeten deze verwijzing door geven aan de weer gave.



## <a name="create-a-web-page"></a>Een webpagina maken

Het project dat u hebt gemaakt, maakt standaard een aantal client weergaven. De exacte weer gaven zijn afhankelijk van de versie van de kern .NET die u gebruikt (we gebruiken 2,1 in dit voor beeld). Ze bevinden zich allemaal in de map **views** van het project. U hoeft alleen het bestand index. cshtml te wijzigen (in de **weer gaven/basismap** ).

Verwijder de inhoud van index. cshtml in zijn geheel en bouw het bestand opnieuw op in de volgende stappen.

1. We gebruiken twee kleine afbeeldingen in de weer gave. U kunt uw eigen gebruiken of kopiëren over de installatie kopieën vanuit het project GitHub: Azure-logo. png en Search. png. Deze twee installatie kopieën moeten worden geplaatst in de map **wwwroot/afbeeldingen** .

2. De eerste regel van index. cshtml moet verwijzen naar het model dat we gebruiken om gegevens te communiceren tussen de client (de weer gave) en de server (de-controller), het **SearchData** -model dat we hebben gemaakt. Voeg deze regel toe aan het bestand index. cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Het is standaard handig om een titel voor de weer gave in te voeren, zodat de volgende regels:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Na de titel voert u een verwijzing in naar een HTML-opmaak model, dat we binnenkort zullen maken.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nu naar het vlees van de weer gave. Een belang rijke ding waarmee u rekening moet houden, is dat de weer gave twee situaties moet afhandelen. Ten eerste moet de weer gave worden afgehandeld wanneer de app voor het eerst wordt gestart en heeft de gebruiker nog geen Zoek tekst ingevoerd. Ten tweede moet de weer gave van resultaten naast het zoekvak worden afgehandeld voor herhaalde gebruik door de gebruiker. Voor het afhandelen van deze twee situaties moeten we controleren of het model dat aan de weer gave is gegeven, null is. Een null-model geeft aan dat we in de eerste van de twee situaties (de eerste keer dat de app wordt uitgevoerd). Voeg het volgende toe aan het bestand index. cshtml en lees de opmerkingen.

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

6. Ten slotte voegen we het opmaak model toe. Selecteer in Visual Studio in het menu **bestand** de optie **nieuw/bestand** en vervolgens **stijl blad** (met **Algemeen** gemarkeerd). Vervang de standaard code door het volgende. Dit bestand wordt niet meer gedetailleerd weer gegeven, de stijlen zijn standaard-HTML.

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

7. Sla het opmaak model bestand op als hotels. CSS, in de map wwwroot/CSS, naast het standaard bestand site. CSS.

Hiermee voltooit u de weer gave. We maken een goede voortgang. De modellen en weer gaven zijn voltooid, maar alleen de controller blijft over om alles samen te koppelen.

## <a name="define-methods"></a>Methoden definiëren

We moeten de inhoud van de ene controller (**Home-controller**) wijzigen, die standaard wordt gemaakt.

1. Open het HomeController.cs-bestand en vervang de instructies **using** door de volgende.

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

### <a name="add-index-methods"></a>Index methoden toevoegen

We hebben twee **index** methoden nodig, één waarbij geen para meters worden opgegeven (voor het geval dat de app voor het eerst wordt geopend), en één model als para meter (voor wanneer de gebruiker Zoek tekst heeft ingevoerd). De eerste van deze methoden wordt standaard gemaakt. 

1. Voeg de volgende methode toe na de standaard **index ()** -methode.

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

    Let op de **async** -declaratie van de-methode **en de** **onRunQueryAsynce**-aanroep naar de aanvraag van de functie. Deze tref woorden maken het uitvoeren van de aanroepen asynchroon en voor komt u dat threads op de server worden geblokkeerd.

    Het blok **Catch** maakt gebruik van het fout model dat standaard voor ons is gemaakt.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Noteer de fout afhandeling en andere standaard weergaven en-methoden

Afhankelijk van welke versie van .NET core u gebruikt, wordt standaard een andere set standaard weergaven gemaakt. Voor .NET Core 2,1 zijn de standaard weergaven index, informatie over, contact persoon, privacy en fout. Voor .NET Core 2,2 worden bijvoorbeeld de standaard weergaven index, privacy en fout weer gegeven. In beide gevallen kunt u deze standaard pagina's weer geven tijdens het uitvoeren van de app en onderzoeken hoe deze worden verwerkt in de controller.

De fout weergave wordt verderop in deze zelf studie getest.

In het GitHub-voor beeld zijn de niet-gebruikte weer gaven en de bijbehorende acties verwijderd.

### <a name="add-the-runqueryasync-method"></a>De methode RunQueryAsync toevoegen

De aanroep van Azure Cognitive Search is ingekapseld in onze **RunQueryAsync** -methode.

1. Voeg eerst enkele statische variabelen toe om de Azure-service in te stellen en een aanroep om ze te initiëren.

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

2. Voeg nu de methode **RunQueryAsync** toe.

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

    In deze methode zorgen we ervoor dat de Azure-configuratie wordt gestart en vervolgens een aantal zoek parameters instellen. De namen van de velden in de para meter **Select** exact overeenkomen met de namen van de eigenschappen in de klasse **Hotel** . Het is mogelijk om de para meter **Select** uit te laten staan. in dat geval worden alle eigenschappen geretourneerd. Het instellen van geen **Select** -para meters is echter inefficiënt als we alleen geïnteresseerd zijn in een subset van de gegevens. Door de eigenschappen op te geven waar we geïnteresseerd zijn, worden alleen deze eigenschappen geretourneerd.

    De asynchrone aanroep voor zoeken (**model. resultList = await _indexClient. documenten. SearchAsync&lt;Hotel&gt;(model. brons, para meters);**) is wat deze zelf studie en app allemaal over zijn. De klasse **DocumentSearchResult** is interessant en er is een goed idee (wanneer de app wordt uitgevoerd) om hier een onderbrekings punt in te stellen en een debugger te gebruiken om de inhoud van **model. resultList**te controleren. U moet weten dat het intuïtief is, zodat u de gegevens krijgt die u hebt gevraagd en niet veel anders.

Nu voor het moment van waarheid.

### <a name="test-the-app"></a>De app testen

Nu gaan we controleren of de app correct wordt uitgevoerd.

1. Selecteer **fouten opsporen/starten zonder fout opsporing** of druk op de toets F5. Als u dingen correct hebt gecodeerd, krijgt u de eerste index weergave.

     ![De app openen](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Voer tekst in, bijvoorbeeld ' strand ' (of een tekst die in aanmerking komt), en klik op het zoek pictogram. U moet een aantal resultaten krijgen.

     ![Zoeken naar "strand"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Geef vijf ster op. Houd er rekening mee dat u geen resultaten krijgt. Een geavanceerdere zoek opdracht zou ' vijf sterren ' behandelen als synoniem voor ' luxe ' en retour neren deze resultaten. Het gebruik van synoniemen is beschikbaar in azure Cognitive Search, maar we zullen deze niet in de eerste zelf studies behandelen.
 
4. Voer ' hot ' in als Zoek tekst. Er worden _geen_ vermeldingen geretourneerd met het woord ' Hotel '. Onze zoek opdracht zoekt alleen hele woorden, maar er worden wel enkele resultaten geretourneerd.

5. Probeer andere woorden: ' pool ', ' schijnt ', ' View ' en wat. U ziet dat Azure Cognitive Search op de eenvoudigste manier werkt, maar nog steeds overtuigend is.

## <a name="test-edge-conditions-and-errors"></a>Voor waarden en fouten van de test rand

Het is belang rijk om te controleren of de functies voor fout afhandeling werken zoals het zou moeten, zelfs wanneer alles perfect werkt. 

1. In de methode **index** voert u na de **try {** Call, de regel **nieuwe uitzonde ring ()** in. Deze uitzonde ring dwingt een fout af wanneer er wordt gezocht in tekst.

2. Voer de app uit, voer "Bar" in als Zoek tekst en klik op het zoek pictogram. De uitzonde ring moet resulteren in de fout weergave.

     ![Een fout forceren](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Het wordt beschouwd als een beveiligings risico voor het retour neren van interne fout nummers op fout pagina's. Als uw app is bedoeld voor algemeen gebruik, doet u een onderzoek naar de veilige en beste prak tijken van wat er moet worden geretourneerd als er een fout optreedt.

3. Verwijder **throw nieuwe uitzonde ring ()** wanneer u tevreden bent over de fout afhandeling werkt zoals in dat geval.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende Takeaways van dit project:

* Een aanroep van Azure Cognitive Search is beknopt en het is eenvoudig om de resultaten te interpreteren.
* Asynchrone aanroepen voegen een kleine mate van complexiteit toe aan de controller, maar zijn de best practice als u van plan bent om kwaliteits-apps te ontwikkelen.
* Deze app heeft een zoek opdracht voor eenvoudig tekst uitgevoerd, gedefinieerd door wat is ingesteld in **searchParameters**. Deze ene klasse kan echter worden gevuld met veel leden die verfijning toevoegen aan een zoek opdracht. Er is niet veel extra werk nodig om deze app aanzienlijk krachtiger te maken.

## <a name="next-steps"></a>Volgende stappen

Om de beste gebruikers ervaring met Azure Cognitive Search te bieden, moeten we meer functies toevoegen, met name paginering (met behulp van pagina nummers of oneindig schuiven), en automatisch aanvullen/suggesties. We moeten ook complexere zoek parameters overwegen (bijvoorbeeld geografische zoek acties op hotels binnen een opgegeven straal van een bepaald punt en de volg orde van de zoek resultaten).

Deze volgende stappen worden behandeld in een reeks zelf studies. Laten we beginnen met paginering.

> [!div class="nextstepaction"]
> [C#-zelf studie: paginering van zoek resultaten-Azure Cognitive Search](tutorial-csharp-paging.md)


