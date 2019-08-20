---
title: Met behulp van Java-bibliotheek voor bulksgewijs executor bulksgewijs importeren en bijwerken van bewerkingen in Azure Cosmos DB
description: Bulksgewijs importeren en bijwerken van Azure Cosmos DB-documenten met behulp van grote hoeveelheden executor Java-bibliotheek.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: ef006e94ee22886f1129c7c9ca31e20503312fe3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616935"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Bulksgewijs executor Java-clientbibliotheek gebruiken om te bulksgewijs bewerkingen uitvoeren op Azure Cosmos DB-gegevens

In deze zelfstudie vindt u instructies over het gebruik van de Azure Cosmos DB bulksgewijs executor Java-bibliotheek voor het importeren en bijwerken van Azure Cosmos DB-documenten. Zie voor meer informatie over het bulksgewijs executor-bibliotheek en hoe Hiermee kunt u gebruikmaken van de enorme doorvoer en opslag, [bulk-overzicht van de bibliotheek executor](bulk-executor-overview.md) artikel. In deze zelf studie bouwt u een Java-toepassing die wille keurige documenten genereert en deze worden bulksgewijs geïmporteerd in een Azure Cosmos-container. Na het importeren wordt u bulksgewijs sommige eigenschappen van een document bij te werken. 

Op dit moment wordt de bibliotheek voor bulksgewijs uitvoering alleen ondersteund door Azure Cosmos DB-API-accounts voor SQL-API'S en Gremlin. In dit artikel wordt beschreven hoe u een bulk-uitvoerder Java-bibliotheek met SQL API-accounts gebruikt. Zie voor meer informatie over het gebruik van grote hoeveelheden executor .NET-bibliotheek met Gremlin-API, [bulksgewijs bewerkingen uitvoeren in Azure Cosmos DB Gremlin API](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.  

