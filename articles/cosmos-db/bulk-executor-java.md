---
title: Bulkexecutor Java-bibliotheek gebruiken in Azure Cosmos DB om bulkimport- en updatebewerkingen uit te voeren
description: Azure Cosmos DB-documenten importeren en bijwerken met behulp van java-bibliotheek voor bulkuitvoermiddelen
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: f5c6562c6def1fa588724b3bc5da502536b16aa9
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985640"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Gebruik de BulkExecutor-bibliotheek voor Java om bulkbewerkingen uit te voeren in Azure Cosmos DB

Deze zelfstudie bevat instructies over het gebruik van de Java-bibliotheek bulkexecutor van Azure Cosmos DB om Azure Cosmos DB-documenten te importeren en bij te werken. Zie het overzichtsartikel van [de bulkexecutorLibrary](bulk-executor-overview.md) voor meer informatie over de bibliotheek voor bulkuitvoerbeleid en hoe deze u helpt gebruik te maken van enorme doorvoer en opslag. In deze zelfstudie bouwt u een Java-toepassing die willekeurige documenten genereert en deze bulk wordt geïmporteerd in een Azure Cosmos-container. Na het importeren worden sommige eigenschappen van een document in bulk bijgewerkt. 

Momenteel wordt de bulkexecutorbibliotheek alleen ondersteund door Azure Cosmos DB SQL API- en Gremlin-API-accounts. In dit artikel wordt beschreven hoe u de Java-bibliotheek met bulkuitvoerbestanden gebruikt met SQL API-accounts. Zie [Bulkbewerkingen uitvoeren in Azure Cosmos DB Gremlin API voor](bulk-executor-graph-dotnet.md)meer informatie over het gebruik van bulkexecutor .NET-bibliotheek met Gremlin API.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.  

