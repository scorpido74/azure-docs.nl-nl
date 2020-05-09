---
title: C#-zelf studie indexering optimaliseren met de Push-API
titleSuffix: Azure Cognitive Search
description: Meer informatie over het efficiënt indexeren van gegevens met behulp van de Push-API van Azure Cognitive Search. Deze zelf studie en voorbeeld code zijn in C#.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 85ac56eb20eabf308d6686a047d8c5ede914fed9
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966440"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Zelf studie: indexeren optimaliseren met de Push-API

Azure Cognitive Search ondersteunt [twee basis benaderingen](search-what-is-data-import.md) voor het importeren van gegevens in een zoek index: het *pushen* van uw gegevens in de index via een programma of het aanwijzen van een [Azure Cognitive Search indexer](search-indexer-overview.md) bij een ondersteunde gegevens bron om de gegevens op te *halen* .

In deze zelf studie wordt beschreven hoe u efficiënt gegevens kunt indexeren met behulp van het [push model](search-what-is-data-import.md#pushing-data-to-an-index) door aanvragen te verwerken en een exponentiële uitstel-strategie voor opnieuw proberen te gebruiken. U kunt [de toepassing downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). In dit artikel worden de belangrijkste aspecten van de toepassing en factoren beschreven waarmee u rekening moet houden bij het indexeren van gegevens.

In deze zelf studie wordt C# en de [.NET SDK](https://aka.ms/search-sdk) gebruikt om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een index maken
> * Verschillende batch grootten testen om de meest efficiënte grootte te bepalen
> * Gegevens asynchroon indexeren
> * Meerdere threads gebruiken om de indexerings snelheden te verhogen
> * Een exponentiële uitstel-strategie voor opnieuw proberen gebruiken om mislukte items opnieuw op te doen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogram ma's zijn vereist voor deze zelf studie.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), een wille keurige versie. Voor beelden van code en instructies zijn getest in de gratis Community-editie.

+ [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement.

<a name="get-service-info"></a>

## <a name="download-files"></a>Bestanden downloaden

De bron code voor deze zelf studie bevindt zich in de map [optimzize-data-indexering](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) in de GitHub-opslag plaats [Azure-samples/Azure-Search-DotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="key-considerations"></a>Belangrijkste overwegingen

Bij het pushen van gegevens naar een index, zijn er verschillende belang rijke aandachtspunten die van invloed zijn op de indexerings snelheid. Meer informatie over deze factoren vindt u in het [artikel index grote gegevens sets](search-howto-large-index.md).

Er zijn zes belang rijke factoren die u moet overwegen:

+ Servicelaag **en aantal partities/replica's** : het toevoegen van partities en het verg Roten van uw niveau, verhogen de index snelheid.
+ **Index schema** : velden toevoegen en extra eigenschappen toevoegen aan velden (zoals *Doorzoek*bare, *facet*bare of *filterbaar*) beide de indexerings snelheden verlagen.
+ **Batch grootte** : de optimale Batch grootte varieert op basis van uw index schema en gegevensset.
+ **Aantal threads/werk nemers** : een enkele thread biedt geen volledige voor delen van indexerings snelheden
+ **Strategie voor opnieuw proberen** : een exponentiële uitstel-strategie voor opnieuw proberen moet worden gebruikt om de indexering te optimaliseren.
+ **Netwerk gegevens overdrachts snelheden** : gegevens overdrachts snelheden kunnen een beperkende factor zijn. Indexeer gegevens uit uw Azure-omgeving om de snelheid van gegevens overdracht te verhogen.


## <a name="1---create-azure-cognitive-search-service"></a>1-Azure Cognitive Search-service maken

Voor het volt ooien van deze zelf studie hebt u een Azure Cognitive Search-service nodig die u [in de portal kunt maken](search-create-service-portal.md). U kunt het beste dezelfde laag gebruiken die u in productie wilt gebruiken, zodat u de indexerings snelheden nauw keurig en kunnen testen.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en-URL voor Azure Cognitive Search ophalen

Voor API-aanroepen zijn de service-URL en een toegangs sleutel vereist. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

## <a name="2---set-up-your-environment"></a>2-uw omgeving instellen

1. Start Visual Studio en open **OptimizeDataIndexing. SLN**.
1. Open **appSettings. json** in Solution Explorer om verbindings gegevens op te geven.
1. Voor `searchServiceName`, als de volledige URL "https://my-demo-service.search.windows.net" is, is de naam van de service die u wilt bieden "My-demo-service".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3-de code verkennen

Zodra u *appSettings. json*hebt bijgewerkt, moet het voorbeeld programma in **OptimizeDataIndexing. SLN** klaar zijn om te worden gemaakt en uitgevoerd.

Deze code is afgeleid van de [C#-Snelstartgids](search-get-started-dotnet.md). Meer gedetailleerde informatie over de basis beginselen van het werken met de .NET SDK vindt u in dat artikel.

Deze eenvoudige C#/.net-ontwikkeling.-console-app voert de volgende taken uit:

+ Hiermee maakt u een nieuwe index op basis van de gegevens structuur van de C#-klasse (die ook verwijst naar de klasse Address).
+ Test verschillende batch grootten om de meest efficiënte grootte te bepalen
+ Gegevens asynchroon indexeren
    + Meerdere threads gebruiken om de indexerings snelheden te verhogen
    + Een exponentiële uitstel-strategie voor opnieuw proberen te gebruiken voor het opnieuw proberen van mislukte items

 Voordat u het programma uitvoert, moet u een paar minuten duren om de code en de index definities voor dit voor beeld te bestuderen. De relevante code bevindt zich in verschillende bestanden:

  + **Hotel.cs** en **Address.cs** bevatten het schema dat de index definieert
  + **DataGenerator.cs** bevat een eenvoudige klasse waarmee u eenvoudig grote hoeveel heden Hotel gegevens kunt maken
  + **ExponentialBackoff.cs** bevat code voor het optimaliseren van het indexerings proces zoals hieronder wordt beschreven
  + **Program.cs** bevat functies voor het maken en verwijderen van de index van Azure Cognitive Search, het indexeren van batches met gegevens en het testen van verschillende batch grootten

### <a name="creating-the-index"></a>De index maken

In dit voorbeeld programma wordt de .NET SDK gebruikt om een Azure Cognitive Search-index te definiëren en te maken. Het maakt gebruik van de [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) -klasse om een index structuur te genereren op basis van een C#-gegevens model klasse.

Het gegevens model wordt gedefinieerd door de klasse hotel, dat ook verwijzingen bevat naar de klasse address. De FieldBuilder zoomt uit op meerdere klassen definities om een complexe gegevens structuur voor de index te genereren. Tags voor meta gegevens worden gebruikt voor het definiëren van de kenmerken van elk veld, bijvoorbeeld of het zoekbaar of sorteerbaar is.

In de volgende fragmenten uit het **Hotel.cs** -bestand ziet u hoe een enkel veld en een verwijzing naar een andere gegevens model klasse kunnen worden opgegeven.

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

In het **Program.cs** -bestand wordt de index gedefinieerd met een naam en een veld verzameling die wordt gegenereerd `FieldBuilder.BuildForType<Hotel>()` door de methode en die vervolgens als volgt worden gemaakt:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>Gegevens genereren

Er wordt een eenvoudige klasse geïmplementeerd in het **DataGenerator.cs** -bestand om gegevens te genereren voor het testen. Het enige doel van deze klasse is om het eenvoudig te maken om een groot aantal documenten te genereren met een unieke ID voor indexering.

Als u een lijst van 100.000 hotels met unieke Id's wilt ophalen, voert u de volgende twee regels code uit:

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

Er zijn twee soorten hotels die in dit voor beeld kunnen worden getest: **klein** en **groot**.

Het schema van uw index kan een grote invloed hebben op de index snelheid. Als gevolg hiervan is het zinvol deze klasse te converteren om gegevens te genereren die overeenkomen met uw beoogde index schema nadat u deze zelf studie hebt door lopen.

## <a name="4---test-batch-sizes"></a>4-batch-grootten testen

Azure Cognitive Search ondersteunt de volgende Api's om één of meerdere documenten in een index te laden:

+ [Documenten toevoegen, bijwerken of verwijderen (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) of [indexBatch-klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

Als u documenten in batches indexeert, worden de index prestaties aanzienlijk verbeterd. Deze batches kunnen Maxi maal 1000 documenten of Maxi maal ongeveer 16 MB per batch bevatten.

Het bepalen van de optimale Batch grootte voor uw gegevens is een belang rijk onderdeel van het optimaliseren van de index snelheid. De twee primaire factoren die van invloed zijn op de optimale Batch grootte zijn:

+ Het schema van uw index
+ De grootte van uw gegevens

Omdat de optimale Batch grootte afhankelijk is van uw index en uw gegevens, is het de beste manier om verschillende batch grootten te testen om te bepalen wat resulteert in de snelste indexerings snelheden voor uw scenario.

Met de volgende functie wordt een eenvoudige benadering van het testen van batch grootten gedemonstreerd.

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocuments(indexClient, hotels);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }
}
```

Omdat niet alle documenten dezelfde grootte hebben (hoewel ze zich in dit voor beeld bevinden), schatten we de grootte van de gegevens die we verzenden naar de zoek service. We doen dit met behulp van de onderstaande functie waarmee het object voor het eerst naar JSON wordt geconverteerd en vervolgens de grootte in bytes. Met deze techniek kunnen we bepalen welke batch grootten het meest efficiënt zijn in het aantal MB/s-indexerings snelheden.

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

Voor de functie is `ISearchIndexClient` een vereist en het aantal pogingen dat u wilt testen voor elke batch grootte. Omdat er voor elke batch een aantal verschillen kan optreden, wordt elke batch drie keer geprobeerd om de resultaten statistisch te maken.

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

Wanneer u de functie uitvoert, ziet u een uitvoer zoals hieronder in de console:

   ![Uitvoer van de functie test Batch grootte](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Uitvoer van de functie test Batch grootte")

Identificeer welke Batch grootte het efficiëntst is en gebruik deze batch grootte in de volgende stap van de zelf studie. Mogelijk ziet u een in MB/s voor verschillende batch grootten.

## <a name="5---index-data"></a>5-index gegevens

Nu we de Batch grootte hebben geïdentificeerd die we willen gebruiken, is de volgende stap het indexeren van de gegevens. Als u gegevens efficiënt wilt indexeren, doet u het volgende voor beeld:

* Maakt gebruik van meerdere threads/werk rollen.
* Implementeert een exponentiële uitstel-strategie voor opnieuw proberen.

### <a name="use-multiple-threadsworkers"></a>Meerdere threads/werk nemers gebruiken

Als u optimaal gebruik wilt maken van de indexerings snelheden van Azure Cognitive Search, moet u waarschijnlijk meerdere threads gebruiken voor het gelijktijdig verzenden van batch-indexerings aanvragen naar de service.  

Enkele van de belangrijkste overwegingen die hierboven worden beschreven, hebben invloed op het optimale aantal threads. U kunt dit voor beeld wijzigen en testen met verschillende thread aantallen om het optimale thread aantal voor uw scenario te bepalen. Als er echter meerdere threads gelijktijdig worden uitgevoerd, kunt u profiteren van de meeste efficiëntie-winsten van.

Wanneer u de aanvragen van de zoek service aanpast, kunnen er [HTTP-status codes](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) worden weer gegeven die aangeven dat de aanvraag niet volledig is voltooid. Tijdens het indexeren zijn twee veelvoorkomende HTTP-status codes:

+ **503-Service niet beschikbaar** : deze fout betekent dat het systeem zwaar wordt belast en dat de aanvraag op dit moment niet kan worden verwerkt.
+ **207 multi-status** : deze fout betekent dat sommige documenten zijn geslaagd, maar dat er ten minste één is mislukt.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Een exponentiële uitstel-strategie voor opnieuw proberen implementeren

Als er een fout optreedt, moeten aanvragen opnieuw worden geprobeerd met een [exponentiële uitstel-strategie voor opnieuw proberen](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Met de .NET SDK van Azure Cognitive Search worden automatisch 503s en andere mislukte aanvragen opnieuw geprobeerd, maar u moet uw eigen logica implementeren om 207s opnieuw uit te voeren. Open source-hulpprogram ma's, zoals [Polly](https://github.com/App-vNext/Polly) , kunnen ook worden gebruikt voor het implementeren van een strategie voor opnieuw proberen. 

In dit voor beeld implementeren we onze eigen exponentiële uitstel-strategie voor opnieuw proberen. Voor het implementeren van deze strategie beginnen we met het definiëren van enkele `maxRetryAttempts` variabelen, waaronder `delay` de en de eerste voor een mislukte aanvraag:

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Het is belang rijk om [IndexBatchException](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet) op te vangen, omdat deze uitzonde ringen aangeven dat de indexerings bewerking slechts gedeeltelijk is geslaagd (207s). Mislukte items moeten opnieuw worden geprobeerd met de `FindFailedActionsToRetry` methode waarmee u eenvoudig een nieuwe batch met alleen de mislukte items kunt maken.

Andere uitzonde `IndexBatchException` ringen dan moeten ook worden onderschept en aangeven dat de aanvraag volledig is mislukt. Deze uitzonde ringen zijn minder gangbaar, met name met de .NET SDK, omdat deze automatisch nieuwe pogingen 503s.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Hier verpakken we de exponentiële uitstel-code naar een functie zodat deze eenvoudig kan worden aangeroepen.

Vervolgens wordt er een andere functie gemaakt om de actieve threads te beheren. Deze functie is niet hier opgenomen, maar kan ook worden gevonden in [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs). De functie kan worden aangeroepen met de volgende opdracht, `hotels` waarbij de gegevens die u wilt uploaden, `1000` de Batch grootte is en `8` het aantal gelijktijdige threads is:

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

Wanneer u de functie uitvoert, ziet u een uitvoer zoals hieronder:

![Uitvoer van de functie index gegevens](media/tutorial-optimize-data-indexing/index-data-start.png "Uitvoer van de functie index gegevens")

Wanneer een batch met documenten mislukt, wordt een fout afgedrukt die de fout aangeeft en wordt de batch opnieuw geprobeerd:

![Fout van de functie index gegevens](media/tutorial-optimize-data-indexing/index-data-error.png "Uitvoer van de functie test Batch grootte")

Nadat de functie is voltooid, kunt u controleren of alle documenten aan de index zijn toegevoegd.

## <a name="6---explore-index"></a>6-Verken index

U kunt de gevulde zoek index verkennen nadat het programma programmatisch is uitgevoerd of met behulp van de [**Zoek Verkenner**](search-explorer.md) in de portal.

### <a name="programatically"></a>Programmatisch

Er zijn twee hoofd opties voor het controleren van het aantal documenten in een index: de API [aantal documenten](https://docs.microsoft.com/rest/api/searchservice/count-documents) en de [API index statistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics). Beide paden hebben mogelijk enige extra tijd nodig om bij te werken, dus er wordt geen waarschuwing weer gegeven als het aantal geretourneerde documenten lager is dan u aanvankelijk had verwacht.

#### <a name="count-documents"></a>Documenten tellen

Met de bewerking aantal documenten wordt het aantal documenten in een zoek index opgehaald:

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>Index statistieken ophalen

De bewerking index statistieken ophalen retourneert een aantal documenten voor de huidige index, plus het gebruik van de opslag. De index statistieken duren langer dan het aantal documenten dat moet worden bijgewerkt.

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Azure Portal

Open in Azure Portal de pagina **overzicht** van de zoek service en zoek de index **Optimize-Indexing** in de lijst **indexen** .

  ![Lijst met Azure Cognitive Search-indexen](media/tutorial-optimize-data-indexing/portal-output.png "Lijst met Azure Cognitive Search-indexen")

Het *aantal documenten* en de *opslag grootte* zijn gebaseerd op de [API voor index statistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) en het kan enkele minuten duren om de update uit te voeren.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele stadia van de ontwikkeling kunt u het beste de objecten uit Azure Cognitive Search verwijderen en uw code zo instellen dat deze opnieuw worden opgebouwd. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeld code voor deze zelf studie wordt gecontroleerd op bestaande indexen en worden deze verwijderd zodat u de code opnieuw kunt uitvoeren.

U kunt ook de portal gebruiken om indexen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met het concept van het efficiënt opnemen van gegevens, gaan we eens kijken naar de architectuur van de Lucene-query en hoe zoeken in volledige tekst werkt in azure Cognitive Search.

> [!div class="nextstepaction"]
> [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
