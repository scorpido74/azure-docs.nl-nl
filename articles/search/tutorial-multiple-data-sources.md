---
title: C#-zelfstudie voor het indexeren van meerdere Azure-gegevensbronnen
titleSuffix: Azure Cognitive Search
description: Meer informatie over het importeren van gegevens uit meerdere gegevensbronnen in één Azure Cognitive Search-index met behulp van indexeerfuncties. In deze zelfstudie en in de voorbeeldcode wordt gebruikgemaakt van C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 06b80b5fe14a7a913d8ad8454c6568b04fe01c2f
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819802"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Zelfstudie: Indexeren vanuit meerdere gegevensbronnen met behulp van de .NET-SDK

Met Azure Cognitive Search kunt u gegevens uit meerdere gegevensbronnen importeren, analyseren en indexeren in één geconsolideerde zoekindex. Dit is handig in situaties waarin gestructureerde gegevens worden geaggregeerd met minder gestructureerde gegevens of zelfs gegevens die bestaan uit tekst zonder opmaak die afkomstig is uit andere bronnen, zoals tekst-, HTML- of JSON-documenten.

In deze zelfstudie wordt beschreven hoe u hotelgegevens van een Azure Cosmos DB-gegevensbron indexeert en hoe u deze samenvoegt met hotelkamergegevens uit documenten uit de Azure-blobopslag. Het resultaat is een gecombineerde zoekindex voor hotels met complexe gegevenstypen.

In deze zelfstudie wordt gebruikgemaakt van C# en de [.NET SDK](/dotnet/api/overview/azure/search). In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Voorbeeldgegevens uploaden en gegevensbronnen maken
> * De documentsleutel identificeren
> * De index definiëren en maken
> * Hotelgegevens uit Azure Cosmos DB indexeren
> * Hotelkamergegevens uit blobopslag samenvoegen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-files"></a>Bestanden downloaden

De broncode voor deze zelfstudie bevindt zich in de GitHub-opslagplaats [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples), in de map [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources).

## <a name="1---create-services"></a>1- Services maken

In deze zelfstudie wordt gebruikgemaakt van Azure Cognitive Search voor indexering en query's, Azure Cosmos DB voor één gegevensset en Azure-blobopslag voor de tweede gegevensset. 

Maak, indien mogelijk, alle services in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kunnen uw services zich in elke regio bevinden.

In dit voorbeeld worden twee kleine gegevenssets gebruikt, waarin zeven fictieve hotels worden beschreven. In één set worden de hotels zelf beschreven, en wordt in een Azure Cosmos DB-database geladen. De andere set bevat details over de hotelkamers en is beschikbaar als zeven afzonderlijke JSON-bestanden die moeten worden geüpload naar de Azure-blobopslag.

### <a name="start-with-cosmos-db"></a>Aan de slag met Cosmos DB

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en ga naar uw Azure Cosmos DB-account. Open vervolgens de pagina Overzicht.

1. Selecteer **Data Explorer** en selecteer **Nieuwe database**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Een nieuwe database maken" border="false":::

1. Voer de naam **hotel-rooms-db** in. Accepteer de standaardwaarden voor de overige instellingen.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Een nieuwe database maken" border="false":::

1. Een nieuwe container maken. Gebruik de bestaande database die u zojuist hebt gemaakt. Voer **hotels** in als containernaam en gebruik **/HotelId** als partitiesleutel.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Een nieuwe database maken" border="false":::

1. Selecteer **Items** onder **Hotels** en klik vervolgens op **Item uploaden** op de opdrachtbalk. Ga naar en selecteer het bestand **cosmosdb/HotelsDataSubset_CosmosDb.json** in de projectmap.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Een nieuwe database maken" border="false":::

