---
title: C#-zelfstudie voor het maken van uw eerste app
titleSuffix: Azure Cognitive Search
description: Meer informatie over het stapsgewijs maken van uw eerste C#-zoek-app. De zelfstudie biedt zowel een downloadkoppeling naar een werkende app op GitHub als het volledige proces om de app helemaal zelf te bouwen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec98762ac5918437e8fdb8426b54b79b1fb5b222
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939718"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Zelfstudie: Uw eerste zoek-app maken met behulp van de .NET SDK

In deze zelfstudie leert u hoe u een web-app maakt waarmee de resultaten van een zoekindex worden opgevraagd en geretourneerd met behulp van Azure Cognitive Search en Visual Studio.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een ontwikkelomgeving instellen
> * Gegevensstructuren modelleren
> * Een webpagina maken voor het verzamelen van query-invoer en het weergeven van resultaten
> * Een zoekmethode definiëren
> * De app testen

U leert ook hoe eenvoudig een zoekaanroep is. De belangrijkste instructies in de code die u ontwikkelt, zijn in de volgende paar regels ingekapseld.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Met slechts één aanroep wordt een query uitgevoerd op de index en worden resultaten geretourneerd.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Zoeken naar '*pool*'" border="true":::

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt een bestaande, gehoste voorbeeldindex gebruikt, zodat u zich kunt richten op het bouwen van een zoekpagina waarmee een queryreeks voor de aanvraag wordt verzameld en resultaten worden geretourneerd. De index bevat fictieve hotelgegevens. Zodra u een basispagina hebt, kunt u deze in de volgende lessen uitbreiden met paginering, facetten en automatisch aanvullen.

Een voltooide versie van de code in deze zelfstudie vindt u in het volgende project:

