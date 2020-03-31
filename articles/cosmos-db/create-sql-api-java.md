---
title: Snelstart - Java gebruiken om een documentdatabase te maken met Azure Cosmos DB
description: Deze quickstart presenteert een Java-codevoorbeeld waarmee u verbinding maken en query's maken met de Azure Cosmos DB SQL API
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/31/2019
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 489f241453436190213b99cb4e7be0688a8b6237
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240175"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Snelstart: een Java-app bouwen om Azure Cosmos DB SQL API-gegevens te beheren


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze quickstart maakt en beheert u een Azure Cosmos DB SQL API-account vanuit de Azure-portal en gebruikt u een Java-app die is gekloond vanuit GitHub. Eerst maakt u een Azure Cosmos DB SQL API-account met behulp van de Azure-portal, maakt u vervolgens een Java-app met de SQL Java SDK en voegt u vervolgens resources toe aan uw Cosmos DB-account met behulp van de Java-toepassing. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wijs `JAVA_HOME` uw omgevingsvariabele op de map waar de JDK is geïnstalleerd.
- Een [Maven binair archief](https://maven.apache.org/download.cgi). Op Ubuntu, `apt-get install maven` uitvoeren om Maven te installeren.
- [Git.](https://www.git-scm.com/downloads) Voer op Ubuntu uit `sudo apt-get install git` om Git te installeren.

## <a name="introductory-notes"></a>Inleidende notities

*De structuur van een Cosmos DB account.* Ongeacht api- of programmeertaal bevat een Cosmos *DB-account* nul of meer *databases,* een *database* (DB) bevat nul of meer *containers*en een *container* bevat nul of meer items, zoals in het onderstaande diagram wordt weergegeven:

![Azure Cosmos-accountentiteiten](./media/databases-containers-items/cosmos-entities.png)

U hier meer lezen over databases, containers en [items.](databases-containers-items.md) Een paar belangrijke eigenschappen worden gedefinieerd op het niveau van de container, waaronder *ingerichte doorvoer* en *partitiesleutel*. 

De ingerichte doorvoer wordt gemeten in Request Units *(RU's)* die een monetaire prijs hebben en een belangrijke bepalende factor zijn in de bedrijfskosten van de rekening. Ingerichte doorvoer kan worden geselecteerd op granulariteit per container of per database granulariteit, maar de doorvoerspecificatie op containerniveau heeft meestal de voorkeur. U hier meer lezen over het inrichten van [doorvoer.](set-throughput.md)

Als items in een Cosmos DB-container worden ingevoegd, wordt de database horizontaal door meer opslag en rekenkracht toe te voegen om aanvragen af te handelen. Opslag- en rekencapaciteit worden toegevoegd in afzonderlijke eenheden die *partities*worden genoemd en u moet één veld in uw documenten kiezen als de partitiesleutel die elk document aan een partitie toewijst. De manier waarop partities worden beheerd, is dat aan elke partitie een ongeveer gelijk segment wordt toegewezen dat uit het bereik van partitiesleutelwaarden wordt toegewezen. daarom wordt u geadviseerd om een partitiesleutel te kiezen die relatief willekeurig of gelijkmatig verdeeld is. Anders zullen sommige partities aanzienlijk meer aanvragen zien *(hot partition)* terwijl andere partities aanzienlijk minder aanvragen zien *(koude partitie),* en dit moet worden vermeden. U hier meer informatie [vinden](partitioning-overview.md)over partitioneren.

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

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Databasebronnen beheren met behulp van de synchrone (synchronisatie)API

* Initialisatie van `CosmosClient`. De `CosmosClient` logische weergave aan clientzijde voor de Azure Cosmos-databaseservice biedt. Deze client wordt gebruikt om aanvragen aan de service te configureren en uitvoeren.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* `CosmosDatabase`Oprichting.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosContainer`Oprichting.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Item creatie met `createItem` behulp van de methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Point reads worden `readItem` uitgevoerd met behulp van methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* SQL-query's via JSON `queryItems` worden uitgevoerd met behulp van de methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Databasebronnen beheren met behulp van de asynchrone (async)-API

* Async API-aanroepen keren onmiddellijk terug, zonder te wachten op een reactie van de server. In het licht hiervan tonen de volgende codefragmenten de juiste ontwerppatronen voor het uitvoeren van alle voorgaande beheertaken met behulp van async API.

* Initialisatie van `CosmosAsyncClient`. De `CosmosAsyncClient` logische weergave aan clientzijde voor de Azure Cosmos-databaseservice biedt. Deze client wordt gebruikt voor het configureren en uitvoeren van asynchrone aanvragen tegen de service.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* `CosmosAsyncDatabase`Oprichting.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* `CosmosAsyncContainer`Oprichting.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Net als bij de synchronisatie-API wordt `createItem` het maken van items uitgevoerd met behulp van de methode. In dit voorbeeld ziet u hoe `createItem` u op efficiënte wijze talloze async-aanvragen verzenden door u te abonneren op een Reactieve stream die de aanvragen uitgeeft en meldingen afdrukt. Aangezien dit eenvoudige voorbeeld wordt voltooid `CountDownLatch` en beëindigd, worden instanties gebruikt om ervoor te zorgen dat het programma niet wordt beëindigd tijdens het maken van artikelen. **De juiste asynchrone programmeerpraktijk is niet te blokkeren op async-oproepen - in realistische use-cases worden aanvragen gegenereerd vanuit een hoofdlus() die voor onbepaalde tijd wordt uitgevoerd, waardoor de noodzaak om async-oproepen te vergrendelen niet nodig is.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Net als bij de synchronisatie-API `readItem` worden puntreads uitgevoerd met behulp van de methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Net als bij de synchronisatie-API worden SQL-query's via JSON uitgevoerd met behulp van de `queryItems` methode.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

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

3. Gebruik in het venster van de git-terminal de volgende opdracht `sync` om `async` de Java-toepassing te starten (vervang SYNCASYNCMODE door of afhankelijk van welke voorbeeldcode u wilt uitvoeren, vervang YOUR_COSMOS_DB_HOSTNAME door de geciteerde URI-waarde van de portal en vervang YOUR_COSMOS_DB_MASTER_KEY door de geciteerde primaire sleutel van portal)

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    Het terminalvenster geeft een melding dat de database FamilyDB is gemaakt. 
    
4. De app maakt database met naam`AzureSampleFamilyDB`
5. De app maakt container met naam`FamilyContainer`
6. De app voert puntreads uit met object----id's en partitiesleutelwaarde (wat achternaam is in ons voorbeeld). 
7. De app zal items opvragen om alle gezinnen met achternaam op te halen ('Andersen', 'Wakefield', 'Johnson')

7. De resources die zijn gemaakt, worden niet verwijderd door de app. Ga terug naar de portal om [de resources te verwijderen](#clean-up-resources).  uit uw account zodat u daarvoor geen kosten in rekening worden gebracht.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB SQL API-account maakt, een documentdatabase en -container maakt met behulp van de Data Explorer en een Java-app uitvoert om hetzelfde programmatisch te doen. U nu extra gegevens importeren in uw Azure Cosmos DB-account. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
