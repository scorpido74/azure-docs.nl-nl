---
title: 'C#-zelfstudie: indexering optimaliseren met de push-API'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het efficiënt indexeren van gegevens met behulp van de push-API van Azure Cognitive Search. Deze zelfstudie en de voorbeeldcode maken gebruik van C#.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cb012fcc701e9dd18dbe1db5304807b4d96c2a86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757789"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Zelfstudie: indexering optimaliseren met de push-API

In Azure Cognitive Search worden [twee basismethoden](search-what-is-data-import.md) voor het importeren van gegevens in een zoekindex ondersteund: het *pushen* van uw gegevens naar de index via een programma of het verwijzen van een [Azure Cognitive Search-indexeerfunctie](search-indexer-overview.md) naar een ondersteunde gegevensbron om de gegevens *op te halen*.

In deze zelfstudie wordt beschreven hoe u efficiënt gegevens indexeert met behulp van het [push-model](search-what-is-data-import.md#pushing-data-to-an-index) door aanvragen als batches te verwerken en een strategie te gebruiken waarbij exponentieel uitstel opnieuw wordt uitgevoerd. U kunt [de toepassing downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). In dit artikel worden de belangrijkste aspecten van de toepassing beschreven en tevens de factoren waarmee u rekening moet houden bij het indexeren van gegevens.

In deze zelfstudie wordt gebruikgemaakt van C# en de [.NET SDK](/dotnet/api/overview/azure/search) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een index maken
> * Verschillende batchgrootten testen om de meest efficiënte grootte te bepalen
> * Gegevens asynchroon indexeren
> * Meerdere threads gebruiken om de indexeringssnelheden te verhogen
> * Een herhaalstrategie voor exponentiële uitstel gebruiken om mislukte items opnieuw uit te voeren

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogramma's zijn vereist voor deze zelfstudie.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), een willekeurige editie. Voorbeeldcode en instructies zijn getest met de gratis Community-editie.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement.

<a name="get-service-info"></a>

## <a name="download-files"></a>Bestanden downloaden

De broncode voor deze zelfstudie bevindt zich in de GitHub-opslagplaats [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing), in de map [optimzize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="key-considerations"></a>Belangrijkste overwegingen

Bij het pushen van gegevens naar een index zijn er enkele belangrijke aandachtspunten die van invloed zijn op de indexeringssnelheden. Meer informatie over deze factoren vindt u in het artikel [Grote gegevenssets indexeren](search-howto-large-index.md).

Er zijn zes belangrijke factoren die u moet overwegen:

+ **Servicelaag en aantal partities/replica's**: het toevoegen van partities en het verhogen van uw laag, verhogen de indexeringssnelheden.
+ **Indexschema**: het toevoegen van velden en van aanvullende eigenschappen aan velden (zoals *searchable*, *facetable* of *filterable*) verlagen beide de indexeringssnelheden.
+ **Batchgrootte**: de optimale batchgrootte varieert naar gelang uw indexschema en gegevensset.
+ **Aantal threads/werkrollen**: één thread profiteert niet ten volle van indexeringssnelheden
+ **Herhaalstrategie**: er moet een herhaalstrategie voor exponentieel uitstel worden gebruikt om de indexering te optimaliseren.
+ **Snelheden van overdracht van netwerkgegevens**: snelheden van gegevensoverdracht kunnen een beperkende factor zijn. Indexeer gegevens vanuit uw Azure-omgeving om de snelheid van gegevensoverdracht te verhogen.


## <a name="1---create-azure-cognitive-search-service"></a>1 - Azure Cognitive Search-service maken

Voor het voltooien van deze zelfstudie hebt u een Azure Cognitive Search-service nodig, die u [in de portal kunt maken](search-create-service-portal.md). U kunt het beste dezelfde laag gebruiken die u in productie wilt gebruiken, zodat u de indexeringssnelheden nauwkeurig kunt testen en optimaliseren.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search ophalen

Voor API-aanroepen is de service-URL en een toegangssleutel vereist. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

## <a name="2---set-up-your-environment"></a>2 - De omgeving instellen

1. Start Visual Studio en open **OptimizeDataIndexing.sln**.
1. Open **appsettings.json** in Solution Explorer om verbindingsgegevens op te geven.
1. Voor `searchServiceName` geldt: als de volledige URL https://my-demo-service.search.windows.net is, moet u de servicenaam my-demo-service opgeven.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - De code verkennen

Zodra u *appsettings.json* hebt bijgewerkt, moet het voorbeeldprogramma in **OptimizeDataIndexing.sln** klaar zijn om te bouwen en uit te voeren.

Deze code is afkomstig van de [C#-quickstart](search-get-started-dotnet.md). Meer uitgebreide informatie over de basisbeginselen van het werken met de .NET SDK vindt u in dat artikel.

Deze eenvoudige C#-/.NET-console-app voert de volgende taken uit:

+ Maakt een nieuwe index op basis van de gegevensstructuur van de C#-klasse Hotel (die ook verwijst naar de klasse Address).
+ Test verschillende batchgrootten om de meest efficiënte grootte te bepalen
+ Indexeert gegevens asynchroon
    + Gebruik van meerdere threads om de indexeringssnelheden te verhogen
    + Gebruik van een herhaalstrategie voor exponentieel uitstel om mislukte items opnieuw uit te voeren

 Neem, voordat u het programma gaat uitvoeren, even een minuut de tijd om de code en de indexdefinities voor dit voorbeeld te bestuderen. De relevante code bevindt zich in twee bestanden:

  + **Hotel.cs** en **Address.cs** bevatten het schema dat de index definieert
  + **DataGenerator.cs** bevat een eenvoudige klasse waarmee eenvoudig grote hoeveelheden hotelgegevens kunnen worden gemaakt
  + **ExponentialBackoff.cs** bevat code voor het optimaliseren van het indexeringsproces, zoals hieronder wordt beschreven
  + **Program.cs** bevat functies voor het maken en verwijderen van de Azure Cognitive Search-index, het indexeren van batches met gegevens en het testen van verschillende batchgrootten

### <a name="creating-the-index"></a>De index maken

In dit voorbeeldprogramma wordt de .NET SDK gebruikt om een Azure Cognitive Search-index te definiëren en te maken. Er wordt gebruikgemaakt van de [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder)-klasse om een indexstructuur te genereren op basis van een C#-gegevensmodelklasse.

Het gegevensmodel wordt gedefinieerd door de klasse Hotel, die ook verwijzingen bevat naar de klasse Address. Met FieldBuilder wordt ingezoomd op meerdere klassedefinities om een complexe gegevensstructuur voor de index te genereren. Tags voor metagegevens worden gebruikt voor het definiëren van de kenmerken van elk veld; er kan bijvoorbeeld worden aangegeven of een veld doorzoekbaar of sorteerbaar is.

De volgende fragmenten uit het bestand **Hotel.cs** laten zien hoe een enkel veld en een verwijzing naar een andere gegevensmodelklasse kunnen worden opgegeven.

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

In het bestand **Program.cs** wordt de index gedefinieerd met een naam en een veldverzameling en die wordt gegenereerd door de methode `FieldBuilder.BuildForType<Hotel>()`. De index wordt daarna als volgt gemaakt:

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

Er wordt een eenvoudige klasse geïmplementeerd in het bestand **DataGenerator.cs** om gegevens te genereren voor het testen. Het enige doel van deze klasse is om op eenvoudige wijze een groot aantal documenten te genereren met een unieke id voor indexering.

Als u een lijst met 100.000 hotels met unieke id's wilt verkrijgen, voert u de volgende twee regels code uit:

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

Er zijn hotels in twee formaten beschikbaar die in dit voorbeeld kunnen worden getest: **klein** en **groot**.

Het schema van uw index kan een grote invloed hebben op de indexeringssnelheid. Als gevolg hiervan is het zinvol deze klasse te converteren om gegevens te genereren die overeenkomen met uw beoogde indexschema nadat u deze zelfstudie hebt doorlopen.

## <a name="4---test-batch-sizes"></a>4 - Batchgroottes testen

In Azure Cognitive Search worden de volgende API's ondersteund om één of meerdere documenten in een index te laden:

+ [Documenten toevoegen, bijwerken of verwijderen (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction-klasse](/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) of [indexBatch-klasse](/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

Als u documenten in batches indexeert, worden de prestaties van de indexering aanzienlijk verbeterd. Deze batches kunnen maximaal duizend documenten of maximaal circa 16 MB per batch bevatten.

Het vaststellen van de optimale batchgrootte voor uw gegevens is een belangrijk onderdeel van voor het optimaliseren van de indexeringssnelheid. De twee primaire factoren die van invloed zijn op de optimale batchgrootte zijn:

+ Het schema van uw index
+ De hoeveelheid gegevens

Omdat de optimale batchgrootte afhankelijk is van uw index en uw gegevens, kunt u het best verschillende batchgroottes testen om te bepalen wat de snelste indexeringssnelheden voor uw scenario zijn.

Met de volgende functie wordt een eenvoudige benadering van het testen van batchgroottes gedemonstreerd.

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

Omdat niet alle documenten dezelfde grootte hebben (hoewel ze dit in dit voorbeeld wel zijn), wordt een schatting gemaakt van de grootte van de gegevens die we naar de zoekservice verzenden. Dit wordt gedaan met behulp van de onderstaande functie. Hiermee wordt het object eerst naar JSON geconverteerd en wordt vervolgens de grootte in bytes vastgesteld. Met deze techniek kunnen we bepalen welke batchgroottes het meest efficiënt zijn in termen van MB's/indexeringssnelheden.

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

Voor de functie is een `ISearchIndexClient` vereist, evenals het aantal testpogingen voor elke batchgrootte. Omdat voor elke batch enige variatie in indexeringstijden verschillen kan voorkomen, wordt elke batch standaard drie keer getest om statistisch significantere resultaten te verkrijgen.

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

Wanneer u de functie uitvoert, ziet u in de console een uitvoer zoals hieronder weergegeven:

   ![Uitvoer van de functie voor het testen van de batchgrootte](media/tutorial-optimize-data-indexing/test-batch-sizes.png "Uitvoer van de functie voor het testen van de batchgrootte")

Bepaal welke batchgrootte het efficiëntst is en gebruik deze batchgrootte in de volgende stap van de zelfstudie. Mogelijk ziet u een plateauwaarde (in MB) optreden voor de diverse batchgroottes.

## <a name="5---index-data"></a>5 - Gegevens indexeren

Nu de batchgrootte die we willen gebruiken, is vastgesteld, gaan we in de volgende stap de gegevens indexeren. Om gegevens efficiënt te kunnen indexeren, geldt voor dit voorbeeld dat het:

* Gebruikmaakt van meerdere threads/werkrollen.
* Een herhaalstrategie voor exponentieel uitstel implementeert.

### <a name="use-multiple-threadsworkers"></a>Meerdere threads/werkrollen gebruiken

Als u optimaal gebruik wilt maken van de indexeringssnelheden van Azure Cognitive Search, moet u waarschijnlijk meerdere threads gebruiken voor het gelijktijdig verzenden van aanvragen voor batchindexering naar de service.  

Enkele belangrijke overwegingen die hierboven worden beschreven, zijn van invloed op het optimale aantal threads. U kunt dit voorbeeld wijzigen en testen met verschillende aantallen threads om het optimale aantal voor uw scenario te bepalen. Als u echter meerdere threads gelijktijdig uitvoert, profiteert u van de meeste verbeteringen in de efficiëntie.

Wanneer u de aanvragen van de zoekservice verhoogt, kunnen er [HTTP-statuscodes](/rest/api/searchservice/http-status-codes) optreden die aangeven dat de aanvraag niet volledig is voltooid. Twee veelvoorkomende HTTP-statuscodes die zich tijdens het indexeren kunnen voordoen, zijn:

+ **503: service niet beschikbaar**: deze fout betekent dat het systeem zwaar belast wordt en uw aanvraag op dit moment niet kan worden verwerkt.
+ **207: meerdere statussen**: deze fout betekent dat sommige documenten zijn geslaagd, maar dat er ten minste één is mislukt.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Een herhaalstrategie voor exponentieel uitstel implementeren

Als er een fout optreedt, moeten aanvragen opnieuw worden ingediend met een [herhaalstrategie voor exponentieel uitstel](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff).

Met de .NET SDK van Azure Cognitive Search worden aanvragen bij 503-fouten en andere mislukte aanvragen opnieuw uitgevoerd, maar u moet uw eigen logica implementeren om bij 207-fouten aanvragen opnieuw uit te voeren. Opensource-hulpprogramma's als [Polly](https://github.com/App-vNext/Polly) kunnen ook worden gebruikt voor het implementeren van een herhaalstrategie. 

In dit voor beeld implementeren we onze eigen herhaalstrategie voor exponentieel uitstel. Voor het implementeren van deze strategie worden eerst enkele variabelen gedefinieerd, waaronder de `maxRetryAttempts` en de initiële `delay` bij een mislukte aanvraag:

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Het is belangrijk om [IndexBatchException](/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet) te ondervangen, omdat deze uitzonderingen aangeven dat de indexeringsbewerking slechts gedeeltelijk is geslaagd (207-fouten). Mislukte items moeten opnieuw worden geprobeerd met de `FindFailedActionsToRetry`-methode, waarna gemakkelijk een nieuwe batch met alleen de mislukte items kan worden gemaakt.

Andere uitzonderingen dan `IndexBatchException` moeten ook worden afgevangen en geven aan dat de aanvraag volledig is mislukt. Deze uitzonderingen zijn minder gangbaar, met name met de .NET SDK, omdat bij 503-fouten automatisch een nieuwe poging wordt uitgevoerd.

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

Hierna verpakken we de code voor exponentieel uitstel in een functie zodat deze eenvoudig kan worden aangeroepen.

Vervolgens wordt er een andere functie gemaakt om de actieve threads te beheren. Voor het gemak is deze functie hier niet opgenomen, maar u kunt deze vinden in [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v10/OptimizeDataIndexing/ExponentialBackoff.cs). De functie kan worden aangeroepen met de volgende opdracht, waarbij `hotels` de gegevens zijn die we willen uploaden, `1000` de batchgrootte is en `8` het aantal gelijktijdige threads:

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

Wanneer u de functie uitvoert, ziet u een uitvoer zoals hieronder weergegeven:

![Uitvoer van de functie voor het indexeren van gegevens](media/tutorial-optimize-data-indexing/index-data-start.png "Uitvoer van de functie voor het indexeren van gegevens")

Wanneer een batch met documenten mislukt, wordt een fout afgedrukt die de storing aangeeft en tevens de melding dat de batch opnieuw wordt uitgevoerd:

![Fout van de functie voor het indexeren van gegevens](media/tutorial-optimize-data-indexing/index-data-error.png "Uitvoer van de functie voor het testen van de batchgrootte")

Nadat de functie is uitgevoerd, kunt u controleren of alle documenten aan de index zijn toegevoegd.

## <a name="6---explore-index"></a>6 - Index onderzoeken

U kunt de gevulde zoekindex onderzoeken nadat het programma programmatisch is uitgevoerd of gebruikmaken van [**Search Explorer**](search-explorer.md) in de portal.

### <a name="programatically"></a>Programmatisch

Er zijn twee hoofdopties waarmee het aantal documenten in een index kan worden gecontroleerd: de [API voor het tellen van documenten](/rest/api/searchservice/count-documents) en de [API voor het ophalen van de indexstatistieken](/rest/api/searchservice/get-index-statistics). Voor het bijwerken van beide paden is mogelijk enige extra tijd nodig, dus maak u niet ongerust als het aantal geretourneerde documenten lager is dan u aanvankelijk had verwacht.

#### <a name="count-documents"></a>Documenten tellen

Met de bewerking Documenten tellen wordt een telling van het aantal documenten in een zoekindex opgehaald:

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>Indexstatistieken ophalen

Met de bewerking Indexstatistieken ophalen wordt een telling van de documenten voor de huidige index geretourneerd plus de gebruikte hoeveelheid opslag. Het bijwerken van de indexstatistieken duurt langer dan dat van de telling van de documenten.

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Azure Portal

Open in de Azure-portal de pagina **Overzicht** van de zoekservice en zoek in de lijst **Indexen** de index **optimize-indexing**.

  ![Lijst met Azure Cognitive Search-indexen](media/tutorial-optimize-data-indexing/portal-output.png "Lijst met Azure Cognitive Search-indexen")

*Aantal documenten* en *Opslaggrootte* zijn gebaseerd op [API voor het ophalen van indexstatistieken](/rest/api/searchservice/get-index-statistics). Het bijwerken van deze opties kan enige tijd in beslag nemen.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeldcode voor deze zelfstudie wordt gecontroleerd op bestaande indexen en worden deze verwijderd zodat u de code opnieuw kunt uitvoeren.

U kunt de portal ook gebruiken om indexen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

U bent nu bekend met het concept van het efficiënt opnemen van gegevens. Laten we de Lucene-queryarchitectuur eens nader bekijken en zien hoe het zoeken in volledige tekst in zijn werk gaat in Azure Cognitive Search.

> [!div class="nextstepaction"]
> [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)