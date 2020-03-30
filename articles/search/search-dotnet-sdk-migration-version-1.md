---
title: Upgraden naar Azure Search .NET SDK-versie 1.1
titleSuffix: Azure Cognitive Search
description: Migreer code naar de Azure Search .NET SDK versie 1.1 van oudere API-versies. Meer informatie over wat er nieuw is en welke codewijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792383"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Upgraden naar Azure Search .NET SDK-versie 1.1

Als u versie 1.0.2-preview of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u uw toepassing te upgraden naar versie 1.1.

Zie [Azure Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor een meer algemene walkthrough van de SDK met voorbeelden.

> [!NOTE]
> Zodra u een upgrade uitvoert naar versie 1.1 of als u al een versie gebruikt tussen 1.1 en 2.0-preview, moet u upgraden naar versie 3. Zie [Upgraden naar de Azure Search .NET SDK versie 3](search-dotnet-sdk-migration.md) voor instructies.
>

Werk eerst uw NuGet-referentie bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager Console of door met de rechtermuisknop op uw projectreferenties te klikken en "NuGet-pakketten beheren..." te selecteren. in Visual Studio.

Zodra NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u uw project opnieuw op.

Als u eerder versie 1.0.0-preview, 1.0.1-preview of 1.0.2-preview gebruikte, moet de build slagen en bent u klaar om te gaan!

Als u eerder versie 0.13.0-preview of ouder gebruikte, ziet u buildfouten zoals:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

De volgende stap is het oplossen van de build fouten een voor een. De meeste vereisen het wijzigen van een aantal klasse en methode namen die zijn anders genoemd in de SDK. [Lijst met wijzigingen in versie 1.1](#ListOfChangesV1) bevat een lijst met deze naamswijzigingen.

Als u aangepaste klassen gebruikt om uw documenten te modelleren en deze klassen eigenschappen hebben `int` van `bool` niet-tenietbare primitieve typen (bijvoorbeeld of in C#), is er een bugoplossing in de 1.1-versie van de SDK waarvan u op de hoogte moet zijn. Zie [Bugfixes in versie 1.1](#BugFixesV1) voor meer details.

Tot slot, zodra u alle buildfouten hebt opgelost, u wijzigingen aanbrengen in uw toepassing om te profiteren van nieuwe functionaliteit als u dat wenst.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lijst met wijzigingen in versie 1.1
De volgende lijst wordt geordend op de waarschijnlijkheid dat de wijziging van invloed is op uw toepassingscode.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch- en IndexAction-wijzigingen
`IndexBatch.Create`is omgedoopt tot `IndexBatch.New` en heeft `params` niet langer een argument. U kunt `IndexBatch.New` batches gebruiken die verschillende soorten acties combineren (samenvoegingen, deletes, enz.). Daarnaast zijn er nieuwe statische methoden voor het maken van batches `MergeOrUpload`waarbij `Upload`alle acties hetzelfde zijn: `Delete`, , `Merge`, en .

`IndexAction`heeft niet langer openbare constructeurs en de eigenschappen ervan zijn nu onveranderlijk. U moet de nieuwe statische methoden gebruiken voor `Delete` `Merge`het `MergeOrUpload`maken `Upload`van acties voor verschillende doeleinden: , , en . `IndexAction.Create`Verwijderd. Als u de overbelasting hebt gebruikt die alleen `Upload` een document inneemt, moet u dat in plaats daarvan gebruiken.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

U het hierin wijzigen om eventuele buildfouten op te lossen:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Als u wilt, u het verder vereenvoudigen tot dit:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException wijzigingen
De `IndexBatchException.IndexResponse` eigenschap is hernoemd naar `IndexingResults`, `IList<IndexingResult>`en het type is nu .

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

U het hierin wijzigen om eventuele buildfouten op te lossen:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Wijziging van de bewerkingsmethode
Elke bewerking in de Azure Search .NET SDK wordt weergegeven als een set van methode overbelasting voor synchrone en asynchrone bellers. De handtekeningen en factoring van deze methode overbelasting is veranderd in versie 1.1.

De bewerking 'Indexstatistieken downloaden' in oudere versies van de SDK heeft bijvoorbeeld de volgende handtekeningen weergegeven:

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

De methodehandtekeningen voor dezelfde bewerking in versie 1.1 zien er als volgt uit:

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

Vanaf versie 1.1 organiseert de Azure Search .NET SDK bewerkingsmethoden anders:

* Optionele parameters worden nu gemodelleerd als standaardparameters in plaats van extra overbelasting van de methode. Dit vermindert het aantal overbelasting van de methode, soms dramatisch.
* De extensie methoden verbergen nu veel van de vreemde details van HTTP van de beller. Oudere versies van de SDK hebben bijvoorbeeld een antwoordobject met een HTTP-statuscode geretourneerd, die `CloudException` u vaak niet hoefde te controleren omdat de bewerkingsmethoden worden gegenereerd voor een statuscode die op een fout duidt. De nieuwe extensiemethoden retourneren gewoon modelobjecten, waardoor u de moeite bespaart om ze in uw code uit te pakken.
* Omgekeerd stellen de kerninterfaces nu methoden bloot die u meer controle geven op HTTP-niveau als u het nodig hebt. U nu aangepaste HTTP-headers doorgeven om in `AzureOperationResponse<T>` aanvragen te worden opgenomen `HttpRequestMessage` `HttpResponseMessage` en het nieuwe retourtype geeft u directe toegang tot de en voor de bewerking. `AzureOperationResponse`wordt gedefinieerd in `Microsoft.Rest.Azure` de naamruimte en vervangt `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>Wijzigingen scoreparameters
Een nieuwe `ScoringParameter` klasse met de naam is toegevoegd in de nieuwste SDK om het gemakkelijker te maken om parameters te verstrekken aan scoreprofielen in een zoekopdracht. Voorheen `ScoringProfiles` werd de `SearchParameters` eigenschap van `IList<string>`de klasse getypt als ; Nu is het `IList<ScoringParameter>`getypt als .

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

U het hierin wijzigen om eventuele buildfouten op te lossen: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Wijzigingen in modelklasse
Vanwege de handtekeningwijzigingen die zijn beschreven in wijzigingen `Microsoft.Azure.Search.Models` in [de bewerkingsmethode,](#OperationMethodChanges)zijn veel klassen in de naamruimte hernoemd of verwijderd. Bijvoorbeeld:

* `IndexDefinitionResponse`is vervangen door`AzureOperationResponse<Index>`
* De naam van `DocumentSearchResponse` is gewijzigd in `DocumentSearchResult`
* De naam van `IndexResult` is gewijzigd in `IndexingResult`
* `Documents.Count()`retourneert `long` nu een met het aantal documenten in plaats van een`DocumentCountResponse`
* De naam van `IndexGetStatisticsResponse` is gewijzigd in `IndexGetStatisticsResult`
* De naam van `IndexListResponse` is gewijzigd in `IndexListResult`

Om samen `OperationResponse`te vatten, -afgeleide klassen die alleen bestonden om een modelobject te verpakken, zijn verwijderd. De overige klassen hebben hun achtervoegsel veranderd van `Response` . `Result`

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

U het hierin wijzigen om eventuele buildfouten op te lossen:

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

#### <a name="response-classes-and-ienumerable"></a>Responsklassen en IEnumerable
Een aanvullende wijziging die van invloed kan zijn op uw `IEnumerable<T>`code, is dat antwoordklassen die verzamelingen bevatten, niet langer implementeren. In plaats daarvan hebt u rechtstreeks toegang tot de collectieaccommodatie. Als uw code er bijvoorbeeld als volgt uitziet:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

U het hierin wijzigen om eventuele buildfouten op te lossen:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Speciaal geval voor webapplicaties
Als u een webtoepassing hebt `DocumentSearchResponse` die rechtstreeks serialiseert om zoekresultaten naar de browser te verzenden, moet u uw code wijzigen of worden de resultaten niet correct geserialt. Als uw code er bijvoorbeeld als volgt uitziet:

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

U deze wijzigen `.Results` door de eigenschap van het zoekantwoord te verkrijgen om de weergave van zoekresultaten op te lossen:

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

U zult moeten zoeken naar dergelijke gevallen in uw code zelf; **De compiler waarschuwt** u `JsonResult.Data` niet `object`omdat het van type is.

### <a name="cloudexception-changes"></a>CloudException verandert
De `CloudException` klasse is `Hyak.Common` verplaatst van `Microsoft.Rest.Azure` de naamruimte naar de naamruimte. Ook is `Error` het pand omgedoopt `Body`tot .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Wijzigingen searchServiceClient en SearchIndexClient
Het type `Credentials` eigenschap is `SearchCredentials` veranderd van de `ServiceClientCredentials`basisklasse. Als u toegang `SearchCredentials` wilt `SearchIndexClient` tot `SearchServiceClient`de van `SearchCredentials` een of, u gebruik maken van de nieuwe eigenschap.

In oudere versies van `SearchServiceClient` `SearchIndexClient` de SDK, en `HttpClient` had constructors die een parameter nam. Deze zijn vervangen door constructeurs `HttpClientHandler` die een `DelegatingHandler` en een scala aan objecten te nemen. Dit maakt het eenvoudiger om aangepaste handlers te installeren om HTTP-aanvragen vooraf te verwerken als dat nodig is.

Tot slot, de constructeurs die nam een `Uri` en `SearchCredentials` zijn veranderd. Als u bijvoorbeeld een code hebt die er als volgt uitziet:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

U het hierin wijzigen om eventuele buildfouten op te lossen:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Houd er ook rekening mee dat het `ServiceClientCredentials`type parameter referenties is gewijzigd in . Dit is onwaarschijnlijk dat `SearchCredentials` uw code `ServiceClientCredentials`van invloed is, omdat is afgeleid van .

### <a name="passing-a-request-id"></a>Een aanvraag-ID doorgeven
In oudere versies van de SDK u `SearchServiceClient` `SearchIndexClient` een aanvraag-id instellen op de of en deze wordt in elk verzoek aan de REST-API opgenomen. Dit is handig voor het oplossen van problemen met uw zoekservice als u contact moet opnemen met ondersteuning. Het is echter handiger om voor elke bewerking een unieke aanvraag-id in te stellen in plaats van dezelfde ID te gebruiken voor alle bewerkingen. Om deze reden, `SetClientRequestId` de `SearchServiceClient` `SearchIndexClient` methoden van en zijn verwijderd. In plaats daarvan u een aanvraag-ID `SearchRequestOptions` doorgeven aan elke bewerkingsmethode via de optionele parameter.

> [!NOTE]
> In een toekomstige release van de SDK voegen we een nieuw mechanisme toe voor het wereldwijd instellen van een aanvraag-id op de clientobjecten die overeenkomt met de aanpak die door andere Azure SDK's wordt gebruikt.
> 
> 

### <a name="example"></a>Voorbeeld
Als u code hebt die er als volgt uitziet:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

U het hierin wijzigen om eventuele buildfouten op te lossen:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Wijziging van de naam van de interface
De namen van de bewerkingsgroepinterface zijn allemaal gewijzigd om consistent te zijn met de bijbehorende eigenschapsnamen:

* Het type `ISearchServiceClient.Indexes` is omgedoopt `IIndexOperations` van `IIndexesOperations`.
* Het type `ISearchServiceClient.Indexers` is omgedoopt `IIndexerOperations` van `IIndexersOperations`.
* Het type `ISearchServiceClient.DataSources` is omgedoopt `IDataSourceOperations` van `IDataSourcesOperations`.
* Het type `ISearchIndexClient.Documents` is omgedoopt `IDocumentOperations` van `IDocumentsOperations`.

Deze wijziging heeft waarschijnlijk geen invloed op uw code, tenzij u mocks van deze interfaces hebt gemaakt voor testdoeleinden.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Bugfixes in versie 1.1
Er was een bug in oudere versies van de Azure Search .NET SDK met betrekking tot serialisatie van aangepaste modelklassen. De bug kan optreden als u een aangepaste modelklasse hebt gemaakt met een eigenschap van een niet-tenietbaar waardetype.

### <a name="steps-to-reproduce"></a>Stappen om te reproduceren
Maak een aangepaste modelklasse met een eigenschap van niet-tederewaarde. Voeg bijvoorbeeld een `UnitCount` openbare eigenschap `int` van `int?`het type toe in plaats van .

Als u een document indexeert met de standaardwaarde `int`van dat type (bijvoorbeeld 0 voor), wordt het veld null in Azure Search. Als u vervolgens zoeken naar `Search` dat `JsonSerializationException` document, de oproep zal `null` `int`gooien klagen dat het niet kan converteren naar .

Ook kunnen filters niet werken zoals verwacht, omdat null is geschreven naar de index in plaats van de beoogde waarde.

### <a name="fix-details"></a>Details oplossen
We hebben dit probleem opgelost in versie 1.1 van de SDK. Nu, als je een model klasse als deze:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

en u `IntValue` ingesteld op 0, die waarde is nu correct geserialiseerd als 0 op de draad en opgeslagen als 0 in de index. Ronde struikelen werkt ook zoals verwacht.

Er is een mogelijk probleem om op de hoogte te zijn van deze benadering: Als u een modeltype gebruikt met een niet-niet-tedoenbare eigenschap, moet u **garanderen** dat geen documenten in uw index een null-waarde voor het overeenkomstige veld bevatten. Noch de SDK, noch de Azure Search REST API zullen u helpen dit af te dwingen.

Dit is niet alleen een hypothetische probleem: Stel een scenario voor waarin u een nieuw veld toevoegt aan een bestaande index van het type `Edm.Int32`. Na het bijwerken van de indexdefinitie hebben alle documenten een null-waarde voor het nieuwe veld (omdat alle typen null in Azure Search zijn). Als u vervolgens een modelklasse met een niet-nullbare `int`-eigenschap voor dat veld gebruikt, ontvangt u een `JsonSerializationException` zoals deze bij het ophalen van documenten:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Daarom raden we u nog steeds aan nullable typen in uw modelklassen te gebruiken als aanbevolen procedures.

Voor meer informatie over deze bug en de oplossing, zie [dit probleem op GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

