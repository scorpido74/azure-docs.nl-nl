---
title: 'Snelstartgids: node. js gebruiken om een query uit te Azure Cosmos DB SQL-API-account'
description: Node. js gebruiken om een app te maken die verbinding maakt met Azure Cosmos DB SQL-API-account en query gegevens.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 8df78df27ffb7e8bb8fc88567bd0b3d37be20488
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719480"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snelstartgids: node. js gebruiken om verbinding te maken en gegevens op te vragen uit Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze Quick start ziet u hoe u een node. js-app gebruikt om verbinding te maken met het [SQL-API](sql-api-introduction.md) -account in azure Cosmos db. U kunt vervolgens Azure Cosmos DB SQL-query's gebruiken om gegevens op te vragen en te beheren. De node. js-app die u in dit artikel bouwt, maakt gebruik van de [SQL java script SDK](sql-api-sdk-node.md). Deze snelstart maakt gebruik van versie 2.0 van de [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Daarnaast doet u het volgende:
    * [Node.js](https://nodejs.org/en/) versie v6.0.0 of hoger
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Een database maken 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een node. js-app klonen vanaf GitHub, de connection string instellen en uitvoeren.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de Azure Cosmos-database resources in de code worden gemaakt, kunt u de volgende fragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsgegevens bijwerken](#update-your-connection-string). 

Als u bekend bent met de vorige versie van de JavaScript SDK, komen de termen 'verzameling' en 'document' u vertrouwd voor. Azure Cosmos DB ondersteunt [meerdere API-modellen](https://docs.microsoft.com/azure/cosmos-db/introduction). Daarom maakt versie 2.0+ van de JavaScript SDK gebruik van de generieke termen 'verzameling', wat een collectie, een grafiek of tabel kan zijn, en 'item', om de inhoud van de container te beschrijven.

De volgende codefragmenten zijn allemaal afkomstig uit het bestand **app.js**.

* Het `CosmosClient`-object is geïnitialiseerd.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Maak een nieuwe Azure Cosmos-data base.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Er wordt in de Data Base een nieuwe container (verzameling) gemaakt.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Er wordt een item (document) gemaakt.

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Een SQL-query via JSON wordt uitgevoerd op de Family-data base. De query retourneert alle onderliggende items van de "Anderson"-familie. 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar de Azure Portal om de connection string Details van uw Azure Cosmos-account op te halen. Kopieer de connection string in de app zodat deze verbinding kan maken met uw data base.

1. Klik in het [Azure Portal](https://portal.azure.com/)in uw Azure Cosmos-account in de linkernavigatiebalk op **sleutels**en klik vervolgens op **sleutels voor lezen/schrijven**. In de volgende stap gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de URI en primaire sleutel in het bestand `config.js` te kopiëren.

    ![Een toegangssleutel bekijken en kopiëren in Azure Portal, blade Sleutels](./media/create-sql-api-dotnet/keys.png)

2. Open het bestand `config.js`. 

3. Kopieer uw URI-waarde vanaf de portal (met de kopieerknop) en geef deze als waarde aan de eindpuntsleutel in `config.js`. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Kopieer vervolgens de waarde van uw PRIMAIRE SLEUTEL vanaf de portal en geef deze als waarde aan de `config.key` in `config.js`. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>De app kunt uitvoeren

1. Voer `npm install` uit op een terminal zodat de vereiste npm-modules worden geïnstalleerd.

2. Voer `node app.js` uit op een terminal om uw knooppunttoepassing te starten.

U kunt nu teruggaan naar Data Explorer, wijzigen en werken met deze nieuwe gegevens.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account maakt, een container maakt met behulp van Data Explorer en een app uitvoert. U kunt nu aanvullende gegevens importeren in uw Azure Cosmos-data base. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)


