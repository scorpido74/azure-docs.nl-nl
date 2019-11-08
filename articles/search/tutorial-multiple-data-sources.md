---
title: 'C#Zelf studie: meerdere gegevens bronnen indexeren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het importeren van gegevens uit meerdere gegevens bronnen in één Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 69b18cdd4d0bb8e3d13bbacd5d21764004308786
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795643"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-cognitive-search-index"></a>C#Zelf studie: gegevens uit meerdere gegevens bronnen in één Azure Cognitive Search-index combi neren

Met Azure Cognitive Search kunt u gegevens uit meerdere gegevens bronnen importeren, analyseren en indexeren in één gecombineerde zoek index. Dit biedt ondersteuning voor situaties waarbij gestructureerde gegevens worden geaggregeerd met minder gestructureerde of zelfs onbewerkte tekst gegevens uit andere bronnen, zoals tekst-, HTML-of JSON-documenten.

In deze zelf studie wordt beschreven hoe u Hotel gegevens van een Azure Cosmos DB gegevens bron indexeert en samen voegen met details van Hotel kamers die vanuit Azure Blob Storage-documenten worden getrokken. Het resultaat is een gecombineerde zoek index voor hotels met complexe gegevens typen.

In deze zelf C#studie wordt gebruikgemaakt van de .NET-SDK voor Azure Cognitive Search en de Azure Portal om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Voorbeeld gegevens uploaden en gegevens bronnen maken
> * De document sleutel identificeren
> * De index definiëren en maken
> * Hotel gegevens van Azure Cosmos DB indexeren
> * Hotel Room-gegevens uit Blob Storage samen voegen

## <a name="prerequisites"></a>Vereisten

De volgende services, hulpprogram ma's en gegevens worden gebruikt in deze Quick Start. 