* U [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement, gratis en verplichtingen. U ook de Azure Cosmos `https://localhost:8081` DB [Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator) gebruiken met het eindpunt. De primaire sleutel wordt gegeven in [Aanvragen verifiëren](local-emulator.md#authenticating-requests).  

* [Java Development Kit (JDK) 1.7+](/java/azure/jdk/?view=azure-java-stable)  
  - Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.  

  - Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.

* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair [Maven](https://maven.apache.org/)-archief  
  
  - Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.

* Maak een Azure Cosmos DB SQL API-account met behulp van de stappen die zijn beschreven in het gedeelte [Databaseaccount maken](create-sql-api-java.md#create-a-database-account) van het Java-quickstart-artikel.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Laten we nu overschakelen naar het werken met code door het downloaden van een voorbeeld Java-applicatie van GitHub. Deze toepassing voert bulkbewerkingen uit op Azure Cosmos DB-gegevens. Als u de toepassing wilt klonen, opent u een opdrachtprompt, navigeert u naar de map waar u de toepassing wilt kopiëren en voert u de volgende opdracht uit:

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

De gekloonde repository bevat twee voorbeelden "bulkimport" en "bulkupdate" ten opzichte van de map "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" map. De toepassing 'bulkimport' genereert willekeurige documenten en importeert deze naar Azure Cosmos DB. De toepassing 'bulkupdate' werkt sommige documenten in Azure Cosmos DB bij. In de volgende secties bekijken we de code in elk van deze voorbeeld-apps. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Bulkimportgegevens naar Azure Cosmos DB

1. De verbindingstekenreeksen van Azure Cosmos DB worden gelezen als argumenten en toegewezen aan variabelen die zijn gedefinieerd in het bestand CmdLineConfiguration.java.  

2. Vervolgens wordt het object DocumentClient geïnitialiseerd met behulp van de volgende instructies:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Het object DocumentBulkExecutor wordt geïnitialiseerd met een hoge herprobeerwaarden voor wachttijd en aangezocht met een beperking. En dan zijn ze ingesteld op 0 om congestiecontrole door te geven aan DocumentBulkExecutor voor zijn levensduur.  

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

4. Roep de api importAll aan die willekeurige documenten genereert om in bulk te importeren in een Azure Cosmos-container. U de configuratie van de opdrachtregel configureren in het bestand CmdLineConfiguration.java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   De bulk import API accepteert een verzameling van JSON-serialized documenten en het heeft de volgende syntaxis, voor meer details, zie de [API-documentatie:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

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
   |isUpsert    |   Een vlag om upsert van de documenten mogelijk te maken. Als er al een document met een opgegeven id bestaat, wordt het bijgewerkt.  |
   |schakelt AutomaticIdGeneration uit     |   Een vlag om de automatische generatie ID uit te schakelen. Standaard is het ingesteld op true.   |
   |maxConcurrencyPerPartitionRange    |  De maximale mate van gelijktijdigheid per partitiesleutelbereik. De standaardwaarde is 20.  |

   **Definitie van bulkimportresponsobject** Het resultaat van de API-aanroep voor bulkimport bevat de volgende get-methoden:

   |**Parameter**  |**Beschrijving**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   Het totale aantal documenten dat met succes is geïmporteerd uit de documenten die zijn geleverd aan de API-aanroep voor bulkimport.      |
   |dubbele getTotalRequestUnitsConsumed()   |  De total request units (RU) verbruikt door de bulk import API call.       |
   |Duur getTotalTimeTaken()   |    De totale tijd die wordt genomen door de API-aanroep voor bulkimport om de uitvoering te voltooien.     |
   |Lijst\<uitzondering> getErrors() |  Hier wordt de lijst met fouten weergegeven als sommige documenten uit de batch die wordt geleverd aan de API-aanroep voor bulkimport, niet kunnen worden ingevoegd.       |
   |Lijstobject\<> getBadInputDocuments()  |    De lijst met documenten met slechte indeling die niet zijn geïmporteerd in de API-aanroep voor bulkimport. De gebruiker moet de geretourneerde documenten herstellen en opnieuw proberen te importeren. Documenten met slechte opmaak omvatten documenten waarvan de id-waarde geen tekenreeks is (null of een ander gegevenstype wordt als ongeldig beschouwd).     |

5. Nadat u de bulkimporttoepassing gereed hebt, bouwt u het opdrachtregelgereedschap van bron met behulp van de opdracht 'mvn clean package'. Met deze opdracht genereert u een potbestand in de doelmap:  

   ```java
   mvn clean package
   ```

6. Nadat de doelafhankelijkheden zijn gegenereerd, u de toepassing van de bulkimporteur aanroepen met behulp van de volgende opdracht:  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   De bulkimporteur maakt een nieuwe database en een verzameling met de databasenaam, verzamelingsnaam en doorvoerwaarden die zijn opgegeven in het bestand App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Bulkupdategegevens in Azure Cosmos DB

U bestaande documenten bijwerken met de BulkUpdateAsync API. In dit voorbeeld stelt u het veld Naam in op een nieuwe waarde en verwijdert u het veld Beschrijving uit de bestaande documenten. Zie [API-documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)voor de volledige set ondersteunde veldupdatebewerkingen. 

1. Hiermee definieert u de update-items samen met de bijbehorende veldupdatebewerkingen. In dit voorbeeld gebruikt u SetUpdateOperation om het veld Naam en UnsetUpdateOperation bij te werken om het veld Beschrijving uit alle documenten te verwijderen. U ook andere bewerkingen uitvoeren, zoals een documentveld verhogen met een specifieke waarde, specifieke waarden in een matrixveld duwen of een specifieke waarde uit een matrixveld verwijderen. Zie de [API-documentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)voor meer informatie over verschillende methoden die door de API voor bulkupdates worden geleverd.  

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

2. Roep de updateAlle API aan die willekeurige documenten genereert die vervolgens in bulk worden geïmporteerd in een Azure Cosmos-container. U de configuratie van de opdrachtregel configureren die moeten worden doorgegeven in het bestand CmdLineConfiguration.java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   De bulkupdate-API accepteert een verzameling items die moeten worden bijgewerkt. Elk updateitem geeft de lijst op met veldupdatebewerkingen die moeten worden uitgevoerd op een document dat is geïdentificeerd door een id en een waarde van de partitiesleutel. Zie voor meer informatie de [API-documentatie:](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   De methode updateAlle methoden accepteren de volgende parameters:

   |**Parameter** |**Beschrijving** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  De maximale mate van gelijktijdigheid per partitiesleutelbereik. De standaardwaarde is 20.  |
 
   **Definitie van bulkimportresponsobject** Het resultaat van de API-aanroep voor bulkimport bevat de volgende get-methoden:

   |**Parameter** |**Beschrijving**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   Het totale aantal documenten dat met succes is bijgewerkt uit de documenten die zijn geleverd aan de API-aanroep voor bulkupdates.      |
   |dubbele getTotalRequestUnitsConsumed() |  De total request units (RU) verbruikt door de bulk update API call.       |
   |Duur getTotalTimeTaken()  |   De totale tijd die wordt genomen door de bulk update API-aanroep om de uitvoering te voltooien.      |
   |Lijst\<uitzondering> getErrors()   |       Hier wordt de lijst met fouten weergegeven als sommige documenten uit de batch die wordt geleverd aan de API-aanroep voor bulkupdates, niet kunnen worden ingevoegd.      |

3. Nadat u de bulkupdatetoepassing gereed hebt, bouwt u het opdrachtregelgereedschap van bron met behulp van de opdracht 'mvn clean package'. Met deze opdracht genereert u een potbestand in de doelmap:  

   ```
   mvn clean package
   ```

4. Nadat de doelafhankelijkheden zijn gegenereerd, u de toepassing voor bulkupdates aanroepen met behulp van de volgende opdracht:

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Tips voor prestaties 

Houd rekening met de volgende punten voor betere prestaties bij het gebruik van bulkexecutorbibliotheek:

* Voer uw toepassing uit vanaf een Azure VM in dezelfde regio als uw Cosmos DB-accountschrijfgebied voor de beste prestaties.  
* Voor het bereiken van een hogere doorvoer:  

   * Stel de heapgrootte van de JVM in op een groot genoeg getal om een geheugenprobleem bij het verwerken van een groot aantal documenten te voorkomen. Voorgestelde heap grootte: max(3GB, 3 * groottevan (alle documenten doorgegeven aan bulk import API in een batch)).  
   * Er is een voorbewerkingstijd, waardoor u een hogere doorvoer krijgt bij het uitvoeren van bulkbewerkingen met een groot aantal documenten. Dus als u 10.000.000 documenten wilt importeren, is het uitvoeren van bulkimport 10 keer op 10 bulk documenten van elk van grootte 1.000.000 de voorkeur dan bulkimport 100 keer uitvoeren op 100 bulkdocumenten van elk van grootte 100.000 documenten.  

* Het wordt aanbevolen om één DocumentBulkExecutor-object te instantiëren voor de hele toepassing binnen één virtuele machine die overeenkomt met een specifieke Azure Cosmos-container.  

* Aangezien een enkele bulk operatie API uitvoering verbruikt een groot deel van de client machine CPU en netwerk IO. Dit gebeurt door meerdere taken intern voort te brengen, om te voorkomen dat meerdere gelijktijdige taken binnen uw toepassingsproces worden spawnen bij elke uitvoerende API-aanroepen voor bulkbewerking. Als een API-aanroep met één bulkbewerking die op één virtuele machine wordt uitgevoerd, de doorvoer van uw hele container niet kan verbruiken (als de doorvoer van uw container > 1 miljoen RU/s), verdient het de voorkeur om afzonderlijke virtuele machines te maken om gelijktijdig api-aanroepen voor bulkbewerking uit te voeren.

    
## <a name="next-steps"></a>Volgende stappen
* Zie[bulkexecutor SDK-details](sql-api-sdk-bulk-executor-java.md)voor meer informatie over details van het maven-pakket en het vrijgeven van notities van de Java-bibliotheek in bulk.


