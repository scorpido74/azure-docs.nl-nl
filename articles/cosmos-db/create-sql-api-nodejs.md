---
title: Snelstart- Gebruik Node.js om op te vragen vanuit Azure Cosmos DB SQL API-account
description: Node.js gebruiken om een app te maken die verbinding maakt met Azure Cosmos DB SQL API-account en query's.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 0b29f9c1f395e079c97d5877d08bd7bd73c7ea53
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240318"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snelstart: Gebruik Node.js om gegevens van Azure Cosmos DB SQL API-account te verbinden en te query's

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java](create-sql-api-java.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze quickstart maakt en beheert u een Azure Cosmos DB SQL API-account vanuit de Azure-portal en gebruikt u een Node.js-app die is gekloond vanuit GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="walkthrough-video"></a>Walkthrough-video

Bekijk deze video voor een volledige walkthrough van de inhoud in dit artikel.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U ook de [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Node.js 6.0.0+](https://nodejs.org/).
- [Git.](https://www.git-scm.com/downloads)

## <a name="create-an-azure-cosmos-account"></a>Een Azure Cosmos-account maken

Voor dit snelle startdoel u de optie [Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) uitproberen gebruiken om een Azure Cosmos-account te maken.

1. Navigeer naar de gratis pagina [Azure Cosmos DB uitproberen.](https://azure.microsoft.com/try/cosmosdb/)

1. Kies het **SQL API-account** en selecteer **Maken**. Aanmelden met uw Microsoft-account.

1. Nadat de aanmelding is geslaagd, moet uw Azure Cosmos-account gereed zijn. Selecteer **Openen in de Azure-portal** om het nieuw gemaakte account te openen.

Voor de optie 'Probeer Azure Cosmos DB gratis' is geen Azure-abonnement vereist en wordt u een Azure Cosmos-account aangeboden voor een beperkte periode van 30 dagen. Als u het Azure Cosmos-account voor een langere periode wilt gebruiken, moet u in plaats daarvan [het account maken](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) binnen uw Azure-abonnement.

## <a name="add-a-container"></a>Een container toevoegen

U nu het hulpprogramma Gegevensverkenner in de Azure-portal gebruiken om een database en container te maken.

1. Selecteer Nieuwe**container** **van Data Explorer** > .

   Het gebied **Container toevoegen** wordt helemaal rechts weergegeven, het kan zijn dat u naar rechts moet scrollen om het te zien.

   ![Azure Portal Data Explorer, deelvenster Container toevoegen](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. Voer op de pagina **Container toevoegen** de instellingen voor de nieuwe container in.

   | Instelling           | Voorgestelde waarde | Beschrijving                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Database-id**   | Taken           | Voer _Taken_ in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Controleer de optie **Voorhet overslaginrichten** van de database, hiermee u de overslag die is ingericht met de database delen over alle containers in de database. Deze optie helpt ook bij kostenbesparingen. |
   | **Doorvoer**    | 400             | Laat de doorvoer op 400 aanvraageenheden per seconde (RU/s). U kunt de doorvoer later opschalen als u de latentie wilt beperken.                                                                                                                                                                                                                                                    |
   | **Container-ID**  | Items           | Voer _Items in_ als de naam voor uw nieuwe container. Voor id's van containers gelden dezelfde tekenvereisten als voor databasenamen.                                                                                                                                                                                                                                                               |
   | **Partitiesleutel** | /category       | In het voorbeeld dat in dit artikel wordt beschreven, wordt _/categorie_ als partitiesleutel gebruikt.                                                                                                                                                                                                                                                                                                           |

   Naast de voorgaande instellingen u optioneel **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een uniek sleutelbeleid te maken tijdens het maken van een container, zorgt u voor de uniciteit van een of meer waarden per partitiesleutel. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.

   Selecteer **OK**. In Data Explorer worden de nieuwe database en container weergegeven.

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Laten we nu een Node.js-app van GitHub klonen, de verbindingstekenreeks instellen en deze uitvoeren.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de Azure Cosmos-databasebronnen in de code worden gemaakt, u de volgende fragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsreeks bijwerken](#update-your-connection-string).

Als u bekend bent met de vorige versie van de SQL JavaScript SDK, u gewend zijn om de termen _verzameling_ en _document_te zien. Omdat Azure Cosmos DB [meerdere API-modellen](introduction.md)ondersteunt, gebruikt [versie 2.0+ van de JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) de container met algemene termen , die mogelijk een verzameling, grafiek of tabel en _item_ zijn om de inhoud van de container te beschrijven. _container_

De Cosmos DB JavaScript@azure/cosmosSDK heet " " en kan worden geïnstalleerd vanaf npm...

```bash
npm install @azure/cosmos
```

De volgende fragmenten zijn allemaal afkomstig uit het _app.js-bestand._

- Het `CosmosClient` wordt geïmporteerd `@azure/cosmos` uit het npm-pakket.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Een `CosmosClient` nieuw object wordt geïnitialiseerd.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Selecteer de database 'Taken'.

  ```javascript
  const database = client.database(databaseId);
  ```

- Selecteer de container /verzameling Items.

  ```javascript
  const container = database.container(containerId);
  ```

- Selecteer alle items in de container Items.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Maak een nieuw item

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Een item bijwerken

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Een item verwijderen

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> In zowel de methoden "bijwerken" als "verwijderen" moet het item `container.item()`uit de database worden geselecteerd door aan te roepen . De twee doorgegeven parameters zijn de id van het item en de partitiesleutel van het item. In dit geval is de parition-toets de waarde van het veld 'categorie'.

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar de Azure-portal om de verbindingstekenreeksgegevens van uw Azure Cosmos-account op te vragen. Kopieer de verbindingstekenreeks naar de app, zodat deze verbinding kan maken met uw database.

1. Selecteer **Sleutels** in de linkernavigatie in uw Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com/)en selecteer **Vervolgens Leesschrijftoetsen**. Gebruik de kopieerknoppen aan de rechterkant van het scherm om de URI- en primaire sleutel in de volgende stap naar het _app.js-bestand_ te kopiëren.

   ![Een toegangssleutel bekijken en kopiëren in Azure Portal, blade Sleutels](./media/create-sql-api-dotnet/keys.png)

2. In Open het _bestand config.js._

3. Kopieer uw URI-waarde van de portal (met behulp van de kopieerknop) en maak er de waarde van de eindpunttoets in _config.js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopieer vervolgens de primaire sleutelwaarde van de portal `config.key` en maak er de waarde van de in _config.js_. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>De app uitvoeren

1. Run `npm install` in een terminal@azure/cosmosom het " " npm pakket te installeren

2. Voer `node app.js` uit op een terminal om uw knooppunttoepassing te starten.

3. De twee items die u eerder in deze quickstart hebt gemaakt, worden weergegeven. Er wordt een nieuw item gemaakt. De vlag 'isVolledig' op dat item wordt bijgewerkt naar 'true' en uiteindelijk wordt het item verwijderd.

U blijven experimenteren met deze voorbeeldtoepassing of teruggaan naar Gegevensverkenner, wijzigen en werken met uw gegevens.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een container maakt met de Data Explorer en een Node.js-app uitvoert. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [gegevens importeren in azure cosmos db](import-data.md)
