---
title: 'Tutorial: Index data in C# from Azure SQL databases'
titleSuffix: Azure Cognitive Search
description: In this C# tutorial, connect to Azure SQL database, extract searchable data, and load it into an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406714"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Import Azure SQL database in C# using Azure Cognitive Search indexers

Learn how to configure an indexer for extracting searchable data from a sample Azure SQL database. [Indexers](search-indexer-overview.md) are a component of Azure Cognitive Search that crawl external data sources, populating a [search index](search-what-is-an-index.md) with content. Of all indexers, the indexer for Azure SQL Database is the most widely used. 

Een goede vaardigheid in het configureren van indexeerfuncties is nuttig omdat dit de hoeveelheid code die u moet schrijven en onderhouden vereenvoudigt. In plaats van een schemacompatibele JSON-gegevensset voor te bereiden en te pushen, kunt u een indexeerfunctie koppelen aan een gegevensbron en de indexeerfunctie gegevens laten ophalen en in een index plaatsen. U kunt de indexeerfunctie eventueel uitvoeren volgens een terugkerend schema om zo wijzigingen in de onderliggende gegevensbron op te halen.

In this tutorial, use the [Azure Cognitive Search .NET client libraries](https://aka.ms/search-sdk) and a .NET Core console application to perform the following tasks:

> [!div class="checklist"]
> * Informatie over de zoekservice toevoegen aan toepassingsinstellingen
> * Een externe gegevensset voorbereiden in Azure SQL-database 
> * De definities van de index en indexeerfunctie in de voorbeeldcode controleren
> * De code van de indexeerfunctie uitvoeren om gegevens te importeren
> * Zoeken in de index
> * De configuratie van de indexeerfunctie in de portal bekijken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

The following services, tools, and data are used in this quickstart. 

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stores the external data source used by an indexer. De voorbeeldoplossing biedt een SQL-gegevensbestand om de tabel te maken. Steps for creating the service and database are provided in this tutorial.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), any edition, can be used to run the sample solution. Sample code and instructions were tested on the free Community edition.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) provides the sample solution, located in the Azure samples GitHub repository. Download and extract the solution. By default, solutions are read-only. Right-click the solution and clear the read-only attribute so that you can modify files.

> [!Note]
> If you are using the free Azure Cognitive Search service, you are limited to three indexes, three indexers, and three data sources. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="get-a-key-and-url"></a>Get a key and URL

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-connections"></a>Verbindingen instellen
Verbindingsgegevens voor benodigde services worden opgegeven in het bestand **appsettings.json** in de oplossing. 

1. In Visual Studio, open the **DotNetHowToIndexers.sln** file.

1. In Solution Explorer, open **appsettings.json** so that you can populate each setting.  

