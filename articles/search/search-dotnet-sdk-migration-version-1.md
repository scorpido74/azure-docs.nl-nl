---
title: Een upgrade uitvoeren naar Azure Search .NET SDK-versie 1,1
titleSuffix: Azure Cognitive Search
description: Code migreren naar de Azure Search .NET SDK versie 1,1 van oudere API-versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792383"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Een upgrade uitvoeren naar Azure Search .NET SDK-versie 1,1

Als u versie 1.0.2-Preview of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 1,1.

Zie [Azure Search van een .NET-toepassing gebruiken](search-howto-dotnet-sdk.md)voor een meer algemeen overzicht van de SDK, inclusief voor beelden.

> [!NOTE]
> Wanneer u een upgrade uitvoert naar versie 1,1, of als u al een versie gebruikt tussen 1,1 en 2,0-Preview, moet u een upgrade uitvoeren naar versie 3. Zie [upgraden naar de Azure Search .NET SDK versie 3](search-dotnet-sdk-migration.md) voor instructies.
>

U moet eerst uw NuGet-referentie voor `Microsoft.Azure.Search` bijwerken met behulp van de NuGet Package Manager-console of door met de rechter muisknop op uw project verwijzingen te klikken en NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op.

Als u voorheen versie 1.0.0-Preview, 1.0.1-Preview of 1.0.2-Preview had gebruikt, zou de build slagen en u klaar bent om te gaan.

Als u eerder versie 0.13.0-Preview of ouder hebt gebruikt, ziet u build-fouten als de volgende:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