* U kunt [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, gratis en toezeg gingen. U kunt ook de Azure Cosmos DB- [emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) gebruiken met het `https://localhost:8081` eind punt. De primaire sleutel wordt gegeven in [Aanvragen verifiëren](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)  
  - Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.  

  - Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.

* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair [Maven](https://maven.apache.org/)-archief  
  
  - Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.

* Maak een Azure Cosmos DB SQL-API-account met behulp van de stappen die worden beschreven in de sectie [Database account maken](create-sql-api-java.md#create-a-database-account) van het artikel Java Quick Start.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaan we werken met code door een voorbeeld van Java-toepassing downloaden vanuit GitHub. Deze toepassing voert bulksgewijs bewerkingen op Azure Cosmos DB-gegevens. Als u wilt klonen van de toepassing, open een opdrachtprompt, Ga naar de map waar u wilt kopiëren van de toepassing en voer de volgende opdracht uit:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

De gekloonde opslagplaats bevat twee voorbeelden "bulkimport" en "bulkupdate" ten opzichte van de map '\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor'. De toepassing "bulkimport" genereert willekeurige documenten en importeert in Azure Cosmos DB. De toepassing 'bulkupdate' updates een aantal documenten in Azure Cosmos DB. In de volgende secties, zullen we de code in elk van deze voorbeeld-apps bekijken. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Bulksgewijs importeren van gegevens met Azure Cosmos DB

1. De Azure Cosmos DB-verbindingsreeksen worden gelezen als argumenten en toegewezen aan variabelen die zijn gedefinieerd in CmdLineConfiguration.java-bestand.  

2. Naast is de DocumentClient-object geïnitialiseerd met behulp van de volgende instructies:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Het object DocumentBulkExecutor wordt geïnitialiseerd met de waarden voor een hoge nieuwe pogingen voor de wachttijd en aanvragen beperkt. En vervolgens worden ze ingesteld op 0 overbezetting doorgeven aan DocumentBulkExecutor voor de levensduur.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Roep de importal API aan waarmee wille keurige documenten worden gegenereerd om bulksgewijs te importeren in een Azure Cosmos-container. U kunt de opdrachtregel-configuraties in het bestand CmdLineConfiguration.java configureren.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   De bulksgewijs importeren API accepteert een verzameling documenten JSON-geserialiseerd en heeft de volgende syntaxis, voor meer informatie, Zie de [API-documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   De methode importAll accepteert de volgende parameters:
 
   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |isUpsert    |   Een vlag om in te schakelen upsert van de documenten. Als een document met de opgegeven ID bestaat al, wordt deze bijgewerkt.  |
   |disableAutomaticIdGeneration     |   Een vlag om uit te schakelen automatisch genereren van ID. Standaard is ingesteld op true.   |
   |maxConcurrencyPerPartitionRange    |  De maximale graad van gelijktijdigheid per partitiesleutelbereik. De standaardwaarde is 20.  |

   **Bulksgewijs importeren antwoord objectdefinitie** het resultaat van de bulkimport API-aanroep bevat de volgende get-methoden:

   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Het totale aantal documenten die zijn geïmporteerd uit de documenten die zijn opgegeven voor het bulksgewijs importeren API-aanroep.      |
   |dubbele getTotalRequestUnitsConsumed()   |  Het totaal aantal aanvragen van aanvraageenheden (RU) die worden gebruikt door de bulksgewijs importeren API-aanroep.       |
   |Duur getTotalTimeTaken()   |    De totale tijd die door de bulkimport API-aanroep uitgevoerd.     |
   |Uitzonde ring > getErrors () weer geven\< |  Hiermee haalt u de lijst met fouten als bepaalde documenten in de batch doorgegeven aan de bulksgewijs importeren API-aanroep kan niet worden ingevoegd ophalen.       |
   |List\<Object> getBadInputDocuments()  |    De lijst met slechte indeling documenten die zijn niet geïmporteerd in de bulksgewijs importeren API-aanroep. Gebruiker moet de geretourneerde documenten los en probeer het opnieuw importeren. -Ongeldige indeling of meer documenten waarvan u de id-waarde geen tekenreeks is (null of een andere gegevenstype is als ongeldig wordt beschouwd).     |

5. Nadat u de bulksgewijs importeren van de toepassing gereed hebt, bouw het opdrachtregelprogramma van bron met de opdracht 'mvn opschonen pakket'. Met deze opdracht wordt een jar-bestand gegenereerd in de doelmap:  

   ```java
   mvn clean package
   ```

6. Nadat de doel-afhankelijkheden zijn gegenereerd, kunt u de toepassing van de importprogramma bulksgewijs aanroepen met behulp van de volgende opdracht uit:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Het importprogramma bulksgewijs maakt een nieuwe database en een verzameling met de databasenaam, de naam van verzameling en de doorvoer die zijn opgegeven in het bestand App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Gegevens voor bulksgewijs bijwerken in Azure Cosmos DB

U kunt bestaande documenten met behulp van de API BulkUpdateAsync bijwerken. In dit voorbeeld wordt u het veld naam ingesteld op een nieuwe waarde en de beschrijving van veld verwijderen uit de bestaande documenten. Voor een volledig overzicht van ondersteunde veld updatebewerkingen, Zie [API-documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Hiermee definieert u de update-items, samen met de bijbehorende veld update-bewerkingen. In dit voorbeeld gebruikt u SetUpdateOperation het veld naam en UnsetUpdateOperation het omschrijvingsveld verwijderen uit alle documenten bij te werken. U kunt ook andere bewerkingen zoals het verhogen van een veld van het document door een specifieke waarde uitvoeren, push-specifieke waarden in het matrixveld van een of een specifieke waarde verwijderen uit een matrixveld. Zie voor meer informatie over verschillende methoden die door de bulk-update API, de [API-documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Roep de updateAll-API aan waarmee wille keurige documenten worden gegenereerd die vervolgens bulksgewijs worden geïmporteerd in een Azure Cosmos-container. U kunt de opdrachtregel configuraties moeten worden doorgegeven in CmdLineConfiguration.java bestand configureren.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   De bulk-update API accepteert een verzameling items moeten worden bijgewerkt. Elk item bijwerken Hiermee geeft u de lijst met veld update-bewerkingen worden uitgevoerd op een document aangeduid met een ID en een waarde voor de partitiesleutel. Zie voor meer informatie de [API-documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   De methode updateAll accepteert de volgende parameters:

   |**Parameter** |**Beschrijving** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  De maximale graad van gelijktijdigheid per partitiesleutelbereik. De standaardwaarde is 20.  |
 
   **Bulksgewijs importeren antwoord objectdefinitie** het resultaat van de bulkimport API-aanroep bevat de volgende get-methoden:

   |**Parameter** |**Beschrijving**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Het totale aantal documenten die zijn bijgewerkt uit de documenten die is opgegeven voor de Bulkupdate-API-aanroep.      |
   |dubbele getTotalRequestUnitsConsumed() |  Het totale aantal aanvraageenheden (RU) die worden gebruikt door de bulk-update-API-aanroep.       |
   |Duur getTotalTimeTaken()  |   De totale tijd die door de bulksgewijs bijwerken API-aanroep uitgevoerd.      |
   |Uitzonde ring > getErrors () weer geven\<   |    Hiermee haalt u de lijst met fouten als bepaalde documenten in de batch is opgegeven voor de bulk-update API-aanroep kan niet worden ingevoegd ophalen.      |

3. Nadat u het grootste deel bij het bijwerken van de toepassing gereed hebt, bouw het opdrachtregelprogramma van bron met de opdracht 'mvn opschonen pakket'. Met deze opdracht wordt een jar-bestand gegenereerd in de doelmap:  

   ```
   mvn clean package
   ```

4. Nadat de doel-afhankelijkheden zijn gegenereerd, kunt u de toepassing van de update bulksgewijs aanroepen met behulp van de volgende opdracht uit:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tips voor prestaties 

Houd rekening met de volgende punten voor betere prestaties bij het gebruik van grote hoeveelheden executor-bibliotheek:

* Voor optimale prestaties moet u de toepassing uitvoeren vanuit een Azure-VM in dezelfde regio als de schrijfregio van uw Cosmos DB-account.  
* Voor het bereiken van hogere doorvoer:  

   * Grootte van de heap van JVM ingesteld op een aantal groot genoeg is om te voorkomen dat een probleem met geheugen bij het verwerken van grote aantal documenten. Heap-grootte aanbevolen: max (3GB, 3 * sizeof (alle documenten die worden doorgegeven aan bulksgewijs importeren API in één batch)).  
   * Er is een voorverwerking tijd, dit ontvangt u een hogere doorvoer bij het uitvoeren van bulkbewerkingen met een groot aantal documenten. Dus als u importeren 10.000.000 documenten wilt, is waarop bulkimport 10 keer 10 bulksgewijs documenten elk van de grootte van 1.000.000 beter dan het uitvoeren van bulkimport 100 keer op 100 bulksgewijs documenten elk van de grootte van 100.000 documenten.  

* Het is raadzaam om één DocumentBulkExecutor-object voor de hele toepassing te instantiëren binnen één virtuele machine die overeenkomt met een specifieke Azure Cosmos-container.  

* Omdat de uitvoering van een enkele bulksgewijs bewerking API een grote hoeveelheid CPU- en IO van de client-computer verbruikt. Dit gebeurt door bij het maken van meerdere taken intern, te voorkomen dat bij het maken van meerdere gelijktijdige taken binnen uw het toepassingsproces dat elke uitvoering bulksgewijze bewerking API-aanroepen. Als een enkel bulksgewijs bewerking API-aanroep die wordt uitgevoerd op een enkele virtuele machine niet kan gebruiken voor uw hele containerdoorvoer (als uw container doorvoer > 1 miljoen RU/s), is het beter om te maken van afzonderlijke virtuele machines voor het gelijktijdig uitvoeren van bulksgewijs bewerking API-aanroepen.

    
## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over de details van de maven-pakket en release-opmerkingen van bulksgewijs executor Java-bibliotheek,[bulksgewijs details over de SDK van de executor](sql-api-sdk-bulk-executor-java.md).


