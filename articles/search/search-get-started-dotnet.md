---
title: 'Snelstart: een zoekindex maken in C# met .NET'
titleSuffix: Azure Cognitive Search
description: In deze C# quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert met de Azure Cognitive Search .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77589212"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Snelstart: een Azure Cognitive Search-index maken in C# met behulp van de .NET SDK
> [!div class="op_single_selector"]
> * [C #](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Maak een .NET Core C#-consoletoepassing die een Azure Cognitive Search-index maakt, laadt en opvraagt met behulp van Visual Studio en de [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). In dit artikel wordt uitgelegd hoe u de toepassing stap voor stap maakt. U ook [de volledige toepassing downloaden en uitvoeren.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

> [!NOTE]
> De democode in dit artikel gebruikt de synchrone methoden van de Azure Cognitive Search .NET SDK voor eenvoud. Voor productiescenario's raden we echter aan om de asynchrone methoden in uw eigen toepassingen te gebruiken om ze schaalbaar en responsief te houden. Bijvoorbeeld, je zou `CreateAsync` `DeleteAsync` kunnen `Create` gebruiken `Delete`en in plaats van en .

## <a name="prerequisites"></a>Vereisten

De volgende services en tools zijn vereist voor deze quickstart.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), elke editie. Voorbeeldcode en instructies werden getest op de gratis communautaire editie.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Voor oproepen naar de service is een URL-eindpunt en een toegangssleutel vereist voor elk verzoek. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal de querysleutel ook. Het is een aanbevolen manier om queryaanvragen uit te geven met alleen-lezen toegang.

