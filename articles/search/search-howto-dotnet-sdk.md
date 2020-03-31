---
title: Azure Cognitive Search gebruiken in .NET
titleSuffix: Azure Cognitive Search
description: Meer informatie over het gebruik van Azure Cognitive Search in een .NET-toepassing met C# en de .NET SDK. Codegebaseerde taken omvatten verbinding maken met de service, indexinhoud en een index opvragen.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283067"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Azure Cognitive Search gebruiken vanuit een .NET-toepassing

Dit artikel is een walkthrough om u aan de slag te krijgen met de [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). U de .NET SDK gebruiken om een uitgebreide zoekervaring in uw toepassing te implementeren met Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Wat zit er in de Azure Cognitive Search SDK
De SDK bestaat uit een paar clientbibliotheken waarmee u uw indexen, gegevensbronnen, indexers en synoniemenkaarten beheren, evenals documenten uploaden en beheren en query's uitvoeren, zonder dat u de details van HTTP en JSON hoeft te behandelen. Deze clientbibliotheken worden allemaal gedistribueerd als NuGet-pakketten.

Het belangrijkste NuGet-pakket is `Microsoft.Azure.Search`een meta-pakket dat alle andere pakketten als afhankelijkheden bevat. Gebruik dit pakket als u nog maar net bent begonnen of als u weet dat uw toepassing alle functies van Azure Cognitive Search nodig heeft.

De andere NuGet pakketten in de SDK zijn:
 
  - `Microsoft.Azure.Search.Data`: Gebruik dit pakket als u een .NET-toepassing ontwikkelt met Azure Cognitive Search en u hoeft alleen documenten in uw indexen op te vragen of bij te werken. Als u ook indexen, synoniemkaarten of andere bronnen op serviceniveau `Microsoft.Azure.Search` moet maken of bijwerken, gebruikt u het pakket in plaats daarvan.
  - `Microsoft.Azure.Search.Service`: Gebruik dit pakket als u automatisering ontwikkelt in .NET om Azure Cognitive Search-indexen, synoniemkaarten, indexeerders, gegevensbronnen of andere resources op serviceniveau te beheren. Als u alleen documenten in uw indexen hoeft `Microsoft.Azure.Search.Data` op te vragen of bij te werken, gebruikt u het pakket in plaats daarvan. Als u alle functionaliteit van Azure Cognitive `Microsoft.Azure.Search` Search nodig hebt, gebruikt u het pakket in plaats daarvan.
  - `Microsoft.Azure.Search.Common`: Veelvoorkomende typen die nodig zijn voor de Azure Cognitive Search .NET-bibliotheken. U hoeft dit pakket niet direct in uw toepassing te gebruiken. Het is alleen bedoeld om te worden gebruikt als een afhankelijkheid.

De verschillende clientbibliotheken definiëren `Index`klassen `Field`zoals `Document`, , en `Indexes.Create` `Documents.Search` , `SearchServiceClient` evenals `SearchIndexClient` bewerkingen zoals en op de en klassen. Deze klassen zijn ingedeeld in de volgende naamruimten:

* [Microsoft.Azure.Zoeken](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Als u feedback wilt geven voor een toekomstige update van de SDK, raadpleegt u onze [feedbackpagina](https://feedback.azure.com/forums/263029-azure-search/) of maakt u een probleem op [GitHub](https://github.com/azure/azure-sdk-for-net/issues) en vermeldt u 'Azure Cognitive Search' in de uitgiftetitel.

De .NET SDK `2019-05-06` ondersteunt versie van de [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Deze versie bevat ondersteuning voor [complexe typen](search-howto-complex-data-types.md), [AI-verrijking,](cognitive-search-concept-intro.md) [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete)en [JsonLines-parsing-modus](search-howto-index-json-blobs.md) bij het indexeren van Azure Blobs. 

Deze SDK biedt geen ondersteuning voor [Beheerbewerkingen,](https://docs.microsoft.com/rest/api/searchmanagement/) zoals het maken en schalen van zoekservices en het beheren van API-sleutels. Als u uw zoekbronnen vanuit een .NET-toepassing moet beheren, u de [Azure Cognitive Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)gebruiken.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Upgraden naar de nieuwste versie van de SDK
Als u al een oudere versie van de Azure Cognitive Search .NET SDK gebruikt en u wilt upgraden naar de nieuwste algemeen beschikbare versie, wordt in [dit artikel](search-dotnet-sdk-migration-version-9.md) uitgelegd hoe.

## <a name="requirements-for-the-sdk"></a>Vereisten voor de SDK
1. Visual Studio 2017 of hoger.
2. Uw eigen Azure Cognitive Search-service. Om de SDK te kunnen gebruiken, hebt u de naam van uw service en een of meer API-sleutels nodig. [Maak een service in de portal](search-create-service-portal.md) zal u helpen bij deze stappen.
3. Download het Azure Cognitive Search .NET SDK [NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Search) met 'NuGet-pakketten beheren' in Visual Studio. Zoek gewoon naar `Microsoft.Azure.Search` de pakketnaam op NuGet.org (of een van de andere pakketnamen hierboven als u slechts een subset van de functionaliteit nodig hebt).

De Azure Cognitive Search .NET SDK ondersteunt toepassingen die zich richten op het .NET Framework 4.5.2 en hoger, evenals .NET Core 2.0 en hoger.

## <a name="core-scenarios"></a>Kernscenario's
Er zijn verschillende dingen die u moet doen in uw zoektoepassing. In deze zelfstudie behandelen we deze kernscenario's:

* Een index maken
* De index vullen met documenten
* Zoeken naar documenten met behulp van zoeken in volledige tekst en filters

De volgende voorbeeldcode illustreert elk van deze scenario's. Gebruik gerust de codefragmenten in uw eigen applicatie.

### <a name="overview"></a>Overzicht
De voorbeeldtoepassing die we gaan verkennen, maakt een nieuwe index met de naam 'hotels', vult deze met een paar documenten en voert vervolgens enkele zoekopdrachten uit. Hier is het belangrijkste programma, met de totale stroom:

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

We lopen dit stap voor stap door. Eerst moeten we een `SearchServiceClient`nieuwe creëren. Met dit object u indexen beheren. Als u er een wilt maken, moet u uw Azure Cognitive Search-servicenaam en een beheer-API-sleutel opgeven. U deze gegevens `appsettings.json` invoeren in het bestand van de [voorbeeldtoepassing.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

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
> Als u een onjuiste sleutel opgeeft (bijvoorbeeld een querysleutel `SearchServiceClient` waarbij `CloudException` een beheersleutel vereist was), wordt er een met het foutbericht 'Verboden' gegooid wanneer u er voor het eerst een bewerkingsmethode op aanroept, zoals `Indexes.Create`. Als u dit overkomt, controleert u onze API-sleutel.
> 
> 

De volgende regels roepen methoden op om een index met de naam 'hotels' te maken, deze eerst te verwijderen als deze al bestaat. We zullen lopen door deze methoden een beetje later.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Vervolgens moet de index worden ingevuld. Om de index te vullen, `SearchIndexClient`hebben we een . Er zijn twee manieren om er een te `Indexes.GetClient` verkrijgen: `SearchServiceClient`door het te bouwen, of door een beroep te doen op de . Dat laatste gebruiken we voor het gemak.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> In een typische zoektoepassing kunnen indexbeheer en populatie worden verwerkt door een afzonderlijk onderdeel van zoekopdrachten. `Indexes.GetClient`is handig voor het vullen van een index, `SearchCredentials`omdat het bespaart u de moeite van het verstrekken van extra . Dit wordt uitgevoerd door de administratorsleutel die u hebt gebruikt om de `SearchServiceClient` te maken die u wilt doorgeven aan de nieuwe `SearchIndexClient`. In het deel van uw toepassing dat query's uitvoert, `SearchIndexClient` is het echter beter om de directe queryte maken, zodat u een querysleutel doorgeven, waarmee u alleen gegevens lezen in plaats van een beheersleutel. Dit komt overeen met het -principe van minimale bevoegdheden en zorgt ervoor dat uw toepassing veilig is. U [hier](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)meer informatie vinden over beheerderssleutels en querysleutels.
> 
> 

Nu we een, `SearchIndexClient`kunnen we de index te vullen. Index bevolking wordt gedaan door een andere methode die we later zullen lopen door middel van.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Tot slot voeren we een paar zoekopdrachten uit en geven we de resultaten weer. Deze keer gebruiken `SearchIndexClient`we een ander:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

We zullen de `RunQueries` methode later nader bekijken. Hier is de code `SearchIndexClient`om de nieuwe te maken:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Deze keer gebruiken we een querysleutel omdat we geen schrijftoegang tot de index nodig hebben. U deze gegevens `appsettings.json` invoeren in het bestand van de [voorbeeldtoepassing.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

Als u deze toepassing uitvoert met een geldige servicenaam en API-sleutels, moet de uitvoer er uitzien als dit voorbeeld: (Sommige console-uitvoer is vervangen door "..." ter illustratie.)

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

De volledige broncode van de applicatie wordt aan het einde van dit artikel verstrekt.

Vervolgens zullen we een kijkje nemen op elk `Main`van de methoden genoemd door .

### <a name="creating-an-index"></a>Een index maken
Na het `SearchServiceClient` `Main` maken van een , verwijdert de "hotels" index als deze al bestaat. Deze verwijdering gebeurt volgens de volgende methode:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Deze methode gebruikt `SearchServiceClient` de gegeven om te controleren of de index bestaat, en zo ja, verwijder het.

> [!NOTE]
> De voorbeeldcode in dit artikel gebruikt de synchrone methoden van azure cognitive search .NET SDK voor eenvoud. Het wordt aanbevolen in uw eigen toepassingen asynchrone methoden te gebruiken, zodat de toepassingen schaalbaar zijn en goed reageren. Bijvoorbeeld, in de bovenstaande methode `ExistsAsync` `DeleteAsync` die `Exists` u `Delete`zou kunnen gebruiken en in plaats van en .
> 
> 

Hiermee `Main` maakt u een nieuwe "hotels"-index door deze methode aan te roepen:

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

Met deze methode `Index` wordt een `Field` nieuw object gemaakt met een lijst met objecten die het schema van de nieuwe index definieert. Elk veld heeft een naam, gegevenstype en verschillende kenmerken die het zoekgedrag definiëren. De `FieldBuilder` klasse gebruikt reflectie om `Field` een lijst met objecten voor de index `Hotel` te maken door de openbare eigenschappen en kenmerken van de gegeven modelklasse te onderzoeken. We nemen de `Hotel` klas later van dichtbij.

> [!NOTE]
> U de lijst `Field` met objecten `FieldBuilder` altijd rechtstreeks maken in plaats van indien nodig te gebruiken. U bijvoorbeeld geen modelklasse gebruiken of u moet mogelijk een bestaande modelklasse gebruiken die u niet wilt wijzigen door kenmerken toe te voegen.
>
> 

Naast velden u ook scoreprofielen, suggesties of CORS-opties toevoegen aan de index (deze parameters worden weggelaten uit het voorbeeld voor beknoptheid). U vindt meer informatie over het object Index en de samenstellende onderdelen ervan in de [SDK-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)en in de [api-verwijzing azure cognitive search rest.](https://docs.microsoft.com/rest/api/searchservice/)

### <a name="populating-the-index"></a>De index vullen
De volgende `Main` stap in vult de nieuw gemaakte index. Deze indexpopulatie gebeurt volgens de volgende methode: (Sommige code vervangen door "..." ter illustratie.  Bekijk de volledige voorbeeldoplossing voor de volledige gegevensbevolkingscode.)

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

Deze methode bestaat uit vier delen. De eerste maakt een `Hotel` array van `Room` 3 objecten elk met 3 objecten die zal dienen als onze invoergegevens te uploaden naar de index. Deze gegevens zijn hard gecodeerd voor eenvoud. In uw eigen toepassing zullen uw gegevens waarschijnlijk afkomstig zijn van een externe gegevensbron, zoals een SQL-database.

Het tweede deel `IndexBatch` maakt een met de documenten. U geeft de bewerking op die u op de batch wilt toepassen `IndexBatch.Upload`op het moment dat u deze maakt, in dit geval door aan te roepen . De batch wordt vervolgens geüpload naar `Documents.Index` de Azure Cognitive Search-index met de methode.

> [!NOTE]
> In dit voorbeeld uploaden we alleen documenten. Als u wijzigingen in bestaande documenten wilt samenvoegen of documenten `IndexBatch.Merge`wilt `IndexBatch.MergeOrUpload`verwijderen, u batches maken door aan te roepen, of `IndexBatch.Delete` in plaats daarvan. U ook verschillende bewerkingen in `IndexBatch.New`één batch mengen `IndexAction` door aan te roepen, waarbij een verzameling objecten wordt aangenomen, die azure cognitive search vertelt om een bepaalde bewerking op een document uit te voeren. U `IndexAction` elk met een eigen bewerking maken `IndexAction.Merge` `IndexAction.Upload`door de bijbehorende methode aan te roepen, zoals, enzovoort.
> 
> 

Het derde deel van deze methode is een catch block dat een belangrijke foutcase voor indexering verwerkt. Als uw Azure Cognitive Search-service sommige documenten in `IndexBatchException` de batch `Documents.Index`niet indexeert, wordt een door . Deze uitzondering kan optreden als u documenten indexeert terwijl uw service zwaar wordt belast. **Wij raden u aan deze aanvraag expliciet in uw code te behandelen.** U kunt de indexering van documenten die niet zijn geïndexeerd vertragen en vervolgens opnieuw uitvoeren, maar u kunt ook een logboek maken en doorgaan zoals in het voorbeeld. U kunt ook een andere bewerking uitvoeren, afhankelijk van de vereisten omtrent de gegevensconsistentie van de toepassing.

> [!NOTE]
> U [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) de methode gebruiken om een nieuwe batch te maken `Index`die alleen de acties bevat die in een vorige aanroep zijn mislukt voor. Er is een discussie over hoe goed te gebruiken [op StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Ten slotte `UploadDocuments` vertraagt de methode twee seconden. Indexering gebeurt asynchroon in uw Azure Cognitive Search-service, dus de voorbeeldtoepassing moet even wachten om ervoor te zorgen dat de documenten beschikbaar zijn voor zoeken. Dergelijke vertragingen zijn doorgaans alleen nodig is demo’s, testen en voorbeeldtoepassingen.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>De verwerking van documenten door .NET SDK
U vraagt zich misschien af hoe de Azure Cognitive Search .NET SDK `Hotel` instanties van een door de gebruiker gedefinieerde klasse als deze kan uploaden naar de index. Laten we eens kijken naar de `Hotel` klas om die vraag te beantwoorden:

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

Het eerste wat opvalt is dat de `Hotel` naam van elke openbare eigenschap in de klasse wordt toegewezen aan een veld met dezelfde naam in de indexdefinitie. Als u wilt dat elk veld begint met een kleine letter (camel case), u de SDK `[SerializePropertyNamesAsCamelCase]` vertellen om de eigenschapsnamen automatisch in kaart te brengen in een camel-case met het attribuut op de klasse. Dit scenario komt vaak voor in .NET-toepassingen die gegevensbinding uitvoeren wanneer het doelschema buiten de controle van de toepassingsontwikkelaar valt zonder dat de naamgevingsrichtlijnen voor pascal-toepassingen in .NET hoeven te worden overtreden.

> [!NOTE]
> De Azure Cognitive Search .NET SDK gebruikt de [JSON.NET-bibliotheek NewtonSoft](https://www.newtonsoft.com/json/help/html/Introduction.htm) om uw aangepaste modelobjecten van en naar JSON te serialiseren en te deserialiseren. U kunt deze serialisatie indien nodig aanpassen. Zie [Aangepaste serialisatie met JSON.NET](#JsonDotNet)voor meer informatie.
> 
> 

Het tweede ding om op te merken is `IsFilterable`elke `IsSearchable` `Key`eigenschap `Analyzer`is versierd met attributen zoals , , , en . Deze kenmerken worden rechtstreeks toegewezen aan de [bijbehorende veldkenmerken in een Azure Cognitive Search-index](/rest/api/searchservice/create-index). De `FieldBuilder` klasse gebruikt deze eigenschappen om velddefinities voor de index te construeren.

Het derde belangrijke `Hotel` ding over de klasse is de gegevenstypen van de openbare eigenschappen. De .NET-typen van deze eigenschappen worden toegewezen aan de gelijkwaardige veldtypen in de definitie van de index. De tekenreekseigenschap `Category` is bijvoorbeeld toegewezen aan het veld `category` van type `Edm.String`. Er zijn vergelijkbare typetoewijzingen `DateTimeOffset?`tussen `Edm.DateTimeOffset` `bool?`, `Edm.Boolean`, enzovoort. De specifieke regels voor de typetoewijzing worden gedocumenteerd met de `Documents.Get` methode in de Azure Cognitive Search [.NET SDK-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). De `FieldBuilder` klas zorgt voor deze toewijzing voor u, maar het kan nog steeds nuttig zijn om te begrijpen in het geval u eventuele serialisatieproblemen moet oplossen.

Heb je het `SmokingAllowed` pand opgemerkt?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Het `JsonIgnore` kenmerk op deze `FieldBuilder` eigenschap vertelt de om het niet te serialiseren aan de index als een veld.  Dit is een geweldige manier om door klanten berekende eigenschappen te maken die u gebruiken als helpers in uw toepassing.  In dit geval `SmokingAllowed` geeft de `Room` accommodatie `Rooms` aan of er in de collectie gerookt mag worden.  Als alle vals zijn, geeft het aan dat het gehele hotel niet roken toestaat.

Sommige eigenschappen, `Address` `Rooms` zoals en zijn exemplaren van .NET-klassen.  Deze eigenschappen vertegenwoordigen complexere gegevensstructuren en vereisen daarom velden met een [complex gegevenstype](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) in de index.

De `Address` eigenschap vertegenwoordigt een set `Address` van meerdere waarden in de klasse, hieronder gedefinieerd:

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

Deze klasse bevat de standaardwaarden die worden gebruikt om adressen in de Verenigde Staten of Canada te beschrijven. U dergelijke typen gebruiken om logische velden samen te groeperen in de index.

De `Rooms` eigenschap vertegenwoordigt `Room` een array van objecten:

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

Uw gegevensmodel in .NET en het bijbehorende indexschema moeten zijn ontworpen ter ondersteuning van de zoekervaring die u aan uw eindgebruiker wilt geven. Elk object op het hoogste niveau in .NET, dat wil zeggen document in de index, komt overeen met een zoekresultaat dat u in uw gebruikersinterface zou presenteren. In een hotelzoektoepassing willen uw eindgebruikers bijvoorbeeld zoeken op de naam van het hotel, kenmerken van het hotel of de kenmerken van een bepaalde kamer. We zullen een aantal query voorbeelden een beetje later.

Deze mogelijkheid om uw eigen klassen te gebruiken om te communiceren met documenten in de index werkt in beide richtingen; U ook zoekresultaten ophalen en de SDK deze automatisch laten deserialiseren naar een type van uw keuze, zoals we in de volgende sectie zullen zien.

> [!NOTE]
> De Azure Cognitive Search .NET SDK ondersteunt ook `Document` dynamisch getypte documenten met behulp van de klasse, een sleutel/waardetoewijzing van veldnamen naar veldwaarden. Dit is handig in situaties waar u het schema van de index op het moment van ontwerp nog niet weet of wanneer het niet handig zou zijn om verbinding te maken met specifieke modelklassen Alle methoden in de SDK die werken met documenten hebben overloads die met werken de `Document`-klasse, evenals sterk getypeerde overloads die een generiek typeparameter moeten uitvoeren. Alleen de laatste worden gebruikt in de voorbeeldcode in deze zelfstudie. De [ `Document` klasse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) erft van `Dictionary<string, object>`.
> 
>

**Waarom u nullable-gegevenstypen moet gebruiken**

Wanneer u uw eigen modelklassen ontwerpt om in kaart te brengen met een `bool` Azure `int` Cognitive Search-index, `bool?` raden `bool`we u aan eigenschappen van waardetypen aan te geven zoals en te nietig te verklaren (bijvoorbeeld in plaats van ). Als u een niet-nullbare eigenschap gebruikt, moet u **garanderen** dat de documenten in de index geen null-waarde voor het betreffende veld bevatten. Noch de SDK, noch de Azure Cognitive Search-service zullen u helpen dit af te dwingen.

Dit is niet alleen een hypothetische probleem: Stel een scenario voor waarin u een nieuw veld toevoegt aan een bestaande index van het type `Edm.Int32`. Na het bijwerken van de indexdefinitie hebben alle documenten een null-waarde voor dat nieuwe veld (omdat alle typen nietig zijn in Azure Cognitive Search). Als u vervolgens een modelklasse met een niet-nullbare `int`-eigenschap voor dat veld gebruikt, ontvangt u een `JsonSerializationException` zoals deze bij het ophalen van documenten:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Daarom wordt u aangeraden nullbare typen in uw modelklassen te gebruiken.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Aangepaste serialisatie met JSON.NET
De SDK gebruikt JSON.NET voor het serialiseren en deserialiseren van documenten. U serialisatie en deserialisatie indien nodig `JsonConverter` `IContractResolver`aanpassen door uw eigen of. Zie voor meer informatie de [JSON.NET documentatie.](https://www.newtonsoft.com/json/help/html/Introduction.htm) Dit kan handig zijn wanneer u een bestaande modelklasse vanuit uw toepassing wilt aanpassen voor gebruik met Azure Cognitive Search en andere geavanceerdere scenario's. Met aangepaste serialisatie u bijvoorbeeld:

* Bepaalde eigenschappen van uw modelklasse opnemen of uitsluiten als documentvelden.
* Kaart tussen eigenschapsnamen in uw code en veldnamen in uw index.
* Maak aangepaste kenmerken die kunnen worden gebruikt voor toewijzingseigenschappen aan documentvelden.

Voorbeelden van het implementeren van aangepaste serialisatie vindt u in de eenheidstests voor de Azure Cognitive Search .NET SDK op GitHub. Een goed uitgangspunt is [deze map.](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models) Het bevat klassen die worden gebruikt door de aangepaste serialisatietests.

### <a name="searching-for-documents-in-the-index"></a>Zoeken naar documenten in de index
De laatste stap in de voorbeeldtoepassing is het zoeken naar enkele documenten in de index:

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

Elke keer dat een query wordt uitgevoerd, `SearchParameters` maakt deze methode eerst een nieuw object. Dit object wordt gebruikt om extra opties voor de query op te geven, zoals sorteren, filteren, paging en faceting. Met deze methode stellen we `Filter` `Select`de `OrderBy`eigenschap `Top` , , en eigenschap in voor verschillende query's. Alle `SearchParameters` eigenschappen zijn [hier](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)gedocumenteerd.

De volgende stap is om de zoekopdracht daadwerkelijk uit te voeren. Het uitvoeren van de `Documents.Search` zoekopdracht gebeurt met behulp van de methode. Voor elke query geven we de zoektekst door `"*"` die we als tekenreeks willen gebruiken (of als er geen zoektekst is), plus de eerder gemaakte zoekparameters. We specificeren `Hotel` ook als `Documents.Search`de typeparameter voor , die de SDK vertelt om `Hotel`documenten in de zoekresultaten te deserialiseren in objecten van het type .

> [!NOTE]
> Hier vindt u meer informatie over de syntaxis van de [queryexpressie.](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)
> 
> 

Ten slotte, na elke query deze methode iterates door alle wedstrijden in de zoekresultaten, het afdrukken van elk document op de console:

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

Laten we eens een kijkje nemen op elk van de query's op zijn beurt. Hier is de code om de eerste query uit te voeren:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

In dit geval zoeken we de hele index naar het woord "motel" in elk doorzoekbaar veld `Select` en willen we alleen de hotelnamen ophalen, zoals gespecificeerd door de parameter. Hier zijn de resultaten:

    Name: Secret Point Motel

    Name: Twin Dome Motel

De volgende vraag is een beetje interessanter.  We willen hotels vinden die een kamer hebben met een nachttarief van minder dan $ 100 en alleen de hotel-ID en beschrijving retourneren:

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

Deze query gebruikt `$filter` een `Rooms/any(r: r/BaseRate lt 100)`OData-expressie om de documenten in de index te filteren. Dit maakt gebruik van de [operator](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) om de 'BaseRate lt 100' toe te passen op elk item in de Kamers collectie. Hier vindt u meer informatie over de syntaxis van OData die Azure Cognitive Search [ondersteunt.](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)

Hier zijn de resultaten van de query:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Vervolgens willen we de twee beste hotels vinden die onlangs zijn gerenoveerd en de naam van het hotel en de laatste renovatiedatum laten zien. Dit is de code: 

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

In dit geval gebruiken we opnieuw de `OrderBy` syntaxis van OData om de parameter op te geven als `lastRenovationDate desc`. We hebben `Top` ook ingesteld op 2 om ervoor te zorgen dat we alleen de bovenste twee documenten te krijgen. Net als voorheen `Select` stellen we aan welke velden moeten worden geretourneerd.

Hier zijn de resultaten:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Tot slot willen we alle hotels namen die overeenkomen met het woord "hotel" te vinden:

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

En hier zijn de resultaten, die alle velden `Select` omvatten, omdat we niet de eigenschap te specificeren:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Deze stap voltooit de tutorial, maar stop hier niet. **Volgende stappen bieden aanvullende bronnen voor meer informatie over Azure Cognitive Search.

## <a name="next-steps"></a>Volgende stappen
* Neem de referenties voor de [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) en [REST API](https://docs.microsoft.com/rest/api/searchservice/) door.
* Bekijk [naamgevingsconventies](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) om de regels voor het benoemen van verschillende objecten te leren.
* Controleer [ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) in Azure Cognitive Search.
