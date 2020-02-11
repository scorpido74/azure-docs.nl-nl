---
title: 'Snelstartgids: een zoek index maken met C# behulp van .net'
titleSuffix: Azure Cognitive Search
description: In deze C# Quick Start vindt u informatie over het maken van een index, het laden van gegevens en het uitvoeren van query's met behulp van de Azure COGNITIVE Search .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: e9f43f729fc9e08054cd160b82945ee4bcd5821c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121658"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Snelstartgids: een Azure Cognitive Search-index maken C# in met behulp van de .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Maak een .NET core C# -console toepassing die een Azure Cognitive search-index maakt, laadt en opvraagt met Visual Studio en de [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). In dit artikel wordt uitgelegd hoe u de stap per stap van de toepassing maakt. U kunt ook [de volledige toepassing downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> In de demo code in dit artikel wordt gebruikgemaakt van de synchrone methoden van de Azure Cognitive Search .NET SDK voor eenvoud. Voor productie scenario's kunt u echter het beste de asynchrone methoden in uw eigen toepassingen gebruiken om ze schaalbaar en responsief te maken. U kunt bijvoorbeeld `CreateAsync` en `DeleteAsync` gebruiken in plaats van `Create` en `Delete`.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogram ma's zijn vereist voor deze Quick Start.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), een wille keurige versie. Voor beelden van code en instructies zijn getest in de gratis Community-editie.