De volgende stap is het oplossen van de build-fouten één voor één. Voor de meeste is het wijzigen van bepaalde klasse-en methode namen waarvan de naam is gewijzigd in de SDK. [Lijst met belang rijke wijzigingen in versie 1,1](#ListOfChangesV1) bevat een lijst met deze naam wijzigingen.

Als u aangepaste klassen gebruikt voor het model leren van uw documenten en deze klassen eigenschappen hebben van primitieve typen waarvoor geen null-waarde is toegestaan (bijvoorbeeld `int` of `bool` C#in), is er een probleem oplossing in de 1,1-versie van de SDK waarvan u op de hoogte moet zijn. Zie [Fout oplossingen in versie 1,1](#BugFixesV1) voor meer informatie.

Ten slotte kunt u, nadat u een opbouw fout hebt aangebracht, wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lijst met belang rijke wijzigingen in versie 1,1
De volgende lijst wordt geordend op basis van de kans dat de wijziging invloed heeft op de code van uw toepassing.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch-en IndexAction-wijzigingen
de naam van `IndexBatch.Create` is gewijzigd in `IndexBatch.New` en er is geen `params` argument meer. U kunt `IndexBatch.New` gebruiken voor batches die verschillende soorten acties combi neren (samen voegingen, verwijderingen, enzovoort). Daarnaast zijn er nieuwe statische methoden voor het maken van batches waarin alle acties hetzelfde zijn: `Delete`, `Merge`, `MergeOrUpload`en `Upload`.

`IndexAction` heeft geen open bare constructors meer en de bijbehorende eigenschappen zijn nu onveranderbaar. Gebruik de nieuwe statische methoden voor het maken van acties voor verschillende doel einden: `Delete`, `Merge`, `MergeOrUpload`en `Upload`. `IndexAction.Create` is verwijderd. Als u de overbelasting voor alleen een document hebt gebruikt, moet u in plaats daarvan `Upload` gebruiken.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Als u wilt, kunt u deze nog verder vereenvoudigen:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException wijzigingen
De naam van de eigenschap `IndexBatchException.IndexResponse` is gewijzigd in `IndexingResults`en het type ervan is nu `IList<IndexingResult>`.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Wijzigingen in de bewerkings methode
Elke bewerking in de Azure Search .NET SDK wordt weer gegeven als een set methode-Overloads voor synchrone en asynchrone aanroepers. De hand tekeningen en factoring van deze methode-overbelasting is gewijzigd in versie 1,1.

De bewerking index statistieken ophalen in oudere versies van de SDK heeft bijvoorbeeld deze hand tekeningen weer gegeven:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

De methode-hand tekeningen voor dezelfde bewerking in versie 1,1 zien er als volgt uit:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Vanaf versie 1,1 worden de bewerkings methoden door de Azure Search .NET SDK anders ingedeeld:

* Optionele para meters worden nu als standaard parameters gemodelleerd in plaats van extra methode Overloads. Dit vermindert het aantal overbelasting van de methode, soms aanzienlijk.
* De uitbreidings methoden verbergen nu een groot deel van de overbodige details van HTTP van de aanroeper. Oudere versies van de SDK hebben bijvoorbeeld een antwoord object geretourneerd met een HTTP-status code, die u vaak niet hoeft te controleren, omdat bewerkings methoden `CloudException` genereren voor elke status code die een fout aanduidt. De nieuwe uitbreidings methoden retour neren alleen model objecten, waardoor u geen problemen hoeft op te slaan in uw code.
* Daarentegen bieden de kern interfaces nu methoden die u meer controle geven over het HTTP-niveau als u dit nodig hebt. U kunt nu aangepaste HTTP-headers door geven die moeten worden opgenomen in-aanvragen en het nieuwe `AzureOperationResponse<T>` retour type geeft u direct toegang tot de `HttpRequestMessage` en `HttpResponseMessage` voor de bewerking. `AzureOperationResponse` is gedefinieerd in de `Microsoft.Rest.Azure` naam ruimte en vervangt `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>ScoringParameters wijzigingen
Er is een nieuwe klasse met de naam `ScoringParameter` toegevoegd in de nieuwste SDK, zodat u gemakkelijker para meters kunt opgeven voor het scoren van profielen in een zoek opdracht. Voorheen werd de eigenschap `ScoringProfiles` van de klasse `SearchParameters` getypt als `IList<string>`; Nu wordt deze getypt als `IList<ScoringParameter>`.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Wijzigingen in model klassen
Als gevolg van de wijzigingen in de hand tekening die worden beschreven in de [bewerkings methode](#OperationMethodChanges), zijn veel klassen in de naam ruimte van de `Microsoft.Azure.Search.Models` hernoemd of verwijderd. Bijvoorbeeld:

* `IndexDefinitionResponse` is vervangen door `AzureOperationResponse<Index>`
* De naam van `DocumentSearchResponse` is gewijzigd in `DocumentSearchResult`
* De naam van `IndexResult` is gewijzigd in `IndexingResult`
* `Documents.Count()` retourneert nu een `long` met het aantal documenten in plaats van een `DocumentCountResponse`
* De naam van `IndexGetStatisticsResponse` is gewijzigd in `IndexGetStatisticsResult`
* De naam van `IndexListResponse` is gewijzigd in `IndexListResult`

Als u wilt samenvatten, worden `OperationResponse`afgeleide klassen die alleen beschikbaar waren voor het verpakken van een model object, verwijderd. Het achtervoegsel van de resterende klassen is gewijzigd van `Response` naar `Result`.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

#### <a name="response-classes-and-ienumerable"></a>Antwoord klassen en IEnumerable
Een extra wijziging die van invloed kan zijn op uw code is dat de antwoord klassen die verzamelingen bevatten niet langer `IEnumerable<T>`implementeren. In plaats daarvan kunt u rechtstreeks toegang krijgen tot de verzamelings eigenschap. Als uw code er bijvoorbeeld als volgt uitziet:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Speciaal geval voor webtoepassingen
Als u een webtoepassing hebt die `DocumentSearchResponse` rechtstreeks serieert om Zoek resultaten naar de browser te verzenden, moet u de code wijzigen of worden de resultaten niet op de juiste wijze geserialiseerd. Als uw code er bijvoorbeeld als volgt uitziet:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

U kunt dit wijzigen door de eigenschap `.Results` van het Zoek resultaat op te halen om de weer gave van zoek resultaten te herstellen:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

U zult in uw code zelf moeten zoeken naar dergelijke gevallen; **De compiler waarschuwt u niet** omdat `JsonResult.Data` van het type `object`is.

### <a name="cloudexception-changes"></a>CloudException wijzigingen
De klasse `CloudException` is verplaatst van de `Hyak.Common` naam ruimte naar de `Microsoft.Rest.Azure` naam ruimte. De naam van de eigenschap `Error` is ook gewijzigd in `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient-en SearchIndexClient-wijzigingen
Het type van de `Credentials` eigenschap is gewijzigd van `SearchCredentials` in de basis klasse, `ServiceClientCredentials`. Als u toegang nodig hebt tot de `SearchCredentials` van een `SearchIndexClient` of `SearchServiceClient`, gebruikt u de nieuwe `SearchCredentials`-eigenschap.

In oudere versies van de SDK had `SearchServiceClient` en `SearchIndexClient` constructors die een `HttpClient`-para meter hebben gevolgd. Deze zijn vervangen door constructors die een `HttpClientHandler` en een matrix van `DelegatingHandler`-objecten nemen. Dit maakt het gemakkelijker om aangepaste handlers te installeren en zo nodig HTTP-aanvragen vooraf te verwerken.

Ten slotte hebben de constructors die een `Uri` hebben geduurd en `SearchCredentials` gewijzigd. Als u bijvoorbeeld code hebt die er ongeveer als volgt uitziet:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Houd er ook rekening mee dat het type van de para meter referenties is gewijzigd in `ServiceClientCredentials`. Dit is waarschijnlijk niet van invloed op uw code, omdat `SearchCredentials` is afgeleid van `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Een aanvraag-ID door geven
In oudere versies van de SDK kunt u een aanvraag-ID instellen op de `SearchServiceClient` of `SearchIndexClient` en deze vervolgens opnemen in elke aanvraag aan de REST API. Dit is handig voor het oplossen van problemen met de zoek service als u contact moet opnemen met de ondersteuning. Het is echter handiger om voor elke bewerking een unieke aanvraag-ID in te stellen, in plaats van dezelfde ID voor alle bewerkingen te gebruiken. Daarom zijn de `SetClientRequestId` methoden van `SearchServiceClient` en `SearchIndexClient` verwijderd. In plaats daarvan kunt u een aanvraag-ID door geven aan elke bewerkings methode via de optionele para meter `SearchRequestOptions`.

> [!NOTE]
> In een toekomstige release van de SDK voegen we een nieuw mechanisme toe voor het instellen van een aanvraag-ID globaal op de client objecten die consistent zijn met de benadering die wordt gebruikt door andere Azure-Sdk's.
> 
> 

### <a name="example"></a>Voorbeeld
Als u code hebt die er ongeveer als volgt uitziet:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

U kunt dit wijzigen in dit om eventuele build-fouten op te lossen:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Wijzigingen in de interface naam
De interface namen van de bewerkings groep zijn allemaal gewijzigd zodat deze consistent zijn met de bijbehorende eigenschaps namen:

* De naam van het type `ISearchServiceClient.Indexes` is gewijzigd van `IIndexOperations` in `IIndexesOperations`.
* De naam van het type `ISearchServiceClient.Indexers` is gewijzigd van `IIndexerOperations` in `IIndexersOperations`.
* De naam van het type `ISearchServiceClient.DataSources` is gewijzigd van `IDataSourceOperations` in `IDataSourcesOperations`.
* De naam van het type `ISearchIndexClient.Documents` is gewijzigd van `IDocumentOperations` in `IDocumentsOperations`.

Deze wijziging is waarschijnlijk niet van invloed op uw code, tenzij u de modellen van deze interfaces hebt gemaakt voor test doeleinden.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Oplossingen voor fouten in versie 1,1
Er is een fout opgetreden in oudere versies van de Azure Search .NET SDK met betrekking tot serialisatie van aangepaste model klassen. De bug kan optreden als u een aangepaste model klasse hebt gemaakt met een eigenschap van een Waardetype waarvoor null niet is toegestaan.

### <a name="steps-to-reproduce"></a>Stappen om te reproduceren
Maak een aangepaste model klasse met een eigenschap van het waardetype waarvoor geen null-waarde is toegestaan. U kunt bijvoorbeeld een eigenschap Public `UnitCount` van het type `int` toevoegen in plaats van `int?`.

Als u een document indexeert met de standaard waarde van dat type (bijvoorbeeld 0 voor `int`), is het veld in Azure Search null. Als u het document vervolgens zoekt, wordt de oproep van de `Search` gegenereerd `JsonSerializationException` klagen dat `null` niet naar `int`kan worden geconverteerd.

Filters werken mogelijk niet zoals verwacht, omdat null is geschreven naar de index in plaats van de gewenste waarde.

### <a name="fix-details"></a>Details herstellen
Dit probleem is opgelost in versie 1,1 van de SDK. Als u nu een model klasse hebt die er als volgt uitziet:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

en u `IntValue` instelt op 0, wordt die waarde nu op de juiste wijze geserialiseerd als 0 op de kabel en opgeslagen als 0 in de index. Round-circuits werken ook zoals verwacht.

Er is een mogelijk probleem met deze aanpak: als u een model type gebruikt met een niet-nullbare eigenschap, moet u **garanderen** dat geen documenten in uw index een null-waarde voor het bijbehorende veld bevatten. De SDK noch de Azure Search REST API helpen u om dit af te dwingen.

Dit is niet alleen een hypothetische probleem: Stel een scenario voor waarin u een nieuw veld toevoegt aan een bestaande index van het type `Edm.Int32`. Na het bijwerken van de indexdefinitie hebben alle documenten een null-waarde voor het nieuwe veld (omdat alle typen null in Azure Search zijn). Als u vervolgens een modelklasse met een niet-nullbare `int`-eigenschap voor dat veld gebruikt, ontvangt u een `JsonSerializationException` zoals deze bij het ophalen van documenten:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Daarom raden we u aan om nullbare typen in uw model klassen als best practice te gebruiken.

Raadpleeg [Dit probleem op github](https://github.com/Azure/azure-sdk-for-net/issues/1063)voor meer informatie over deze fout en de oplossing.

