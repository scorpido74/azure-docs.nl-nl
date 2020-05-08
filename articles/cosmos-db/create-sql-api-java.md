---
title: 'Quick Start: Java gebruiken om een document database te maken met behulp van Azure Cosmos DB'
description: In deze Quick start ziet u een voor beeld van een Java-code waarmee u verbinding kunt maken met de Azure Cosmos DB SQL-API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 1d818957daa53efc856a345a4886e814fdaab6f3
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858136"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Quick Start: een Java-app maken voor het beheren van Azure Cosmos DB SQL-API-gegevens


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze Quick Start maakt en beheert u een Azure Cosmos DB SQL-API-account van de Azure Portal en gebruikt u een Java-app die is gekloond van GitHub. Eerst maakt u een Azure Cosmos DB SQL-API-account met behulp van de Azure Portal, vervolgens maakt u een Java-app met behulp van de SQL Java SDK en voegt u vervolgens bronnen toe aan uw Cosmos DB-account met behulp van de Java-toepassing. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement. U kunt ook de [Azure Cosmos DB-emulator](https://aka.ms/cosmosdb-emulator) met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`gebruiken.
- [Jdk 8 (Java Development Kit)](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Verwijs `JAVA_HOME` uw omgevings variabele naar de map waarin de JDK is geïnstalleerd.
- Een [binair maven-archief](https://maven.apache.org/download.cgi). Voer op Ubuntu uit `apt-get install maven` om Maven te installeren.
- [Git](https://www.git-scm.com/downloads). Voer op Ubuntu uit `sudo apt-get install git` om Git te installeren.

## <a name="introductory-notes"></a>Inleidende opmerkingen

*De structuur van een Cosmos DB-account.* Een Cosmos DB- *account* bevat geen API-of programmeer taal, maar heeft geen of meer *data bases*, een *Data Base* (DB) bevat nul of meer *containers*en een *container* bevat nul of meer items, zoals in het onderstaande diagram wordt weer gegeven:

![Azure Cosmos-account entiteiten](./media/databases-containers-items/cosmos-entities.png)

U kunt hier meer lezen over data bases, containers en items [.](databases-containers-items.md) Enkele belang rijke eigenschappen worden op het niveau van de container gedefinieerd, onder de *ingerichte door Voer* en de *partitie sleutel*. 

De ingerichte door Voer wordt gemeten in aanvraag eenheden (*RUs*) die een monetaire prijs hebben en die een substantiële bepaling van de bedrijfs kosten van het account zijn. Ingerichte door Voer kan worden geselecteerd bij granulatie op basis van een container of nauw keurigheid per data base, maar de specificatie voor door Voer op container niveau verdient doorgaans de voor keur. U kunt hier meer lezen over het inrichten van de door Voer [.](set-throughput.md)

Naarmate items worden ingevoegd in een Cosmos DB container, groeit de data base horizon taal door meer opslag en reken kracht toe te voegen voor het afhandelen van aanvragen. Opslag-en reken capaciteit worden toegevoegd aan discrete eenheden die *partities*worden genoemd, en u moet één veld in uw documenten selecteren als partitie sleutel die elk document aan een partitie toewijst. De manier waarop partities worden beheerd, is dat aan elke partitie een ongeveer gelijk segment uit het bereik van partitie sleutel waarden wordt toegewezen. Daarom wordt u aangeraden een partitie sleutel te kiezen die relatief wille keurig of gelijkmatig gedistribueerd is. Als dat niet het geval is, zien sommige partities veel meer aanvragen (*Hot Partition*), terwijl andere partities veel minder aanvragen (*koude partitie*) zien, en dit moet worden vermeden. Meer informatie over partitionering vindt u [hier](partitioning-overview.md).

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een documentdatabase kunt maken, moet u een SQL-API-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Nu gaan we werken met code. We gaan nu een SQL API-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Sla dit anders over en ga naar [De app uitvoeren](#run-the-app). 


# <a name="sync-api"></a>[API synchroniseren](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Database resources beheren met behulp van de synchrone API (Sync)

* Initialisatie van `CosmosClient`. De `CosmosClient` biedt logische weer gave aan de client zijde voor de Azure Cosmos-database service. Deze client wordt gebruikt om aanvragen aan de service te configureren en uitvoeren.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase`zelf.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer`zelf.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Item maken met behulp `createItem` van de-methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Punt Lees bewerkingen worden uitgevoerd `readItem` met behulp van de methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-query's via JSON worden uitgevoerd met `queryItems` behulp van de-methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[Async-API](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Database resources beheren met de asynchrone API (async)

* Asynchrone API-aanroepen worden direct geretourneerd zonder dat er wordt gewacht op een reactie van de server. In het licht hiervan geven de volgende code fragmenten goede ontwerp patronen weer voor het uitvoeren van alle voor gaande beheer taken met behulp van async API.

* Initialisatie van `CosmosAsyncClient`. De `CosmosAsyncClient` biedt logische weer gave aan de client zijde voor de Azure Cosmos-database service. Deze client wordt gebruikt om asynchrone aanvragen voor de service te configureren en uit te voeren.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase`zelf.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer`zelf.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Net als bij de Sync-API wordt het maken van items `createItem` tot stand gebracht met behulp van de-methode. Dit voor beeld laat zien hoe u op efficiënte `createItem` wijze talloze async-aanvragen afgeeft door zich te abonneren op een reactieve stroom die de aanvragen uitgeeft en meldingen verzendt. Omdat dit eenvoudige voor beeld wordt uitgevoerd om te worden voltooid `CountDownLatch` en beëindigd, worden instanties gebruikt om ervoor te zorgen dat het programma niet wordt beëindigd tijdens het maken van een item. **De juiste asynchrone programmerings procedure is niet blok keren op asynchrone aanroepen-in realistische use-case-aanvragen worden gegenereerd op basis van een hoofd ()-lus die voor onbepaalde tijd wordt uitgevoerd, waardoor de nood zaak voor het vergren delen van asynchrone aanroepen niet meer nodig is.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Net als bij de Sync-API worden punt Lees bewerkingen `readItem` uitgevoerd met behulp van de methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Net als bij de API Sync worden SQL-query's via JSON uitgevoerd met `queryItems` behulp van de-methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>De app uitvoeren

Ga nu terug naar de Azure-portal om de verbindingsreeksgegevens op te halen en start de app met uw eindpuntgegevens. Hierdoor kan de app communiceren met de gehoste database.

1. Ga met de opdracht `cd` in het git-terminalvenster naar de map met voorbeeldcode.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. Gebruik in het git-terminalvenster de volgende opdracht om de vereiste Java-pakketten te installeren.

    ```bash
    mvn package
    ```

3. Gebruik in het git-Terminal venster de volgende opdracht om de Java-toepassing te starten (Vervang `sync` SYNCASYNCMODE `async` met of afhankelijk van de voorbeeld code die u wilt uitvoeren, vervang YOUR_COSMOS_DB_HOSTNAME door de waarde van de geciteerde URI van de portal en vervang YOUR_COSMOS_DB_MASTER_KEY door de geciteerde primaire sleutel van de portal)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Het terminalvenster geeft een melding dat de database FamilyDB is gemaakt. 
    
4. De app maakt een Data Base met de naam`AzureSampleFamilyDB`
5. De app maakt een container met de naam`FamilyContainer`
6. De app voert punt lezen uit met behulp van object-Id's en de partitie sleutel waarde (in het voor beeld lastName). 
7. Met de app worden items gezocht voor het ophalen van alle families met achternaam in (' Splinter ', ' Wakefield ', ' Johnson ')

7. De resources die zijn gemaakt, worden niet verwijderd door de app. Ga terug naar de portal om [de resources te verwijderen](#clean-up-resources).  uit uw account zodat u daarvoor geen kosten in rekening worden gebracht.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB SQL-API-account maakt, een document database en-container maakt met behulp van de Data Explorer en een Java-app uitvoert om hetzelfde te doen met een programma. U kunt nu aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