+ [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Aanroepen naar de service vereisen een URL-eind punt en een toegangs sleutel voor elke aanvraag. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de query sleutel op. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van Visual Studio en het maken van een nieuw console-app-project dat kan worden uitgevoerd op .NET core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

De [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) bestaat uit een aantal client bibliotheken die worden gedistribueerd als NuGet-pakketten.

Voor dit project gebruikt u versie 9 van het `Microsoft.Azure.Search` NuGet-pakket en het meest recente `Microsoft.Extensions.Configuration.Json` NuGet-pakket.

1. Selecteer in **Hulpprogram ma's** > **NuGet package manager** **NuGet-pakketten beheren voor oplossing...** . 

1. Klik op **Bladeren**.

1. Zoek naar `Microsoft.Azure.Search` en selecteer versie 9.0.1 of hoger.

1. Klik aan de rechter kant op **installeren** om de assembly toe te voegen aan uw project en de oplossing.

1. Herhaal dit voor `Microsoft.Extensions.Configuration.Json`en selecteer versie 2.2.0 of hoger.


### <a name="add-azure-cognitive-search-service-information"></a>Informatie over Azure Cognitive Search-service toevoegen

1. Klik in Solution Explorer met de rechter muisknop op het project en selecteer > **Nieuw item toevoegen...** . 

1. In nieuw item toevoegen zoekt u naar ' JSON ' om een JSON-gerelateerde lijst met item typen te retour neren.

1. Kies **JSON-bestand**, noem het bestand appSettings. json en klik op **toevoegen**. 

1. Voeg het bestand toe aan de uitvoermap. Klik met de rechter muisknop op appSettings. json en selecteer **Eigenschappen**. Selecteer in **kopiëren naar uitvoermap**de optie **kopiëren indien nieuwer**.

1. Kopieer de volgende JSON naar het nieuwe JSON-bestand. Vervang de naam van de zoek service (YOUR-SEARCH-SERVICE-NAME) en de beheer-API-sleutel (uw-beheer-API-sleutel) met geldige waarden. Als uw service-eind punt `https://mydemo.search.windows.net`, zou de service naam ' mydemo ' zijn.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Klasse toevoegen. Methode ' bestanden voor uw project

Wanneer de resultaten naar het console venster worden afgedrukt, moeten afzonderlijke velden uit het object Hotel als teken reeksen worden geretourneerd. U kunt [toString ()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) implementeren om deze taak uit te voeren, waarbij u de benodigde code kopieert naar twee nieuwe bestanden.

1. Voeg twee lege klassedefinities toe aan uw project: Address.Methods.cs, Hotel.Methods.cs

1. In Address.Methods.cs overschrijft u de standaard inhoud met de volgende code, [regel 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. In Hotel.Methods.cs kopieert u [regels 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1-index maken

De hotels index bestaat uit eenvoudige en complexe velden, waarbij een eenvoudig veld "naam Hotel" of "Beschrijving" is, en complexe velden een adres met subvelden zijn of een verzameling van kamers. Wanneer een index complexe typen bevat, isoleert u de complexe veld definities in afzonderlijke klassen.

1. Voeg twee lege klassedefinities toe aan uw project: Address.cs, Hotel.cs

1. In Address.cs overschrijft u de standaard inhoud met de volgende code:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. In Hotel.cs definieert de klasse de algemene structuur van de index, met inbegrip van verwijzingen naar de adres klasse.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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
        }
    }
    ```

    Kenmerken van het veld bepalen hoe het wordt gebruikt in een toepassing. Het `IsSearchable` kenmerk moet bijvoorbeeld worden toegewezen aan elk veld dat moet worden opgenomen in een zoek opdracht in volledige tekst. 
    
    > [!NOTE]
    > In de .NET SDK moeten velden expliciet worden voorzien van het kenmerk [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)en [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Dit gedrag is in tegens telling tot de REST API waarmee impliciete toewijzing wordt ingeschakeld op basis van het gegevens type (bijvoorbeeld: eenvoudige teken reeks velden zijn automatisch doorzoekbaar).

    Precies één veld in de index van het type `string` moet het *sleutel* veld zijn, waarbij elk document uniek wordt geïdentificeerd. In dit schema wordt de sleutel `HotelId`.

    In deze index gebruiken de beschrijvings velden de optionele eigenschap [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) , opgegeven als u de standaard standaard-lucene Analyzer wilt overschrijven. In het veld `description_fr` wordt gebruikgemaakt van de Franse[FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)(Frans/lucene Analyzer), omdat de Franse tekst wordt opgeslagen. De `description` maakt gebruik van de optionele micro soft language Analyzer ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. Maak in Program.cs een instantie van de klasse [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) om verbinding te maken met de service, met behulp van waarden die zijn opgeslagen in het configuratie bestand van de toepassing (appSettings. json). 

   `SearchServiceClient` heeft een [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) -eigenschap met alle methoden die u nodig hebt om Azure Cognitive Search-indexen te maken, weer te geven, bij te werken of te verwijderen. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Als dat mogelijk is, kunt u één exemplaar van `SearchServiceClient` in uw toepassing delen om te voor komen dat er te veel verbindingen worden geopend. Klassen methoden zijn thread-veilig om dergelijke delen mogelijk te maken.

   De klasse heeft verschillende constructors. De gewenste constructor krijgt u de naam van uw zoekservice en een `SearchCredentials`-object als parameters. `SearchCredentials` verpakt uw api-sleutel.

    In de index definitie is de eenvoudigste manier om de `Field`-objecten te maken, door het aanroepen van de `FieldBuilder.BuildForType`-methode, waarbij een model klasse wordt door gegeven voor de type parameter. Een modelklasse heeft eigenschappen die worden toegewezen aan de velden van uw index. Met deze toewijzing kunt u documenten uit uw zoek index binden aan exemplaren van uw model klasse.

    > [!NOTE]
    > Als u niet van plan bent om een modelklasse te gebruiken, moet u alsnog de index definiëren door rechtstreeks `Field`-objecten te maken. U kunt de naam van het veld aan de constructor doorgeven, samen met het gegevenstype (of analyse voor tekenreeksvelden). U kunt ook andere eigenschappen instellen, zoals `IsSearchable`, `IsFilterable`, om een paar te noemen.
    >

1. Druk op F5 om de app te bouwen en de index te maken. 

    Als het project is gebouwd, wordt een console venster geopend, waarbij status berichten worden geschreven naar het scherm voor het verwijderen en maken van de index. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-documenten laden

In azure Cognitive Search zijn documenten gegevens structuren die beide invoer zijn voor het indexeren en uitvoeren van query's. De invoer van documenten kan worden opgehaald uit een externe gegevens bron en kan rijen zijn in een Data Base, blobs in Blob-opslag of JSON-documenten op schijf. In dit voor beeld maken we een snelkoppeling en sluiten we JSON-documenten voor vier hotels in de code zelf. 

Bij het uploaden van documenten moet u een [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) -object gebruiken. Een `IndexBatch` bevat een verzameling [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) -objecten, elk met een document en een eigenschap waarmee Azure Cognitive Search welke actie moet worden uitgevoerd ([uploaden, samen voegen, verwijderen en mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Maak in Program.cs een matrix met documenten en index acties, en geef de matrix door aan `IndexBatch`. De onderstaande documenten voldoen aan de Quick Start-index van het Hotel, zoals gedefinieerd door de hotels en adres klassen.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Zodra u het`IndexBatch`-object initialiseert, kunt u het naar de index verzenden door [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) aan te roepen in uw [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) -object. `Documents` is een eigenschap van `SearchIndexClient` die methoden biedt voor het toevoegen, wijzigen, verwijderen of opvragen van documenten in uw index.

    Het `try`/`catch` rond de aanroep van de methode voor `Index` het indexeren van index fouten, wat kan gebeuren als uw service zwaar wordt belast. In productie code kunt u de documenten die zijn mislukt, uitstellen en vervolgens opnieuw proberen te indexeren, of het logboek op een andere manier afhandelen die voldoet aan de vereisten voor gegevens consistentie van uw toepassing.

    De 2-seconden-vertraging compenseert voor indexering, die asynchroon is, zodat alle documenten kunnen worden geïndexeerd voordat de query's worden uitgevoerd. Het coderen van een vertraging is doorgaans alleen nodig in demo's, testen en voorbeeld toepassingen.

1. In Program.cs verwijdert u in Main de regels voor "2 documenten laden". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Druk op F5 om de app opnieuw te bouwen. 

    Als het project is gebouwd, wordt er een console venster geopend, waarin status berichten worden geschreven, met een bericht over het uploaden van documenten. In de Azure Portal, op de pagina **overzicht** van zoek services, moeten de Snelstartgids-index nu vier documenten hebben.

Ga voor meer informatie over document verwerking naar ["hoe de .NET SDK documenten verwerkt"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

U kunt query resultaten ophalen zodra het eerste document is geïndexeerd, maar de werkelijke tests van uw index moeten wachten tot alle documenten zijn geïndexeerd. 

In deze sectie worden twee soorten functionaliteit toegevoegd: query logica en resultaten. Gebruik voor query's de methode [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) . Met deze methode worden Zoek tekst en andere [para meters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)gebruikt. 

De klasse [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) vertegenwoordigt de resultaten.


1. Maak in Program.cs een WriteDocuments-methode waarmee Zoek resultaten in de-console worden afgedrukt.

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

1. Maak een RunQueries-methode om query's uit te voeren en resultaten te retour neren. De resultaten zijn hotels-objecten. U kunt de para meter Select gebruiken voor het Opper vlak van afzonderlijke velden. Als een veld niet is opgenomen in de para meter Select, wordt de bijbehorende eigenschap Hotel null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Er zijn twee [manieren om te voldoen aan de voor waarden in een query](search-query-overview.md#types-of-queries): zoeken in volledige tekst en filters. Een zoek opdracht in volledige tekst zoekt naar een of meer voor waarden in `IsSearchable` velden in uw index. Een filter is een booleaanse expressie die wordt geëvalueerd ten opzichte van `IsFilterable` velden in een index. U kunt zoeken in volledige tekst en filters samen of afzonderlijk gebruiken.

    Zoekopdrachten en filters worden allebei uitgevoerd met behulp van de `Documents.Search`-methode. Een zoekopdracht kan worden doorgegeven aan de `searchText`-parameter, terwijl een filterexpressie kan worden doorgegeven in de eigenschap `Filter` van de `SearchParameters`-klasse. Als u wilt filteren zonder te zoeken, geeft u `"*"` door voor de `searchText`-parameter. Om te zoeken zonder te filteren, stelt u de eigenschap `Filter` niet in of geeft u niet door aan een `SearchParameters`-instantie.

1. In Program.cs, in Main, de regels voor ' 3-Search ' oncommentaar. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. De oplossing is nu voltooid. Druk op F5 om de app opnieuw samen te stellen en het programma volledig uit te voeren. 

    De uitvoer bevat dezelfde berichten als voorheen, met toevoeging van query-informatie en-resultaten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze C# Quick Start hebt u een reeks taken uitgevoerd voor het maken van een index, het laden van documenten en het uitvoeren van query's. In verschillende fasen hebben we snelkoppelingen nodig om de code te vereenvoudigen voor lees baarheid en lees vaardigheid. Als u vertrouwd bent met de basis concepten, raden we u aan het volgende artikel te verkennen voor een onderzoek naar alternatieve benaderingen en concepten waarmee uw kennis wordt uitgediept. 

De voorbeeld code en index zijn uitgebreide versies van deze versie. In het volgende voor beeld wordt een verzameling ruimtes toegevoegd, worden verschillende klassen en acties gebruikt en wordt de verwerking van de werk wijze nader bekeken.

> [!div class="nextstepaction"]
> [Ontwikkelen in .NET](search-howto-dotnet-sdk.md)