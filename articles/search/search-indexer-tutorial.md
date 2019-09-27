---
title: C#Vind Index gegevens uit Azure SQL-data bases-Azure Search
description: Een C# code voorbeeld waarin wordt getoond hoe u verbinding maakt met Azure SQL database, Doorzoek bare gegevens ophaalt en laadt in een Azure search index.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 1ba0a965de356cfbe7d9a1cfc8d6d2e8da092934
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327180"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>C#Vind Een Azure SQL-database verkennen met de indexeerfuncties van Azure Search

Meer informatie over hoe u een Indexeer functie kunt configureren voor het extra heren van Doorzoek bare gegevens uit een Azure-voor beeld-SQL database. [Indexeerfuncties](search-indexer-overview.md) zijn onderdelen van Azure Search die externe gegevensbronnen verkennen en een [zoekindex](search-what-is-an-index.md) vullen met inhoud. Van alle Indexeer functies is de Indexeer functie voor Azure SQL Database het meest gebruikte. 

Een goede vaardigheid in het configureren van indexeerfuncties is nuttig omdat dit de hoeveelheid code die u moet schrijven en onderhouden vereenvoudigt. In plaats van een schemacompatibele JSON-gegevensset voor te bereiden en te pushen, kunt u een indexeerfunctie koppelen aan een gegevensbron en de indexeerfunctie gegevens laten ophalen en in een index plaatsen. U kunt de indexeerfunctie eventueel uitvoeren volgens een terugkerend schema om zo wijzigingen in de onderliggende gegevensbron op te halen.

