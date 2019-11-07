---
title: 'NoSQL-zelfstudie: SQL-API voor Azure Cosmos DB Java SDK'
description: Een NoSQL-zelfstudie waarmee u een online database en een Java-consoletoepassing maakt met de SQL-API voor Azure Cosmos DB. Azure SQL is een NoSQL-database voor JSON.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: a046f97dccdcc4a9cb9fe180447c1ff9a316f0df
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720813"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>NoSQL-zelfstudie: een SQL-API Java-consoletoepassing maken

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Welkom bij de NoSQL-zelfstudie over de SQL-API voor Azure Cosmos DB Java SDK. Wanneer u deze zelfstudie hebt voltooid, beschikt u over een consoletoepassing waarmee u Azure Cosmos DB-resources kunt maken en er query's op kunt uitvoeren.

We behandelen de volgende onderwerpen:

* Een Azure Cosmos DB-account maken en er verbinding mee maken
* Uw Visual Studio-oplossing configureren
* Een online-database maken
* Een verzameling maken
* JSON-documenten maken
* Query's uitvoeren op de verzameling
* JSON-documenten maken
* Query's uitvoeren op de verzameling
* Een document vervangen
* Een document verwijderen
* De database verwijderen

Tijd om aan de slag te gaan.

## <a name="prerequisites"></a>Vereisten
Zorg ervoor dat u over de volgende zaken beschikt:

* Een actief Azure-account. Als u nog geen account hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks).
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Stap 1: een Azure Cosmos DB-account maken
Begin met het maken van een Azure Cosmos DB-account. Als u al een account hebt dat u wilt gebruiken, kunt u verder naar de stap [Het GitHub-project klonen](#GitClone). Als u de Azure Cosmos DB-emulator gebruikt, volgt u de stappen in [Azure Cosmos DB-emulator](local-emulator.md) om de emulator in te stellen en meteen naar [Het GitHub-project klonen](#GitClone) te gaan.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Stap 2: het GitHub-project klonen
Kloon eerst de GitHub-opslagplaats voor [Get Started with Azure Cosmos DB and Java](https://github.com/Azure-Samples/documentdb-java-getting-started) (Aan de slag met Azure Cosmos DB en Java). Voer bijvoorbeeld vanuit een lokale map het volgende uit als u het voorbeeldproject lokaal wilt ophalen.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

De map bevat een `pom.xml` voor het project en een `src`-map met Java-broncode, waaronder `Program.java`. Deze toont hoe u eenvoudige bewerkingen kunt uitvoeren met Azure Cosmos DB, zoals het maken van documenten en het uitvoeren van query's op gegevens binnen een verzameling. De `pom.xml` bevat een afhankelijkheid voor de [Azure Cosmos DB Java SDK in Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Stap 3: verbinding maken met een Azure Cosmos DB-account
Ga vervolgens terug naar de [Azure-portal](https://portal.azure.com) om uw eindpunt en primaire hoofdsleutel op te halen. Uw toepassing heeft het Azure Cosmos DB-eindpunt en de primaire sleutel nodig om te bepalen waarmee verbinding moet worden gemaakt en om ervoor te zorgen dat Azure Cosmos DB de verbinding van uw toepassing vertrouwt.

Ga in Azure Portal naar uw Azure Cosmos DB-account en klik daarna op **Sleutels**. Kopieer in de portal de URI en plak deze in `https://FILLME.documents.azure.com` in het bestand Program.java. Kopieer vervolgens de PRIMAIRE SLEUTEL van de portal en plak deze in `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Scherm afbeelding van de Azure Portal die door de NoSQL-zelf studie wordt gebruikt om een Java-Console toepassing te maken. Schermopname van Azure Portal waarin een Azure Cosmos DB-account wordt weergegeven met de hub ACTIEF gemarkeerd. Verder is de knop SLEUTELS gemarkeerd op de Azure Cosmos DB-accountblade en zijn de waarden URI, PRIMAIRE SLEUTEL en SECUNDAIRE SLEUTEL gemarkeerd op de blade Sleutels][keys]

## <a name="step-4-create-a-database"></a>Stap 4: een database maken
Uw Azure Cosmos DB-[database](databases-containers-items.md#azure-cosmos-databases) kan worden gemaakt met de methode [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) van de klasse **DocumentClient**. Een database is een logische container voor een JSON-documentopslag, gepartitioneerd in verzamelingen.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Stap 5: een verzameling maken
> [!WARNING]
> Met **createCollection** maakt u een nieuwe verzameling met gereserveerde doorvoer, wat gevolgen heeft voor de kosten. Zie onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie.
> 
> 

U kunt een verzameling maken met de methode [createCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) van de klasse **DocumentClient**. Een verzameling is een container van JSON-documenten en de bijbehorende JavaScript-toepassingslogica.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos containers can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Stap 6: JSON-documenten maken
U kunt een document maken met de methode [createDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) van de klasse **DocumentClient**. Documenten bestaan uit door gebruikers gedefinieerde (willekeurige) JSON-inhoud. U kunt nu een of meer documenten invoegen. Als u al gegevens hebt die u in de database wilt opslaan, kunt u het [hulpprogramma voor gegevensmigratie](import-data.md) van Azure Cosmos DB gebruiken voor het importeren van gegevens in een database.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagram waarin u de hiërarchische relatie ziet tussen het account, de online database, de verzameling en de documenten die in de NoSQL-zelfstudie worden gebruikt om een Java-consoletoepassing te maken](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Stap 7: query's uitvoeren op Azure Cosmos DB-resources
Azure Cosmos DB biedt ondersteuning voor uitgebreide [query's](how-to-sql-query.md) op de JSON-documenten die zijn opgeslagen in elke verzameling.  De volgende voorbeeldcode laat zien hoe u query's kunt toepassen op documenten in Azure Cosmos DB met behulp van de SQL-syntaxis met de methode [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Stap 8: JSON-document vervangen
Azure Cosmos DB ondersteunt het bijwerken van JSON-documenten met behulp van de methode [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Stap 9: JSON-document verwijderen
Azure Cosmos DB ondersteunt ook het verwijderen van JSON-documenten met behulp van de methode [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Stap 10: de database verwijderen
Als u de gemaakte database verwijdert, worden de database en alle onderliggende resources (verzamelingen, documenten enz.) verwijderd.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Stap 11: uw Java-consoletoepassing volledig uitvoeren
Als u de toepassing in de console wilt uitvoeren, gaat u naar de projectmap en compileert u de code met Maven:
    
    mvn package

Als u `mvn package` uitvoert, wordt de nieuwste Azure Cosmos DB-bibliotheek vanuit Maven gedownload en wordt `GetStarted-0.0.1-SNAPSHOT.jar` geproduceerd. Voer vervolgens de app uit door het volgende uit te voeren:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Gefeliciteerd! U hebt de NoSQL-zelfstudie voltooid en beschikt nu over een werkende Java-consoletoepassing.

## <a name="next-steps"></a>Volgende stappen
* Wilt u een zelfstudie voor Java-web-apps volgen? Zie [Build a web application with Java using Azure Cosmos DB](sql-api-java-application.md) (Een Java-web-app maken met Azure Cosmos DB).
* Leer hoe het [bewaken van een Azure Cosmos DB-account](monitor-accounts.md) werkt.
* Voer query's uit op onze voorbeeldgegevensset in de [Queryspeelplaats](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