1. Gebruik de knop Vernieuwen om de weergave van de items in de verzameling hotels te vernieuwen. Er worden zeven nieuwe databasedocumenten weergegeven.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Meld u aan bij [Azure Portal](https://portal.azure.com), ga naar uw Azure-opslagaccount, klik op **Blobs** en op **+ Container**.

1. [Maak een blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md) met de naam **hotel-rooms** om de JSON-voorbeeldbestanden voor hotelkamers op te slaan. U kunt het niveau voor openbare toegang instellen op een van de geldige waarden.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Een nieuwe database maken" border="false":::

1. Nadat de container is gemaakt, opent u deze en selecteert u **Uploaden** op de opdrachtbalk. Navigeer naar de map met de voorbeeldbestanden. Selecteer deze allemaal en klik op **Uploaden**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Een nieuwe database maken" border="false":::

Nadat de upload is voltooid, worden de bestanden weergegeven in de lijst van de gegevenscontainer.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search ophalen

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Een nieuwe database maken" border="false":::

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2 - Uw omgeving instellen

1. Open Visual Studio 2019 en selecteer in het menu **Extra** de optie **NuGet Package Manager** en **NuGet-pakketten voor oplossing beheren...** . 

1. Ga naar het tabblad **Bladeren** en zoek en installeer **Microsoft.Azure.Search** (versie 9.0.1 of hoger). U moet door een paar extra dialoogvensters klikken om de installatie te voltooien.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Een nieuwe database maken" border="false":::

1. Zoek naar het NuGet-pakket **Microsoft.Extensions.Configuration.Json** en installeer dit ook.

1. Open het oplossingsbestand **AzureSearchMultipleDataSources.sln**.

1. Bewerk het bestand **appsettings.json** in Solution Explorer om verbindingsgegevens op te geven.  

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

In de eerste twee vermeldingen worden de URL en beheersleutels voor de Azure Cognitive Search-service gebruikt. Als het eindpunt bijvoorbeeld `https://mydemo.search.windows.net` is, moet deze servicenaam worden opgegeven: `mydemo`.

In de volgende vermeldingen worden de accountnamen en de verbindingsreeksgegevens van de Azure-blobopslag- en Azure Cosmos DB-gegevensbronnen opgegeven.

## <a name="3---map-key-fields"></a>3 - Sleutelvelden toewijzen

Voor het samenvoegen van inhoud moeten beide gegevensstromen zijn gericht op dezelfde documenten in de zoekindex. 

In Azure Cognitive Search bevat het sleutelveld een unieke id van elk document. Elke zoekindex moet precies één sleutelveld van het type `Edm.String` bevatten. Het sleutelveld moet aanwezig zijn voor elk document in een gegevensbron dat wordt toegevoegd aan de index. (Dit is zelfs het enige vereiste veld.)

Wanneer u gegevens van meerdere gegevensbronnen indexeert, zorgt u ervoor dat elke binnenkomende rij of elk binnenkomend document een algemene documentsleutel bevat om gegevens van twee fysiek afzonderlijke brondocumenten samen te voegen in een nieuw zoekdocument in de gecombineerde index. 

Vaak moet er op voorhand goed worden gepland om een betekenisvolle documentsleutel voor uw index te verzinnen en ervoor te zorgen dat deze aanwezig is in beide gegevensbronnen. In deze demo is de sleutel `HotelId` van elk hotel in Cosmos DB ook aanwezig in de JSON-kamerblobs in de blobopslag.

De Azure Cognitive Search-indexeerfuncties kunnen veldtoewijzingen gebruiken om gegevensvelden een nieuwe naam te geven en zelfs opnieuw in te delen tijdens het indexeren, zodat de brongegevens naar het juiste indexveld kunnen worden geleid. In Cosmos DB heet de hotel-id bijvoorbeeld **`HotelId`** . In de JSON-blobbestanden voor de hotelkamers is de hotel-id echter **`Id`** . Dit wordt door het programma verwerkt door het veld **`Id`** van de blobs toe te wijzen aan het sleutelveld **`HotelId`** in de index.

> [!NOTE]
> In de meeste gevallen zijn automatisch gegenereerde documentsleutels, zoals die standaard worden gemaakt door bepaalde indexeerfuncties, geen goede documentsleutels voor gecombineerde indexen. Over het algemeen moet u een betekenisvolle, unieke sleutelwaarde gebruiken die al bestaat in, of eenvoudig kan worden toegevoegd aan, uw gegevensbronnen.

## <a name="4---explore-the-code"></a>4 - De code verkennen

Zodra de gegevens en configuratie-instellingen kloppen, kan het voorbeeldprogramma in **AzureSearchMultipleDataSources.sln** worden gebouwd en uitgevoerd.

Deze eenvoudige C#-/.NET-console-app voert de volgende taken uit:

* Maakt een nieuwe index op basis van de gegevensstructuur van de C#-hotelklasse (die ook verwijst naar de adres- en kamerklassen).
* Maakt een nieuwe gegevensbron en een indexeerfunctie om Azure Cosmos DB-gegevens toe te wijzen aan indexvelden. Dit zijn beide objecten in Azure Cognitive Search.
* Voert de indexeerfunctie uit zodat hotelgegevens vanuit Cosmos DB worden geladen.
* Maakt een tweede gegevensbron en een indexeerfunctie om JSON-blobgegevens toe te wijzen aan indexvelden.
* Voert de tweede indexeerfunctie uit om kamergegevens uit de blobopslag te laden.

 Neem, voordat u het programma gaat uitvoeren, even een minuut de tijd om de code en de index- en indexeerfunctiedefinities voor dit voorbeeld te bestuderen. De relevante code staat in twee bestanden:

  + **Hotel.cs** bevat het schema dat de index definieert
  + **Program.cs** bevat functies voor het maken van de Azure Cognitive Search-index, -gegevensbronnen en -indexeringen en laadt de gecombineerde resultaten in de index.

### <a name="create-an-index"></a>Een index maken

In dit voorbeeldprogramma wordt de .NET-SDK gebruikt om een Azure Cognitive Search-index te definiëren en te maken. Er wordt gebruikgemaakt van de [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder)-klasse om een indexstructuur te genereren op basis van een C#-gegevensmodelklasse.

Het gegevensmodel wordt gedefinieerd door de hotelklasse, die ook verwijzingen bevat naar de adres- en kamerklassen. Met FieldBuilder wordt ingezoomd op meerdere klassedefinities om een complexe gegevensstructuur te genereren voor de index. Tags voor metagegevens worden gebruikt voor het definiëren van de kenmerken van elk veld. Er kan bijvoorbeeld worden aangegeven of een veld doorzoekbaar of sorteerbaar is.

In de volgende fragmenten uit het bestand **Hotel.cs** wordt weergegeven hoe een enkel veld en een verwijzing naar een andere gegevensmodelklasse kunnen worden opgegeven.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

In het bestand **Program.cs** wordt de index gedefinieerd met een naam en een veldverzameling die wordt gegenereerd door de methode `FieldBuilder.BuildForType<Hotel>()`. De index wordt daarna als volgt gemaakt:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Een Azure Cosmos DB-gegevensbron en -indexeerfunctie maken

Het hoofdprogramma bevat logica om de Azure Cosmos DB-gegevensbron te maken voor de hotelgegevens.

Eerst wordt de naam van de Azure Cosmos DB-database samengevoegd met de verbindingsreeks. Vervolgens wordt het gegevensbronobject gedefinieerd, inclusief de instellingen die specifiek zijn voor Azure Cosmos DB-bronnen, zoals de eigenschap [useChangeDetection].

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

Wanneer de gegevensbron is gemaakt, stelt het programma een Azure Cosmos DB-indexeerfunctie in met de naam **hotel-rooms-cosmos-indexer**.

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
Het programma verwijdert alle bestaande indexeerfuncties met dezelfde naam voordat de nieuwe wordt gemaakt (mocht u dit voorbeeld meer dan eens willen uitvoeren).

In dit voorbeeld wordt een schema voor de indexeerfunctie gedefinieerd zodat deze een keer per dag wordt uitgevoerd. U kunt de planningseigenschap uit deze aanroep verwijderen als u niet wilt dat de indexeerfunctie in de toekomst automatisch opnieuw wordt uitgevoerd.

### <a name="index-azure-cosmos-db-data"></a>Azure Cosmos DB-gegevens indexeren

Zodra de gegevensbron en de indexeerfunctie zijn gemaakt, is de code die de indexeerfunctie uitvoert, kort:

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

Dit voorbeeld bevat een eenvoudig try-catch-blok om eventuele fouten te rapporteren die tijdens de uitvoering kunnen optreden.

Nadat de Azure Cosmos DB-indexeerfunctie is uitgevoerd, bevat de zoekindex een volledige reeks voorbeelden van hoteldocumenten. Het veld Rooms van elk hotel is echter een lege matrix, omdat de gegevensbron van Azure Cosmos DB geen kamergegevens bevat. Vervolgens haalt het programma gegevens op uit de blobopslag om de kamergegevens te laden en samen te voegen.

### <a name="create-blob-storage-data-source-and-indexer"></a>Gegevensbron op basis van blobopslag en de indexeerfunctie maken

Voor het ophalen van de kamergegevens stelt het programma eerst een gegevensbron voor de blobopslag in zodat er wordt verwezen naar een set afzonderlijke JSON-blob-bestanden.

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

Wanneer de gegevensbron is gemaakt, stelt het programma een blob-indexeerfunctie in met de naam **hotel-rooms-blob-indexer**.

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

De JSON-blobs bevatten een sleutelveld met de naam **`Id`** in plaats van **`HotelId`** . In de code wordt de klasse `FieldMapping` gebruikt om de indexeerfunctie te laten weten dat de veldwaarde **`Id`** moet verwijzen naar de documentsleutel **`HotelId`** in de index.

Indexeerfuncties voor bloboplag kunnen parameters gebruiken die de te gebruiken parseermodus identificeren. De parseermodus verschilt voor blobs, afhankelijk van of ze één document of meerdere documenten in dezelfde blob vertegenwoordigen. In dit voorbeeld vertegenwoordigt elke blob een enkel indexdocument. In de code wordt dus de parameter `IndexingParameters.ParseJson()` gebruikt.

Voor meer informatie over het parseren van parameters voor de indexeerfunctie voor JSON-blobs raadpleegt u [JSON-blobs indexeren](search-howto-index-json-blobs.md). Voor meer informatie over het opgeven van deze parameters met behulp van de .NET-SDK raadpleegt u de klasse [IndexerParametersExtension](/dotnet/api/microsoft.azure.search.models.indexingparametersextensions).

Het programma verwijdert alle bestaande indexeerfuncties met dezelfde naam voordat de nieuwe wordt gemaakt (mocht u dit voorbeeld meer dan eens willen uitvoeren).

In dit voorbeeld wordt een schema voor de indexeerfunctie gedefinieerd zodat deze een keer per dag wordt uitgevoerd. U kunt de planningseigenschap uit deze aanroep verwijderen als u niet wilt dat de indexeerfunctie in de toekomst automatisch opnieuw wordt uitgevoerd.

### <a name="index-blob-data"></a>Blobgegevens indexeren

Zodra de gegevensbron (blobopslag) en de indexeerfunctie zijn gemaakt, is de code die de indexeerfunctie uitvoert, eenvoudig:

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

Omdat de index al is gevuld met hotelgegevens uit de Azure Cosmos DB-database, werkt de blobindexeerfunctie de bestaande documenten in de index bij en worden de kamergegevens toegevoegd.

> [!NOTE]
> Als u dezelfde niet-sleutelvelden in beide gegevensbronnen hebt en de gegevens in deze velden niet overeenkomen, bevat de index de waarden van de indexeerfunctie die het meest recent is uitgevoerd. In ons voorbeeld bevatten beide gegevensbronnen het veld **HotelName**. Als de gegevens in dit veld om de een of andere reden verschillen, worden voor documenten met dezelfde sleutelwaarde de **HotelName**-gegevens uit de gegevensbron die het laatst is geïndexeerd opgeslagen in de index.

## <a name="5---search"></a>5 - Zoeken

U kunt de gevulde zoekindex onderzoeken nadat het programma is uitgevoerd. Gebruik hiervoor [**Search Explorer**](search-explorer.md) in de portal.

Open in Azure Portal de pagina **Overzicht** van de zoekservice en zoek in de lijst **Indexen** de index **hotel-rooms-sample**.

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Een nieuwe database maken" border="false":::

Klik op de index hotel-rooms-sample in de lijst. U ziet een Search Explorer-interface voor de index. Voer een query in voor een term, bijvoorbeeld luxe. Er wordt ten minste één document in de resultaten weergegeven. In dit document staat een lijst met kamerobjecten in de kamermatrix.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeldcode voor deze zelfstudie wordt gecontroleerd op bestaande objecten en worden deze verwijderd zodat u de code opnieuw kunt uitvoeren.

U kunt de portal gebruiken om indexen, indexeerfuncties en gegevensbronnen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met het opnemen van gegevens uit verschillende bronnen, kunnen we dieper ingaan op de configuratie van de indexeerfunctie, te beginnen met Cosmos DB.

> [!div class="nextstepaction"]
> [Een indexeerfunctie voor Azure Cosmos DB configureren](search-howto-index-cosmosdb.md)