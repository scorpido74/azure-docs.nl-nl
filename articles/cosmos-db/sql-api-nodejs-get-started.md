---
title: Node.js-zelfstudie voor de SQL-API voor Azure Cosmos DB
description: Een Node.js-zelfstudie die laat zien hoe u verbinding kunt maken met en een query kunt uitvoeren op Azure Cosmos DB met behulp van de SQL-API
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.openlocfilehash: ef493b6b21eb0ba0ad6d22a21e4e205a9fecacb6
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858067"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Zelf studie: een node. JS-Console-app bouwen met de Java script SDK om Azure Cosmos DB SQL-API-gegevens te beheren

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Als ontwikkelaar hebt u mogelijk toepassingen die gebruikmaken van NoSQL-documentgegevens. U kunt een SQL-API-account in Azure Cosmos DB gebruiken om deze documentgegevens op te slaan en te openen. In deze zelfstudie ziet u hoe u een Node.js-consoletoepassing bouwt voor het maken van Azure Cosmos DB-resources en om deze resources te doorzoeken.

In deze zelfstudie doet u het volgende:

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

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Uw Node.js-toepassing instellen

Voordat u begint met het schrijven van code voor het maken van de toepassing, kunt u het framework bouwen voor uw app. Voer de volgende stappen uit voor het instellen van uw Node.js-toepassing die de frameworkcode bevat:

1. Open uw favoriete terminal.
2. Ga naar de map of directory waarin u de Node.js-toepassing wilt opslaan.
3. Maak lege java script-bestanden met de volgende opdrachten:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Maak en initialiseer een `package.json`-bestand. Gebruik de volgende opdracht:
   * ```npm init -y```

5. Installeer de @azure/cosmos-module via npm. Gebruik de volgende opdracht:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>De configuraties van uw app instellen

Nu uw app bestaat, moet u controleren of deze kan communiceren met Azure Cosmos DB. Zoals wordt weergegeven in de volgende stappen, kunt u door het bijwerken van een aantal configuratie-instellingen uw app instellen voor communicatie met Azure Cosmos DB:

1. Open het bestand *config. js* in uw favoriete tekst editor.

1. Kopieer het volgende code fragment en plak het in het bestand *config. js* en stel de `endpoint` eigenschappen `key` en de Azure Cosmos DB eind punt-URI en primaire sleutel in. De-data base, container namen worden ingesteld op **taken** en **items**. De partitie sleutel die u voor deze toepassing gebruikt, is **Category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   U vindt het eind punt en de belangrijkste gegevens in het deel venster **sleutels** van de [Azure Portal](https://portal.azure.com).

   ![Schermopname van het ophalen sleutels uit de Azure-portal][keys]

De Java script-SDK maakt gebruik van de algemene voor waarden *container* en *item*. Een container kan een verzameling, een graaf of een tabel zijn. Een item kan een document, rand/hoekpunt of rij zijn en is de inhoud binnen een container. In het vorige code fragment wordt de `module.exports = config;` code gebruikt om het configuratie object te exporteren, zodat u ernaar kunt verwijzen in het bestand *app. js* .

## <a name="create-a-database-and-a-container"></a>Een Data Base en een container maken

1. Open het bestand *databaseContext. js* in uw favoriete tekst editor.

1. Kopieer en plak de volgende code in het bestand *databaseContext. js* . Met deze code wordt een functie gedefinieerd waarmee de data base ' taken ', ' items ' en de container worden gemaakt als deze nog niet bestaan in uw Azure Cosmos-account:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Een database is de logische container voor items die zijn gepartitioneerd in containers. U maakt een Data Base met behulp `createIfNotExists` van de-of-functie voor het maken van de **data bases** -klasse. Een container bestaat uit items die in het geval van de SQL API JSON-documenten zijn. U maakt een container met behulp van `createIfNotExists` de-of-functie maken vanuit de klasse **containers** . Nadat u een container hebt gemaakt, kunt u de gegevens opslaan en opvragen.

   > [!WARNING]
   > Het maken van een container heeft gevolgen voor de prijzen. Ga naar onze [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/), zodat u wat weet u kunt verwachten.

## <a name="import-the-configuration"></a>De configuratie importeren

1. Open het bestand *app. js* in uw favoriete tekst editor.

1. Kopieer en plak de onderstaande code om de `@azure/cosmos` module, de configuratie en de databaseContext te importeren die u in de vorige stappen hebt gedefinieerd. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Verbinding maken met het Azure Cosmos-account

Kopieer en plak de volgende code in het bestand *app. js* om een nieuw CosmosClient-object te maken met het eerder opgeslagen eind punt en de nieuwe sleutel.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Als u verbinding maakt met de **Cosmos DB emulator**, schakelt u TLS-verificatie uit voor uw knooppunt proces:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Nu u de code hebt om de Azure Cosmos DB-client te initialiseren, kunt u zich verder verdiepen in het werken met Azure Cosmos DB-resources.

## <a name="query-items"></a><a id="QueryItem"></a>Query-items

Azure Cosmos DB biedt ondersteuning voor uitgebreide query's op JSON-items die zijn opgeslagen in elke container. De volgende voorbeeld code bevat een query die u kunt uitvoeren op de items in uw container. U kunt de items opvragen met behulp van de query functie `Items` van de-klasse. Voeg de volgende code toe aan het bestand *app. js* om de items uit uw Azure Cosmos-account op te vragen:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Een item maken

Een item kan worden gemaakt met behulp van de functie Create `Items` van de-klasse. Als u de SQL-API gebruikt, worden items als documenten geprojecteerd. Deze zijn door de gebruiker gedefinieerde (willekeurige) JSON-inhoud. In deze zelf studie maakt u een nieuw item in de taken database.

1. Definieer in het bestand app. js de definitie van het item:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Voeg de volgende code toe om het eerder gedefinieerde item te maken:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Een item bijwerken

Azure Cosmos DB ondersteunt de vervanging van de inhoud van items. Kopieer en plak de volgende code in het bestand *app. js* . Met deze code wordt een item uit de container opgehaald en wordt het veld *isComplete* bijgewerkt naar waar.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Een item verwijderen

Azure Cosmos DB biedt ondersteuning voor het verwijderen van JSON-items. De volgende code laat zien hoe u een item kunt ophalen op basis van de ID en het kunt verwijderen. Kopieer en plak de volgende code in het bestand *app. js* :

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Uw Node.js-toepassing uitvoeren

De code moet er nu als volgt uit zien:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

Zoek in de terminal het ```app.js```-bestand en voer de volgende opdracht uit:

```bash 
node app.js
```

U ziet de uitvoer van uw GetStarted-app. De uitvoer moet overeenkomen met de onderstaande voorbeeldtekst.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>De volledige Node.js-zelfstudieoplossing gebruiken

Als u geen tijd hebt gehad om de stappen in deze zelfstudie uit te voeren of als u alleen de code wilt downloaden, kunt u deze ophalen van [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Als u de Aan de slag-oplossing wilt uitvoeren met alle code uit dit artikel, hebt u het volgende nodig:

* Een [Azure Cosmos DB-account][create-account].
* De [Aan de slag](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)-oplossing die beschikbaar is via GitHub.

Installeer de afhankelijkheden van het project via npm. Gebruik de volgende opdracht:

* ```npm install``` 

Werk vervolgens in het ```config.js``` bestand de waarden config. endpoint en config. key bij zoals beschreven in [stap 3: Stel de configuraties van uw app](#Config)in.  

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
