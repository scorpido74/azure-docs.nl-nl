---
title: 'Zelfstudie: Gegevens uit Azure SQL-databases in C indexeren # '
titleSuffix: Azure Cognitive Search
description: Maak in deze C#-zelfstudie verbinding met Azure SQL-database, haal doorzoekbare gegevens uit en laad deze in een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 7660c89032ea3ef8371655b94b75c1f60603ee32
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78193965"
---
# <a name="tutorial-use-c-to-index-data-from-sql-databases-in-azure-cognitive-search"></a>Zelfstudie: C#gebruiken om gegevens uit SQL-databases in Azure Cognitive Search te indexeren

Configureer een [indexeerder](search-indexer-overview.md) om doorzoekbare gegevens uit azure SQL-database te extraheren en deze naar een zoekindex in Azure Cognitive Search te verzenden. 

In deze zelfstudie worden C# en de [.NET SDK](https://aka.ms/search-sdk) gebruikt om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een gegevensbron maken die verbinding maakt met Azure SQL Database
> * Een indexeerfunctie maken
> * Een indexer uitvoeren om gegevens in een index te laden
> * Een index opvragen als verificatiestap

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Een](search-create-service-portal.md) [bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) maken of zoeken 

> [!Note]
> U gebruik maken van de gratis service voor deze tutorial. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerders en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u ruimte hebt op uw service om de nieuwe bronnen te accepteren.

## <a name="download-files"></a>Bestanden downloaden

Broncode voor deze zelfstudie bevindt zich in de map [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) in de [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub repository.

## <a name="1---create-services"></a>1 - Services maken

Deze zelfstudie gebruikt Azure Cognitive Search voor indexering en query's en Azure SQL Database als externe gegevensbron. Maak indien mogelijk zowel services in dezelfde regio als resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan Azure SQL Database zich in elke regio bevinden.

### <a name="start-with-azure-sql-database"></a>Beginnen met Azure SQL-database

Maak in deze stap een externe gegevensbron in Azure SQL Database die een indexer kan crawlen. U de Azure-portal en het *hotels.sql-bestand* uit de voorbeelddownload gebruiken om de gegevensset in Azure SQL Database te maken. Azure Cognitive Search verbruikt afgevlakte rijsets, zoals een die wordt gegenereerd vanuit een weergave of query. Het SQL-bestand in de voorbeeldoplossing maakt en vult één tabel.

Als u een bestaande Azure SQL Database-bron hebt, u de tabel met hotels eraan toevoegen, te beginnen bij stap 4.

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/).

1. Een **SQL-database**zoeken of maken . U kunt de standaardinstellingen en de laagste prijscategorie gebruiken. Eén voordeel van het maken van een server is dat u de gebruikersnaam en het wachtwoord van een beheerder kunt opgeven die in een latere stap nodig zijn om tabellen te maken en te laden.

   ![De pagina Nieuwe database](./media/search-indexer-tutorial/indexer-new-sqldb.png "De pagina Nieuwe database")

1. Klik **op Controleren + maken** om de nieuwe server en database te implementeren. Wacht tot de server en database zijn geïmplementeerd.

1. Klik in het navigatiedeelvenster op **Query-editor (voorbeeld)** en voer de gebruikersnaam en het wachtwoord van de serverbeheerder in. 

   Als de toegang wordt geweigerd, kopieert u het IP-adres van de client uit het foutbericht en klikt u vervolgens op de firewallkoppeling **Server instellen** om een regel toe te voegen die toegang geeft vanaf uw clientcomputer, met behulp van uw client-IP voor het bereik. Het kan enkele minuten duren voordat de regel van kracht wordt.

1. Klik in de queryeditor op **Query openen** en navigeer naar de locatie van het *bestand hotels.sql* op uw lokale computer. 

1. Selecteer het bestand en klik op **Openen**. Het script moet er ongeveer uitzien als in de volgende schermafbeelding:

   ![SQL-script](./media/search-indexer-tutorial/sql-script.png "SQL-script")

1. Klik op **Uitvoeren** om de query uit te voeren. In het resultatenvenster zou u het bericht moeten zien dat de query is gelukt voor 3 rijen.

1. Als u een rijenset uit deze tabel wilt retourneren, kunt u de volgende query uitvoeren als verificatiestap:

    ```sql
    SELECT * FROM Hotels
    ```

1. Kopieer de ADO.NET verbindingstekenreeks voor de database. Kopieer onder **Instellingen** > **verbindingstekenreeksen**de ADO.NET verbindingstekenreeks, vergelijkbaar met het onderstaande voorbeeld.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

U moet deze verbinding string in de volgende oefening, het opzetten van uw omgeving.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De volgende component is Azure Cognitive Search, die u [maken in de portal.](search-create-service-portal.md) U de gratis laag gebruiken om deze walkthrough te voltooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een api-sleutel voor beheerders en URL ophalen voor Azure Cognitive Search

