---
title: Azure-Cognitive Search in .NET gebruiken
titleSuffix: Azure Cognitive Search
description: Meer informatie over het gebruik van Azure Cognitive Search in een .NET C# -toepassing met behulp van en de .NET SDK. Op code gebaseerde taken zijn het maken van een verbinding met de service, het indexeren van inhoud en het opvragen van query's in een index.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190972"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Azure Cognitive Search gebruiken vanuit een .NET-toepassing

Dit artikel bevat een overzicht om u te helpen met de [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). U kunt de .NET SDK gebruiken om een uitgebreide zoek ervaring in uw toepassing te implementeren met behulp van Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Wat is de Azure Cognitive Search SDK?
De SDK bestaat uit een aantal client bibliotheken waarmee u uw indexen, gegevens bronnen, Indexeer functies en synoniemen kunt beheren, evenals documenten kunt uploaden en beheren, en query's kunt uitvoeren, zonder dat u de details van HTTP en JSON hoeft te hoeven afhandelen. Deze client bibliotheken zijn allemaal gedistribueerd als NuGet-pakketten.

Het belangrijkste NuGet-pakket is `Microsoft.Azure.Search`, een meta pakket dat alle andere pakketten als afhankelijkheden bevat. Gebruik dit pakket als u zojuist aan de slag bent of als u weet dat uw toepassing alle functies van Azure Cognitive Search nodig heeft.

De andere NuGet-pakketten in de SDK zijn:
 
  - `Microsoft.Azure.Search.Data`: gebruik dit pakket als u een .NET-toepassing ontwikkelt met Azure Cognitive Search en u alleen documenten in uw indexen hoeft op te vragen of bij te werken. Als u ook indexen, synoniemen of andere bronnen op service niveau wilt maken of bijwerken, gebruikt u in plaats daarvan het `Microsoft.Azure.Search`-pakket.
  - `Microsoft.Azure.Search.Service`: gebruik dit pakket als u Automation in .NET ontwikkelt voor het beheren van Azure Cognitive Search indexen, synoniemen, Indexeer functies, gegevens bronnen of andere resources op service niveau. Als u alleen documenten in uw indexen hoeft op te vragen of bij te werken, gebruikt u in plaats daarvan het `Microsoft.Azure.Search.Data`-pakket. Als u de functionaliteit van Azure Cognitive Search nodig hebt, gebruikt u het `Microsoft.Azure.Search` pakket in plaats daarvan.
  - `Microsoft.Azure.Search.Common`: algemene typen die nodig zijn voor de Azure Cognitive Search .NET-bibliotheken. U hoeft dit pakket niet rechtstreeks in uw toepassing te gebruiken. Het is alleen bedoeld om te worden gebruikt als afhankelijkheid.

