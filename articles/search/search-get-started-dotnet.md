---
title: 'Quickstart: Een zoekindex maken in .NET'
titleSuffix: Azure Cognitive Search
description: In deze C#-quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van de Azure.Search.Documents-clientbibliotheek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ffcb2c741838b1292eaf0793cd625c99d252068c
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462068"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Quickstart: Een zoekindex maken met behulp van de Azure.Search.Documents-clientbibliotheek.

Gebruik de nieuwe [Azure.Search.Documents (versie 11)-clientbibliotheek](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) om een .NET Core-consoletoepassing te maken in C# waarmee een zoekindex kan worden gemaakt, geladen en opgevraagd.

[Download de broncode](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) om te beginnen met een voltooid project of volg de stappen in dit artikel om uw eigen project te maken.

> [!NOTE]
> Zoekt u een eerdere versie? Zie [Een zoekindex maken met Microsoft. Azure.Search v10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u beschikken over de volgende hulpprogramma's en services:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt een gratis service voor deze quickstart gebruiken. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), een willekeurige editie. Voorbeeldcode is getest in de gratis Community-editie van Visual Studio 2019.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>Een sleutel en eindpunt ophalen

Voor aanroepen naar de service zijn voor elke aanvraag een URL-eindpunt en een toegangssleutel vereist. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. In **Instellingen** > **Sleutels**, kunt u een beheerderssleutel ophalen voor volledige rechten op de service. Deze heeft u nodig voor het maken en verwijderen van objecten. Er zijn twee uitwisselbare primaire en secundaire sleutels. U kunt beide gebruiken.

   ![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-project"></a>Uw project instellen

Open Visual Studio en maak een nieuw Console App-project dat kan worden uitgevoerd op .NET Core. 

### <a name="install-the-nuget-package"></a>Installeer het NuGet-pakket van

Nadat het project is gemaakt, voegt u de clientbibliotheek toe. Het [Azure.Search.Documents-pakket](https://www.nuget.org/packages/Azure.Search.Documents/) bestaat uit één clientbibliotheek die alle API's bevat die worden gebruikt om te werken met een zoekservice in .NET.

1. Selecteer onder **Hulpprogramma's** > **NuGet-pakketbeheer** de optie **NuGet-pakketten voor oplossing beheren...** . 

1. Klik op **Bladeren**.

1. Zoek `Azure.Search.Documents` en selecteer versie 11.0.0.

1. Klik op **Installeren** aan de rechterkant om de assembly toe te voegen aan uw project en oplossing.

### <a name="create-a-search-client"></a>Een zoekclient maken

1. Wijzig in **Program.cs** de naamruimte in `AzureSearch.SDK.Quickstart.v11` en voeg vervolgens de volgende `using`-instructies toe.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Twee clients maken: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) maakt de index en [SearchClient](/dotnet/api/azure.search.documents.searchclient) werkt met een bestaande index. Beide hebben het service-eindpunt en een beheerder-API-sleutel nodig voor verificatie van rechten voor maken/verwijderen.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Een index maken

In deze quickstart wordt een index van hotels gemaakt die u met hotelgegevens laadt en waarop u query's uitvoert. In deze stap definieert u de velden in de index. Elke definitie bevat een naam, gegevenstype en kenmerken die bepalen hoe het veld wordt gebruikt.

In dit voorbeeld worden synchrone methoden van de Azure.Search.Documents-bibliotheek gebruikt voor eenvoud en leesbaarheid. Voor productiescenario's moet u echter asynchrone methoden gebruiken om uw app op een schaalbare en responsieve manier te laten werken. U kunt bijvoorbeeld [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) gebruiken in plaats van [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Voeg een lege klassedefinitie toe aan uw project: **Hotel.cs**

1. Definieer in **Hotel.cs** de structuur van een hoteldocument.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. Geef in **Program.cs** de velden en kenmerken op. [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) en [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) worden gebruikt voor het maken van een index.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Door kenmerken in het veld wordt bepaald hoe het veld wordt gebruikt in een toepassing. Het `IsFilterable`-kenmerk moet bijvoorbeeld worden toegewezen aan elk veld dat een filterexpressie ondersteunt.

In tegenstelling tot eerdere versies van de .NET SDK waarvoor [IsSearchable-](/dotnet/api/microsoft.azure.search.models.field.issearchable) vereist is voor doorzoekbare tekenreeksvelden, kunt u [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) en [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) gebruiken om definities te stroomlijnen.

Net als bij de vorige versies zijn er nog andere kenmerken vereist voor de definitie zelf. Bijvoorbeeld [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)en [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) moet expliciet worden voorzien van een kenmerk, zoals weergegeven in het bovenstaande voorbeeld. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Azure Cognitive Search doorzoekt inhoud die in de service is opgeslagen. In deze stap laadt u JSON-documenten die overeenkomen met de hotelindex die u zojuist hebt gemaakt.

In Azure Cognitive Search zijn documenten gegevensstructuren die zowel de invoer van indexeringen als de uitvoer van query's zijn. Als u de documenten hebt verkregen via een externe gegevensbron, bestaat de documentinvoer mogelijk uit rijen in een database, blobs in Blob Storage of JSON-documenten op een schijf. In dit voorbeeld nemen we de korte route en gaan we JSON-documenten voor vijf hotels in de code zelf insluiten. 

Bij het uploaden van documenten moet u een [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1)-object gebruiken. Een IndexDocumentsBatch bevat een verzameling van [acties](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), die elk een document en een eigenschap bevat waarmee Azure Cognitive Search wordt aangestuurd om een actie uit te voeren ([uploaden, samenvoegen, verwijderen en mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Maak in **Program.cs**een matrix met documenten en indexacties, en geef de matrix vervolgens door aan `ndexDocumentsBatch`De onderstaande documenten voldoen aan de hotels-quickstart-v11-index, zoals gedefinieerd door de hotelklasse.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    Nadat u het [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1)-object hebt geïnitialiseerd, kunt u het naar de index sturen door [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) aan te roepen op uw [SearchClient](/dotnet/api/azure.search.documents.searchclient)-object.

1. Omdat dit een console-app is waarmee alle opdrachten opeenvolgend worden uitgevoerd, moet u een wachttijd van 2 seconden tussen indexeren en query's toevoegen.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    De vertraging van 2 seconden compenseert de indexering (die asynchroon is), zodat alle documenten kunnen worden geïndexeerd voordat de query's worden uitgevoerd. Codering in een vertraging is doorgaans alleen nodig in demo's, testen en voorbeeldtoepassingen.

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

U kunt queryresultaten ophalen zodra het eerste document wordt geïndexeerd, maar wacht met het daadwerkelijk testen van uw index totdat alle documenten zijn geïndexeerd.

In deze sectie worden twee functies toegevoegd: querylogica en resultaten. Gebruik voor query's de methode [Search](/dotnet/api/azure.search.documents.searchclient.search). Deze methode gebruikt zoektekst (de querytekenreeks) en andere [opties](/dotnet/api/azure.search.documents.searchoptions).

De [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1)-klasse vertegenwoordigt de resultaten.

1. In **Program.cs** maakt u een WriteDocuments-methode waarmee zoekresultaten op de console worden afgedrukt.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Maak een RunQueries-methode om query's uit te voeren en resultaten te retourneren. Resultaten zijn Hotel-objecten.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

Er zijn twee [manieren om termen in een query te vergelijken](search-query-overview.md#types-of-queries): zoeken op volledige tekst en filters:

+ Een query naar volledige tekst zoekt naar een of meer termen in doorzoekbare velden in uw index. De eerste query zoekt naar volledige tekst. Zoeken naar volledige tekst produceert relevantiescores die worden gebruikt om de resultaten te rangschikken.

+ Een filter is een Booleaanse uitdrukking die wordt geëvalueerd over [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)-velden in een index. Filterquery's bevatten waarden of sluiten ze uit. Daarom is er geen relevantiescore gekoppeld aan een filterquery. De laatste twee query's tonen het zoeken met filters.

U kunt zoeken op volledige tekst en filters samen of afzonderlijk gebruiken.

Zoekopdrachten en filters worden uitgevoerd met behulp van de methode [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search). Een zoekquery kan worden doorgegeven in de `searchText`-tekenreeks, terwijl een filterexpressie kan worden doorgegeven in de eigenschap [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) van de klasse [Search Options](/dotnet/api/azure.search.documents.searchoptions). Als u wilt filteren zonder te zoeken, geeft u `"*"` door voor de `searchText`-parameter van de [Zoekmethode](/dotnet/api/azure.search.documents.searchclient.search). Om te zoeken zonder te filteren, stelt u de eigenschap `Filter` niet in of geeft u niet door aan een `SearchOptions`-instantie.

## <a name="run-the-program"></a>Het programma uitvoeren

Druk op F5 om de app opnieuw te bouwen en het programma in zijn geheel uit te voeren. 

De uitvoer bevat berichten van [Console.WriteLIne](/dotnet/api/system.console.writeline), met toevoeging van query-informatie en -resultaten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze C#-quickstart hebt u een reeks taken uitgevoerd om een index te maken, hier documenten in te laden en query's uit te voeren. In verschillende fasen hebben we korte routes genomen om de code te vereenvoudigen voor leesbaarheid en begrijpelijkheid. Als u voldoende bekend bent met de basisconcepten, is het raadzaam het volgende artikel te lezen om alternatieve methoden en concepten te verkennen om uw kennis uit te breiden. 

> [!div class="nextstepaction"]
> [Ontwikkelen in .NET](search-howto-dotnet-sdk.md)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)