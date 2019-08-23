---
title: Node.js-zelfstudie voor de SQL-API voor Azure Cosmos DB
description: Een Node.js-zelfstudie die laat zien hoe u verbinding kunt maken met en een query kunt uitvoeren op Azure Cosmos DB met behulp van de SQL-API
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 5de0fe47d8ca6372c35cc4f148f97b366a1a742c
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971401"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Zelfstudie: Een Node.js-console-app bouwen met de JavaScript SDK voor het beheren van gegevens in Azure Cosmos DB SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van NoSQL-documentgegevens. U kunt een SQL-API-account in Azure Cosmos DB gebruiken om deze documentgegevens op te slaan en te openen. In deze zelfstudie ziet u hoe u een Node.js-consoletoepassing bouwt voor het maken van Azure Cosmos DB-resources en om deze resources te doorzoeken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure Cosmos DB-account maken en er verbinding mee maken.
> * Uw toepassing instellen.
> * Een database maken.
> * Een container maken.
> * Items toevoegen aan de container.
> * Basisbewerkingen uitvoeren op de items, container en database.

## <a name="prerequisites"></a>Vereisten 

Zorg ervoor dat u over de volgende bronnen beschikt:

* Een actief Azure-account. Als u nog geen abonnement hebt, kunt u zich registreren voor een [gratis Azure-proefversie](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 of hoger.

## <a name="create-azure-cosmos-db-account"></a>Een Azure Cosmos DB-account maken

Begin met het maken van een Azure Cosmos DB-account. Als u al een account hebt dat u wilt gebruiken, kunt u verder naar de stap [Uw Node.js-toepassing instellen](#SetupNode). Als u de Azure Cosmos DB-emulator gebruikt, volgt u de stappen in [Azure Cosmos DB-emulator](local-emulator.md) om de emulator in te stellen en meteen naar [Uw Node.js-toepassing instellen](#SetupNode) te gaan. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Uw Node.js-toepassing instellen

Voordat u begint met het schrijven van code voor het maken van de toepassing, kunt u het framework bouwen voor uw app. Voer de volgende stappen uit voor het instellen van uw Node.js-toepassing die de frameworkcode bevat:

1. Open uw favoriete terminal.
2. Ga naar de map of directory waarin u de Node.js-toepassing wilt opslaan.
3. Maak twee lege JavaScript-bestanden met de volgende opdrachten:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Maak en initialiseer een `package.json`-bestand. Gebruik de volgende opdracht:
   * ```npm init -y```

5. Installeer de @azure/cosmos-module via npm. Gebruik de volgende opdracht:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>De configuratie van de app instellen

Nu uw app bestaat, moet u controleren of deze kan communiceren met Azure Cosmos DB. Zoals wordt weergegeven in de volgende stappen, kunt u door het bijwerken van een aantal configuratie-instellingen uw app instellen voor communicatie met Azure Cosmos DB:

1. Open ```config.js``` in uw favoriete teksteditor.

1. Kopieer en plak het onderstaande codefragment. Stel de eigenschappen ```config.endpoint``` en ```config.key``` in op de eindpunt-URI en de primaire sleutel van Azure Cosmos DB. Deze beide configuraties vindt u in [Azure Portal](https://portal.azure.com).

   ![Schermopname van het ophalen sleutels uit de Azure-portal][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.key = "~your primary key here~";
   ``` 

1. Kopieer de ```database```-, ```container```- en ```items```-gegevens en plak deze in uw ```config```-object hieronder, waar u de eigenschappen ```config.endpoint``` en ```config.key``` instelt. Als u al gegevens hebt die u in de database wilt opslaan, kunt u het hulpprogramma voor gegevensmigratie in Azure Cosmos DB gebruiken in plaats van de gegevens hier te definiëren. Het config. js-bestand moet de volgende code bevatten:

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   De JavaScript SDK maakt gebruik van de algemene termen *container* en *item*. Een container kan een verzameling, een graaf of een tabel zijn. Een item kan een document, rand/hoekpunt of rij zijn en is de inhoud binnen een container. 
   
   `module.exports = config;`de code wordt gebruikt om het ```config``` object te exporteren, zodat u ernaar kunt verwijzen in ```app.js``` het bestand.

## <a id="Connect"></a>Verbinding maken met een Azure Cosmos DB-account

1. Open uw lege ```app.js```-bestand in de teksteditor. Kopieer en plak de onderstaande code om de ```@azure/cosmos```-module en de zojuist gemaakte ```config```-module te importeren.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Kopieer en plak de code om de eerder opgeslagen ```config.endpoint``` en ```config.key``` te gebruiken voor het maken van een nieuwe CosmosClient.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });
   ```
   
> [!Note]
> Schakel SSL-verificatie voor uw knooppunt proces uit als u verbinding wilt maken met de **Cosmos DB-emulator**:
>   ```
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Nu u de code hebt om de Azure Cosmos DB-client te initialiseren, kunt u zich verder verdiepen in het werken met Azure Cosmos DB-resources.

## <a name="create-a-database"></a>Een database maken

1. Kopieer en plak de onderstaande code om de database-id en de container-id in te stellen. Op basis van deze id's zoekt de Azure Cosmos DB-client de juiste database en container.

   ```javascript
   const client = new CosmosClient({ endpoint, key });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Er kan een database worden gemaakt met behulp van de functie `createIfNotExists` of create van de klasse **Databases**. Een database is de logische container voor items die zijn gepartitioneerd in containers. 

2. Kopieer en plak de methoden **createDatabase** en **readDatabase** in het app.js-bestand onder de definitie van ```databaseId``` en ```containerId```. De functie **createDatabase** maakt een nieuwe Data Base met de ```FamilyDatabase```id, die is ```config``` opgegeven vanuit het object, als deze nog niet bestaat. De functie **readDatabase** leest de definitie van de database om ervoor te zorgen dat de database bestaat.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { resource: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Kopieer de code en plak deze onder de plaats waar u de functies **createDatabase** en **readDatabase** instelt om de helperfunctie **exit** toe te voegen waarmee het afsluitende bericht wordt afgedrukt. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Kopieer de code en plak deze onder de plaats waar u de functie **exit** instelt om de functies **createDatabase** en **readDatabase** aan te roepen.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Op dit moment moet de code in ```app.js``` er uitzien als de volgende code:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { resource: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Een container maken

Maak vervolgens een container in het Azure Cosmos DB-account, zodat u de gegevens kunt opslaan en opvragen. 

> [!WARNING]
> Het maken van een container heeft gevolgen voor de prijzen. Ga naar onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/), zodat u wat weet u kunt verwachten.

Er kan een container worden gemaakt met behulp van de functie `createIfNotExists` of create van de klasse **Containers**.  Een container bestaat uit items - in het geval van de SQL-API zijn dit JSON-documenten - en bijbehorende JavaScript-toepassingslogica.

1. Kopieer en plak de functies **createContainer** en **readContainer** onder de functie **readDatabase** in het app.js-bestand. Als de database nog niet bestaat wordt met de functie **createContainer** een nieuwe container gemaakt met id ```containerId```, opgegeven vanuit het ```config```-object. De functie **readContainer** leest de containerdefinitie om te verifiëren of de container bestaat.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Kopieer en plak de code onder de aanroep van **readDatabase** om de functies **createContainer** en **readContainer** uit te voeren.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Op dit punt moet de code in ```app.js``` er als volgt uitzien:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Een item maken

Er kan een item worden gemaakt met de functie create van de klasse **Items**. Als u de SQL-API gebruikt, worden items als documenten geprojecteerd. Deze zijn door de gebruiker gedefinieerde (willekeurige) JSON-inhoud. U kunt nu een item invoegen in Azure Cosmos DB.

1. Kopieer de functie **createFamilyItem** en plak deze onder de functie **readContainer**. De functie **createFamilyItem** maakt de items met de JSON-gegevens die in het ```config```-object zijn opgeslagen. We controleren of een item met dezelfde ID nog niet bestaat voordat u het maakt.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. Kopieer en plak de code onder de aanroep van **readContainer** om de functie **createFamilyItem** uit te voeren.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Query's uitvoeren op Azure Cosmos DB-resources

Azure Cosmos DB biedt ondersteuning voor uitgebreide query's voor de JSON-documenten die in elke container zijn opgeslagen. In de volgende voorbeeldcode ziet u een query die u kunt uitvoeren op de documenten in uw container.

1. Kopieer en plak de functie **queryContainer** onder de functie **createFamilyItem** in het app.js-bestand. Azure Cosmos DB biedt ondersteuning voor SQL-achtige query's, zoals hieronder wordt weergegeven.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { resources } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).fetchAll();
    for (var queryResult of resources) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Kopieer en plak de code onder de aanroepen van **createFamilyItem** om de functie **queryContainer** uit te voeren.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Een item vervangen
Azure Cosmos DB biedt ondersteuning voor het vervangen van inhoud van items.

1. Kopieer en plak de functie **replaceFamilyItem** onder de functie **queryContainer** in het app.js-bestand. Merk op dat de waarde van de eigenschap 'grade' van een onderliggend item van 5 is gewijzigd in 6.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. Kopieer en plak de code onder de aanroep van **queryContainer** om de functie **replaceFamilyItem** uit te voeren. Voeg ook de code toe om **queryContainer** opnieuw aan te roepen om te verifiëren dat het item is gewijzigd.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Een item verwijderen

Azure Cosmos DB biedt ondersteuning voor het verwijderen van JSON-items.

1. Kopieer en plak de functie **deleteFamilyItem** onder de functie **replaceFamilyItem**.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Kopieer en plak de code onder de aanroep van de tweede **queryContainer** om de functie **deleteFamilyItem** uit te voeren.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Database verwijderen

Als u de gemaakte database verwijdert, worden de database en alle onderliggende resources (containers, items, enzovoort) verwijderd.

1. Kopieer en plak de functie **cleanup** onder de functie **deleteFamilyItem** om de database en alle onderliggende resources te verwijderen.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Kopieer en plak de code onder de aanroep van **deleteFamilyItem** om de functie **cleanup** uit te voeren.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Uw Node.js-toepassing uitvoeren

De code moet er nu als volgt uit zien:

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit: 

```bash 
node app.js
```

U ziet de uitvoer van uw GetStarted-app. De uitvoer moet overeenkomen met de onderstaande voorbeeldtekst.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>De volledige Node.js-zelfstudieoplossing gebruiken 

Als u geen tijd hebt gehad om de stappen in deze zelfstudie uit te voeren of als u alleen de code wilt downloaden, kunt u deze ophalen van [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Als u de Aan de slag-oplossing wilt uitvoeren met alle code uit dit artikel, hebt u het volgende nodig: 

* Een [Azure Cosmos DB-account][create-account]. 
* De [Aan de slag](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)-oplossing die beschikbaar is via GitHub. 

Installeer de afhankelijkheden van het project via npm. Gebruik de volgende opdracht: 

* ```npm install``` 

Daarna gebruikt u in het bestand ```config.js``` de waarden config.endpoint en config.primaryKey, zoals wordt beschreven in [Stap 3: de configuratie van de app instellen](#Config).  

Zoek vervolgens het ```app.js```-bestand in de terminal en voer de volgende opdracht uit:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, het Azure Cosmos DB-account en alle gerelateerde resources verwijderen wanneer u de resources niet meer nodig hebt. Hiertoe selecteert u de resourcegroep die u hebt gebruikt voor het Azure Cosmos DB-account en selecteert u **Verwijderen**. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Cosmos DB-account controleren](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