In de verschillende client bibliotheken worden klassen zoals `Index`, `Field`en `Document`gedefinieerd, evenals bewerkingen als `Indexes.Create` en `Documents.Search` in de klassen `SearchServiceClient` en `SearchIndexClient`. Deze klassen zijn ingedeeld in de volgende naam ruimten:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Als u feedback wilt geven voor een toekomstige update van de SDK, raadpleegt u onze [feedback pagina](https://feedback.azure.com/forums/263029-azure-search/) of maakt u een probleem op [github](https://github.com/azure/azure-sdk-for-net/issues) en vermeldt u ' Azure Cognitive Search ' in de titel van het probleem.

De .NET SDK ondersteunt versie `2019-05-06` van de [rest API Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/). Deze versie bevat ondersteuning voor [complexe typen](search-howto-complex-data-types.md), [AI-verrijking](cognitive-search-concept-intro.md), [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete)en [JsonLines-parserings modus bij het indexeren van](search-howto-index-json-blobs.md) Azure-blobs. 

Deze SDK biedt geen ondersteuning voor [beheer bewerkingen](https://docs.microsoft.com/rest/api/searchmanagement/) , zoals het maken en schalen van zoek services en het beheren van API-sleutels. Als u uw Zoek resources wilt beheren vanuit een .NET-toepassing, kunt u de [Azure Cognitive Search .net Management SDK](https://aka.ms/search-mgmt-sdk)gebruiken.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgraden naar de nieuwste versie van de SDK
Als u al een oudere versie van de Azure Cognitive Search .NET SDK gebruikt en u een upgrade wilt uitvoeren naar de meest recente, algemeen beschik bare versie, wordt in [dit artikel](search-dotnet-sdk-migration-version-9.md) uitgelegd hoe dat werkt.

## <a name="requirements-for-the-sdk"></a>Vereisten voor de SDK
1. Visual Studio 2017 of hoger.
2. Uw eigen Azure Cognitive Search-service. Als u de SDK wilt gebruiken, hebt u de naam van uw service en een of meer API-sleutels nodig. [Een service maken in de portal](search-create-service-portal.md) helpt u bij het uitvoeren van deze stappen.
3. Down load het Azure Cognitive Search .NET SDK [NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Search) met behulp van ' NuGet-pakketten beheren ' in Visual Studio. Zoek alleen naar de pakket naam `Microsoft.Azure.Search` op NuGet.org (of een van de andere pakket namen hierboven als u alleen een subset van de functionaliteit nodig hebt).

De Azure Cognitive Search .NET SDK ondersteunt toepassingen die gericht zijn op de .NET Framework 4.5.2 en hoger, evenals .NET Core 2,0 en hoger.

## <a name="core-scenarios"></a>Kern scenario's
Er zijn verschillende dingen die u moet doen in uw zoek toepassing. In deze zelf studie worden de volgende kern scenario's behandeld:

* Een index maken
* De index met documenten vullen
* Zoeken naar documenten met zoeken in volledige tekst en filters

De volgende voorbeeld code illustreert elk van deze scenario's. U kunt de code fragmenten in uw eigen toepassing gebruiken.

### <a name="overview"></a>Overzicht
De voorbeeld toepassing die we verkennen, maakt een nieuwe index met de naam "Hotels", vult deze met een paar documenten en voert vervolgens een aantal Zoek query's uit. Hier volgt het hoofd programma, met daarin de algehele stroom:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> U vindt de volledige broncode van de voorbeeldtoepassing die in deze walkthrough wordt gebruikt, op [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Deze stap wordt stapsgewijs door lopen. Eerst moet u een nieuw `SearchServiceClient`maken. Met dit object kunt u indexen beheren. Als u een abonnement wilt maken, moet u de Azure Cognitive Search-service naam opgeven, evenals een beheer-API-sleutel. U kunt deze informatie invoeren in het `appsettings.json`-bestand van de [voorbeeld toepassing](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Als u een onjuiste sleutel opgeeft (bijvoorbeeld een query sleutel waarvoor een beheerders sleutel is vereist), genereert de `SearchServiceClient` een `CloudException` met het fout bericht ' verboden ' de eerste keer dat u een bewerkings methode aanroept, zoals `Indexes.Create`. Als dit het geval is, controleert u onze API-sleutel.
> 
> 

De volgende regels roepen methoden om een index met de naam "Hotels" te maken, die als eerste worden verwijderd als deze al bestaat. We gaan deze methoden later even door lopen.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Vervolgens moet de index worden ingevuld. Voor het vullen van de index is een `SearchIndexClient`vereist. Er zijn twee manieren om er een te verkrijgen: door deze te maken of door `Indexes.GetClient` op de `SearchServiceClient`aan te roepen. We gebruiken de laatste voor het gemak.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> In een typische Zoek toepassing kunnen index beheer en-populatie worden verwerkt door een afzonderlijk onderdeel van zoek query's. `Indexes.GetClient` is handig voor het vullen van een index, omdat u hiermee geen extra `SearchCredentials`kunt leveren. Dit wordt uitgevoerd door de administratorsleutel die u hebt gebruikt om de `SearchServiceClient` te maken die u wilt doorgeven aan de nieuwe `SearchIndexClient`. In het deel van uw toepassing die query's uitvoert, is het echter beter om de `SearchIndexClient` rechtstreeks te maken, zodat u een query sleutel kunt door geven, waardoor u alleen gegevens in plaats van een Administrator sleutel kan lezen. Dit is consistent met het principe van minimale bevoegdheden en helpt om uw toepassing veiliger te maken. Meer informatie over de beheer sleutels en query sleutels vindt u [hier](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Nu we een `SearchIndexClient`hebben, kunnen we de index vullen. Index populatie wordt uitgevoerd door een andere methode die we later gaan door lopen.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Ten slotte worden er een aantal Zoek query's uitgevoerd en worden de resultaten weer gegeven. Deze keer gebruiken we een andere `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

We gaan later de `RunQueries` methode nader bekijken. Hier volgt de code voor het maken van de nieuwe `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Deze keer gebruiken we een query sleutel omdat we geen schrijf toegang tot de index nodig hebben. U kunt deze informatie invoeren in het `appsettings.json`-bestand van de [voorbeeld toepassing](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Als u deze toepassing uitvoert met een geldige service naam en API-sleutel, moet de uitvoer er als volgt uitzien: (sommige console-uitvoer is vervangen door "..." voor illustratie doeleinden.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

De volledige bron code van de toepassing wordt aan het einde van dit artikel vermeld.

We gaan nu nader kijken naar elk van de methoden die worden aangeroepen door `Main`.

### <a name="creating-an-index"></a>Een index maken
Na het maken van een `SearchServiceClient`, `Main` de index "Hotels" verwijderd als deze al bestaat. Deze verwijdering wordt uitgevoerd door de volgende methode:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Deze methode gebruikt de opgegeven `SearchServiceClient` om te controleren of de index bestaat, en als dit het geval is, verwijdert u deze.

> [!NOTE]
> In de voorbeeld code in dit artikel wordt gebruikgemaakt van de synchrone methoden van de Azure Cognitive Search .NET SDK voor eenvoud. Het wordt aanbevolen in uw eigen toepassingen asynchrone methoden te gebruiken, zodat de toepassingen schaalbaar zijn en goed reageren. In de bovenstaande methode kunt u bijvoorbeeld `ExistsAsync` en `DeleteAsync` gebruiken in plaats van `Exists` en `Delete`.
> 
> 

`Main` maakt vervolgens een nieuwe index "Hotels" door deze methode aan te roepen:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Met deze methode maakt u een nieuw `Index`-object met een lijst `Field` objecten die het schema van de nieuwe index definieert. Elk veld heeft een naam, gegevens type en verschillende kenmerken waarmee het zoek gedrag wordt gedefinieerd. De klasse `FieldBuilder` gebruikt reflectie om een lijst met `Field` objecten voor de index te maken door de open bare eigenschappen en kenmerken van de opgegeven `Hotel` model klasse te controleren. De `Hotel` klasse wordt verderop op dit moment nader bekeken.

> [!NOTE]
> U kunt altijd de lijst met `Field` objecten rechtstreeks maken in plaats van `FieldBuilder` zo nodig te gebruiken. Het is bijvoorbeeld mogelijk dat u een model klasse niet wilt gebruiken of dat u een bestaande model klasse moet gebruiken die u niet wilt wijzigen door kenmerken toe te voegen.
>
> 

Naast velden kunt u ook Score profielen, Voorst Ellen of CORS-opties toevoegen aan de index (deze para meters worden wegge laten uit het voor beeld voor de boog). U kunt meer informatie vinden over het index-object en de onderdelen daarvan in de [SDK-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), maar ook in de referentie voor [Azure Cognitive Search rest API](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>De index vullen
Met de volgende stap in `Main` wordt de zojuist gemaakte index gevuld. Deze index populatie wordt uitgevoerd in de volgende methode: (sommige code is vervangen door '... ' voor illustratie doeleinden.  Bekijk de volledige voorbeeld oplossing voor de volledige gegevens populatie code.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Deze methode heeft vier delen. Met de eerste maakt u een matrix van 3 `Hotel` objecten met drie `Room` objecten die als de invoer gegevens moeten worden geüpload naar de index. Deze gegevens worden in code vastgelegd voor eenvoud. In uw eigen toepassing zijn uw gegevens waarschijnlijk afkomstig uit een externe gegevens bron, zoals een SQL database.

Met het tweede deel wordt een `IndexBatch` met de documenten gemaakt. U geeft de bewerking op die u wilt Toep assen op de batch op het moment dat u deze maakt, in dit geval door het aanroepen van `IndexBatch.Upload`. De batch wordt vervolgens geüpload naar de Azure Cognitive Search-index met de methode `Documents.Index`.

> [!NOTE]
> In dit voor beeld worden alleen documenten geüpload. Als u wijzigingen wilt samen voegen in bestaande documenten of documenten wilt verwijderen, kunt u batches maken door `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`of `IndexBatch.Delete` aan te roepen. U kunt ook verschillende bewerkingen in één batch combi neren door `IndexBatch.New`aan te roepen, die een verzameling `IndexAction` objecten aanroept, waarmee wordt aangegeven dat er in azure Cognitive Search een bepaalde bewerking moet worden uitgevoerd op een document. U kunt elke `IndexAction` met een eigen bewerking maken door de overeenkomstige methode, zoals `IndexAction.Merge`, `IndexAction.Upload`, enzovoort aan te roepen.
> 
> 

Het derde deel van deze methode is een catch-blok dat een belang rijke fout bij het indexeren afhandelt. Als uw Azure Cognitive Search-service een aantal documenten in de batch niet kan indexeren, wordt een `IndexBatchException` gegenereerd door `Documents.Index`. Deze uitzonde ring kan optreden als u documenten indexeert terwijl uw service zwaar wordt belast. **Wij raden u aan deze aanvraag expliciet in uw code te behandelen.** U kunt de indexering van documenten die niet zijn geïndexeerd vertragen en vervolgens opnieuw uitvoeren, maar u kunt ook een logboek maken en doorgaan zoals in het voorbeeld. U kunt ook een andere bewerking uitvoeren, afhankelijk van de vereisten omtrent de gegevensconsistentie van de toepassing.

> [!NOTE]
> U kunt de methode [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) gebruiken om een nieuwe batch te maken met alleen de acties die zijn mislukt in een eerdere aanroep van `Index`. Er is een discussie over hoe u deze op de juiste wijze kunt gebruiken [op stack overflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Ten slotte wordt de `UploadDocuments` methode twee seconden vertragingen. Het indexeren vindt asynchroon plaats in uw Azure Cognitive Search-service. de voorbeeld toepassing moet daarom een korte tijd wachten om ervoor te zorgen dat de documenten kunnen worden doorzocht. Dergelijke vertragingen zijn doorgaans alleen nodig is demo’s, testen en voorbeeldtoepassingen.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>De verwerking van documenten door .NET SDK
U vraagt zich misschien af hoe de Azure Cognitive Search .NET SDK instanties van een door de gebruiker gedefinieerde klasse kan uploaden, zoals `Hotel` naar de index. We kijken naar de klasse `Hotel` om deze vraag te beantwoorden:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

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

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

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

Het eerste dat u ziet, is dat de naam van elke open bare eigenschap in de `Hotel` klasse wordt toegewezen aan een veld met dezelfde naam in de index definitie. Als u wilt dat elk veld begint met een kleine letter (' Camel case '), kunt u de SDK laten weten dat de namen van eigenschappen automatisch moeten worden toegewezen aan Camel-case met het kenmerk `[SerializePropertyNamesAsCamelCase]` op de klasse. Dit scenario is gebruikelijk in .NET-toepassingen die gegevens bindingen uitvoeren waarbij het doel schema zich buiten het beheer van de toepassings ontwikkelaar bevindt, zonder dat de naamgevings richtlijnen van de "Pascal-case" in .NET moeten worden geschonden.

> [!NOTE]
> De Azure Cognitive Search .NET SDK maakt gebruik van de [newton soft JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) -bibliotheek om uw aangepaste model objecten te serialiseren en deserialiseren naar JSON. U kunt deze serialisatie indien nodig aanpassen. Zie [aangepaste serialisatie met JSON.net](#JsonDotNet)voor meer informatie.
> 
> 

De tweede ding is dat elke eigenschap wordt gedecoreerd met kenmerken als `IsFilterable`, `IsSearchable`, `Key`en `Analyzer`. Deze kenmerken worden rechtstreeks aan de [corresponderende veld kenmerken in een Azure Cognitive search-index](/rest/api/searchservice/create-index)toegewezen. De klasse `FieldBuilder` gebruikt deze eigenschappen om veld definities voor de index te maken.

Het derde belang rijk voor de `Hotel` klasse is de gegevens typen van de open bare eigenschappen. De .NET-typen van deze eigenschappen worden toegewezen aan de gelijkwaardige veldtypen in de definitie van de index. De tekenreekseigenschap `Category` is bijvoorbeeld toegewezen aan het veld `category` van type `Edm.String`. Er zijn soort gelijke toewijzingen tussen `bool?`, `Edm.Boolean`, `DateTimeOffset?`en `Edm.DateTimeOffset`, enzovoort. De specifieke regels voor de toewijzing van het type worden gedocumenteerd met de methode `Documents.Get` in de [Naslag informatie voor Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). De `FieldBuilder` klasse zorgt ervoor dat deze toewijzing voor u wordt uitgevoerd, maar het kan wel handig zijn om te begrijpen in het geval dat u eventuele problemen met de serialisatie moet oplossen.

Ziet u de eigenschap `SmokingAllowed`?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Met het kenmerk `JsonIgnore` van deze eigenschap wordt aan de `FieldBuilder` aangegeven dat deze niet wordt geserialiseerd met de index als een veld.  Dit is een uitstekende manier om berekende eigenschappen aan client zijde te maken die u als helpers in uw toepassing kunt gebruiken.  In dit geval geeft de eigenschap `SmokingAllowed` aan of een `Room` in de `Rooms`-verzameling roken is.  Als alles onwaar is, geeft het aan dat het hele hotel geen roken toestaat.

Sommige eigenschappen, zoals `Address` en `Rooms`, zijn exemplaren van .NET-klassen.  Deze eigenschappen vertegenwoordigen complexere gegevens structuren en daarom hebben velden met een [complex gegevens type](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) vereist in de index.

De eigenschap `Address` vertegenwoordigt een set met meerdere waarden in de klasse `Address`, zoals hieronder gedefinieerd:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
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

Deze klasse bevat de standaard waarden die worden gebruikt voor het beschrijven van adressen in de Verenigde Staten of Canada. U kunt typen als dit gebruiken om logische velden samen te groeperen in de index.

De eigenschap `Rooms` vertegenwoordigt een matrix van `Room`-objecten:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
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

Uw gegevens model in .NET en het bijbehorende index schema moeten zijn ontworpen ter ondersteuning van de zoek ervaring die u aan uw eind gebruiker wilt geven. Elk object op het hoogste niveau in .NET, het IE-document in de index, komt overeen met een Zoek resultaat dat u in uw gebruikers interface zou weer geven. U kunt bijvoorbeeld in een toepassing voor het zoeken in een hotel zoeken op de naam van het Hotel, de functies van het hotel of de kenmerken van een bepaalde kamer. Enkele voor beelden van query's worden verderop beschreven.

Deze mogelijkheid om uw eigen klassen te gebruiken voor de interactie met documenten in de index werkt in beide richtingen. U kunt ook Zoek resultaten ophalen en de SDK automatisch deserialiseren naar een type keuze, zoals in de volgende sectie wordt weer geven.

> [!NOTE]
> De Azure Cognitive Search .NET SDK biedt ook ondersteuning voor dynamisch getypeerde documenten met behulp van de `Document`-klasse. Dit is een sleutel/waarde-toewijzing van veld namen naar veld waarden. Dit is handig in situaties waar u het schema van de index op het moment van ontwerp nog niet weet of wanneer het niet handig zou zijn om verbinding te maken met specifieke modelklassen Alle methoden in de SDK die werken met documenten hebben overloads die met werken de `Document`-klasse, evenals sterk getypeerde overloads die een generiek typeparameter moeten uitvoeren. In de voorbeeld code in deze zelf studie wordt alleen deze laatste gebruikt. De [klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) van de`Document` neemt over van `Dictionary<string, object>`.
> 
>

**Waarom u nullable-gegevenstypen moet gebruiken**

Bij het ontwerpen van uw eigen model klassen om toe te wijzen aan een Azure Cognitive Search-index, wordt u aangeraden eigenschappen van waardetypen zoals `bool` en `int` te declareren als Null-waarden (bijvoorbeeld `bool?` in plaats van `bool`). Als u een niet-nullbare eigenschap gebruikt, moet u **garanderen** dat de documenten in de index geen null-waarde voor het betreffende veld bevatten. De SDK en de Azure Cognitive Search-service helpen u dit af te dwingen.

Dit is niet alleen een hypothetische probleem: Stel een scenario voor waarin u een nieuw veld toevoegt aan een bestaande index van het type `Edm.Int32`. Nadat de index definitie is bijgewerkt, hebben alle documenten een null-waarde voor dat nieuwe veld (aangezien alle typen in azure Cognitive Search zijn toegestaan). Als u vervolgens een modelklasse met een niet-nullbare `int`-eigenschap voor dat veld gebruikt, ontvangt u een `JsonSerializationException` zoals deze bij het ophalen van documenten:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Daarom wordt u aangeraden nullbare typen in uw modelklassen te gebruiken.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Aangepaste serialisatie met JSON.NET
De SDK gebruikt JSON.NET voor het serialiseren en deserialiseren van documenten. U kunt serialisatie en deserialisatie zo nodig aanpassen door uw eigen `JsonConverter` of `IContractResolver`te definiëren. Zie de [JSON.net-Documentatie](https://www.newtonsoft.com/json/help/html/Introduction.htm)voor meer informatie. Dit kan handig zijn wanneer u een bestaande model klasse wilt aanpassen uit uw toepassing voor gebruik met Azure Cognitive Search en andere geavanceerde scenario's. Met aangepaste serialisatie kunt u bijvoorbeeld het volgende doen:

* Bepaalde eigenschappen van uw model klasse opnemen of uitsluiten, zodat deze als document velden worden opgeslagen.
* Toewijzen tussen eigenschaps namen in uw code en veld namen in uw index.
* Aangepaste kenmerken maken die kunnen worden gebruikt voor het toewijzen van eigenschappen aan document velden.

U kunt voor beelden vinden van het implementeren van aangepaste serialisatie in de eenheids tests voor de Azure Cognitive Search .NET SDK op GitHub. Een goed uitgangs punt is [deze map](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Het bevat klassen die worden gebruikt door de aangepaste serialisatie tests.

### <a name="searching-for-documents-in-the-index"></a>Zoeken naar documenten in de index
De laatste stap in de voorbeeld toepassing is het zoeken naar sommige documenten in de index:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Telkens wanneer een query wordt uitgevoerd, maakt deze methode eerst een nieuw `SearchParameters`-object. Dit object wordt gebruikt om aanvullende opties voor de query op te geven, zoals sorteren, filteren, paginering en facetten. In deze methode stellen we de eigenschap `Filter`, `Select`, `OrderBy`en `Top` in voor verschillende query's. Alle `SearchParameters` eigenschappen worden [hier](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)beschreven.

De volgende stap is om de zoek query werkelijk uit te voeren. Het uitvoeren van de zoek opdracht wordt uitgevoerd met behulp van de `Documents.Search` methode. Voor elke query wordt de Zoek tekst door gegeven als een teken reeks (of `"*"` als er geen Zoek tekst is), plus de zoek parameters die u eerder hebt gemaakt. We geven ook `Hotel` als de type parameter voor `Documents.Search`op, waarmee de SDK de documenten in de zoek resultaten kan deserialiseren in objecten van het type `Hotel`.

> [!NOTE]
> U kunt [hier](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)meer informatie vinden over de syntaxis van de zoek query-expressie.
> 
> 

Ten slotte wordt na elke query door deze methode alle overeenkomende items in de zoek resultaten door lopen, waarbij elk document naar de-console wordt afgedrukt:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Laten we eens kijken naar elk van de query's. Hier volgt de code voor het uitvoeren van de eerste query:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

In dit geval doorzoeken we de volledige index voor het woord "Motel" in elk Doorzoek bare veld en willen we alleen de namen van hotels ophalen, zoals opgegeven door de para meter `Select`. Dit zijn de resultaten:

    Name: Secret Point Motel

    Name: Twin Dome Motel

De volgende query is iets interessanter.  We willen hotels vinden met een ruimte met een uurtarief van minder dan $100 en alleen de Hotel-ID en de beschrijving retour neren:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Deze query gebruikt een OData-`$filter` expressie, `Rooms/any(r: r/BaseRate lt 100)`om de documenten in de index te filteren. Dit maakt gebruik van de [operator alle](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) om de ' BaseRate lt 100 ' toe te passen op elk item in de verzameling ruimtes. U vindt [hier](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)meer informatie over de OData-syntaxis die door Azure Cognitive Search wordt ondersteund.

Dit zijn de resultaten van de query:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

We willen nu de bovenste twee hotels vinden die het meest recent zijn renovated, en de naam van het hotel en de datum van de laatste renovatie weer geven. Dit is de code: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

In dit geval gebruiken we de OData-syntaxis opnieuw om de `OrderBy`-para meter op te geven als `lastRenovationDate desc`. We stellen `Top` ook in op 2 om ervoor te zorgen dat alleen de beste twee documenten worden opgehaald. Net als voorheen stellen we `Select` in om op te geven welke velden moeten worden geretourneerd.

Dit zijn de resultaten:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Tot slot willen we zoeken naar alle hotels namen die overeenkomen met het woord ' Hotel ':

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

En dit zijn de resultaten, die alle velden bevatten omdat we niet de `Select` eigenschap hebben opgegeven:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Met deze stap wordt de zelf studie voltooid, maar niet meer. \* * De volgende stappen bieden aanvullende bronnen voor meer informatie over Azure Cognitive Search.

## <a name="next-steps"></a>Volgende stappen
* Neem de referenties voor de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) en [REST API](https://docs.microsoft.com/rest/api/searchservice/) door.
* Bekijk [naamgevings](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) regels voor meer informatie over de regels voor het benoemen van verschillende objecten.
* Bekijk [ondersteunde gegevens typen](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) in azure Cognitive Search.
