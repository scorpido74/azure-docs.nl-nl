---
title: 'Azure Cosmos DB: een Node.js-app bouwen met JavaScript SDK voor het beheren van Azure Cosmos DB SQL API-gegevens'
description: Is een Node.js-codevoorbeeld dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit de SQL API van Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: dech
ms.openlocfilehash: bd9405630a471fc1909b1930db8efb7d0419daaa
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495217"
---
# <a name="quickstart-build-a-nodejs-app-using-azure-cosmos-db-sql-api-account"></a>Snelstartgids: een node. js-app bouwen met Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB is de globaal gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query’s op uitvoeren. Deze databases genieten allemaal het voordeel van de globale distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB. 

Deze snelstart laat zien hoe u een [SQL API](sql-api-introduction.md)-account van Azure Cosmos DB, een documentdatabase en een container kunt maken met behulp van de Azure-portal. Vervolgens ontwikkelt u een console-app die is gebouwd op de [SQL JavaScript SDK](sql-api-sdk-node.md) en voert u deze uit. Deze snelstart maakt gebruik van versie 2.0 van de [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Daarnaast doet u het volgende:
    * [Node.js](https://nodejs.org/en/) versie v6.0.0 of hoger
    * [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Een container toevoegen

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een SQL API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren.

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

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsgegevens bijwerken](#update-your-connection-string). 

Als u bekend bent met de vorige versie van de JavaScript SDK, komen de termen 'verzameling' en 'document' u vertrouwd voor. Azure Cosmos DB ondersteunt [meerdere API-modellen](https://docs.microsoft.com/azure/cosmos-db/introduction). Daarom maakt versie 2.0+ van de JavaScript SDK gebruik van de generieke termen 'verzameling', wat een collectie, een grafiek of tabel kan zijn, en 'item', om de inhoud van de container te beschrijven.

De volgende codefragmenten zijn allemaal afkomstig uit het bestand **app.js**.

* De `CosmosClient` is geïnitialiseerd.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Er wordt een nieuwe database gemaakt.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Er wordt een nieuwe container (verzameling) gemaakt.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Er wordt een item (document) gemaakt.

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Er wordt een SQL-query via JSON uitgevoerd.

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

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app.

1. Klik in het [Azure Portal](https://portal.azure.com/)in uw Azure Cosmos-account in de linkernavigatiebalk op **sleutels**en klik vervolgens op **sleutels voor lezen/schrijven**. In de volgende stap gebruikt u de kopieerknoppen aan de rechterkant van het scherm om de URI en primaire sleutel in het bestand `config.js` te kopiëren.

    ![Een toegangssleutel bekijken en kopiëren in Azure Portal, blade Sleutels](./media/create-sql-api-dotnet/keys.png)

2. Open het bestand `config.js`. 

3. Kopieer uw URI-waarde vanaf de portal (met de kopieerknop) en geef deze als waarde aan de eindpuntsleutel in `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Kopieer vervolgens de waarde van uw PRIMAIRE SLEUTEL vanaf de portal en geef deze als waarde aan de `config.key` in `config.js`. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>De app uitvoeren
1. Voer `npm install` uit op een terminal zodat de vereiste npm-modules worden geïnstalleerd.

2. Voer `node app.js` uit op een terminal om uw knooppunttoepassing te starten.

U kunt nu teruggaan naar Data Explorer en deze nieuwe gegevens bekijken, wijzigen, een query erop uitvoeren of er iets anders mee doen. 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos-account maakt, een container maakt met behulp van de Data Explorer en een app uitvoert. Nu kunt u aanvullende gegevens in uw Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)


