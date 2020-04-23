---
title: De tabel-API en Java gebruiken om een app te bouwen - Azure Cosmos DB
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en Java
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e3517804cb66a9f98351e4c68f4f7c4387cee8fe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083798"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>Snelstart: een Java-app bouwen om Azure Cosmos DB Table API-gegevens te beheren

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

In deze quickstart maakt u een Azure Cosmos DB Table API-account en gebruikt u Data Explorer en een Java-app die is gekloond uit GitHub om tabellen en entiteiten te maken. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wijs `JAVA_HOME` uw omgevingsvariabele op de map waar de JDK is geïnstalleerd.
- Een [Maven binair archief](https://maven.apache.org/download.cgi). 
- [Git.](https://www.git-scm.com/downloads) 

## <a name="create-a-database-account"></a>Een databaseaccount maken

> [!IMPORTANT] 
> U moet een nieuw Table-API-account maken om te kunnen werken met de algemeen beschikbare SDK's voor de Table-API. Table-API-accounts die zijn gemaakt tijdens de preview worden niet ondersteund door de algemeen beschikbare SDK's.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Een tabel toevoegen

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Table-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken.

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
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> ! [TIP] Zie het [voorbeeldartikel Cosmos DB Table API](table-storage-how-to-use-java.md) voor een meer gedetailleerde walkthrough van vergelijkbare code. 

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Selecteer **Verbindingstekenreeks**in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/). 

   ![De verbindingstekenreeksgegevens weergeven in het deelvenster Verbindingstekenreeks](./media/create-table-java/cosmos-db-quickstart-connection-string.png)

2. Kopieer de PRIMARY CONNECTION STRING met behulp van de knop Kopiëren aan de rechterkant.

3. Open *config.properties* uit de map *C:\git-samples\storage-table-java-getting-started\src\main\resources.* 

5. De volgende stap is het uitcommentariëren van regel 1, waarna u het commentaar van regel 2 verwijdert. De eerste twee regels moeten er nu als volgt uitzien.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Plak de PRIMARY CONNECTION STRING vanuit de portal in de waarde StorageConnectionString in regel 2. 

    > [!IMPORTANT]
    > Als uw eindpunt documents.azure.com gebruikt, hebt u een preview-account en moet u een [nieuw Table-API-account](#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK voor Table- API.
    >

7. Sla het bestand *config.properties* op.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="run-the-app"></a>De app uitvoeren

1. `cd` in het git-terminalvenster naar de map storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Voer in het venster git-terminal de volgende opdrachten uit om de Java-toepassing uit te voeren.

    ```git
    mvn compile exec:java 
    ```

    In het consolevenster worden de tabelgegevens weergegeven die worden toegevoegd aan de nieuwe tabeldatabase in Azure Cosmos DB.

    U kunt nu teruggaan naar Data Explorer en deze nieuwe gegevens bekijken, wijzigen, opvragen en ermee werken. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een tabel maakt met de Gegevensverkenner en een Java-app uitvoert om tabelgegevens toe te voegen.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)
