---
title: 'C# Zelfstudie: meerdere gegevensbronnen indexeren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het importeren van gegevens uit meerdere gegevensbronnen naar één Azure Cognitive Search-index met behulp van indexeerders. Deze zelfstudie en voorbeeldcode staan in C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271485"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Zelfstudie: Gegevens uit meerdere gegevensbronnen in C indexeren #

Azure Cognitive Search kan gegevens uit meerdere gegevensbronnen importeren, analyseren en indexeren in één geconsolideerde zoekindex. Dit ondersteunt situaties waarin gestructureerde gegevens worden samengevoegd met minder gestructureerde of zelfs platte tekstgegevens uit andere bronnen, zoals tekst-, HTML- of JSON-documenten.

In deze zelfstudie wordt beschreven hoe u hotelgegevens uit een Azure Cosmos DB-gegevensbron indexeert en deze samenvoegt met hotelkamergegevens die zijn afkomstig van Azure Blob Storage-documenten. Het resultaat is een gecombineerde hotelzoekindex met complexe gegevenstypen.

Deze zelfstudie maakt gebruik van C# en de [.NET SDK](https://aka.ms/search-sdk). In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Voorbeeldgegevens uploaden en gegevensbronnen maken
> * De documentsleutel identificeren
> * De index definiëren en maken
> * Hotelgegevens van Azure Cosmos DB indexeren
> * Hotelkamergegevens uit blob-opslag samenvoegen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Een](search-create-service-portal.md) [bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) maken of zoeken 

> [!Note]
> U gebruik maken van de gratis service voor deze tutorial. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerders en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u ruimte hebt op uw service om de nieuwe bronnen te accepteren.

## <a name="download-files"></a>Bestanden downloaden

Broncode voor deze zelfstudie bevindt zich in de [GitHub-opslagplaats azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) in de map [met meerdere gegevensbronnen.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)

## <a name="1---create-services"></a>1 - Services maken

In deze zelfstudie wordt Azure Cognitive Search gebruikt voor indexering en query's, Azure Cosmos DB voor één gegevensset en Azure Blob-opslag voor de tweede gegevensset. 

Maak indien mogelijk alle services in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kunnen uw diensten in elke regio zijn.

Dit voorbeeld maakt gebruik van twee kleine sets van gegevens die zeven fictieve hotels beschrijven. Een set beschrijft de hotels zelf en wordt geladen in een Azure Cosmos DB-database. De andere set bevat hotelkamerdetails en is beschikbaar als zeven afzonderlijke JSON-bestanden die moeten worden geüpload naar Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Begin met Cosmos DB

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en navigeer vervolgens door de pagina Overzicht van het Azure Cosmos DB-account.

1. Selecteer **Gegevensverkenner** en selecteer **Vervolgens Nieuwe database**.

   ![Een nieuwe database maken](media/tutorial-multiple-data-sources/cosmos-newdb.png "Een nieuwe database maken")

1. Voer de naam **hotel-rooms-db**in. Accepteer standaardwaarden voor de overige instellingen.

   ![Database configureren](media/tutorial-multiple-data-sources/cosmos-dbname.png "Database configureren")

1. Maak een nieuwe container. Gebruik de bestaande database die u zojuist hebt gemaakt. Voer **hotels** in voor de containernaam en gebruik **/HotelId** voor de partitiesleutel.

   ![Container toevoegen](media/tutorial-multiple-data-sources/cosmos-add-container.png "Container toevoegen")

1. Selecteer **Items** onder **hotels**en klik op **Item uploaden** op de opdrachtbalk. Navigeer naar en selecteer vervolgens het bestand **cosmosdb/HotelsDataSubset_CosmosDb.json** in de projectmap.

   ![Uploaden naar Azure Cosmos DB-verzameling](media/tutorial-multiple-data-sources/cosmos-upload.png "Uploaden naar Cosmos DB-verzameling")

1. Gebruik de knop Vernieuwen om uw weergave van de items in de hotelscollectie te vernieuwen. Er worden zeven nieuwe databasedocumenten weergegeven.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)navigeer naar uw Azure-opslagaccount, klik op **Blobs**en klik vervolgens op **+ Container**.

1. [Maak een blobcontainer](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) met de naam hotelkamers om de JSON-bestanden van de voorbeeldhotelkamer op te slaan. **hotel-rooms** U het niveau voor openbare toegang instellen op een van de geldige waarden.

   ![Een blob-container maken](media/tutorial-multiple-data-sources/blob-add-container.png "Een blob-container maken")

