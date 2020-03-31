---
title: Een Azure Cosmos DB Node.js-toepassing ontwikkelen met de Gremlin-API
description: Is een Node.js-codevoorbeeld dat u kunt gebruiken om verbinding te maken met en gegevens op te vragen uit Azure Cosmos DB
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: e6456c79dbce1f8bb874ce4c88b932e592235a82
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80244423"
---
# <a name="quickstart-build-a-nodejs-application-by-using-azure-cosmos-db-gremlin-api-account"></a>Snelstart: een Node.js-toepassing bouwen met Azure Cosmos DB Gremlin API-account

> [!div class="op_single_selector"]
> * [Gremlin-console](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [Php](create-graph-php.md)
>  

In deze quickstart maakt en beheert u een Azure Cosmos DB Gremlin (grafiek) API-account vanuit de Azure-portal en voegt u gegevens toe met behulp van een Node.js-app die is gekloond vanuit GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Node.js 0.10.29+](https://nodejs.org/).
- [Git.](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>Een databaseaccount maken

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Een graaf toevoegen

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Gremlin-API-app klonen vanaf GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Open het oplossingenbestand in Visual Studio. 

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de databaseresources in de code worden gemaakt, kunt u de volgende codefragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string). 

De volgende fragmenten zijn allemaal afkomstig uit het *app.js-bestand.*

Deze console-app maakt gebruik van de open-source [Gremlin Node.js](https://www.npmjs.com/package/gremlin) driver.

* De Gremlin-client wordt gemaakt.

    ```javascript
    const authenticator = new Gremlin.driver.auth.PlainTextSaslAuthenticator(
        `/dbs/${config.database}/colls/${config.collection}`, 
        config.primaryKey
    )


    const client = new Gremlin.driver.Client(
        config.endpoint, 
        { 
            authenticator,
            traversalsource : "g",
            rejectUnauthorized : true,
            mimeType : "application/vnd.gremlin-v2.0+json"
        }
    );

    ```

  De configuraties zijn allemaal in *config.js*, die we bewerken in de [volgende sectie](#update-your-connection-string).

* Een reeks functies wordt gedefinieerd voor het uitvoeren van verschillende Gremlin-bewerkingen. Dit is er een van:

    ```javascript
    function addVertex1()
    {
        console.log('Running Add Vertex1'); 
        return client.submit("g.addV(label).property('id', id).property('firstName', firstName).property('age', age).property('userid', userid).property('pk', 'pk')", {
                label:"person",
                id:"thomas",
                firstName:"Thomas",
                age:44, userid: 1
            }).then(function (result) {
                    console.log("Result: %s\n", JSON.stringify(result));
            });
    }
    ```

* Elke functie voert een `client.execute`-methode uit met behulp van een Gremlin-querytekenreeksparameter. Hier volgt een voorbeeld van hoe `g.V().count()` wordt uitgevoerd:

    ```javascript
    function countVertices()
    {
        console.log('Running Count');
        return client.submit("g.V().count()", { }).then(function (result) {
            console.log("Result: %s\n", JSON.stringify(result));
        });
    }
    ```

* Aan het einde van het bestand worden vervolgens alle methoden aangeroepen. Hiermee worden ze achter elkaar uitgevoerd:

    ```javascript
    client.open()
    .then(dropGraph)
    .then(addVertex1)
    .then(addVertex2)
    .then(addEdge)
    .then(countVertices)
    .catch((err) => {
        console.error("Error running query...");
        console.error(err)
    }).then((res) => {
        client.close();
        finish();
    }).catch((err) => 
        console.error("Fatal error:", err)
    );
    ```


## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

1. Open het *bestand config.js.* 

2. Vul in *config.js* `config.endpoint` de sleutel in met de **gremlin-eindpuntwaarde** op de **pagina Overzicht** van uw Cosmos DB-account in de Azure-portal. 

    `config.endpoint = "https://<your_Gremlin_account_name>.gremlin.cosmosdb.azure.com:443/";`

    ![Een toegangssleutel weergeven en kopiëren in de pagina Overzicht](./media/create-graph-nodejs/gremlin-uri.png)

3. Vul in *config.js*de waarde config.primaryKey in met de primaire **sleutelwaarde** van de pagina **Sleutels** van uw Cosmos DB-account in de Azure-portal. 

    `config.primaryKey = "PRIMARYKEY";`

   ![De blade Sleutels in Azure Portal](./media/create-graph-nodejs/keys.png)

4. Voer de databasenaam en de naam van de graaf (container) in voor de waarde van config.database en config.collection. 

Hier volgt een voorbeeld van hoe uw voltooide *config.js-bestand* eruit moet zien:

```javascript
var config = {}

// Note that this must include the protocol (HTTPS:// for .NET SDK URI or wss:// for Gremlin Endpoint) and the port number
config.endpoint = "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"; 
config.primaryKey = "Pams6e7LEUS7LJ2Qk0fjZf3eGo65JdMWHmyn65i52w8ozPX2oxY3iP0yu05t9v1WymAHNcMwPIqNAEv3XDFsEg==";
config.database = "graphdb"
config.collection = "Persons"

module.exports = config;
```

## <a name="run-the-console-app"></a>De app console uitvoeren

1. Open een terminalvenster en `cd` wijzig (via opdracht) in de installatiemap voor het *package.json-bestand* dat in het project is opgenomen.

2. Voer `npm install` uit om de vereiste npm-modules, waaronder `gremlin`, te installeren.

3. Voer `node app.js` uit op een terminal om uw knooppunttoepassing te starten.

## <a name="browse-with-data-explorer"></a>Bladeren met Data Explorer

U kunt nu teruggaan naar Data Explorer in Azure Portal en uw nieuwe graafgegevens bekijken, aanpassen en bewerken en er query's op uitvoeren.

In Gegevensverkenner wordt de nieuwe database weergegeven in het deelvenster **Grafieken.** Vouw de database uit, gevolgd door de container, en selecteer **Graph**.

De gegevens die worden gegenereerd door de voorbeeld-app worden weergegeven in het volgende deelvenster binnen het tabblad **Grafiek** wanneer u het tabblad **Filter toepassen** selecteert.

Probeer `g.V()` voltooien met `.has('firstName', 'Thomas')` om het filter te testen. Houd er rekening mee dat de waarde hoofdlettergevoelig is.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-your-resources"></a>Uw resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een grafiek maakt met Behulp van Data Explorer en een Node.js-app uitvoert om gegevens aan de grafiek toe te voegen. U kunt nu complexere query's maken en met Gremlin krachtige logica implementeren om door een graaf te gaan. 

> [!div class="nextstepaction"]
> [Query’s uitvoeren met Gremlin](tutorial-query-graph.md)