![Een HTTP-eindpunt en toegangssleutel downloaden](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel downloaden")

Voor alle aanvragen is een api-sleutel vereist voor elk verzoek dat naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van Visual Studio en het maken van een nieuw Console App-project dat kan worden uitgevoerd op .NET Core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

De [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) bestaat uit een aantal clientbibliotheken die worden gedistribueerd als NuGet-pakketten.

Gebruik voor dit project versie `Microsoft.Azure.Search` 9 van het `Microsoft.Extensions.Configuration.Json` NuGet-pakket en het nieuwste NuGet-pakket.

1. Selecteer **NuGet-pakketten beheren voor oplossing in** **Tools** > **NuGet Package Manager.** 

1. Klik op **Bladeren**.

1. Zoeken `Microsoft.Azure.Search` naar versie 9.0.1 of hoger.

1. Klik op **Installeren** aan de rechterkant om de assemblage toe te voegen aan uw project en oplossing.

1. Herhaal `Microsoft.Extensions.Configuration.Json`dit voor , het selecteren van versie 2.2.0 of hoger.


### <a name="add-azure-cognitive-search-service-information"></a>Azure Cognitive Search-servicegegevens toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op het project en selecteer Nieuw item **toevoegen...** > **New Item...** . 

1. Zoek in Nieuw item toevoegen naar 'JSON' om een JSON-gerelateerde lijst met itemtypen terug te sturen.

1. Kies **JSON-bestand**, geef het bestand een naam "appsettings.json" en klik op **Toevoegen**. 

1. Voeg het bestand toe aan uw uitvoermap. Klik met de rechtermuisknop op appsettings.json en selecteer **Eigenschappen**. Selecteer Kopiëren **naar uitvoermap**de optie **Kopiëren als nieuwer**.

1. Kopieer het volgende JSON naar uw nieuwe JSON-bestand. Vervang de naam van de zoekservice (UW-ZOEK-SERVICE-NAAM) en de admin-API-sleutel (YOUR-ADMIN-API-KEY) door geldige waarden. Als uw service `https://mydemo.search.windows.net`eindpunt is, zou de service naam "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Voeg klasse toe ". Methode"-bestanden voor uw project

Wanneer de resultaten naar het consolevenster worden afgedrukt, moeten afzonderlijke velden van het object Hotel als tekenreeksen worden geretourneerd. U [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) implementeren om deze taak uit te voeren en de benodigde code naar twee nieuwe bestanden te kopiëren.

1. Voeg twee lege klassendefinities toe aan uw project: Address.Methods.cs, Hotel.Methods.cs

1. Overschrijven van de standaardinhoud met de volgende code in Address.Methods.cs de volgende code, [regels 1-32.](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32)

1. Kopieer in Hotel.Methods.cs [regels 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - Index maken

De hotelindex bestaat uit eenvoudige en complexe velden, waarbij een eenvoudig veld 'HotelName' of 'Beschrijving' is, en complexe velden een adres zijn met subvelden of een verzameling kamers. Wanneer een index complexe typen bevat, isoleert u de complexe velddefinities in afzonderlijke klassen.

1. Voeg twee lege klassendefinities toe aan uw project: Address.cs, Hotel.cs

1. Overschrijf in Address.cs de standaardinhoud met de volgende code:

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

1. In Hotel.cs definieert de klasse de algemene structuur van de index, inclusief verwijzingen naar de adresklasse.

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

    Kenmerken in het veld bepalen hoe het wordt gebruikt in een toepassing. Het `IsSearchable` kenmerk moet bijvoorbeeld worden toegewezen aan elk veld dat moet worden opgenomen in een zoekopdracht met volledige tekst. 
    
    > [!NOTE]
    > In de .NET SDK moeten velden [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)expliciet [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)worden [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet)toegeschreven als , , en . Dit gedrag staat in contrast met de REST API, die impliciet toeschrijving mogelijk maakt op basis van gegevenstype (eenvoudige tekenreeksvelden zijn bijvoorbeeld automatisch doorzoekbaar).

    Precies één veld in `string` uw typeindex moet het *sleutelveld* zijn, waarbij elk document op unieke wijze wordt geïdentificeerd. In dit schema is `HotelId`de sleutel .

    In deze index gebruiken de [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) beschrijvingsvelden de optionele eigenschap, opgegeven wanneer u de standaardLucene-analyzer wilt overschrijven. Het `description_fr` veld maakt gebruik van de Franse Lucene analyzer ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) omdat het slaat Franse tekst. Het `description` is met behulp van de optionele Microsoft taal analyzer ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. Maak in Program.cs een [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) instantie van de klasse om verbinding te maken met de service met behulp van waarden die zijn opgeslagen in het config-bestand van de toepassing (appsettings.json). 

   `SearchServiceClient`heeft [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) een eigenschap, die alle methoden biedt die u nodig hebt om Azure Cognitive Search-indexen te maken, aan te bieden, bij te werken of te verwijderen. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
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

    Deel indien mogelijk één `SearchServiceClient` exemplaar van uw toepassing om te voorkomen dat u te veel verbindingen opent. Klassemethoden zijn draadveilig om dergelijk delen mogelijk te maken.

   De klasse heeft verschillende constructeurs. De gewenste constructor krijgt u de naam van uw zoekservice en een `SearchCredentials`-object als parameters. `SearchCredentials` verpakt uw api-sleutel.

    In de indexdefinitie is de eenvoudigste `Field` manier om `FieldBuilder.BuildForType` de objecten te maken door de methode aan te roepen en een modelklasse voor de typeparameter door te geven. Een modelklasse heeft eigenschappen die worden toegewezen aan de velden van uw index. Met deze toewijzing u documenten uit uw zoekindex binden aan exemplaren van uw modelklasse.

    > [!NOTE]
    > Als u niet van plan bent om een modelklasse te gebruiken, moet u alsnog de index definiëren door rechtstreeks `Field`-objecten te maken. U kunt de naam van het veld aan de constructor doorgeven, samen met het gegevenstype (of analyse voor tekenreeksvelden). U ook andere `IsSearchable` `IsFilterable`eigenschappen instellen, zoals, om er een paar te noemen.
    >

1. Druk op F5 om de app te bouwen en de index te maken. 

    Als het project succesvol wordt opgebouwd, wordt een consolevenster geopend, waarin statusberichten naar het scherm worden geschreven voor het verwijderen en maken van de index. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

In Azure Cognitive Search zijn documenten gegevensstructuren die zowel invoer zijn voor indexering als uitvoer van query's. Zoals verkregen uit een externe gegevensbron, kunnen documentinvoer rijen in een database, blobs in Blob-opslag of JSON-documenten op schijf zijn. In dit voorbeeld nemen we een snelkoppeling en sluiten we JSON-documenten in voor vier hotels in de code zelf. 

Bij het uploaden van [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) documenten moet u een object gebruiken. Een `IndexBatch` bevat een [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) verzameling objecten, die elk een document en een eigenschap bevatten die Azure Cognitive Search vertelt welke actie moet worden uitgevoerd[(uploaden, samenvoegen, verwijderen en samenvoegenOf Uploaden).](search-what-is-data-import.md#indexing-actions)

1. Maak in Program.cs een reeks documenten en indexacties en `IndexBatch`geef de array door aan . De onderstaande documenten voldoen aan de hotel-quickstart-index, zoals gedefinieerd door de hotel- en adresklassen.

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

    Zodra u het`IndexBatch` object initialiseert, u het [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) naar [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) de index sturen door een beroep te doen op uw object. `Documents`is een `SearchIndexClient` eigenschap van die methoden biedt voor het toevoegen, wijzigen, verwijderen of opvragen van documenten in uw index.

    `try` / De `catch` omgeving van `Index` de aanroep naar de methode vangt indexeringsfouten op, wat kan gebeuren als uw service zwaar wordt belast. In productiecode u de documenten die zijn mislukt, uitstellen en vervolgens opnieuw proberen te indexeren of inloggen en doorgaan zoals het voorbeeld doet, of deze op een andere manier afhandelen die voldoet aan de vereisten voor gegevensconsistentie van uw toepassing.

    De vertraging van 2 seconden compenseert de indexering, die asynchroon is, zodat alle documenten kunnen worden geïndexeerd voordat de query's worden uitgevoerd. Coderen in een vertraging is meestal alleen nodig in demo's, tests en voorbeeldtoepassingen.

1. In Program.cs, in het algemeen, uncomment de lijnen voor "2 - Load documenten". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Druk op F5 om de app opnieuw op te bouwen. 

    Als het project wordt voltooid, wordt een consolevenster geopend, waarbij statusberichten worden geschreven, dit keer met een bericht over het uploaden van documenten. In de Azure-portal, op de pagina **Overzicht** van de zoekservice, moet de index voor hotels-quickstart nu 4 documenten hebben.

Zie ["Hoe de .NET SDK omgaat met documenten"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)voor meer informatie over documentverwerking.

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

U queryresultaten krijgen zodra het eerste document is geïndexeerd, maar het daadwerkelijk testen van uw index moet wachten tot alle documenten zijn geïndexeerd. 

In deze sectie worden twee onderdelen van functionaliteit toegevoegd: querylogica en resultaten. Voor query's [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) gebruikt u de methode. Deze methode neemt zoektekst en andere [parameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

De [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) klasse vertegenwoordigt de resultaten.


1. Maak in Program.cs een WriteDocuments-methode waarmee zoekresultaten worden afgedrukt op de console.

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

1. Maak een methode RunQueries om query's uit te voeren en resultaten te retourneren. Resultaten zijn hotelobjecten. U de parameter selecteren gebruiken om afzonderlijke velden weer te geven. Als een veld niet is opgenomen in de parameter select, wordt de bijbehorende eigenschap Hotel nietig verklaard.

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

    Er zijn twee [manieren om termen in een query te matchen:](search-query-overview.md#types-of-queries)full-text search en filters. Een zoekopdracht met volledige tekst zoekt naar `IsSearchable` een of meer termen in velden in uw index. Een filter is een booleaanse `IsFilterable` expressie die wordt geëvalueerd over velden in een index. U zoeken en filteren op volledige tekst samen of afzonderlijk gebruiken.

    Zoekopdrachten en filters worden allebei uitgevoerd met behulp van de `Documents.Search`-methode. Een zoekopdracht kan worden doorgegeven aan de `searchText`-parameter, terwijl een filterexpressie kan worden doorgegeven in de eigenschap `Filter` van de `SearchParameters`-klasse. Als u wilt filteren zonder te zoeken, geeft u `"*"` door voor de `searchText`-parameter. Om te zoeken zonder te filteren, stelt u de eigenschap `Filter` niet in of geeft u niet door aan een `SearchParameters`-instantie.

1. In Program.cs, in het algemeen, uncomment de lijnen voor "3 - Zoeken". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. De oplossing is nu klaar. Druk op F5 om de app opnieuw op te bouwen en het programma in zijn geheel uit te voeren. 

    Uitvoer bevat dezelfde berichten als voorheen, met toevoeging van query-informatie en resultaten.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze C# quickstart hebt u een reeks taken doorlopen om een index te maken, deze te laden met documenten en query's uit te voeren. In verschillende stadia namen we snelkoppelingen om de code te vereenvoudigen voor leesbaarheid en begrip. Als u vertrouwd bent met de basisconcepten, raden we het volgende artikel aan voor een verkenning van alternatieve benaderingen en concepten die uw kennis zullen verdiepen. 

De voorbeeldcode en -index zijn uitgebreide versies van deze code. Het volgende voorbeeld voegt een collectie Kamers toe, maakt gebruik van verschillende klassen en acties en neemt een kijkje in hoe verwerking werkt.

> [!div class="nextstepaction"]
> [Hoe te ontwikkelen in .NET](search-howto-dotnet-sdk.md)
