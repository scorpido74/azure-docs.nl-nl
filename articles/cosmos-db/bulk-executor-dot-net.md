---
title: Bulkexecutor .NET-bibliotheek gebruiken in Azure Cosmos DB voor bulkimport- en updatebewerkingen
description: Bulk importeert en updatet de Azure Cosmos DB-documenten met behulp van de bulkexecutor .NET-bibliotheek.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: d7600267dcd196a9a5c06c29774ea21d582cd7ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246875"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>De bulkexecutor .NET-bibliotheek gebruiken om bulkbewerkingen uit te voeren in Azure Cosmos DB

Deze zelfstudie bevat instructies over het gebruik van de bulkexecutor .NET-bibliotheek om documenten te importeren en bij te werken naar een Azure Cosmos-container. Zie het overzichtsartikel voor [bulkexecutorlibrary](bulk-executor-overview.md) voor meer informatie over de bibliotheek voor bulkuitvoerbedrijven en hoe deze u helpt gebruik te maken van enorme doorvoer en opslag. In deze zelfstudie ziet u een voorbeeld .NET-toepassing die willekeurig gegenereerde documenten importeert in een Azure Cosmos-container. Na het importeren ziet u hoe u de geïmporteerde gegevens in bulk bijwerken door patches op te geven als bewerkingen die u op specifieke documentvelden wilt uitvoeren.

