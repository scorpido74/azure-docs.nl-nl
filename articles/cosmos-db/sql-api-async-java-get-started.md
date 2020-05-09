---
title: 'Zelf studie: een Java-app maken met Azure Cosmos DB async-SDK voor het beheren van gegevens in een SQL-API-account'
description: In deze zelfstudie leert u hoe u SQL API-accounts kunt gebruiken voor het opslaan van en toegang krijgen tot gegevens in Azure Cosmos DB met behulp van een asynchrone Java-toepassing.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: eb30b8b210d34205620dec8efe059ef50c45f8e4
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858663"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Zelf studie: een Java-app bouwen met de asynchrone Java-SDK voor het beheren van gegevens die zijn opgeslagen in een SQL-API-account

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van NoSQL-documentgegevens. U kunt het SQL-API-account in Azure Cosmos DB gebruiken om deze documentgegevens op te slaan en te openen. In deze zelfstudie leert u hoe u een Java-toepassing bouwt met Async Java SDK voor het opslaan en beheren van documentgegevens. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een Azure Cosmos-account maken en verbinden
> * Uw oplossing configureren
> * Een verzameling maken
> * JSON-documenten maken
> * Query's uitvoeren op de verzameling

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over de volgende bronnen beschikt:

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](/java/azure/jdk/?view=azure-java-stable).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Maak een Azure Cosmos-account door de volgende stappen uit te voeren:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="clone-the-github-repository"></a><a id="GitClone"></a>De GitHub-opslag plaats klonen

Kloon de GitHub-opslagplaats voor [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) (Aan de slag met Azure Cosmos DB en Java). Voer bijvoorbeeld vanuit een lokale map het volgende uit als u het voorbeeldproject lokaal wilt ophalen.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

De map bevat de map `pom.xml` en het bestand `src/main/java/com/microsoft/azure/cosmosdb/sample` met Java-broncode, inclusief `Main.java`. Het project bevat code die is vereist voor het uitvoeren van bewerkingen met Azure Cosmos DB, zoals het maken van documenten en opvragen van gegevens binnen een verzameling. Het bestand `pom.xml` bevat een afhankelijkheid voor de [Azure Cosmos DB Java SDK in Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a name="connect-to-an-azure-cosmos-account"></a><a id="Connect"></a>Verbinding maken met een Azure Cosmos-account

Ga vervolgens terug naar de [Azure Portal](https://portal.azure.com) om uw eind punt en de primaire hoofd sleutel op te halen. Uw toepassing heeft het Azure Cosmos DB-eindpunt en de primaire sleutel nodig om te bepalen waarmee verbinding moet worden gemaakt en om ervoor te zorgen dat Azure Cosmos DB de verbinding van uw toepassing vertrouwt. Het bestand `AccountSettings.java` bevat de primaire sleutel en de URI-waarden. 

Ga in Azure Portal naar uw Azure Cosmos-account en klik vervolgens op **Sleutels**. Kopieer de URI en de PRIMAIRE SLEUTEL van de portal en plak deze in het bestand `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Schermopname voor ophalen sleutels uit portal][keys]

## <a name="initialize-the-client-object"></a>Het clientobject initialiseren

Initialiseer het clientobject met behulp van de waarden van de host-URI en van de primaire sleutel die zijn gedefinieerd in het bestand AccountSettings.java.

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a name="create-a-database"></a><a id="CreateDatabase"></a>Een database maken

Maak uw Azure Cosmos-data base met `createDatabaseIfNotExists()` behulp van de methode van de klasse DocumentClient. Een database is een logische container voor een JSON-documentopslag, gepartitioneerd in verzamelingen.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a name="create-a-collection"></a><a id="CreateColl"></a>Een verzameling maken

U kunt een verzameling maken met de methode `createDocumentCollectionIfNotExists()` van de klasse DocumentClient. Een verzameling is een container van JSON-documenten en de bijbehorende JavaScript-toepassingslogica.

> [!WARNING]
> Met **createCollection** maakt u een nieuwe verzameling met gereserveerde doorvoer, wat gevolgen heeft voor de kosten. Zie onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a name="create-json-documents"></a><a id="CreateDoc"></a>JSON-documenten maken

Maak een document met de methode createDocument van de klasse DocumentClient. Documenten bestaan uit door gebruikers gedefinieerde (willekeurige) JSON-inhoud. U kunt nu een of meer documenten invoegen. Het bestand src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java definieert de familie waartoe de JSON-documenten behoren. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a name="query-azure-cosmos-db-resources"></a><a id="Query"></a>Query's uitvoeren op Azure Cosmos DB-resources

Azure Cosmos DB biedt ondersteuning voor uitgebreide query's voor de JSON-documenten die zijn opgeslagen in elke verzameling. De volgende voorbeeldcode laat zien hoe u query's kunt toepassen op documenten in Azure Cosmos DB met behulp van de SQL-syntaxis met de methode `queryDocuments`.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a name="run-your-java-console-application"></a><a id="Run"></a>Uw Java-consoletoepassing uitvoeren

Als u de toepassing in de console wilt uitvoeren, gaat u naar de projectmap en compileert u de code met Maven:

```bash
mvn package
```

Als u `mvn package` uitvoert, wordt de nieuwste Azure Cosmos DB-bibliotheek vanuit Maven gedownload en wordt `GetStarted-0.0.1-SNAPSHOT.jar` geproduceerd. Voer vervolgens de app uit door het volgende uit te voeren:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

U hebt de NoSQL-zelfstudie voltooid en beschikt nu over een werkende Java-consoletoepassing.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, het Azure Cosmos-account en alle gerelateerde resources verwijderen wanneer u ze niet meer nodig hebt. Daarvoor selecteert u de resourcegroep voor de virtuele machine en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Java-app ontwikkelt met Async Java SDK voor het beheren van SQL-API-gegevens in Azure Cosmos DB. U kunt nu verdergaan met het volgende artikel:

> [!div class="nextstepaction"]
> [Een Node.js-console-app maken met JavaScript SDK en Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
