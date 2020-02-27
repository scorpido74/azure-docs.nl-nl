---
title: 'Zelf studie: gegevens indexeren C# vanuit Azure SQL-data bases'
titleSuffix: Azure Cognitive Search
description: In deze C# zelf studie maakt u verbinding met Azure SQL database, extraheert u Doorzoek bare gegevens en laadt u deze in een Azure Cognitive search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650049"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Zelf studie: Azure SQL-gegevens C# indexeren met Azure Cognitive Search-Indexeer functies

Met C#kunt u een [Indexeer functie](search-indexer-overview.md) configureren waarmee Doorzoek bare gegevens uit Azure SQL database worden geëxtraheerd en naar een zoek index worden verzonden. Deze zelf studie maakt gebruik van [Azure Cognitive Search .net-client bibliotheken](https://aka.ms/search-sdk) en een .net core-console toepassing om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een gegevens bron maken die verbinding maakt met Azure SQL Database
> * Een Indexeer functie configureren
> * Een Indexeer functie uitvoeren om gegevens in een index te laden
> * Een query uitvoeren op een index als een verificatie stap

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Een bestaande zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [maken](search-create-service-portal.md) of zoeken 

> [!Note]
> U kunt de gratis service voor deze zelf studie gebruiken. Een gratis zoek service beperkt u tot drie indexen, drie Indexeer functies en drie gegevens bronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u over voldoende ruimte beschikt om de nieuwe resources te accepteren.

## <a name="download-source-code"></a>Bron code downloaden

De bron code voor deze zelf studie bevindt zich in de map [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) in de GitHub-opslag plaats [Azure-samples/Search-DotNet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) .

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Voor API-aanroepen zijn de service-URL en een toegangs sleutel vereist. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

## <a name="set-up-connections"></a>Verbindingen instellen

1. Start Visual Studio en open **DotNetHowToIndexers. SLN**.

1. Open in Solution Explorer **appSettings. json** en vervang de waarden van de tijdelijke aanduiding door verbindings gegevens naar uw zoek service. Als de volledige URL "https://my-demo-service.search.windows.net" is, is de naam van de service die u wilt bieden "My-demo-service".

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

Voor de laatste invoer is een bestaande data base vereist. U maakt deze in de volgende stap.

## <a name="prepare-sample-data"></a>Voorbeeld gegevens voorbereiden

In deze stap maakt u een externe gegevens bron op Azure SQL Database die door een Indexeer functie kan worden verkend. U kunt de Azure-portal en het bestand *hotels.sql* uit het voorbeeld gebruiken om de gegevensset in Azure SQL Database te maken. Azure Cognitive Search verbruikt samengevoegde rijen sets, zoals een die is gegenereerd op basis van een weer gave of query. Het SQL-bestand in de voorbeeldoplossing maakt en vult één tabel.

Als u een bestaande Azure SQL Database resource hebt, kunt u de tabel Hotels toevoegen, beginnend bij stap 4.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/).

1. Een **SQL database**zoeken of maken. U kunt de standaardinstellingen en de laagste prijscategorie gebruiken. Eén voordeel van het maken van een server is dat u de gebruikersnaam en het wachtwoord van een beheerder kunt opgeven die in een latere stap nodig zijn om tabellen te maken en te laden.

   ![Pagina nieuwe data base](./media/search-indexer-tutorial/indexer-new-sqldb.png "De pagina Nieuwe database")

1. Klik op **beoordeling + maken** om de nieuwe server en Data Base te implementeren. Wacht tot de server en database zijn geïmplementeerd.

1. Klik in het navigatie deel venster op **query-editor (preview)** en voer de gebruikers naam en het wacht woord van de server beheerder in. 

   Als de toegang wordt geweigerd, kopieert u het client-IP-adres uit het fout bericht en klikt u vervolgens op de koppeling **instellen Server firewall** om een regel toe te voegen die toegang vanaf uw client computer toestaat met behulp van uw client-IP voor het bereik. Het kan enkele minuten duren voordat de regel van kracht wordt.

1. In query-editor klikt u op **query openen** en navigeert u naar de locatie van het bestand *Hotels. SQL* op uw lokale computer. 

1. Selecteer het bestand en klik op **Openen**. Het script moet er ongeveer uitzien als in de volgende schermafbeelding:

   ![SQL-script](./media/search-indexer-tutorial/sql-script.png "SQL-script")

1. Klik op **Uitvoeren** om de query uit te voeren. In het resultatenvenster zou u het bericht moeten zien dat de query is gelukt voor 3 rijen.

1. Als u een rijenset uit deze tabel wilt retourneren, kunt u de volgende query uitvoeren als verificatiestap:

    ```sql
    SELECT * FROM Hotels
    ```

1. Kopieer de ADO.NET-connection string voor de data base. Kopieer onder **instellingen** > **verbindings reeksen**de Connection String ADO.net, vergelijkbaar met het voor beeld hieronder.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Plak de verbindingsreeks in 'AzureSqlConnectionString' als derde vermelding in het bestand **appsettings.json** in Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Voer uw wacht woord in in het connection string in het bestand **appSettings. json** . Data Base-en gebruikers namen worden in uw connection string gekopieerd, maar het wacht woord moet hand matig worden ingevoerd.

## <a name="build-the-solution"></a>De oplossing bouwen

Druk op F5 om de oplossing te bouwen. Het programma wordt uitgevoerd in de foutopsporingsmodus. De status van elke bewerking wordt weergegeven in een consolevenster.

   ![Console-uitvoer](./media/search-indexer-tutorial/console-output.png "Console-uitvoer")