1. Nadat de container is gemaakt, opent u deze en selecteert **u Uploaden** op de opdrachtbalk. Navigeer naar de map met de voorbeeldbestanden. Selecteer ze allemaal en klik op **Uploaden.**

   ![Bestanden uploaden](media/tutorial-multiple-data-sources/blob-upload.png "Bestanden uploaden")

Nadat de upload is voltooid, moeten de bestanden worden weergegeven in de lijst voor de gegevenscontainer.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De derde component is Azure Cognitive Search, die u [maken in de portal.](search-create-service-portal.md) U de gratis laag gebruiken om deze walkthrough te voltooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een api-sleutel voor beheerders en URL ophalen voor Azure Cognitive Search

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangssleutel nodig. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal de querysleutel ook. Het is een aanbevolen manier om queryaanvragen uit te geven met alleen-lezen toegang.

   ![De servicenaam en -beheerder en querysleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2 - Uw omgeving instellen

1. Start Visual Studio 2019 en selecteer in het menu **Extra** **NuGet Package Manager** en beheer **nugetpakketten voor oplossing...**. 

1. Zoek **op het** tabblad Bladeren naar en installeer **vervolgens Microsoft.Azure.Search** (versie 9.0.1 of hoger). U moet door aanvullende dialogen klikken om de installatie te voltooien.

    ![NuGet gebruiken om Azure-bibliotheken toe te voegen](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Zoek naar het **Microsoft.Extensions.Configuration.Json** NuGet-pakket en installeer het ook.

1. Open het oplossingsbestand **AzureSearchMultipleDataSources.sln**.

1. Bewerk in Solution Explorer het bestand **appsettings.json** om verbindingsgegevens toe te voegen.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

De eerste twee vermeldingen gebruiken de URL- en beheerderssleutels voor uw Azure Cognitive Search-service. Gezien een eindpunt `https://mydemo.search.windows.net`van bijvoorbeeld de te verstrekken `mydemo`servicenaam is .

In de volgende vermeldingen worden accountnamen en verbindingstekenreeksgegevens opgegeven voor de azure blob-opslag- en Azure Cosmos DB-gegevensbronnen.

## <a name="3---map-key-fields"></a>3 - Belangrijke velden toewijzen

Het samenvoegen van inhoud vereist dat beide gegevensstromen zich richten op dezelfde documenten in de zoekindex. 

In Azure Cognitive Search identificeert het sleutelveld op unieke wijze elk document. Elke zoekindex moet precies één `Edm.String`belangrijk veld van het type hebben. Dat sleutelveld moet voor elk document aanwezig zijn in een gegevensbron die aan de index wordt toegevoegd. (In feite is het het enige vereiste veld.)

Controleer bij het indexeren van gegevens uit meerdere gegevensbronnen of elke binnenkomende rij of document een gemeenschappelijke documentsleutel bevat om gegevens uit twee fysiek verschillende brondocumenten samen te voegen tot een nieuw zoekdocument in de gecombineerde index. 

Het vereist vaak een vooraf gepland planning om een zinvolle documentsleutel voor uw index te identificeren en ervoor te zorgen dat deze in beide gegevensbronnen bestaat. In deze demo `HotelId` is de sleutel voor elk hotel in Cosmos DB ook aanwezig in de kamers JSON blobs in Blob opslag.

Azure Cognitive Search-indexeerders kunnen veldtoewijzingen gebruiken om gegevensvelden tijdens het indexeringsproces te hernoemen en zelfs te hervormen, zodat brongegevens naar het juiste indexveld kunnen worden geleid. In Cosmos DB wordt bijvoorbeeld de **`HotelId`** hotel-id genoemd . Maar in de JSON blob bestanden voor de **`Id`** hotelkamers, het hotel identifier is vernoemd. Het programma verwerkt dit **`Id`** door het veld **`HotelId`** van de blobs in kaart te brengen naar het sleutelveld in de index.

> [!NOTE]
> In de meeste gevallen maken automatisch gegenereerde documentsleutels, zoals die standaard door sommige indexeerders, geen goede documentsleutels voor gecombineerde indexen. In het algemeen wilt u een zinvolle, unieke sleutelwaarde gebruiken die al bestaat in of gemakkelijk kan worden toegevoegd aan uw gegevensbronnen.

## <a name="4---explore-the-code"></a>4 - Ontdek de code

Zodra de gegevens- en configuratie-instellingen zijn geïnstalleerd, moet het voorbeeldprogramma in **AzureSearchMultipleDataSources.sln** klaar zijn om te bouwen en uit te voeren.

Deze eenvoudige C#/.NET-console-app voert de volgende taken uit:

* Hiermee maakt u een nieuwe index op basis van de gegevensstructuur van de klasse C# Hotel (die ook verwijst naar de klassen Adres en Kamer).
* Hiermee maakt u een nieuwe gegevensbron en een indexerdie Azure Cosmos DB-gegevens in kaart brengt om velden te indexeren. Dit zijn beide objecten in Azure Cognitive Search.
* Voert de indexer om hotelgegevens van Cosmos DB te laden.
* Hiermee maakt u een tweede gegevensbron en een indexerdie JSON-blobgegevens inkaart om velden te indexeren.
* Als u de tweede indexer uitvoert die de gegevens van Kamers uit Blob-opslag laadt.

 Voordat u het programma uitvoert, neemt u een minuut de tijd om de code en de index- en indexerdefinities voor dit voorbeeld te bestuderen. De relevante code staat in twee bestanden:

  + **Hotel.cs** bevat het schema dat de index definieert
  + **Program.cs** functies bevat die de Azure Cognitive Search-index, gegevensbronnen en indexeerders maken en de gecombineerde resultaten in de index laden.

### <a name="create-an-index"></a>Een index maken

Dit voorbeeldprogramma gebruikt de .NET SDK om een Azure Cognitive Search-index te definiëren en te maken. Het maakt gebruik van de klasse [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) om een indexstructuur te genereren vanuit een C#-gegevensmodelklasse.

Het gegevensmodel wordt gedefinieerd door de klasse Hotel, die ook verwijzingen bevat naar de klassen Adres en Kamer. De FieldBuilder zoomt in met meerdere klassendefinities om een complexe gegevensstructuur voor de index te genereren. Metagegevenstags worden gebruikt om de kenmerken van elk veld te definiëren, zoals of het doorzoekbaar of sorteerbaar is.

In de volgende fragmenten uit het **Hotel.cs-bestand** u zien hoe één veld en een verwijzing naar een andere gegevensmodelklasse kunnen worden opgegeven.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

In het **Program.cs** bestand wordt de index gedefinieerd met een `FieldBuilder.BuildForType<Hotel>()` naam en een veldverzameling die door de methode wordt gegenereerd en vervolgens als volgt gemaakt:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB-gegevensbron en -indexermaken

Vervolgens bevat het hoofdprogramma logica om de Azure Cosmos DB-gegevensbron voor de hotelsgegevens te maken.

Eerst wordt de naam azure cosmos DB-database aan de verbindingstekenreeks samengevoegd. Vervolgens definieert het gegevensbronobject, inclusief instellingen die specifiek zijn voor Azure Cosmos DB-bronnen, zoals de eigenschap [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Nadat de gegevensbron is gemaakt, stelt het programma een Azure Cosmos DB-indexeerder in met de naam **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Het programma verwijdert bestaande indexeerders met dezelfde naam voordat u de nieuwe maakt, voor het geval u dit voorbeeld meerdere tijd wilt uitvoeren.

In dit voorbeeld wordt een schema voor de indexer gedefinieerd, zodat deze eenmaal per dag wordt uitgevoerd. U de eigenschap planning uit dit gesprek verwijderen als u niet wilt dat de indexer in de toekomst automatisch opnieuw wordt uitgevoerd.

### <a name="index-azure-cosmos-db-data"></a>Azure Cosmos DB-gegevens indexeren

Zodra de gegevensbron en de indexer zijn gemaakt, is de code die de indexer uitvoert kort:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Dit voorbeeld bevat een eenvoudig try-catch-blok om eventuele fouten te melden die kunnen optreden tijdens de uitvoering.

Nadat de Azure Cosmos DB-indexer is uitgevoerd, bevat de zoekindex een volledige set voorbeeldhoteldocumenten. Het gebied kamers voor elk hotel is echter een lege array, omdat de Azure Cosmos DB-gegevensbron geen kamerdetails bevatte. Vervolgens trekt het programma uit Blob-opslag om de ruimtegegevens te laden en samen te voegen.

### <a name="create-blob-storage-data-source-and-indexer"></a>Blob-opslaggegevensbron en -indexer maken

Om de kamerdetails te krijgen, stelt het programma eerst een Blob-opslaggegevensbron in om te verwijzen naar een set afzonderlijke JSON-blobbestanden.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Nadat de gegevensbron is gemaakt, stelt het programma een blob-indexer in met de naam **hotel-rooms-blob-indexer .**

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

De JSON-blobs bevatten **`Id`** een **`HotelId`** sleutelveld met de naam in plaats van . De code `FieldMapping` gebruikt de klasse om de **`Id`** indexervoor **`HotelId`** te vertellen de veldwaarde naar de documentsleutel in de index te leiden.

Blob-opslagindexeerders kunnen parameters gebruiken die de te gebruiken parsingsmodus identificeren. De parsing-modus verschilt voor blobs die één document vertegenwoordigen of meerdere documenten binnen dezelfde blob. In dit voorbeeld vertegenwoordigt elke blob één indexdocument, `IndexingParameters.ParseJson()` zodat de code de parameter gebruikt.

Zie [Index JSON blobs](search-howto-index-json-blobs.md)voor meer informatie over indexerparameters voor JSON-blobs. Zie de klasse [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) voor meer informatie over het opgeven van deze parameters met behulp van de .NET SDK.

Het programma verwijdert bestaande indexeerders met dezelfde naam voordat u de nieuwe maakt, voor het geval u dit voorbeeld meerdere tijd wilt uitvoeren.

In dit voorbeeld wordt een schema voor de indexer gedefinieerd, zodat deze eenmaal per dag wordt uitgevoerd. U de eigenschap planning uit dit gesprek verwijderen als u niet wilt dat de indexer in de toekomst automatisch opnieuw wordt uitgevoerd.

### <a name="index-blob-data"></a>Blobgegevens indexeren

Zodra de blob-opslaggegevensbron en -indexer zijn gemaakt, is de code waarop de indexer wordt uitgevoerd eenvoudig:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Omdat de index al is gevuld met hotelgegevens uit de Azure Cosmos DB-database, werkt de blob-indexer de bestaande documenten in de index bij en voegt de kamerdetails toe.

> [!NOTE]
> Als u dezelfde niet-sleutelvelden in beide gegevensbronnen hebt en de gegevens in die velden niet overeenkomen, bevat de index de waarden van welke indexer het meest recent is uitgevoerd. In ons voorbeeld bevatten beide gegevensbronnen een **hotelnaamveld.** Als de gegevens in dit veld om de een of andere reden verschillend zijn, voor documenten met dezelfde sleutelwaarde, zijn de **HotelName-gegevens** van de gegevensbron die het meest recent is geïndexeerd, de waarde die in de index is opgeslagen.

## <a name="5---search"></a>5 - Zoeken

U de gevulde zoekindex verkennen nadat het programma is uitgevoerd met behulp van de [**zoekverkenner**](search-explorer.md) in de portal.

Open in azure-portal de pagina **Overzicht** van de zoekservice en zoek de **index voor hotelkamers-voorbeeld** in de lijst **Indexen.**

  ![Lijst met Azure Cognitive Search-indexen](media/tutorial-multiple-data-sources/index-list.png "Lijst met Azure Cognitive Search-indexen")

Klik op de index van het hotel-kamers-voorbeeld in de lijst. U ziet een Search Explorer-interface voor de index. Voer een query in voor een term als 'Luxe'. U moet ten minste één document in de resultaten zien en in dit document moet een lijst met ruimteobjecten in de array kamers worden weergegeven.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele ontwikkelingsfase is de meest praktische benadering voor ontwerpiteratie het verwijderen van de objecten uit Azure Cognitive Search en uw code in staat te stellen ze opnieuw op te bouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

De voorbeeldcode voor deze zelfstudie controleert op bestaande objecten en verwijdert deze, zodat u uw code opnieuw uitvoeren.

U de portal ook gebruiken om indexen, indexeerders en gegevensbronnen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling Alle resources of Resourcegroepen in het linkernavigatiedeelvenster.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met het concept van het innemen van gegevens uit meerdere bronnen, laten we eens een kijkje nemen op indexer configuratie, te beginnen met Cosmos DB.

> [!div class="nextstepaction"]
> [Een Azure Cosmos DB-indexer configureren](search-howto-index-cosmosdb.md)