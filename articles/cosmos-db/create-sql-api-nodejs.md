---
title: 'Snelstartgids: node. js gebruiken om een query uit te zoeken vanuit Azure Cosmos DB SQL-API-account'
description: Node. js gebruiken om een app te maken die verbinding maakt met Azure Cosmos DB SQL-API-account en query gegevens.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: dech
ms.openlocfilehash: 117d4a5c1c4ac00e6d6a561f7dc4254a15a24f9c
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330682"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Snelstartgids: node. js gebruiken om verbinding te maken en gegevens op te vragen uit Azure Cosmos DB SQL-API-account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In deze Quick Start maakt en beheert u een Azure Cosmos DB SQL-API-account van de Azure Portal en gebruikt u een node. js-app die is gekloond van GitHub. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Of [Probeer gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) zonder een Azure-abonnement. U kunt ook de [Azure Cosmos DB emulator](https://aka.ms/cosmosdb-emulator) gebruiken met een URI van `https://localhost:8081` en de sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node. js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Een Azure Cosmos-account maken

Voor dit Quick Start kunt u de optie [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) gebruiken om een Azure Cosmos-account te maken.

1. Ga naar de pagina [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) .

1. Kies het **SQL** -API-account en selecteer **maken**. Meld u aan met uw Microsoft-account zoals Outlook.

1. Nadat het aanmelden is gelukt, moet uw Azure Cosmos-account gereed zijn. Selecteer **openen in het Azure Portal** om het zojuist gemaakte account te openen.

Voor de optie ' Probeer Azure Cosmos DB gratis ' hebt u geen Azure-abonnement nodig en biedt u een Azure Cosmos-account voor een beperkte periode van 30 dagen. Als u het Azure Cosmos-account gedurende een langere periode wilt gebruiken, moet u in plaats daarvan [het account maken](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) binnen uw Azure-abonnement.

## <a name="add-a-container"></a>Een container toevoegen

U kunt nu het hulp programma Data Explorer in de Azure Portal gebruiken om een Data Base en container te maken. 

1. Selecteer **Data Explorer** > **nieuwe container**. 
    
    Het gebied **container toevoegen** wordt helemaal rechts weer gegeven. mogelijk moet u naar rechts scrollen om het weer te geven.

    ![Het deel venster Azure Portal Data Explorer, container toevoegen](./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png)

2. Voer op de pagina **container toevoegen** de instellingen voor de nieuwe container in.

    |Instelling|Voorgestelde waarde|Beschrijving
    |---|---|---|
    |**Database-id**|Taken|Voer *Taken* in als de naam voor de nieuwe database. Databasenamen moeten tussen de 1 en 255 tekens zijn en mogen geen `/, \\, #, ?` bevatten en mogen niet eindigen met een spatie. Schakel de optie **doorvoer database inrichten** in, zodat u de door Voer die is ingericht voor de data base, kunt delen in alle containers in de data base. Deze optie helpt ook bij het besparen van kosten. |
    |**Doorvoer**|400|De door Voer bij 400 aanvraag eenheden per seconde (RU/s) behouden. U kunt de doorvoer later opschalen als u de latentie wilt beperken.| 
    |**Container-ID**|Items|Voer *items* in als de naam voor de nieuwe container. Container-Id's hebben dezelfde teken vereisten als database namen.|
    |**Partitie sleutel**| /category| Het voor beeld dat in dit artikel wordt beschreven, maakt gebruik van *Category* als de partitie sleutel.|
    
    Naast de voor gaande instellingen kunt u eventueel **unieke sleutels** voor de container toevoegen. In dit voorbeeld laten we het veld leeg. Unieke sleutels bieden ontwikkelaars de mogelijkheid om een gegevensintegriteitslaag aan de database toe te voegen. Door een beleid voor unieke sleutels te maken tijdens het maken van een container, zorgt u ervoor dat een of meer waarden per partitie sleutel uniek zijn. Raadpleeg het artikel [Unique keys in Azure Cosmos DB](unique-keys.md) (Unieke sleutels in Azure Cosmos DB) voor meer informatie.
    
    Selecteer **OK**. De Data Explorer de nieuwe data base en container worden weer gegeven.

