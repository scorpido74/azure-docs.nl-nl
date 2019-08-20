---
title: De .NET-bibliotheek voor bulk-uitvoering gebruiken voor het uitvoeren van bulk import-en update bewerkingen in Azure Cosmos DB
description: Azure Cosmos DB documenten bulksgewijs importeren en bijwerken met behulp van de .NET-bibliotheek voor bulksgewijs door voeren.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 0b74c7b178ee4512067de4b8decba0c3c565ccd4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616970"
---
# <a name="use-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Bulksgewijs uitvoeren van de .NET-bibliotheek gebruiken voor bulk bewerkingen in Azure Cosmos DB

Deze zelf studie bevat instructies over het gebruik van de invoerder .NET-bibliotheek van Azure Cosmos DB voor het importeren en bijwerken van documenten naar de Azure Cosmos-container. Zie overzichts artikel bulk-uitvoerder [bibliotheek](bulk-executor-overview.md) voor meer informatie over de bibliotheek voor bulk doorvoer In deze zelf studie wordt een voor beeld van een .NET-toepassing weer geven waarmee wille keurig gegenereerde documenten bulksgewijs worden geïmporteerd in een Azure Cosmos-container. Na het importeren ziet u hoe u de geïmporteerde gegevens bulksgewijs kunt bijwerken door het opgeven van patches als bewerkingen die moeten worden uitgevoerd op specifieke document velden. 