API-aanroepen vereisen de service-URL en een toegangssleutel. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![Een HTTP-eindpunt en toegangssleutel downloaden](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel downloaden")

## <a name="2---set-up-your-environment"></a>2 - Uw omgeving instellen

1. Start Visual Studio en open **DotNetHowToIndexers.sln**.

1. Open in Solution Explorer **appsettings.json** om verbindingsgegevens te verstrekken.

1. Voor `searchServiceName`, als dehttps://my-demo-service.search.windows.netvolledige URL is " ", de service naam te bieden is "my-demo-service".

1. Want `AzureSqlConnectionString`de tekenreeksindeling is vergelijkbaar met dit:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Controleer in de verbindingstekenreeks of de verbindingstekenreeks een geldig wachtwoord bevat. Terwijl de database en gebruikersnamen worden gekopieerd, moet het wachtwoord handmatig worden ingevoerd.

## <a name="3---create-the-pipeline"></a>3 - De pijplijn maken

Indexers vereisen een gegevensbronobject en een index. Relevante code is in twee bestanden:

  + **hotel.cs**, met een schema dat de index definieert
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

Het hoofdprogramma bevat logica voor het maken van een client, een index, een gegevensbron en een indexer. De code controleert op en verwijdert bestaande resources met dezelfde naam, waarbij ervan wordt uitgegaan dat u dit programma meerdere keren uitvoert.

Het gegevensbronobject is geconfigureerd met instellingen die specifiek zijn voor Azure SQL-databasebronnen, inclusief [gedeeltelijke of incrementele indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) voor het gebruik van de ingebouwde functies voor [wijzigingsdetectie](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) van Azure SQL. De demo hotels database in Azure SQL heeft een "soft delete" kolom met de naam **IsDeleted**. Wanneer deze kolom is ingesteld op true in de database, verwijdert de indexer het bijbehorende document uit de Azure Cognitive Search-index.

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

Een indexerobject is platform-agnostisch, waarbij configuratie, planning en aanroep hetzelfde zijn, ongeacht de bron. Deze voorbeeldindexer bevat een planning, een resetoptie die de indexergeschiedenis wist en roept een methode aan om de indexer onmiddellijk te maken en uit te voeren.

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

## <a name="4---build-the-solution"></a>4 - Bouw de oplossing

Druk op F5 om de oplossing te bouwen en uit te voeren. Het programma wordt uitgevoerd in de foutopsporingsmodus. De status van elke bewerking wordt weergegeven in een consolevenster.

   ![Console-uitvoer](./media/search-indexer-tutorial/console-output.png "Console-uitvoer")

Uw code wordt lokaal uitgevoerd in Visual Studio en maakt verbinding met uw zoekservice op Azure, die op zijn beurt verbinding maakt met Azure SQL Database en de gegevensset ophaalt. Met zoveel bewerkingen zijn er verschillende potentiële uitvalpunten. Als er een fout optreedt, controleert u eerst de volgende voorwaarden:

+ De verbindingsgegevens van de zoekservice die u opgeeft, zijn in deze zelfstudie beperkt tot de servicenaam. Als u de volledige URL hebt ingevoerd, stopt de verwerking bij het maken van de index, met het foutbericht dat er geen verbinding kan worden gemaakt.

+ Gegevens over de databaseverbinding in **appsettings.json**. Dit moet de ADO.NET-verbindingsreeks zijn die u hebt verkregen via de portal en die is gewijzigd, zodat deze een gebruikersnaam en wachtwoord bevat die geldig zijn voor uw database. Het gebruikersaccount moet machtigingen hebben om gegevens op te halen. Uw lokale client IP-adres moet worden toegestaan toegang.

+ Bronlimieten. Bedenk dat de laag Gratis limieten van 3 indexen, indexeerders en gegevensbronnen heeft. Een service met de maximale limiet kan geen nieuwe objecten maken.

## <a name="5---search"></a>5 - Zoeken

Gebruik Azure-portal om het maken van objecten te verifiëren en gebruik **vervolgens Zoekverkenner** om de index op te vragen.

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en open elke lijst op de pagina **Overzicht** van uw zoekservice om te controleren of het object is gemaakt. **Indexen**, **Indexers**en **Data Sources** zullen respectievelijk "hotels", "azure-sql-indexer" en "azure-sql" hebben.

   ![Tegels met indexeerfuncties en gegevensbronnen](./media/search-indexer-tutorial/tiles-portal.png)

1. Selecteer de hotelindex. Op de pagina met hotels is **Zoekexplorer** het eerste tabblad. 

1. Klik **op Zoeken** om een lege query uit te geven. 

   De drie vermeldingen in de index worden geretourneerd als JSON-documenten. Search Explorer retourneert documenten in JSON, zodat u de volledige structuur kunt bekijken.

   ![Een index opvragen](./media/search-indexer-tutorial/portal-search.png "Een index opvragen")
   
1. Geef vervolgens een zoekreeks op: `search=river&$count=true`. 

   Deze query roept een zoekopdracht aan die in de volledige tekst zoekt naar de term `river` en het resultaat bevat het aantal overeenkomende documenten. Het aantal overeenkomende documenten retourneren kan handig zijn in testscenario's wanneer u een grote index met duizenden of miljoenen documenten hebt. In dit geval komt slechts één document overeen met de query.

1. Voer ten slotte een zoektekenreeks in die de JSON-uitvoer beperkt tot de gewenste velden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   De respons van de query wordt beperkt tot geselecteerde velden, wat resulteert in een beknoptere uitvoer.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele ontwikkelingsfase is de meest praktische benadering voor ontwerpiteratie het verwijderen van de objecten uit Azure Cognitive Search en uw code in staat te stellen ze opnieuw op te bouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

De voorbeeldcode voor deze zelfstudie controleert op bestaande objecten en verwijdert deze, zodat u uw code opnieuw uitvoeren.

U de portal ook gebruiken om indexen, indexeerders en gegevensbronnen te verwijderen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling Alle resources of Resourcegroepen in het linkernavigatiedeelvenster.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basisprincipes van SQL Database-indexering, nemen we eenkijkje bij de indexerconfiguratie.

> [!div class="nextstepaction"]
> [Een Azure SQL-databaseindexer configureren](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)