- [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

- [Maak een Azure Cosmos DB account](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) voor het opslaan van de gegevens van het voorbeeld Hotel.

- [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor het opslaan van de voorbeeld gegevens van JSON-blobs.

- [Installeer Visual Studio](https://visualstudio.microsoft.com/) om te gebruiken als IDE.

### <a name="install-the-project-from-github"></a>Het project installeren vanuit GitHub

1. Ga naar de voorbeeld opslagplaats op GitHub: [Azure-Search-DotNet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecteer **klonen of downloaden** en maak uw persoonlijke lokale kopie van de opslag plaats.
1. Open Visual Studio en installeer het Microsoft Azure Cognitive Search NuGet-pakket, als dit nog niet is geïnstalleerd. Selecteer in het menu **extra** de optie **NuGet package manager** en klik vervolgens **op NuGet-pakketten beheren voor oplossing...** . Zoek en Installeer **micro soft. Azure. Search** (versie 9.0.1 of hoger) op het tabblad **Bladeren** . U moet door de extra dialoog vensters klikken om de installatie te volt ooien.

    ![NuGet gebruiken om Azure-bibliotheken toe te voegen](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Ga in Visual Studio naar uw lokale opslag plaats en open het oplossings bestand **AzureSearchMultipleDataSources. SLN**.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Als u met uw Azure Cognitive Search-service wilt communiceren, hebt u de service-URL en een toegangs sleutel nodig. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Een geldige sleutel brengt een vertrouwens relatie tot stand, op basis van aanvraag, tussen de toepassing die de aanvraag verzendt en de service die deze verwerkt.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Voor beeld-Azure Cosmos DB gegevens voorbereiden

In dit voor beeld worden twee kleine sets gegevens gebruikt waarin zeven fictieve hotels worden beschreven. In één set worden de hotels zelf beschreven en worden deze in een Azure Cosmos DB-Data Base geladen. De andere set bevat details over de hotel kamer en is beschikbaar als zeven afzonderlijke JSON-bestanden die moeten worden geüpload naar Azure Blob Storage.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com)en navigeer vervolgens naar de overzichts pagina van uw Azure Cosmos DB-account.

1. Klik in de menu balk op container toevoegen. Geef ' nieuwe data base maken ' op en gebruik de naam **Hotel-ruimtes-DB**. Voer **Hotels** in voor de naam van de verzameling en **/HotelId** voor de partitie sleutel. Klik op **OK** om de data base en de container te maken.

   ![Azure Cosmos DB container toevoegen](media/tutorial-multiple-data-sources/cosmos-add-container.png "Een Azure Cosmos DB-container toevoegen")

1. Ga naar de Cosmos DB-Data Explorer en selecteer het **item items** onder de container **Hotels** in de **Hotel-kamers-DB** -data base. Klik vervolgens op **item uploaden** op de opdracht balk.

   ![Uploaden naar Azure Cosmos DB verzameling](media/tutorial-multiple-data-sources/cosmos-upload.png "Uploaden naar Cosmos DB verzameling")

1. Klik in het deel venster uploaden op de knop map en navigeer vervolgens naar het bestand **cosmosdb/HotelsDataSubset_CosmosDb. json** in de projectmap. Klik op **OK** om het uploaden te starten.

   ![Selecteer het bestand dat u wilt uploaden](media/tutorial-multiple-data-sources/cosmos-upload2.png "Selecteer het bestand dat u wilt uploaden")

1. Gebruik de knop Vernieuwen om de weer gave van de items in de hotels-verzameling te vernieuwen. Er moeten zeven nieuwe database documenten worden weer gegeven.

## <a name="prepare-sample-blob-data"></a>Voor beeld van BLOB-gegevens voorbereiden

1. [Meld u aan bij de Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op **blobs**en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) met de naam **Hotel-kamers** voor het opslaan van de voor beeld-json-bestanden van de hotel kamer. U kunt het niveau van open bare toegang instellen op een van de geldige waarden.

   ![Een BLOB-container maken](media/tutorial-multiple-data-sources/blob-add-container.png "Een blob-container maken")

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk.

   ![Uploaden op de opdracht balk](media/search-semi-structured-data/upload-command-bar.png "Uploaden op de opdracht balk")

1. Navigeer naar de map met de voorbeeld bestanden. Selecteer alles en klik vervolgens op **uploaden**.

   ![Bestanden uploaden](media/tutorial-multiple-data-sources/blob-upload.png "Bestanden uploaden")

Nadat het uploaden is voltooid, worden de bestanden weer gegeven in de lijst voor de gegevens container.

## <a name="set-up-connections"></a>Verbindingen instellen

Verbindings gegevens voor de Search-service en de gegevens bronnen worden opgegeven in het bestand **appSettings. json** in de oplossing. 

1. Open in Visual Studio het bestand **AzureSearchMultipleDataSources. SLN** .

1. Bewerk het bestand **appSettings. json** in Solution Explorer.  

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

### <a name="identify-the-document-key"></a>De document sleutel identificeren

In azure Cognitive Search is het sleutel veld een unieke identificatie voor elk document in de index. Elke zoek index moet precies één sleutel veld van het type `Edm.String`hebben. Het sleutel veld moet aanwezig zijn voor elk document in een gegevens bron dat wordt toegevoegd aan de index. (In feite is dit het enige vereiste veld.)

Wanneer u gegevens uit meerdere gegevens bronnen indexeert, moet elke waarde van de gegevens bron sleutel worden toegewezen aan hetzelfde sleutel veld in de gecombineerde index. Er is vaak een planning vooraf van tevoren vereist om een zinvolle document sleutel voor uw index te identificeren en ervoor te zorgen dat deze zich in elke gegevens bron bevindt.

Azure Cognitive Search Indexeer functies kunnen veld toewijzingen gebruiken om de gegevens velden tijdens het indexerings proces een andere naam te geven en te Format teren, zodat de bron gegevens naar het juiste index veld kunnen worden omgeleid.

In het voor beeld Azure Cosmos DB gegevens wordt de Hotel-id bijvoorbeeld **HotelId**genoemd. Maar in de JSON-BLOB-bestanden voor de hotel kamers heet de Hotel-id de naam **id**. Dit wordt door het programma verwerkt door het veld **id** van de blobs te koppelen aan het sleutel veld **HotelId** in de index.

> [!NOTE]
> In de meeste gevallen automatisch gegenereerde document sleutels, zoals die standaard door bepaalde Indexeer functies gemaakt, maken geen goede document sleutels voor gecombineerde indexen. Over het algemeen wilt u een betekenis volle, unieke sleutel waarde gebruiken die al bestaat in, of eenvoudig kan worden toegevoegd aan uw gegevens bronnen.

## <a name="understand-the-code"></a>De code begrijpen

Zodra de gegevens en configuratie-instellingen zijn geïmplementeerd, moet het voorbeeld programma in **AzureSearchMultipleDataSources. SLN** klaar zijn om te worden gemaakt en uitgevoerd.

Deze eenvoudige C#/.net-ontwikkeling.-console-app voert de volgende taken uit:
* Hiermee maakt u een nieuwe Azure Cognitive Search-index op basis van de C# gegevens structuur van de klasse hotel (die ook verwijst naar de adres-en room-klassen).
* Hiermee maakt u een Azure Cosmos DB gegevens bron en een Indexeer functie waarmee Azure Cosmos DB gegevens worden toegewezen aan index velden.
* Hiermee wordt de Azure Cosmos DB Indexeer functie uitgevoerd om Hotel gegevens te laden.
* Hiermee maakt u een Azure Blob Storage-gegevens bron en een Indexeer functie waarmee JSON BLOB-gegevens worden toegewezen aan index velden.
* Hiermee wordt de Azure Blob Storage-indexer uitgevoerd om ruimten-gegevens te laden.

 Voordat u het programma uitvoert, moet u een paar minuten duren om de code en de definities van de index en de Indexeer functie voor dit voor beeld te bestuderen. De relevante code staat in twee bestanden:

  + **Hotel.cs** bevat het schema dat de index definieert
  + **Program.cs** bevat functies voor het maken van de Azure Cognitive search-index, gegevens bronnen en indexeringen en het laden van de gecombineerde resultaten in de index.

### <a name="define-the-index"></a>De index definiëren

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

De JSON-blobs bevatten een sleutel veld met de naam **id** in plaats van **HotelId**. De code gebruikt de `FieldMapping`-klasse om de Indexeer functie de waarde van het veld id te laten verwijzen naar de **HotelId** **-** document sleutel in de index.

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

## <a name="search-your-json-files"></a>Uw JSON-bestanden doorzoeken

U kunt de gevulde zoek index verkennen nadat het programma is uitgevoerd, met behulp van de [**Search Explorer**](search-explorer.md) in de portal.

Open in Azure Portal de pagina **overzicht** van de zoek service en zoek de index **Hotel-ruimtes-voor beeld** in de lijst **indexen** .

  ![Lijst met Azure Cognitive Search-indexen](media/tutorial-multiple-data-sources/index-list.png "Lijst met Azure Cognitive Search-indexen")

Klik op de index Hotel-ruimtes-voor beeld in de lijst. U ziet een Search Explorer-interface voor de index. Voer een query in voor een term zoals "luxe". Er wordt ten minste één document in de resultaten weer gegeven. in dit document moet een lijst met room-objecten in de ruimten van de kamers staan.

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om na een zelf studie op te schonen, is door de resource groep te verwijderen die de Azure Cognitive Search-service bevat. U kunt de resourcegroep nu verwijderen om alles daarin permanent te verwijderen. In de portal bevindt de naam van de resource groep zich op de pagina overzicht van de Azure Cognitive Search-service.

## <a name="next-steps"></a>Volgende stappen

Er zijn verschillende benaderingen en meerdere opties voor het indexeren van JSON-blobs. Als uw bron gegevens JSON-inhoud bevatten, kunt u deze opties bekijken om te zien wat het beste werkt voor uw scenario.

> [!div class="nextstepaction"]
> [JSON-blobs indexeren met Azure Cognitive Search BLOB-Indexer](search-howto-index-json-blobs.md)

U kunt gestructureerde index gegevens uit een gegevens bron uitbreiden met cognitieve verrijkte gegevens van ongestructureerde blobs of volledige-tekst inhoud. In de volgende zelf studie ziet u hoe u Cognitive Services in combi natie met Azure Cognitive Search kunt gebruiken met behulp van de .NET SDK.

> [!div class="nextstepaction"]
> [Cognitive Services-API's aanroepen in een Azure Cognitive Search Indexing-pijp lijn](cognitive-search-tutorial-blob-dotnet.md)
