---
title: 'C#Zelf studie: meerdere gegevens bronnen indexeren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het importeren van gegevens uit meerdere gegevens bronnen in één Azure Cognitive Search-index met behulp van Indexeer functies. Deze zelf studie en voorbeeld code zijn C#in.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 272926e6c3572f03cc316ee696893941fd91968d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206874"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Zelf studie: index gegevens uit meerdere gegevens bronnen inC#

Met Azure Cognitive Search kunt u gegevens uit meerdere gegevens bronnen importeren, analyseren en indexeren in één geconsolideerde zoek index. Dit biedt ondersteuning voor situaties waarbij gestructureerde gegevens worden geaggregeerd met minder gestructureerde of zelfs onbewerkte tekst gegevens uit andere bronnen, zoals tekst-, HTML-of JSON-documenten.

In deze zelf studie wordt beschreven hoe u Hotel gegevens van een Azure Cosmos DB gegevens bron indexeert en samen voegen met details van Hotel kamers die vanuit Azure Blob Storage-documenten worden getrokken. Het resultaat is een gecombineerde zoek index voor hotels met complexe gegevens typen.

In deze zelf C# studie wordt de [.NET SDK](https://aka.ms/search-sdk)gebruikt. In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
> * Voorbeeld gegevens uploaden en gegevens bronnen maken
> * De document sleutel identificeren
> * De index definiëren en maken
> * Hotel gegevens van Azure Cosmos DB indexeren
> * Hotel Room-gegevens uit Blob Storage samen voegen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Een bestaande zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [maken](search-create-service-portal.md) of zoeken 

> [!Note]
> U kunt de gratis service voor deze zelf studie gebruiken. Een gratis zoek service beperkt u tot drie indexen, drie Indexeer functies en drie gegevens bronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u over voldoende ruimte beschikt om de nieuwe resources te accepteren.

## <a name="download-files"></a>Bestanden downloaden

De bron code voor deze zelf studie vindt u in de GitHub-opslag plaats [Azure-Search-DotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) in de map met [meerdere gegevens bronnen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) .

## <a name="1---create-services"></a>1-services maken

In deze zelf studie maakt gebruik van Azure Cognitive Search voor indexering en query's, Azure Cosmos DB voor één gegevensset en Azure Blob-opslag voor de tweede gegevens verzameling. 

Maak indien mogelijk alle services in dezelfde regio en resource groep voor nabijheid en beheer baarheid. In de praktijk kunnen uw services zich in een wille keurige regio bevinden.

In dit voor beeld worden twee kleine sets gegevens gebruikt waarin zeven fictieve hotels worden beschreven. In één set worden de hotels zelf beschreven en worden deze in een Azure Cosmos DB-Data Base geladen. De andere set bevat details over de hotel kamer en is beschikbaar als zeven afzonderlijke JSON-bestanden die moeten worden geüpload naar Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Beginnen met Cosmos DB

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en navigeer vervolgens naar de overzichts pagina van uw Azure Cosmos DB-account.

1. Selecteer **Data Explorer** en selecteer vervolgens **nieuwe data base**.

   ![Een nieuwe data base maken](media/tutorial-multiple-data-sources/cosmos-newdb.png "Een nieuwe database maken")

1. Voer de naam in **Hotel-kamers-DB**. Accepteer de standaard waarden voor de overige instellingen.

   ![Data base configureren](media/tutorial-multiple-data-sources/cosmos-dbname.png "Data base configureren")

1. Maak een nieuwe container. Gebruik de bestaande data base die u zojuist hebt gemaakt. Voer **Hotels** in als container naam en gebruik **/HotelId** voor de partitie sleutel.

   ![Container toevoegen](media/tutorial-multiple-data-sources/cosmos-add-container.png "Container toevoegen")

1. Selecteer **items** onder **Hotels**en klik vervolgens op **item uploaden** op de opdracht balk. Ga naar en selecteer het bestand **cosmosdb/HotelsDataSubset_CosmosDb. json** in de projectmap.

   ![Uploaden naar Azure Cosmos DB verzameling](media/tutorial-multiple-data-sources/cosmos-upload.png "Uploaden naar Cosmos DB verzameling")