Bulksgewijs executor-bibliotheek wordt momenteel ondersteund door Azure Cosmos DB SQL API en Gremlin-API-accounts. In dit artikel wordt beschreven hoe u een bulk-uitvoerder-.NET-bibliotheek met SQL API-accounts gebruikt. Zie voor meer informatie over het gebruik van grote hoeveelheden executor .NET-bibliotheek met Gremlin-API, [bulksgewijs bewerkingen uitvoeren in Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md). 

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 nog niet is geïnstalleerd, kunt u de [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u Azure-ontwikkeling inschakelt tijdens de installatie van Visual Studio.

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint. 

* [Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, zonder kosten en zonder verplichtingen. U kunt ook de Azure Cosmos DB- [emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) gebruiken met het `https://localhost:8081` eind punt. De primaire sleutel wordt gegeven in [Aanvragen verifiëren](local-emulator.md#authenticating-requests).

* Maak een Azure Cosmos DB SQL-API-account met behulp van de stappen in het gedeelte [Database account maken](create-sql-api-dotnet.md#create-account) van het artikel .net quick start. 

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu naar het werken met code door enkele voor beelden van .NET-toepassingen te downloaden vanuit GitHub. Deze toepassingen voeren bulk bewerkingen uit op Azure Cosmos DB gegevens. Als u de toepassingen wilt klonen, opent u een opdracht prompt, navigeert u naar de map waarnaar u deze wilt kopiëren en voert u de volgende opdracht uit:

```
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

De gekloonde opslag plaats bevat twee voor beelden van ' BulkImportSample ' en ' BulkUpdateSample '. U kunt een van de voorbeeld toepassingen openen, de verbindings reeksen in het bestand app. config bijwerken met de verbindings reeksen van uw Azure Cosmos DB-account, de oplossing bouwen en deze uitvoeren. 

De toepassing ' BulkImportSample ' genereert wille keurige documenten en importeert deze in Azure Cosmos DB. Met de toepassing ' BulkUpdateSample ' worden de geïmporteerde documenten bulksgewijs bijgewerkt door patches op te geven als bewerkingen die moeten worden uitgevoerd op specifieke document velden. In de volgende secties controleert u de code in elk van deze voor beelden van apps.

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Bulksgewijs importeren van gegevens met Azure Cosmos DB

1. Ga naar de map ' BulkImportSample ' en open het bestand ' BulkImportSample. SLN '.  

2. De verbindings reeksen van de Azure Cosmos DB worden opgehaald uit het bestand app. config, zoals wordt weer gegeven in de volgende code:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   Het importprogramma bulksgewijs maakt een nieuwe database en een verzameling met de databasenaam, de naam van verzameling en de doorvoer die zijn opgegeven in het bestand App.config. 

3. De volgende keer dat het object DocumentClient wordt geïnitialiseerd met de directe TCP-verbindings modus:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. Het BulkExecutor-object wordt geïnitialiseerd met een hoge waarde voor nieuwe pogingen voor wacht tijd en vertraagde aanvragen. En ze worden vervolgens ingesteld op 0 om de levens duur van de congestie controle door te geven aan BulkExecutor.  

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

5. De toepassing roept de BulkImportAsync-API aan. De .NET-bibliotheek biedt twee overbelasting van de API voor bulk import: een die een lijst met geserialiseerde JSON-documenten accepteert en de andere een lijst met gegedeserialiseerde POCO-documenten accepteert. Raadpleeg [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet)voor meer informatie over de definities van elk van deze overbelaste methoden.

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **De methode BulkImportAsync accepteert de volgende para meters:**
   
   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |enableUpsert    |   Een markering om upsert van de documenten in te scha kelen. Als een document met de opgegeven ID bestaat al, wordt deze bijgewerkt. Deze waarde is standaard ingesteld op ONWAAR.      |
   |disableAutomaticIdGeneration    |    Een vlag om uit te schakelen automatisch genereren van ID. Standaard is ingesteld op true.     |
   |maxConcurrencyPerPartitionKeyRange    | De maximale graad van gelijktijdigheid per partitie sleutel bereik, ingesteld op NULL zorgt ervoor dat de tape wisselaar een standaard waarde van 20 gebruikt. |
   |maxInMemorySortingBatchSize     |  Het maximum aantal documenten dat wordt opgehaald uit de document-enumerator die wordt door gegeven aan de API-aanroep in elke fase.  Voor de sorteer fase vóór verwerking in het geheugen voordat bulksgewijs wordt geïmporteerd, wordt door het instellen op null de standaard waarde van min (documenten. Count, 1000000) gebruikt.       |
   |cancellationToken    |    Het annulerings token om bulksgewijs importeren zonder problemen af te sluiten.     |

   **Definitie van antwoord object voor bulk import** Het resultaat van de API-aanroep voor Bulk Import bevat de volgende kenmerken:

   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |NumberOfDocumentsImported (lang)   |  Het totale aantal documenten die zijn geïmporteerd uit de documenten die zijn opgegeven voor het bulksgewijs importeren API-aanroep.       |
   |TotalRequestUnitsConsumed (double)   |   Het totaal aantal aanvragen van aanvraageenheden (RU) die worden gebruikt door de bulksgewijs importeren API-aanroep.      |
   |TotalTimeTaken (time span)    |   De totale tijd die door de bulkimport API-aanroep uitgevoerd.      |
   |BadInputDocuments (lijst\<object >)   |     De lijst met slechte indeling documenten die zijn niet geïmporteerd in de bulksgewijs importeren API-aanroep. Gebruiker moet de geretourneerde documenten los en probeer het opnieuw importeren. -Ongeldige indeling of meer documenten waarvan u de id-waarde geen tekenreeks is (null of een andere gegevenstype is als ongeldig wordt beschouwd).    |

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Gegevens voor bulksgewijs bijwerken in Azure Cosmos DB

U kunt bestaande documenten met behulp van de API BulkUpdateAsync bijwerken. In dit voorbeeld wordt u het veld naam ingesteld op een nieuwe waarde en de beschrijving van veld verwijderen uit de bestaande documenten. Raadpleeg [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)voor meer informatie over de volledige set ondersteunde update bewerkingen voor velden. 

1. Ga naar de map ' BulkUpdateSample ' en open het bestand ' BulkUpdateSample. SLN '.  

2. Definieer de update-items samen met de bijbehorende veld Update bewerkingen. In dit voorbeeld gebruikt u SetUpdateOperation het veld naam en UnsetUpdateOperation het omschrijvingsveld verwijderen uit alle documenten bij te werken. U kunt ook andere bewerkingen zoals het verhogen van een veld van het document door een specifieke waarde uitvoeren, push-specifieke waarden in het matrixveld van een of een specifieke waarde verwijderen uit een matrixveld. Raadpleeg de [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)voor meer informatie over de verschillende methoden van de API voor bulksgewijs bijwerken.

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

3. De toepassing roept de BulkUpdateAsync-API aan. Raadpleeg [API-documentatie](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet)voor meer informatie over de definitie van de BulkUpdateAsync-methode.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **De methode BulkUpdateAsync accepteert de volgende para meters:**

   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   De maximale graad van gelijktijdigheid per partitie sleutel bereik, ingesteld op NULL zorgt ervoor dat de standaard waarde van 20 wordt gebruikt voor de tape wisselaar.   |
   |maxInMemorySortingBatchSize    |    Het maximum aantal update-items dat is opgehaald uit de enumerator update-items die zijn door gegeven aan de API-aanroep in elke fase voor het vooraf verwerken van de verwerking in het geheugen voordat bulksgewijs wordt bijgewerkt. als deze optie is ingesteld op NULL, wordt de standaard waarde van min (updateItems. Count, 1000000).     |
   | cancellationToken|Het annulerings token voor het op de juiste wijze afsluiten van bulk-update. |

   **Definitie van antwoord object voor bulk update** Het resultaat van de API-aanroep voor bulk updates bevat de volgende kenmerken:

   |**Parameter**  |**Beschrijving** |
   |---------|---------|
   |NumberOfDocumentsUpdated (lang)    |   Het totaal aantal documenten dat is bijgewerkt met de API-aanroep voor bulksgewijs bijwerken.      |
   |TotalRequestUnitsConsumed (double)   |    Het totale aantal aanvraageenheden (RU) die worden gebruikt door de bulk-update-API-aanroep.    |
   |TotalTimeTaken (time span)   | De totale tijd die door de bulksgewijs bijwerken API-aanroep uitgevoerd. |
    
## <a name="performance-tips"></a>Tips voor prestaties 

Houd rekening met de volgende punten voor betere prestaties bij het gebruik van grote hoeveelheden executor-bibliotheek:

* Voor de beste prestaties voert u uw toepassing uit vanaf een virtuele machine van Azure die zich in dezelfde regio bevindt als de schrijf regio van uw Cosmos DB-account.  

* Het is raadzaam om één BulkExecutor-object voor de hele toepassing te instantiëren binnen één virtuele machine die overeenkomt met een specifieke Cosmos-container.  

* Omdat de uitvoering van een enkele bulksgewijs bewerking API een grote hoeveelheid CPU- en IO van de client-computer verbruikt. Dit gebeurt door bij het maken van meerdere taken intern, te voorkomen dat bij het maken van meerdere gelijktijdige taken binnen uw het toepassingsproces dat elke uitvoering bulksgewijze bewerking API-aanroepen. Als eenmalige API-aanroepen voor bulk bewerkingen die worden uitgevoerd op één virtuele machine, de door Voer van de gehele container niet kunnen verbruiken (als de door Voer van uw container > 1.000.000 RU/s), is het raadzaam om afzonderlijke virtuele machines te maken die gelijktijdig kunnen worden uitgevoerd. API-aanroepen in bulk bewerking.  

* Zorg ervoor dat InitializeAsync () wordt aangeroepen nadat een BulkExecutor-object is geactiveerd om de doel-Cosmos container partitie toewijzing op te halen.  

* Zorg ervoor dat **gcServer** is ingeschakeld voor betere prestaties in de app. config van uw toepassing.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* De bibliotheek verzendt traceringen die kunnen worden verzameld in een logboek bestand of in de-console. Als u beide wilt inschakelen, voegt u het volgende toe aan de app. config van uw toepassing.

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
* Zie voor meer informatie over Nuget-pakket gegevens en release opmerkingen van de .NET-bibliotheek voor bulk-uitvoerder-[SDK](sql-api-sdk-bulk-executor-dot-net.md). 