The first two entries you can fill in right now, using the URL and admin keys for your Azure Cognitive Search service. Given an endpoint of `https://mydemo.search.windows.net`, the service name to provide is `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

The last entry requires an existing database. You'll create it in the next step.

## <a name="prepare-sample-data"></a>Prepare sample data

In deze stap maakt u een externe gegevensbron die een indexeerfunctie kan verkennen. U kunt de Azure-portal en het bestand *hotels.sql* uit het voorbeeld gebruiken om de gegevensset in Azure SQL Database te maken. Azure Cognitive Search consumes flattened rowsets, such as one generated from a view or query. Het SQL-bestand in de voorbeeldoplossing maakt en vult één tabel.

In de volgende oefening wordt ervan uitgegaan dat er geen bestaande server of database is en u maakt beide in stap 2. Als u een bestaande resource hebt, kunt u de tabel hotels er desgewenst aan toevoegen vanaf stap 4.

1. [Sign in to the Azure portal](https://portal.azure.com/). 

2. Find or create an **Azure SQL Database** to create a database, server, and resource group. U kunt de standaardinstellingen en de laagste prijscategorie gebruiken. Eén voordeel van het maken van een server is dat u de gebruikersnaam en het wachtwoord van een beheerder kunt opgeven die in een latere stap nodig zijn om tabellen te maken en te laden.

   ![De pagina Nieuwe database](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Klik op **Maken** om de nieuwe server en database te implementeren. Wacht tot de server en database zijn geïmplementeerd.

4. Open de pagina SQL Database voor de nieuwe database als deze nog niet is geopend. De resourcenaam moet *SQL-database* zijn en niet *SQL-server*.

   ![De pagina SQL-database](./media/search-indexer-tutorial/hotels-db.png)

4. On the navigation pane, click **Query editor (preview)** .

5. Klik op **Aanmelden** en voer de gebruikersnaam en het wachtwoord van de serverbeheerder in.

6. Klik op **Query openen** en navigeer naar de locatie van *hotels.sql*. 

7. Selecteer het bestand en klik op **Openen**. Het script moet er ongeveer uitzien als in de volgende schermafbeelding:

   ![SQL-script](./media/search-indexer-tutorial/sql-script.png)

8. Klik op **Uitvoeren** om de query uit te voeren. In het resultatenvenster zou u het bericht moeten zien dat de query is gelukt voor 3 rijen.

9. Als u een rijenset uit deze tabel wilt retourneren, kunt u de volgende query uitvoeren als verificatiestap:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    De prototypequery, `SELECT * FROM Hotels`, werkt niet in de query-editor. De voorbeeldgegevens bevatten geografische coördinaten in het veld Locatie dat momenteel niet door de editor wordt verwerkt. Voor een lijst met andere kolommen waarvoor u een query kunt uitvoeren, kunt u deze instructie uitvoeren: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Nu u een externe gegevensset hebt, kopieert u de ADO.NET-verbindingsreeks voor de database. Ga op de pagina SQL Database van uw database naar **Instellingen** > **Verbindingsreeksen** en kopieer de ADO.NET-verbindingsreeks.
 
    Een ADO.NET-verbindingsreeks ziet eruit als in het volgende voorbeeld, dat is gewijzigd zodat een geldige databasenaam, gebruikersnaam en wachtwoord worden gebruikt.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Plak de verbindingsreeks in 'AzureSqlConnectionString' als derde vermelding in het bestand **appsettings.json** in Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>De code begrijpen

Once the data and configuration settings are in place, the sample program in **DotNetHowToIndexers.sln** is ready to build and run. Voordat u dat doet, moet u even de tijd nemen om de definities van de index en de indexeerfuncties voor dit voorbeeld te bestuderen. De relevante code staat in twee bestanden:

  + **hotel.cs**, dat het schema bevat dat de index definieert
  + **Program.cs**, dat de functies bevat die de structuren in uw service maken en beheren

### <a name="in-hotelcs"></a>In hotel.cs

Het indexschema definieert de verzameling met velden, met inbegrip van kenmerken die toegestane bewerkingen opgeven, bijvoorbeeld of de volledige tekst van een veld kan worden doorzocht, gefilterd of gesorteerd zoals wordt weergegeven in de volgende velddefinitie voor HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Een schema kan ook andere elementen bevatten, zoals scoreprofielen om een zoekscore te verbeteren, aangepaste analysefuncties en andere constructies. In dit geval is het schema echter beperkt gedefinieerd en bestaat het alleen uit velden in de voorbeeldgegevenssets.

In deze zelfstudie haalt de indexeerfunctie gegevens op uit één gegevensbron. In practice, you can attach multiple indexers to the same index, creating a consolidated searchable index from multiple data sources. U kunt dezelfde combinatie van index en indexeerfunctie gebruiken, waarbij alleen de gegevensbronnen verschillen, of één index gebruiken met verschillende combinaties van indexeerfunctie en gegevensbron, afhankelijk van waar u flexibiliteit nodig hebt.

### <a name="in-programcs"></a>In Program.cs

The main program includes logic for creating a client, an index, a data source, and an indexer. De code controleert op en verwijdert bestaande resources met dezelfde naam, waarbij ervan wordt uitgegaan dat u dit programma meerdere keren uitvoert.

The data source object is configured with settings that are specific to Azure SQL database resources, including [incremental indexing](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) for leveraging the built-in [change detection features](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) of Azure SQL. The demo hotels database in Azure SQL has a "soft delete" column named **IsDeleted**. When this column is set to true in the database, the indexer removes the corresponding document from the Azure Cognitive Search index.

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

An indexer object is platform-agnostic, where  configuration, scheduling, and invocation are the same regardless of the source. This example indexer includes a schedule, a reset option that clears indexer history, and calls a method to create and run the indexer immediately.

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



## <a name="run-the-indexer"></a>De indexeerfunctie uitvoeren

In deze stap wordt het programma gecompileerd en uitgevoerd. 

1. Klik in Solution Explorer met de rechtermuisknop op **DotNetHowToIndexers** en selecteer **Build**.
2. Klik nogmaals met de rechtermuisknop op **DotNetHowToIndexers**, gevolgd door **Debug** > **Start new instance**.

Het programma wordt uitgevoerd in de foutopsporingsmodus. De status van elke bewerking wordt weergegeven in een consolevenster.

  ![SQL-script](./media/search-indexer-tutorial/console-output.png)

Uw code wordt lokaal uitgevoerd in Visual Studio en maakt verbinding met uw zoekservice in Azure, die op zijn beurt de verbindingstekenreeks gebruikt om verbinding te maken met Azure SQL Database en de gegevensset op te halen. Met zo veel verschillende bewerkingen zijn er veel punten waar een fout kan optreden, maar als er een fout optreedt, controleert u eerst het volgende:

+ De verbindingsgegevens van de zoekservice die u opgeeft, zijn in deze zelfstudie beperkt tot de servicenaam. Als u de volledige URL hebt ingevoerd, stopt de verwerking bij het maken van de index, met het foutbericht dat er geen verbinding kan worden gemaakt.

+ Gegevens over de databaseverbinding in **appsettings.json**. Dit moet de ADO.NET-verbindingsreeks zijn die u hebt verkregen via de portal en die is gewijzigd, zodat deze een gebruikersnaam en wachtwoord bevat die geldig zijn voor uw database. Het gebruikersaccount moet machtigingen hebben om gegevens op te halen.

+ Bronlimieten. Recall that the Free tier has limits of 3 indexes, indexers, and data sources. Een service met de maximale limiet kan geen nieuwe objecten maken.

## <a name="search-the-index"></a>Zoeken in de index 

Klik in de Azure Portal op de overzichtspagina van de zoekservice op **Search Explorer** bovenaan om enkele query's voor de nieuwe index te verzenden.

1. Klik op **Index wijzigen** bovenaan om de index *hotels* te selecteren.

2. Klik op de knop **Zoeken** om een lege zoekopdracht te geven. 

   De drie vermeldingen in de index worden geretourneerd als JSON-documenten. Search Explorer retourneert documenten in JSON, zodat u de volledige structuur kunt bekijken.

3. Geef vervolgens een zoekreeks op: `search=river&$count=true`. 

   Deze query roept een zoekopdracht aan die in de volledige tekst zoekt naar de term `river` en het resultaat bevat het aantal overeenkomende documenten. Het aantal overeenkomende documenten retourneren kan handig zijn in testscenario's wanneer u een grote index met duizenden of miljoenen documenten hebt. In dit geval komt slechts één document overeen met de query.

4. Voer ten slotte een zoektekenreeks in die de JSON-uitvoer beperkt tot de gewenste velden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   De respons van de query wordt beperkt tot geselecteerde velden, wat resulteert in een beknoptere uitvoer.

## <a name="view-indexer-configuration"></a>De configuratie van de indexeerfunctie bekijken

Alle indexeerfuncties, inclusief de functie die u zojuist via programmacode hebt gemaakt, worden vermeld in de portal. U kunt de definitie van een indexeerfunctie openen en de gegevensbron ervan weergeven of een vernieuwingsschema configureren om nieuwe en gewijzigde rijen op te halen.

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, click the links for **Indexes**, **Indexers**, and **Data Sources**.
3. Select individual objects to view or modify configuration settings.

   ![Tegels met indexeerfuncties en gegevensbronnen](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service. U kunt de resourcegroep nu verwijderen om alles daarin permanent te verwijderen. In the portal, the resource group name is on the Overview page of Azure Cognitive Search service.

## <a name="next-steps"></a>Volgende stappen

You can attach AI enrichment algorithms to an indexer pipeline. Als volgende stap kunt u verdergaan met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Documenten in Azure Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md)