Momenteel wordt de bulkexecutorbibliotheek alleen ondersteund door de Azure Cosmos DB SQL API- en Gremlin-API-accounts. In dit artikel wordt beschreven hoe u de bulkexecutor .NET-bibliotheek met SQL API-accounts gebruiken. Zie [Bulkbewerkingen uitvoeren in de Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md)voor meer informatie over het gebruik van de bulkexecutor .NET-bibliotheek met Gremlin-API.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 nog niet hebt geïnstalleerd, u de [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u 'Azure-ontwikkeling' inschakelt tijdens de installatie van Visual Studio.

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

* U [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement, gratis en verplichtingen. U ook de Azure Cosmos `https://localhost:8081` [DB-emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) met het eindpunt gebruiken. De primaire sleutel wordt gegeven in [Aanvragen verifiëren](local-emulator.md#authenticating-requests).

* Maak een Azure Cosmos DB SQL API-account met behulp van de stappen die zijn beschreven in de sectie [Databaseaccount maken](create-sql-api-dotnet.md#create-account) van het quickstart-artikel .NET.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Laten we nu overschakelen naar het werken met code door het downloaden van een voorbeeld .NET applicatie van GitHub. Deze toepassing voert bulkbewerkingen uit op de gegevens die zijn opgeslagen in uw Azure Cosmos-account. Als u de toepassing wilt klonen, opent u een opdrachtprompt, navigeert u naar de map waar u de toepassing wilt kopiëren en voert u de volgende opdracht uit:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

De gekloonde opslagplaats bevat twee voorbeelden "BulkImportSample" en "BulkUpdateSample". U een van de voorbeeldtoepassingen openen, de verbindingstekenreeksen in app.config-bestand bijwerken met de verbindingstekenreeksen van uw Azure Cosmos DB-account, de oplossing bouwen en uitvoeren.

De toepassing 'BulkImportSample' genereert willekeurige documenten en bulk importeert deze naar uw Azure Cosmos-account. De bulktoepassing 'BulkUpdateSample' wordt bijgewerkt door patches op te geven als bewerkingen die moeten worden uitgevoerd op specifieke documentvelden. In de volgende secties bekijkt u de code in elk van deze voorbeeld-apps.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Bulkimporterenvan gegevens naar een Azure Cosmos-account

1. Navigeer naar de map BulkImportSample en open het bestand 'BulkImportSample.sln'.  

2. De verbindingstekenreeksen van Azure Cosmos DB worden opgehaald uit het bestand App.config, zoals weergegeven in de volgende code:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   De bulkimporteur maakt een nieuwe database en een container met de databasenaam, containernaam en de doorvoerwaarden die zijn opgegeven in het bestand App.config.

3. Vervolgens wordt het object DocumentClient geïnitialiseerd met de direct TCP-verbindingsmodus:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Het object BulkExecutor wordt geïnitialiseerd met een hoge waarde voor het opnieuw proberen voor wachttijd en aangeschreven aanvragen. En dan zijn ze ingesteld op 0 om congestie controle door te geven aan BulkExecutor voor zijn levensduur.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. De toepassing roept de BulkImportAsync API op. De .NET-bibliotheek biedt twee overbelastingen van de bulkimport-API - een die een lijst met geserialiseerde JSON-documenten accepteert en de andere die een lijst met gedeserialiseerde POCO-documenten accepteert. Voor meer informatie over de definities van elk van deze overbelaste methoden, raadpleegt u de [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync-methode accepteert de volgende parameters:**
   
   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |enableUpsert    |   Een vlag om upsert-bewerkingen op de documenten in te schakelen. Als er al een document met de opgegeven id bestaat, wordt het bijgewerkt. Standaard is deze ingesteld op false.      |
   |schakelt AutomaticIdGeneration uit    |    Een vlag om de automatische generatie ID uit te schakelen. Standaard is het ingesteld op true.     |
   |maxConcurrencyPerPartitionKeyRange    | De maximale mate van gelijktijdigheid per partitiesleutelbereik, instellen op null, zorgt ervoor dat bibliotheek een standaardwaarde van 20 gebruikt. |
   |maxInMemorySortingBatchSize     |  Het maximum aantal documenten dat uit de documentenumerator wordt gehaald, dat in elke fase wordt doorgegeven aan de API-aanroep. Voor de sorteerfase in het geheugen die plaatsvindt voordat bulk wordt geïmporteerd, wordt deze parameter ingesteld op null, waardoor bibliotheek de standaardminimumwaarde (documents.count, 1000000) gebruikt.       |
   |annuleringToken    |    Het annuleringstoken om de bulkimportbewerking op een elegante manier af te sluiten.     |

   **Definitie van bulkimportresponsobject** Het resultaat van de API-aanroep voor bulkimport bevat de volgende kenmerken:

   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |Aantal geïmporteerde documenten (lang)   |  Het totale aantal documenten dat met succes is geïmporteerd uit de totale documenten die zijn geleverd aan de API-aanroep voor bulkimport.       |
   |TotalRequestUnitsConsumed (dubbel)   |   De total request units (RU) verbruikt door de bulk import API call.      |
   |TotalTimeTaken (TimeSpan)    |   De totale tijd die wordt genomen door de API-aanroep voor bulkimport om de uitvoering te voltooien.      |
   |BadInputDocuments (object>\<lijst)   |     De lijst met documenten met slechte indeling die niet zijn geïmporteerd in de API-aanroep voor bulkimport. Repareer de geretourneerde documenten en probeer opnieuw te importeren. Documenten met slechte opmaak omvatten documenten waarvan de id-waarde geen tekenreeks is (null of een ander gegevenstype wordt als ongeldig beschouwd).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Bulkupdategegevens in uw Azure Cosmos-account

U bestaande documenten bijwerken met de BulkUpdateAsync API. In dit voorbeeld stelt `Name` u het veld in `Description` op een nieuwe waarde en verwijdert u het veld uit de bestaande documenten. Raadpleeg de [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)voor de volledige set ondersteunde updatebewerkingen.

1. Navigeer naar de map 'BulkUpdateSample' en open het bestand 'BulkUpdateSample.sln'.  

2. Definieer de update-items samen met de bijbehorende veldupdatebewerkingen. In dit voorbeeld gebruikt `SetUpdateOperation` u `Name` om `UnsetUpdateOperation` het veld `Description` bij te werken en het veld uit alle documenten te verwijderen. U ook andere bewerkingen uitvoeren, zoals een documentveld verhogen met een specifieke waarde, specifieke waarden in een matrixveld duwen of een specifieke waarde uit een matrixveld verwijderen. Raadpleeg de [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)voor meer informatie over verschillende methoden die door de API voor bulkupdates worden geleverd.

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. De toepassing roept de BulkUpdateAsync API aan. Raadpleeg de [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet)voor meer informatie over de definitie van de bulkupdateasync-methode.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync-methode accepteert de volgende parameters:**

   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   De maximale mate van gelijktijdigheid per partitiesleutelbereik, waarbij deze parameter wordt ingesteld op null, zorgt ervoor dat de bibliotheek de standaardwaarde(20) gebruikt.   |
   |maxInMemorySortingBatchSize    |    Het maximum aantal update-items dat uit de updateitems is gehaald, is in elke fase doorgegeven aan de API-aanroep. Voor de sorteerfase in het geheugen die plaatsvindt voordat deze in bulk wordt bijgewerkt, wordt de standaardminimumwaarde van de bibliotheek ingesteld op null(updateItems.count, 1000000).     |
   | annuleringToken|Het annuleringstoken om de bulkupdatebewerking op een elegante manier af te sluiten. |

   **Definitie van bulkupdate-antwoordobject** Het resultaat van de API-aanroep voor bulkupdates bevat de volgende kenmerken:

   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |Aantalbijgewerkte documenten (lang)    |   Het aantal documenten dat met succes is bijgewerkt ten opzichte van de totale documenten die zijn geleverd aan de API-aanroep voor bulkupdates.      |
   |TotalRequestUnitsConsumed (dubbel)   |    De total request units (RU's) verbruikt door de bulk update API call.    |
   |TotalTimeTaken (TimeSpan)   | De totale tijd die wordt genomen door de API-aanroep voor bulkupdates om de uitvoering te voltooien. |
    
## <a name="performance-tips"></a>Tips voor prestaties 

Houd rekening met de volgende punten voor betere prestaties bij het gebruik van de bibliotheek voor bulkuitvoer:

* Voer uw toepassing uit vanaf een virtuele Azure-machine die zich in dezelfde regio bevindt als het schrijfgebied van uw Azure Cosmos-account.  

* Het wordt aanbevolen om één `BulkExecutor` object voor de hele toepassing te instantiëren binnen één virtuele machine die overeenkomt met een specifieke Azure Cosmos-container.  

* Aangezien een enkele bulk operatie API uitvoering verbruikt een groot deel van de client machine CPU en netwerk IO (Dit gebeurt door het paaien van meerdere taken intern). Vermijd het spawnen van meerdere gelijktijdige taken binnen uw toepassingsproces die API-aanroepen voor bulkbewerking uitvoeren. Als een API-aanroep met één bulkbewerking die op één virtuele machine wordt uitgevoerd, de doorvoer van de volledige container niet kan verbruiken (als de doorvoer van uw container > 1 miljoen RU/s) mogelijk is, heeft het de voorkeur om afzonderlijke virtuele machines te maken om gelijktijdig de API-aanroepen voor bulkbewerking uit te voeren.  

* Zorg `InitializeAsync()` ervoor dat de methode wordt aangeroepen nadat u een object BulkExecutor hebt geinstantieerd om de partitiemap van de doelcosmoscontainer op te halen.  

* Zorg ervoor dat **gcServer in** de App.Config van uw toepassing is ingeschakeld voor betere prestaties
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* De bibliotheek zendt sporen uit die kunnen worden verzameld in een logboekbestand of op de console. Als u beide wilt inschakelen, voegt u de volgende code toe aan het App.Config-bestand van uw toepassing.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Volgende stappen

* Zie de details van de [bulkuitvoerder SDK](sql-api-sdk-bulk-executor-dot-net.md)voor meer informatie over de details van het Nuget-pakket en de releasenotes.