Uw code wordt lokaal uitgevoerd in Visual Studio en maakt verbinding met uw zoek service op Azure, die op zijn beurt verbinding maakt met Azure SQL Database en de gegevensset ophaalt. Met dit aantal bewerkingen zijn er verschillende mogelijke fout punten. Als er een fout optreedt, controleert u eerst de volgende voor waarden:

+ De verbindingsgegevens van de zoekservice die u opgeeft, zijn in deze zelfstudie beperkt tot de servicenaam. Als u de volledige URL hebt ingevoerd, stopt de verwerking bij het maken van de index, met het foutbericht dat er geen verbinding kan worden gemaakt.

+ Gegevens over de databaseverbinding in **appsettings.json**. Dit moet de ADO.NET-verbindingsreeks zijn die u hebt verkregen via de portal en die is gewijzigd, zodat deze een gebruikersnaam en wachtwoord bevat die geldig zijn voor uw database. Het gebruikersaccount moet machtigingen hebben om gegevens op te halen. Het IP-adres van uw lokale client moet toegang hebben.

+ Bronlimieten. De laag gratis heeft een limiet van 3 indexen, Indexeer functies en gegevens bronnen. Een service met de maximale limiet kan geen nieuwe objecten maken.

## <a name="check-results"></a>Resultaten controleren

Gebruik Azure Portal om het maken van objecten te controleren en zoek vervolgens in de index met behulp van **Search Explorer** .

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en open op de pagina **overzicht** van de zoek service elke lijst in de weer gave om te controleren of het object is gemaakt. **Indexes,** **Indexeer functies**en **gegevens bronnen** hebben respectievelijk "Hotels", "Azure-SQL-indexer" en "Azure-SQL".

   ![Tegels met indexeerfuncties en gegevensbronnen](./media/search-indexer-tutorial/tiles-portal.png)

1. Selecteer de index Hotels. **Zoek Explorer** op de pagina hotels is het eerste tabblad. 

1. Klik op **zoeken** om een lege query uit te voeren. 

   De drie vermeldingen in de index worden geretourneerd als JSON-documenten. Search Explorer retourneert documenten in JSON, zodat u de volledige structuur kunt bekijken.

   ![Een query uitvoeren op een index](./media/search-indexer-tutorial/portal-search.png "Een query uitvoeren op een index")
   
1. Geef vervolgens een zoekreeks op: `search=river&$count=true`. 

   Deze query roept een zoekopdracht aan die in de volledige tekst zoekt naar de term `river` en het resultaat bevat het aantal overeenkomende documenten. Het aantal overeenkomende documenten retourneren kan handig zijn in testscenario's wanneer u een grote index met duizenden of miljoenen documenten hebt. In dit geval komt slechts één document overeen met de query.

1. Voer ten slotte een zoektekenreeks in die de JSON-uitvoer beperkt tot de gewenste velden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   De respons van de query wordt beperkt tot geselecteerde velden, wat resulteert in een beknoptere uitvoer.

## <a name="explore-the-code"></a>De code verkennen

Nu u weet wat de voorbeeld code maakt, gaan we terug naar de oplossing om de code te controleren. De relevante code bevindt zich in twee bestanden:

  + **Hotel.cs**, met een schema dat de index definieert
  + **Program.cs**, met functies voor het maken en beheren van structuren in uw service

### <a name="in-hotelcs"></a>In hotel.cs

Het indexschema definieert de verzameling met velden, met inbegrip van kenmerken die toegestane bewerkingen opgeven, bijvoorbeeld of de volledige tekst van een veld kan worden doorzocht, gefilterd of gesorteerd zoals wordt weergegeven in de volgende velddefinitie voor HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Een schema kan ook andere elementen bevatten, zoals scoreprofielen om een zoekscore te verbeteren, aangepaste analysefuncties en andere constructies. In dit geval is het schema echter beperkt gedefinieerd en bestaat het alleen uit velden in de voorbeeldgegevenssets.

### <a name="in-programcs"></a>In Program.cs

Het hoofd programma bevat logica voor het maken van een-client, een index, een gegevens bron en een Indexeer functie. De code controleert op en verwijdert bestaande resources met dezelfde naam, waarbij ervan wordt uitgegaan dat u dit programma meerdere keren uitvoert.

Het gegevens bron object is geconfigureerd met instellingen die specifiek zijn voor Azure SQL database-resources, inclusief [gedeeltelijke of incrementele indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) voor het gebruik van de ingebouwde functies voor het [detecteren van wijzigingen](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) van Azure SQL. De voorbeeld database van de demo in Azure SQL heeft een kolom ' voorlopig verwijderen ' met de naam **IsDeleted**. Als deze kolom is ingesteld op True in de data base, verwijdert de Indexeer functie het bijbehorende document uit de Azure Cognitive Search-index.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Een Indexeer functie-object is platform-neutraal, waarbij de configuratie, planning en aanroep hetzelfde zijn, ongeacht de bron. Dit voor beeld bevat een schema, een reset optie waarmee de indexerings geschiedenis wordt gewist en een methode wordt aangeroepen om de Indexeer functie direct te maken en uit te voeren.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling alle resources of resource groepen in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven.

## <a name="next-steps"></a>Volgende stappen

In azure Cognitive Search zijn Indexeer functies beschikbaar voor meerdere Azure-gegevens bronnen. Als volgende stap bekijkt u de Indexeer functie voor Azure Blob Storage.

> [!div class="nextstepaction"]
> [Documenten in Azure Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md)