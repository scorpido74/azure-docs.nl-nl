---
title: 'Snelstartgids: een web-app maken met behulp van de Azure API voor Mongo DB en Java SDK-Azure Cosmos DB'
description: Biedt een voorbeeld van Java-code dat u kunt gebruiken om met de API van Azure Cosmos DB voor MongoDB verbinding te maken met gegevens en er query's op uit te voeren.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: bfded407790d01d65feec0b6e069c23739064e97
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756925"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Quick Start: een console-app maken met Java en de MongoDB-API in Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

In deze Quick Start gebruikt u de Azure Cosmos DB-API voor Mongo DB en Java SDK voor het maken van een web-app voor een console. Met Azure Cosmos DB kunt u snel document-, sleutel-en grafiek databases maken en doorzoeken, die allemaal profiteren van de mogelijkheden van globale distributie en horizontale schaal op basis van Cosmos DB.

In deze snelstart ziet u hoe u een Cosmos-account kunt maken met de [API van Azure Cosmos DB voor MongoDB](mongodb-introduction.md). U gaat vervolgens een console-app ontwikkelen en implementeren met behulp van het [MongoDB Java-stuurprogramma](https://docs.mongodb.com/ecosystem/drivers/java/). 

## <a name="prerequisites"></a>Vereisten

Voordat u met dit voorbeeld aan de slag gaat, moet u aan de volgende vereisten voldoen:
* [De JDK voor Azure en Azure Stack JDK versie 8 installeren](https://aka.ms/azure-jdks)
* Maven (voer `apt-get install maven` uit als u niet over Maven beschikt)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Een verzameling toevoegen

Geef uw nieuwe database de naam **db**, en uw nieuwe verzameling **verz**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Open daarna de code in uw favoriete editor. 

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

De volgende codefragmenten zijn allemaal afkomstig uit het bestand Program.java.

* De DocumentClient wordt geïnitialiseerd.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Er wordt een nieuwe database en verzameling gemaakt.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Een aantal documenten wordt ingevoegd met behulp van `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Een aantal query's wordt uitgevoerd met behulp van `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Selecteer in het account **Quick Start**, selecteer **Java**en kopieer vervolgens de connection string naar het klem bord.

2. Open het bestand `Program.java`, vervang het argument voor de MongoClientURI-constructor door de verbindingsreeks. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>De console-app uitvoeren

1. Voer `mvn package` uit op een terminal zodat de vereiste npm-modules worden geïnstalleerd.

2. Voer `mvn exec:java -D exec.mainClass=GetStarted.Program` uit op een terminal om uw Java-toepassing te starten.

U kunt nu [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) gebruiken om query’s op deze nieuwe gegevens uit te voeren, ze te wijzigen en er op een ander manier mee te werken.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Cosmos DB-account kunt maken, hoe u een verzameling kunt maken en hoe u een console-app kunt uitvoeren. Nu kunt u aanvullende gegevens in uw Cosmos database importeren.

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](mongodb-migrate.md)
