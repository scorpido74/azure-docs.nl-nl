---
title: 'Quickstart: Table-API met Node.js - Azure Cosmos DB'
description: In deze quickstart ziet u hoe u de Table-API in Azure Cosmos DB gebruikt om een toepassing te maken met Azure Portal en Node.js
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/06/2019
ms.author: sngun
ms.openlocfilehash: e0d2d2ea99822c95b9fab73642db37430771c583
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083761"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Quickstart: Een Table-API compileren met Node.js en Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

In deze Quick Start maakt u een Azure Cosmos DB Table-API-account en gebruikt u Data Explorer en een node. js-app die is gekloond van GitHub om tabellen en entiteiten te maken. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement. U kunt ook de [Azure Cosmos DB-emulator](https://aka.ms/cosmosdb-emulator) met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`gebruiken.
- [Node. js 0.10.29 +](https://nodejs.org/) .
- [Git](https://git-scm.com/downloads).

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
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

> ! Tips Zie het artikel [Cosmos DB Table-API voor beeld](table-storage-how-to-use-nodejs.md) voor een gedetailleerd overzicht van soort gelijke code. 

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. Hierdoor kan de app communiceren met de gehoste database. 

1. Selecteer in uw Azure Cosmos DB-account [Azure portal](https://portal.azure.com/)in de Azure Portal **verbindings reeks**. 

    ![De vereiste verbindingsreeksinformatie bekijken en kopiëren vanuit het deelvenster Verbindingsreeks](./media/create-table-nodejs/connection-string.png)

2. Kopieer de primaire VERBINDINGS reeks met behulp van de knop kopiëren aan de rechter kant.

3. Open het bestand *app. config* en plak de waarde in de Connections Tring op regel drie. 

    > [!IMPORTANT]
    > Als uw eindpunt documents.azure.com gebruikt, hebt u een preview-account en moet u een [nieuw Table-API-account](#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK voor Table- API.
    >

3. Sla het bestand *app. config* op.

U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

## <a name="run-the-app"></a>De app uitvoeren

1. `cd` in het git-terminalvenster naar de map storage-table-java-getting-started.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. Voer de volgende opdracht uit om de modules [Azure], [node-uuid], [nconf] en [async] lokaal te installeren, en om een vermelding voor ze in het bestand *package. json* op te slaan.

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. Voer in het git-Terminal venster de volgende opdrachten uit om de node. js-toepassing uit te voeren.

    ```
    node ./tableSample.js 
    ```

    In het consolevenster worden de tabelgegevens weergegeven die worden toegevoegd aan de nieuwe tabeldatabase in Azure Cosmos DB.

    U kunt nu teruggaan naar Data Explorer en deze nieuwe gegevens bekijken, wijzigen, opvragen en ermee werken. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB account maakt, hoe u een tabel maakt met behulp van de Data Explorer en hoe u een node. js-app uitvoert om tabel gegevens toe te voegen.  Nu kunt u een query uitvoeren op uw gegevens met de Table-API.  

> [!div class="nextstepaction"]
> [Tabelgegevens importeren in de Table-API](table-import.md)