* [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Deze zelfstudie is bijgewerkt voor het gebruik van het pakket Azure.Search.Documents (versie 11). Zie het [codevoorbeeld Microsoft.Azure.Search (versie 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10) voor een eerdere versie van de .NET SDK.

## <a name="prerequisites"></a>Vereisten

Omdat u een openbaar zoekindexvoorbeeld gebruikt dat wordt gehost door Microsoft, hebt u geen zoekservice of een Azure-account nodig voor deze zelfstudie.

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Azure Cognitive Search-clientbibliotheek (versie 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Het project installeren en uitvoeren vanuit GitHub

Als u verder wilt gaan naar een werkende app, volgt u de onderstaande stappen om de voltooide code te downloaden en uit te voeren.

1. Zoek het voorbeeld op GitHub: [Eerste app maken](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11).

1. Selecteer in de [hoofdmap](https://github.com/Azure-Samples/azure-search-dotnet-samples) de optie **Code**, gevolgd door **Klonen** of **ZIP-bestand downloaden** om uw persoonlijke lokale kopie van het project te maken.

1. Navigeer met Visual Studio naar en open de oplossing voor de eenvoudige zoekpagina ('1-basic-search-page') en selecteer **Starten zonder foutopsporing** (of druk op F5) om het programma te bouwen en uit te voeren.

1. Dit is een hotelindex. Typ dus enkele woorden die u kunt gebruiken om te zoeken naar hotels (bijvoorbeeld 'wifi', 'view', 'bar', 'parking') en bekijk de resultaten.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Zoeken naar '*pool*'" border="true":::

Hopelijk wordt dit project soepel uitgevoerd en wordt er een web-app uitgevoerd. Veel van de essentiële onderdelen voor complexere zoekopdrachten zijn opgenomen in deze ene app. Daarom is het een goed idee om deze te doorlopen en stap voor stap opnieuw te maken. In de volgende secties komen deze stappen aan de orde.

## <a name="set-up-a-development-environment"></a>Een ontwikkelomgeving instellen

Als u dit project helemaal zelf wilt maken, en daarmee de concepten van Azure Cognitive Search die u in gedachten hebt, begint u met een Visual Studio-project.

1. Selecteer in Visual Studio **Nieuw** > **Project** en vervolgens **ASP.NET Core-webtoepassing**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Zoeken naar '*pool*'" border="true":::

1. Geef het project een naam zoals 'FirstSearchApp' en stel de locatie in. Selecteer **Maken**.

1. Gebruik de projectsjabloon **Web Application (Model-View-Controller)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Zoeken naar '*pool*'" border="true":::

1. Installeer de clientbibliotheek. Selecteer in **Hulpprogramma's** > **NuGet Package Manager** > , **NuGet-pakketten voor oplossing beheren...** de optie **Bladeren** en zoek vervolgens naar 'azure.search.documents'. Installeer **Azure.Search.Documents** (versie 11 of hoger) en accepteer de licentieovereenkomsten en afhankelijkheden.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Zoeken naar '*pool*'" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Azure Cognitive Search initialiseren

Voor dit voorbeeld gebruikt u openbaar beschikbare hotelgegevens. Deze gegevens zijn een willekeurige verzameling van 50 fictieve hotelnamen en beschrijvingen die alleen zijn gemaakt voor het leveren van demogegevens. Geef een naam en API-sleutel op om toegang te krijgen tot deze gegevens.

1. Open **appsettings.json** en vervang de standaardregels door de volgende naam en sleutel. De API-sleutel die hier wordt weergegeven, is geen voorbeeld van een sleutel. Dit is *exact* de sleutel die u nodig hebt voor toegang tot de hotelgegevens. Uw bestand moet er nu als volgt uitzien.

    ```csharp
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

1. In Solution Explorer selecteert u het bestand en wijzigt u in Eigenschappen de instelling **Naar uitvoermap kopiëren** in **Kopiëren indien nieuwer**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Zoeken naar '*pool*'" border="true":::

## <a name="model-data-structures"></a>Gegevensstructuren modelleren

Modellen (C#-klassen) worden gebruikt om gegevens te communiceren tussen de client (de weergave), de server (de controller) en ook de Azure-cloud met behulp van de MVC-architectuur (model, view, controller). Normaal gesproken wordt in deze modellen de structuur weergegeven van de gegevens die worden geopend.

In deze stap modelleert u de gegevensstructuren van de zoekindex, evenals de zoekreeks die wordt gebruikt in de communicatie van de weergave/controller. In de hotelindex heeft elk hotel veel kamers en elk hotel heeft een adres dat uit meerdere delen bestaat. De volledige weergave van een hotel is een hiërarchische en geneste gegevensstructuur. U hebt drie klassen nodig om elk onderdeel te maken.

De set klassen **Hotel**, **Address** en **Room** zijn wat bekend staat als [*complexe typen*](search-howto-complex-data-types.md), een belangrijke functie van Azure Cognitive Search. Complexe typen kunnen bestaan uit een groot aantal klassen en subklassen, en veel complexere gegevensstructuren weergeven dan *eenvoudige typen* (een klasse met alleen primitieve leden).

1. Klik in Solution Explorer met de rechtermuisknop op **Modellen** > **Toevoegen** > **Nieuw item**.

1. Selecteer**Klasse** en geef het item de naam Hotel.cs. Vervang alle inhoud van Hotel.cs door de volgende code. Let op de leden **Address** en **Room** van de klasse. Deze velden zijn zelf klassen, dus hier hebt u ook modellen voor nodig.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Hotel
        {
            [SimpleField(IsFilterable = true, IsKey = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            public Address Address { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true)]
            public GeographyPoint Location { get; set; }

            public Room[] Rooms { get; set; }
        }
    }
    ```

1. Herhaal hetzelfde proces voor het maken van een model voor de klasse **Address**, en geef het bestand de naam Address.cs. Vervang de inhoud door het volgende.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. Volg opnieuw hetzelfde proces om de klasse **Room** te maken, en geef het bestand de naam Room.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Het laatste model dat u in deze zelfstudie maakt, is een klasse met de naam **SearchData** en vertegenwoordigt de invoer van de gebruiker (**searchText**) en de uitvoer van de zoekopdracht (**resultList**). Het type uitvoer is kritiek, **SearchResults&lt;Hotel&gt;** , omdat dit type precies overeenkomt met de resultaten van de zoekopdracht, en u deze verwijzing moet doorgeven aan de weergave. Vervang de standaardsjabloon door de volgende code.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Een webpagina maken

Projectsjablonen worden geleverd met een aantal clientweergaven die zich in de map **Weergaven** bevinden. De exacte weergaven zijn afhankelijk van de versie van Core .NET die u gebruikt (in dit voorbeeld wordt 3.1 gebruikt). Voor deze zelfstudie wijzigt u **Index.cshtml** om de elementen van een zoekpagina op te nemen.

Verwijder de inhoud van Index.cshtml in zijn geheel en bouw het bestand opnieuw op in de volgende stappen.

1. In de zelfstudie worden twee kleine afbeeldingen in de weergave gebruikt: een Azure-logo en een vergrootglaspictogram voor zoeken (azure-logo.png en search.png). Kopieer de installatiekopieën van het GitHub-project naar de map **wwwroot/images** in uw project.

1. De eerste regel van Index.cshtml moet verwijzen naar het model dat wordt gebruikt om gegevens te communiceren tussen de client (de weergave) en de server (de controller). Dit is het **SearchData**-model dat eerder is gemaakt. Voeg deze regel toe aan het bestand Index.cshtml file.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Het is gebruikelijk om een titel voor de weergave in te voeren. De volgende regels moeten dus als volgt zijn:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Na de titel voert u een verwijzing in naar een HTML-opmaakmodel, dat u binnenkort gaat maken.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. In de hoofdtekst van de weergave worden twee gebruiksvoorbeelden verwerkt. Ten eerste moet er een lege pagina zijn bij het eerste gebruik, voordat de zoektekst wordt ingevoerd. Ten tweede moeten resultaten, naast het zoekvak, worden afgehandeld voor herhaalde query's.

   Voor het afhandelen van beide gevallen moeten we controleren of het model dat aan de weergave is verstrekt null is. Een null-model duidt op het eerste gebruiksvoorbeeld (de eerste keer dat de app wordt uitgevoerd). Voeg het volgende toe aan het bestand Index.cshtml en lees de opmerkingen.

    ```csharp
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
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Voeg het opmaakmodel toe. Selecteer in Visual Studio in **Bestand**> **Nieuw** > **Bestand** de optie **Opmaakmodel** (met **Algemeen** gemarkeerd).

   Vervang de code door het volgende. We gaan niet meer uitgebreid in op dit bestand. De stijlen zijn standaard-HTML.

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

1. Sla het opmaakmodelbestand op als hotels.css, in de map **wwwroot/css**, naast het standaardbestand site.css.

Hiermee is de weergave voltooid. Op dit moment zijn beide modellen en weergaven voltooid. Nu is er alleen nog de controller die alles moet koppelen.

## <a name="define-methods"></a>Methoden definiëren

In deze stap wijzigt u de inhoud van de **begincontroller**.

1. Open het HomeController.cs-bestand en vervang de **using**-instructies door het volgende.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Index-methoden toevoegen

In een MVC-app is de methode **Index()** een standaardactiemethode voor een controller. Hiermee wordt de index-HTML-pagina geopend. De standaardmethode, die geen parameters accepteert, wordt in deze zelfstudie gebruikt voor de eerste gebruikstoepassing van de toepassing: een lege zoekpagina weergeven.

In deze sectie wordt de methode uitgebreid voor het ondersteunen van een tweede gebruikstoepassing: de pagina weergeven wanneer een gebruiker de zoektekst heeft ingevoerd. Ter ondersteuning van deze gebruikstoepassing wordt de indexmethode uitgebreid om een model als parameter te accepteren.

1. Voeg de volgende methode toe na de standaard **Index()** -methode.

    ```csharp
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

    Let op de **async**-declaratie van de methode en de **await**-aanroep naar **RunQueryAsync**. Deze trefwoorden zorgen ervoor dat aanroepen asynchroon worden en voorkomen daarmee dat threads op de server worden geblokkeerd.

    Het **catch**-blok maakt gebruik van het foutmodel dat standaard is gemaakt.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>De foutafhandeling en andere standaardweergaven en -methoden bekijken

Afhankelijk van welke versie van .NET core u gebruikt, wordt standaard een iets andere set standaardweergaven gemaakt. Voor .NET Core 3.1 zijn de standaardweergaven Index, Privacy en Error. U kunt deze standaardpagina's weergeven tijdens het uitvoeren van de app en onderzoeken hoe deze worden verwerkt in de controller.

U gaat de weergave Error verderop in deze zelfstudie testen.

In het GitHub-voorbeeld zijn de niet-gebruikte weergaven en de bijbehorende acties verwijderd.

### <a name="add-the-runqueryasync-method"></a>De methode RunQueryAsync toevoegen

De aanroep van Azure Cognitive Search is ingekapseld in onze **RunQueryAsync**-methode.

1. Voeg eerst enkele statische variabelen toe om de Azure-service in te stellen en een aanroep om deze te initiëren.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Voeg nu de **RunQueryAsync**-methode zelf toe.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    Met deze methode zorgen we er eerst voor dat de Azure-configuratie wordt gestart en stellen we vervolgens een aantal zoekopties in. Met de optie **Selecteren** geeft u de velden op die in de resultaten moeten worden geretourneerd, en die dus overeenkomen met de namen van de eigenschappen in de klasse **Hotel**. Als u **Selecteren** weglaat, worden alle niet-verborgen velden geretourneerd. Dit kan inefficiënt zijn als u alleen geïnteresseerd bent in een subset van alle mogelijke velden.

    Met de asynchrone aanroep voor zoeken worden de aanvraag (gemodelleerd als **searchText**) en het antwoord (gemodelleerd als **searchResult**) geformuleerd. Als u fouten opspoort voor deze code, is de **SearchResult**-klasse een goede kandidaat voor het instellen van een onderbrekingspunt als u de inhoud van **model.resultList** wilt controleren. U ziet dat dit intuïtief is, en u de gegevens krijgt die u hebt gevraagd en niet veel meer.

### <a name="test-the-app"></a>De app testen

We gaan nu controleren of de app correct wordt uitgevoerd.

1. Selecteer **Fouten opsporen** > **Starten zonder foutopsporing** of druk op **F5**. Als de app wordt uitgevoerd zoals verwacht, moet u de initiële indexweergave ophalen.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Zoeken naar '*pool*'" border="true":::

1. Voer een queryreeks in, bijvoorbeeld 'beach' (of andere tekst die u te binnen schiet), en klik op het zoekpictogram om de aanvraag te verzenden.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Zoeken naar '*pool*'" border="true":::

1. Probeer 'five star' in te voeren. U ziet dat deze query geen resultaten oplevert. Een geavanceerdere zoekopdracht zou 'five star' behandelen als synoniem voor 'luxury' en deze resultaten retourneren. Ondersteuning voor [synoniemen](search-synonyms.md) is beschikbaar in Azure Cognitive Search, maar wordt niet behandeld in deze reeks zelfstudies.

1. Voer 'hot' in als zoektekst. Er worden _geen_ vermeldingen met het woord 'hotel' opgehaald. Onze zoekopdracht zoekt alleen naar hele woorden, maar er worden wel enkele resultaten opgehaald.

1. Probeer andere woorden: 'pool','sunshine', 'view', enzovoort. U ziet dat Azure Cognitive Search heel eenvoudig werkt, maar wel overtuigend is.

## <a name="test-edge-conditions-and-errors"></a>Voorwaarden en fouten aan de rand testen

Het is belangrijk om te controleren of de functies voor foutafhandeling werken zoals zou moeten, zelfs wanneer alles perfect werkt. 

1. Voer in de **Index**-methode, na de aanroep **try {** de regel **Throw new Exception()** in. Deze uitzondering dwingt een fout af wanneer u in tekst zoekt.

2. Voer de app uit, voer 'bar' in als zoektekst en klik op het zoekpictogram. De uitzondering moet de foutweergave tot gevolg hebben.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Zoeken naar '*pool*'" border="true":::

    > [!Important]
    > Het wordt beschouwd als een beveiligingsrisico om interne foutnummers op foutpagina's te retourneren. Doe als uw app bedoeld is voor algemeen gebruik onderzoek naar veilige en beste procedures voor wat er moet worden geretourneerd als er een fout optreedt.

3. Verwijder **Throw new Exception()** wanneer u tevreden bent over de foutafhandeling.

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende opgedane kennis van dit project:

* Een Azure Cognitive Search-aanroep is beknopt en het is eenvoudig om de resultaten te interpreteren.
* Asynchrone aanroepen voegen enige complexiteit toe aan de controller, maar zijn de best practice als u van plan bent om kwaliteits-apps te ontwikkelen.
* Deze app heeft een eenvoudige zoekopdracht naar tekst uitgevoerd, die is gedefinieerd door wat is ingesteld in **searchOptions**. Deze ene klasse kan echter worden gevuld met veel leden die verfijning toevoegen aan een zoekopdracht. Er is niet veel extra werk nodig om deze app aanzienlijk krachtiger te maken.

## <a name="next-steps"></a>Volgende stappen

Om de gebruikerservaring te verbeteren, moeten we meer functies toevoegen, met name paginering (met behulp van paginanummers of oneindig schuiven) en automatisch aanvullen/suggesties. U kunt ook complexere zoekopties overwegen (bijvoorbeeld geografische zoekacties op hotels binnen een opgegeven straal van een bepaald punt en de volgorde van de zoekresultaten).

Deze volgende stappen worden behandeld in de overige zelfstudies. Laten we beginnen met paginering.

> [!div class="nextstepaction"]
> [C#-zelfstudie: Zoekresultaten pagineren: Azure Cognitive Search](tutorial-csharp-paging.md)