In deze zelf studie gebruikt u de [Azure Search .net-client bibliotheken](https://aka.ms/search-sdk) en een .net core-console toepassing om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Informatie over de zoekservice toevoegen aan toepassingsinstellingen
> * Een externe gegevensset voorbereiden in Azure SQL-database 
> * De definities van de index en indexeerfunctie in de voorbeeldcode controleren
> * De code van de indexeerfunctie uitvoeren om gegevens te importeren
> * Zoeken in de index
> * De configuratie van de indexeerfunctie in de portal bekijken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services, hulpprogram ma's en gegevens worden gebruikt in deze Quick Start. 

[Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

[Azure SQL database](https://azure.microsoft.com/services/sql-database/) slaat de externe gegevens bron op die door een Indexeer functie wordt gebruikt. De voorbeeldoplossing biedt een SQL-gegevensbestand om de tabel te maken. De stappen voor het maken van de service en de Data Base zijn opgenomen in deze zelf studie.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), elke editie, kan worden gebruikt om de voorbeeld oplossing uit te voeren. Voor beelden van code en instructies zijn getest in de gratis Community-editie.

[Azure-samples/Search-DotNet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) biedt de voorbeeld oplossing die zich bevindt in de GitHub-opslag plaats van Azure samples. Down load de oplossing en pak deze uit. Oplossingen zijn standaard alleen-lezen. Klik met de rechter muisknop op de oplossing en schakel het kenmerk alleen-lezen uit zodat u bestanden kunt wijzigen.

> [!Note]
> Als u de gratis Azure Search-service gebruikt, bent u beperkt tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-connections"></a>Verbindingen instellen
Verbindingsgegevens voor benodigde services worden opgegeven in het bestand **appsettings.json** in de oplossing. 

1. Open in Visual Studio het bestand **DotNetHowToIndexers. SLN** .

1. Open **appSettings. json** in Solution Explorer zodat u elke instelling kunt invullen.  

De eerste twee vermeldingen die u nu kunt invullen, met behulp van de URL en de beheer sleutels voor uw Azure Search service. Op basis van een eind punt van `https://mydemo.search.windows.net`, is de te bieden service naam `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Voor de laatste invoer is een bestaande data base vereist. U maakt deze in de volgende stap.

## <a name="prepare-sample-data"></a>Voorbeeld gegevens voorbereiden

In deze stap maakt u een externe gegevensbron die een indexeerfunctie kan verkennen. U kunt de Azure-portal en het bestand *hotels.sql* uit het voorbeeld gebruiken om de gegevensset in Azure SQL Database te maken. Azure Search gebruikt platte rijensets, zoals de sets die worden gegenereerd op basis van een weergave of query. Het SQL-bestand in de voorbeeldoplossing maakt en vult één tabel.

In de volgende oefening wordt ervan uitgegaan dat er geen bestaande server of database is en u maakt beide in stap 2. Als u een bestaande resource hebt, kunt u de tabel hotels er desgewenst aan toevoegen vanaf stap 4.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/). 

2. Zoek of maak een **Azure SQL database** om een Data Base, server en resource groep te maken. U kunt de standaardinstellingen en de laagste prijscategorie gebruiken. Eén voordeel van het maken van een server is dat u de gebruikersnaam en het wachtwoord van een beheerder kunt opgeven die in een latere stap nodig zijn om tabellen te maken en te laden.

   ![De pagina Nieuwe database](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Klik op **Maken** om de nieuwe server en database te implementeren. Wacht tot de server en database zijn geïmplementeerd.

4. Open de pagina SQL Database voor de nieuwe database als deze nog niet is geopend. De resourcenaam moet *SQL-database* zijn en niet *SQL-server*.

   ![De pagina SQL-database](./media/search-indexer-tutorial/hotels-db.png)

4. Klik in het navigatie deel venster op **query-editor (preview)** .

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

Zodra de gegevens en configuratie-instellingen zijn geïmplementeerd, is het voorbeeld programma in **DotNetHowToIndexers. SLN** klaar om te bouwen en uit te voeren. Voordat u dat doet, moet u even de tijd nemen om de definities van de index en de indexeerfuncties voor dit voorbeeld te bestuderen. De relevante code staat in twee bestanden:

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

In deze zelfstudie haalt de indexeerfunctie gegevens op uit één gegevensbron. In de praktijk kunt u meerdere Indexeer functies aan dezelfde index koppelen en een geconsolideerde Doorzoek bare index uit meerdere gegevens bronnen maken. U kunt dezelfde combinatie van index en indexeerfunctie gebruiken, waarbij alleen de gegevensbronnen verschillen, of één index gebruiken met verschillende combinaties van indexeerfunctie en gegevensbron, afhankelijk van waar u flexibiliteit nodig hebt.

### <a name="in-programcs"></a>In Program.cs

Het hoofd programma bevat logica voor het maken van een-client, een index, een gegevens bron en een Indexeer functie. De code controleert op en verwijdert bestaande resources met dezelfde naam, waarbij ervan wordt uitgegaan dat u dit programma meerdere keren uitvoert.

Het gegevens bron object is geconfigureerd met instellingen die specifiek zijn voor Azure SQL database-resources, inclusief [incrementele indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) voor het gebruik van de ingebouwde functie voor het [detecteren van wijzigingen](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) van Azure SQL. De voorbeeld database van de demo in Azure SQL heeft een kolom ' voorlopig verwijderen ' met de naam **IsDeleted**. Als deze kolom is ingesteld op True in de data base, verwijdert de Indexeer functie het bijbehorende document uit de Azure Search index.

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



## <a name="run-the-indexer"></a>De indexeerfunctie uitvoeren

In deze stap wordt het programma gecompileerd en uitgevoerd. 

1. Klik in Solution Explorer met de rechtermuisknop op **DotNetHowToIndexers** en selecteer **Build**.
2. Klik nogmaals met de rechtermuisknop op **DotNetHowToIndexers**, gevolgd door **Debug** > **Start new instance**.

Het programma wordt uitgevoerd in de foutopsporingsmodus. De status van elke bewerking wordt weergegeven in een consolevenster.

  ![SQL-script](./media/search-indexer-tutorial/console-output.png)

Uw code wordt lokaal uitgevoerd in Visual Studio en maakt verbinding met uw zoekservice in Azure, die op zijn beurt de verbindingstekenreeks gebruikt om verbinding te maken met Azure SQL Database en de gegevensset op te halen. Met zo veel verschillende bewerkingen zijn er veel punten waar een fout kan optreden, maar als er een fout optreedt, controleert u eerst het volgende:

+ De verbindingsgegevens van de zoekservice die u opgeeft, zijn in deze zelfstudie beperkt tot de servicenaam. Als u de volledige URL hebt ingevoerd, stopt de verwerking bij het maken van de index, met het foutbericht dat er geen verbinding kan worden gemaakt.

+ Gegevens over de databaseverbinding in **appsettings.json**. Dit moet de ADO.NET-verbindingsreeks zijn die u hebt verkregen via de portal en die is gewijzigd, zodat deze een gebruikersnaam en wachtwoord bevat die geldig zijn voor uw database. Het gebruikersaccount moet machtigingen hebben om gegevens op te halen.

+ Bronlimieten. De laag gratis heeft een limiet van 3 indexen, Indexeer functies en gegevens bronnen. Een service met de maximale limiet kan geen nieuwe objecten maken.

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

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en klik op de pagina **overzicht** van zoek services op de koppelingen naar **indexen**, **Indexeer functies**en **gegevens bronnen**.
3. Selecteer afzonderlijke objecten om configuratie-instellingen weer te geven of te wijzigen.

   ![Tegels met indexeerfuncties en gegevensbronnen](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om op te schonen na een zelfstudie is de resourcegroep met de Azure Search-service te verwijderen. U kunt de resourcegroep nu verwijderen om alles daarin permanent te verwijderen. De naam van de resourcegroep staat in de portal op de pagina Overzicht van de Azure Search-service.

## <a name="next-steps"></a>Volgende stappen

U kunt AI-verrijkings algoritmen koppelen aan een indexerings pijplijn. Als volgende stap kunt u verdergaan met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Documenten in Azure Blob Storage indexeren](search-howto-indexing-azure-blob-storage.md)