## <a name="add-sample-data"></a>Voorbeeldgegevens toevoegen

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Uw gegevens opvragen

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een node. js-app klonen vanaf GitHub, de connection string instellen en uitvoeren.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de Azure Cosmos-database resources in de code worden gemaakt, kunt u de volgende fragmenten bekijken. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsgegevens bijwerken](#update-your-connection-string).

Als u bekend bent met de vorige versie van de SQL java script-SDK, kunt u de voor waarden van de _verzameling_ en het _document_bekijken. Omdat Azure Cosmos DB [meerdere API-modellen](introduction.md)ondersteunt, gebruikt [versie 2.0 + van de Java script SDK](https://www.npmjs.com/package/@azure/cosmos) de _container_algemene voor waarden. Dit kan een verzameling, grafiek of tabel zijn en een _item_ om de inhoud van de container te beschrijven.

De volgende codefragmenten zijn allemaal afkomstig uit het bestand _app.js_.

- Het `CosmosClient`-object is geïnitialiseerd.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Selecteer de data base ' Tasks '.

  ```javascript
  const database = await client.databases(databaseId);
  ```

- Selecteer de container/verzameling items.

  ```javascript
  const container = await client.databases(containerId);
  ```

- Selecteer alle items in de container items.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: results } = await container.items
    .query(querySpec)
    .fetchAll();

  return results;
  ```

- Een nieuw item maken

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Een item bijwerken

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: itemToUpdate } = await container
    .item(id, category)
    .replace(itemToUpdate);

  return result;
  ```

- Een item verwijderen

  ```javascript
  const { resource: result } = await this.container.item(id, category).delete();
  ```

> [!NOTE]
> In de methoden ' update ' en ' delete ' moet het item worden geselecteerd in de-data base door het aanroepen van `container.item()`. De twee door gegeven para meters zijn de id van het item en de partitie sleutel van het item. In dit geval is de sleutel partitioneren de waarde van het veld Category.

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar de Azure Portal om de connection string Details van uw Azure Cosmos-account op te halen. Kopieer de connection string in de app zodat deze verbinding kan maken met uw data base.

1. Selecteer in uw Azure Cosmos DB-account in de [Azure Portal](https://portal.azure.com/)de optie **sleutels** in de linkernavigatiebalk en selecteer **sleutels voor lezen/schrijven**. Gebruik de Kopieer knoppen aan de rechter kant van het scherm om de URI en primaire sleutel in de volgende stap naar het bestand _app. js_ te kopiëren.

   ![Een toegangssleutel bekijken en kopiëren in Azure Portal, blade Sleutels](./media/create-sql-api-dotnet/keys.png)

2. Open in het bestand _config. js_ .

3. Kopieer uw URI-waarde vanuit de portal (met de Kopieer knop) en geef deze als waarde aan de eindpunt sleutel in _config. js_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Kopieer vervolgens de waarde van uw primaire sleutel uit de portal en geef deze als waarde aan de `config.key` in _config. js_. U hebt uw app nu bijgewerkt met alle informatie die nodig is voor de communicatie met Azure Cosmos DB.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>De app kunt uitvoeren

1. Voer `npm install` uit op een terminal zodat de vereiste npm-modules worden geïnstalleerd.

2. Voer `node app.js` uit op een terminal om uw knooppunttoepassing te starten.

U kunt nu teruggaan naar Data Explorer, wijzigen en werken met deze nieuwe gegevens.

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB account maakt, een container maakt met behulp van de Data Explorer en een node. js-app uitvoert. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [Gegevens importeren in Azure Cosmos DB](import-data.md)