1. Gebruik de knop Vernieuwen om de weer gave van de items in de hotels-verzameling te vernieuwen. Er moeten zeven nieuwe database documenten worden weer gegeven.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op **blobs**en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) met de naam **Hotel-kamers** voor het opslaan van de voor beeld-json-bestanden van de hotel kamer. U kunt het niveau van open bare toegang instellen op een van de geldige waarden.

   ![Een BLOB-container maken](media/tutorial-multiple-data-sources/blob-add-container.png "Een blob-container maken")

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk. Navigeer naar de map met de voorbeeld bestanden. Selecteer alles en klik vervolgens op **uploaden**.

   ![Bestanden uploaden](media/tutorial-multiple-data-sources/blob-upload.png "Bestanden uploaden")

Nadat het uploaden is voltooid, worden de bestanden weer gegeven in de lijst voor de gegevens container.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om deze procedure te volt ooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en-URL voor Azure Cognitive Search ophalen

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangs sleutel nodig. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de query sleutel op. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

   ![De service naam en de beheer-en query sleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2-uw omgeving instellen

1. Start Visual Studio 2019 en selecteer in het menu **extra** de optie **NuGet package manager** en vervolgens **NuGet-pakketten beheren voor oplossing...** . 

1. Zoek en Installeer **micro soft. Azure. Search** (versie 9.0.1 of hoger) op het tabblad **Bladeren** . U moet door de extra dialoog vensters klikken om de installatie te volt ooien.

    ![NuGet gebruiken om Azure-bibliotheken toe te voegen](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Zoek naar het **micro soft. Extensions. configuratie. json** NuGet-pakket en installeer dit ook.

1. Open het oplossings bestand **AzureSearchMultipleDataSources. SLN**.

1. Bewerk in Solution Explorer het bestand **appSettings. json** om verbindings gegevens toe te voegen.  

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

De eerste twee vermeldingen gebruiken de URL en de beheer sleutels voor uw Azure Cognitive Search-service. Op basis van een eind punt van `https://mydemo.search.windows.net`, bijvoorbeeld de service naam die u opgeeft, is `mydemo`.

De volgende vermeldingen geven account namen en connection string informatie op voor de Azure Blob Storage en Azure Cosmos DB gegevens bronnen.

## <a name="3---map-key-fields"></a>3: sleutel velden toewijzen

Voor het samen voegen van inhoud moeten beide gegevens stromen zijn gericht op dezelfde documenten in de zoek index. 

In azure Cognitive Search is het sleutel veld een unieke identificatie van elk document. Elke zoek index moet precies één sleutel veld van het type `Edm.String`hebben. Het sleutel veld moet aanwezig zijn voor elk document in een gegevens bron dat wordt toegevoegd aan de index. (In feite is dit het enige vereiste veld.)

Wanneer u gegevens uit meerdere gegevens bronnen indexeert, zorgt u ervoor dat elke binnenkomende rij of elk document een algemene document sleutel bevat om gegevens van twee fysiek afzonderlijke bron documenten samen te voegen in een nieuw Zoek document in de gecombineerde index. 

Er is vaak een planning vooraf van tevoren vereist om een duidelijke document sleutel voor uw index te identificeren en ervoor te zorgen dat deze bestaat in beide gegevens bronnen. In deze demo is de `HotelId` sleutel voor elk Hotel in Cosmos DB ook aanwezig in de JSON-blobs van de ruimten in Blob Storage.

Azure Cognitive Search Indexeer functies kunnen veld toewijzingen gebruiken om de gegevens velden tijdens het indexerings proces een andere naam te geven en te Format teren, zodat de bron gegevens naar het juiste index veld kunnen worden omgeleid. Zo wordt in Cosmos DB bijvoorbeeld de Hotel-id **`HotelId`** genoemd. Maar in de JSON-BLOB-bestanden voor de hotel kamers heet de Hotel-id **`Id`** . Dit wordt door het programma verwerkt door het **`Id`** veld van de blobs te koppelen aan het **`HotelId`** sleutel veld in de index.

> [!NOTE]
> In de meeste gevallen maken Automatische gegenereerde document sleutels, zoals die standaard worden gemaakt door bepaalde Indexeer functies, geen goede document sleutels voor gecombineerde indexen. Over het algemeen wilt u een betekenis volle, unieke sleutel waarde gebruiken die al bestaat in, of eenvoudig kan worden toegevoegd aan uw gegevens bronnen.

## <a name="4---explore-the-code"></a>4-de code verkennen

Zodra de gegevens en configuratie-instellingen zijn geïmplementeerd, moet het voorbeeld programma in **AzureSearchMultipleDataSources. SLN** klaar zijn om te worden gemaakt en uitgevoerd.

Deze eenvoudige C#/.net-ontwikkeling.-console-app voert de volgende taken uit:

* Hiermee maakt u een nieuwe index op basis van de gegevens C# structuur van de klasse hotel (die ook verwijst naar de adres-en room-klassen).
* Hiermee maakt u een nieuwe gegevens bron en een Indexeer functie waarmee Azure Cosmos DB gegevens worden toegewezen aan index velden. Dit zijn beide objecten in azure Cognitive Search.
* De Indexeer functie wordt uitgevoerd om Hotel gegevens van Cosmos DB te laden.
* Hiermee maakt u een tweede gegevens bron en een Indexeer functie waarmee JSON BLOB-gegevens worden toegewezen aan index velden.
* Voert de tweede Indexeer functie uit om opslag ruimten uit de Blobopslag te laden.

 Voordat u het programma uitvoert, moet u een paar minuten duren om de code en de definities van de index en de Indexeer functie voor dit voor beeld te bestuderen. De relevante code staat in twee bestanden:

  + **Hotel.cs** bevat het schema dat de index definieert
  + **Program.cs** bevat functies voor het maken van de Azure Cognitive search-index, gegevens bronnen en indexeringen en het laden van de gecombineerde resultaten in de index.

### <a name="create-an-index"></a>Een index maken

In dit voorbeeld programma wordt de .NET SDK gebruikt om een Azure Cognitive Search-index te definiëren en te maken. Het maakt gebruik van de [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) -klasse om een index structuur te genereren C# op basis van een gegevens model klasse.

Het gegevens model wordt gedefinieerd door de klasse hotel, dat ook verwijzingen bevat naar het adres en de room-klassen. De FieldBuilder zoomt uit op meerdere klassen definities om een complexe gegevens structuur voor de index te genereren. Tags voor meta gegevens worden gebruikt voor het definiëren van de kenmerken van elk veld, zoals of het doorzoekbaar of sorteerbaar is.

In de volgende fragmenten uit het **Hotel.cs** -bestand ziet u hoe een enkel veld en een verwijzing naar een andere gegevens model klasse kunnen worden opgegeven.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

In het **Program.cs** -bestand wordt de index gedefinieerd met een naam en een veld verzameling die wordt gegenereerd door de `FieldBuilder.BuildForType<Hotel>()` methode en die vervolgens als volgt worden gemaakt:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB gegevens bron en Indexeer functie maken

Vervolgens bevat het hoofd programma logica voor het maken van de Azure Cosmos DB gegevens bron voor de hotels-gegevens.

Eerst wordt de naam van de Azure Cosmos DB-Data Base samengevoegd met de connection string. Vervolgens wordt het gegevens bron object gedefinieerd, inclusief de instellingen die specifiek zijn voor Azure Cosmos DB bronnen, zoals de eigenschap [useChangeDetection].

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

Nadat de gegevens bron is gemaakt, wordt in het programma een Azure Cosmos DB Indexeer functie met de naam **Hotel-kamers-Cosmos-Indexeer functie**ingesteld.

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
Er worden bestaande Indexeer functies met dezelfde naam verwijderd voordat u de nieuwe maakt, voor het geval u dit voor beeld meer dan één keer wilt uitvoeren.

In dit voor beeld wordt een schema voor de Indexeer functie gedefinieerd, zodat het eenmaal per dag wordt uitgevoerd. U kunt de eigenschap schema van deze aanroep verwijderen als u niet wilt dat de Indexeer functie in de toekomst automatisch opnieuw wordt uitgevoerd.

### <a name="index-azure-cosmos-db-data"></a>Azure Cosmos DB gegevens indexeren

Zodra de gegevens bron en de Indexeer functie zijn gemaakt, is de code die de Indexeer functie uitvoert, kort:

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

Dit voor beeld bevat een eenvoudig try-catch-blok om eventuele fouten te rapporteren die tijdens de uitvoering kunnen optreden.

Nadat de Azure Cosmos DB Indexeer functie is uitgevoerd, bevat de zoek index een volledige reeks voor beelden van Hotel documenten. Het veld kamers voor elk hotel is echter een lege matrix, omdat de gegevens bron van Azure Cosmos DB geen ruimte meer bevat. Vervolgens wordt het programma opgehaald uit de Blob-opslag om de kamer gegevens te laden en samen te voegen.

### <a name="create-blob-storage-data-source-and-indexer"></a>Gegevens bron en Indexeer functie voor Blob-opslag maken

Als u de ruimte details wilt ophalen, wordt door het programma eerst een Blob Storage-gegevens bron ingesteld om te verwijzen naar een set afzonderlijke JSON-BLOB-bestanden.

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

Nadat de gegevens bron is gemaakt, wordt in het programma een BLOB-Indexeer functie **met de naam Hotel-ruimtes-BLOB-Indexeer functie**ingesteld.

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

De JSON-blobs bevatten een sleutel veld met de naam **`Id`** in plaats van **`HotelId`** . De code gebruikt de `FieldMapping`-klasse om de Indexeer functie de **`Id`** veld waarde te laten verwijzen naar de **`HotelId`** document sleutel in de index.

Indexeer functies voor Blob-opslag kunnen para meters gebruiken die de te gebruiken parsing-modus identificeren. De parserings modus verschilt voor blobs die één document of meerdere documenten binnen dezelfde BLOB vertegenwoordigen. In dit voor beeld vertegenwoordigt elke Blob een enkel index document, zodat de code de para meter `IndexingParameters.ParseJson()` gebruikt.

Zie [JSON-blobs indexeren](search-howto-index-json-blobs.md)voor meer informatie over het parseren van para meters voor de Indexeer functie voor json-blobs. Zie de klasse [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) voor meer informatie over het opgeven van deze para meters met behulp van de .NET SDK.

Er worden bestaande Indexeer functies met dezelfde naam verwijderd voordat u de nieuwe maakt, voor het geval u dit voor beeld meer dan één keer wilt uitvoeren.

In dit voor beeld wordt een schema voor de Indexeer functie gedefinieerd, zodat het eenmaal per dag wordt uitgevoerd. U kunt de eigenschap schema van deze aanroep verwijderen als u niet wilt dat de Indexeer functie in de toekomst automatisch opnieuw wordt uitgevoerd.

### <a name="index-blob-data"></a>BLOB-gegevens indexeren

Zodra de gegevens bron van de Blob-opslag en de Indexeer functie zijn gemaakt, is de code waarmee de Indexeer functie wordt uitgevoerd, eenvoudig:

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

Omdat de index al is gevuld met Hotel gegevens uit de Azure Cosmos DB Data Base, werkt de BLOB-indexer de bestaande documenten in de index bij en worden de details van de ruimte toegevoegd.

> [!NOTE]
> Als u dezelfde niet-sleutel velden in beide gegevens bronnen hebt en de gegevens in deze velden niet overeenkomen, bevat de index de waarden van de meest recente meest recent uitgevoerde indexer. In ons voor beeld bevatten beide gegevens bronnen het veld **hotelnaam** . Als de gegevens in dit veld om de een of andere reden verschillend zijn, voor documenten met dezelfde sleutel waarde, **wordt de waarde** in de index opgeslagen in de gegevens bron die het meest recent is geïndexeerd.

## <a name="5---search"></a>5-zoeken

U kunt de gevulde zoek index verkennen nadat het programma is uitgevoerd, met behulp van de [**Search Explorer**](search-explorer.md) in de portal.

Open in Azure Portal de pagina **overzicht** van de zoek service en zoek de index **Hotel-ruimtes-voor beeld** in de lijst **indexen** .

  ![Lijst met Azure Cognitive Search-indexen](media/tutorial-multiple-data-sources/index-list.png "Lijst met Azure Cognitive Search-indexen")

Klik op de index Hotel-ruimtes-voor beeld in de lijst. U ziet een Search Explorer-interface voor de index. Voer een query in voor een term zoals "luxe". Er wordt ten minste één document in de resultaten weer gegeven. in dit document moet een lijst met room-objecten in de ruimten van de kamers staan.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele stadia van de ontwikkeling kunt u het beste de objecten uit Azure Cognitive Search verwijderen en uw code zo instellen dat deze opnieuw worden opgebouwd. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeld code voor deze zelf studie wordt gecontroleerd op bestaande objecten en worden deze verwijderd zodat u de code opnieuw kunt uitvoeren.

U kunt ook de portal gebruiken om indexen, Indexeer functies en gegevens bronnen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling alle resources of resource groepen in het navigatie deel venster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met het concept van het opnemen van gegevens uit meerdere bronnen, gaan we de configuratie van de Indexeer functie nader bekijken, te beginnen met Cosmos DB.

> [!div class="nextstepaction"]
> [Een Azure Cosmos DB Indexeer functie configureren](search-howto-index-cosmosdb.md)