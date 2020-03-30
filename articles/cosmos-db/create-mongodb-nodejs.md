---
title: 'Snelstart: een Node.js MongoDB-app verbinden met Azure Cosmos DB'
description: In deze snelstartgids wordt beschreven hoe u een bestaande MongoDB-app die is geschreven in Node.js verbindt met Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 7e3e9e6c76d67db03ea812a4832e98f4449c9aba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77061655"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Snelstart: een bestaande MongoDB Node.js-web-app migreren naar Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

In deze quickstart maakt en beheert u een Azure Cosmos DB voor Mongo DB API-account met behulp van de Azure Cloud Shell en met een MEAN-app (MongoDB, Express, Angular en Node.js) gekloond vanuit GitHub. Azure Cosmos DB is een databaseservice met meerdere modellen waarmee u snel document-, tabel-, sleutelwaarde- en grafiekdatabases maken en opvragen met globale distributie- en horizontale schaalmogelijkheden.

## <a name="prerequisites"></a>Vereisten
- Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Of [probeer Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement. U de [Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) Emulator `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`ook gebruiken met de verbindingstekenreeks.
- [Node.js](https://nodejs.org/), en een werkkennis van Node.js.
- [Git.](https://git-scm.com/downloads)
- Als u Azure Cloud Shell niet wilt gebruiken, [azure CLI 2.0+](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen. Deze voorbeeldopslagplaats bevat de standaard [MEAN.js](https://meanjs.org/)-toepassing.

1. Open een opdrachtprompt, maak een nieuwe map met de naam git-samples en sluit vervolgens de opdrachtprompt.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Open een git-terminalvenster, bijvoorbeeld git bash, en gebruik de `cd`-opdracht om naar de nieuwe map te gaan voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

Deze MongoDB-app geschreven in Node.js maakt verbinding met uw Azure Cosmos DB-database, die de MongoDB-client ondersteunt. Met andere woorden, het is transparant voor de toepassing dat de gegevens worden opgeslagen in een Azure Cosmos DB-database.

Installeer de vereiste pakketten en start de toepassing.

```bash
cd mean
npm install
npm start
```
Er wordt een poging gedaan om de toepassing te verbinden met een MongoDB-bron, wat mislukt. Sluit de toepassing wanneer de uitvoer het volgende retourneert: [MongoError: connect ECONNREFUSED 127.0.0.1:27017].

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit onderwerp gebruikmaken van Azure CLI versie 2.0 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren] als u moet installeren of upgraden. 

Als u een geïnstalleerde Azure CLI gebruikt, meldt u zich aan bij uw Azure-abonnement met de [inlogopdracht az](/cli/azure/reference-index#az-login) en volgt u de aanwijzingen op het scherm. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>De Azure Cosmos DB-module toevoegen

Als u van een geïnstalleerde Azure CLI gebruikmaakt, controleert u of het onderdeel `cosmosdb` al is geïnstalleerd door de opdracht `az` uit te voeren. Als `cosmosdb` in de lijst met basisopdrachten staat, gaat u verder met de volgende opdracht. U kunt deze stap overslaan als u de Azure Cloud Shell gebruikt.

Als `cosmosdb` niet in de lijst met basisopdrachten staat, installeert u [Azure CLI](/cli/azure/install-azure-cli) opnieuw.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [resourcegroep](../azure-resource-manager/management/overview.md) met de [naam AZ-groep.](/cli/azure/group#az-group-create) Een Azure-resourcegroep is een logische container waarin Azure-resources, zoals web-apps, databases en opslagaccounts, worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep gemaakt in de regio Europa - west. Kies een unieke naam voor de resourcegroep.

Als u Azure Cloud Shell gebruikt, selecteert u **Proberen,** volg de aanwijzingen op het scherm om in te loggen en kopieert u de opdracht naar de opdrachtprompt.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Maak een Cosmos-account met de opdracht [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

Vervang in de volgende opdracht waar u de plaatsaanduiding `<cosmosdb-name>` ziet staan, de accountnaam met uw unieke Cosmos-accountnaam. Deze unieke naam wordt gebruikt als onderdeel van uw Cosmos DB-eindpunt (`https://<cosmosdb-name>.documents.azure.com/`). De naam moet daarom uniek zijn binnen alle Cosmos-accounts in Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

De parameter `--kind MongoDB` maakt MongoDB-clientverbindingen mogelijk.

Wanneer de Azure Cosmos DB-account wordt gemaakt toont de Azure CLI informatie die lijkt op het volgende voorbeeld. 

> [!NOTE]
> In dit voorbeeld wordt JSON gebruikt als de Azure CLI-uitvoerindeling. Dit is standaardindeling. Zie [Output formats for Azure CLI commands](https://docs.microsoft.com/cli/azure/format-output-azure-cli) (Uitvoerindelingen voor Azure CLI-opdrachten) als u een andere uitvoerindeling wilt gebruiken.

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Uw Node.js-toepassing verbinden met de database

In deze stap koppelt u uw MEAN.js-voorbeeldtoepassing aan het Azure Cosmos DB-databaseaccount dat u zojuist hebt gemaakt. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configureer de verbindingsreeks in uw Node.js-toepassing

Open `config/env/local-development.js` in uw MEAN.js-opslagplaats.

Vervang de inhoud van dit bestand door de volgende code. Zorg ervoor dat u ook de twee `<cosmosdb-name>`-plaatsaanduidingen vervangt door de naam van uw Cosmos-account.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>De sleutel ophalen

U hebt de databasesleutel nodig om verbinding te kunnen maken met een Cosmos-database. Gebruik de lijst met az [cosmosdb-toetsen](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) om de primaire sleutel op te halen.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

De Azure CLI voert informatie uit die lijkt op het volgende voorbeeld. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Kopieer de waarde van `primaryMasterKey`. Plak deze op de `<primary_master_key>` in `local-development.js`.

Sla uw wijzigingen op.

### <a name="run-the-application-again"></a>Voer de toepassing opnieuw uit.

Voer `npm start` opnieuw uit. 

```bash
npm start
```

Er verschijnt nu een consolebericht dat de ontwikkelomgeving gereed is voor gebruik. 

Ga `http://localhost:3000` naar in een browser. Selecteer **Aanmelden** in het bovenste menu en probeer twee dummy-gebruikers te maken. 

De MEAN.js-voorbeeldtoepassing slaat gebruikersgegevens op in de database. Als het lukt en MEAN.js automatisch aanmeldt met de gemaakte gebruiker, is uw Azure Cosmos DB-verbinding actief. 

![MEAN.js maakt een geslaagde verbinding met MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Gegevens bekijken in Data Explorer

Gegevens die zijn opgeslagen in een Cosmos-database kunnen via Azure Portal worden bekeken en er kunnen vanuit de portal query's op worden uitgevoerd.

Meld u aan bij de [Azure Portal](https://portal.azure.com) in uw webbrowser om de gebruikersgegevens die u in de vorige stap hebt gemaakt, te bekijken, query’s erop uit te voeren of andere taken ermee uit te voeren.

Voer in het bovenste zoekvak **Azure Cosmos DB**in . Wanneer uw Cosmos-accountblade wordt geopend, selecteert u uw Cosmos-account. Selecteer **gegevensverkenner**in de linkernavigatie . Vouw uw verzameling uit in het venster Verzamelingen. Dan kunt u de documenten in de verzameling zien, query’s op de gegevens uitvoeren en zelfs opgeslagen procedures, triggers en UDF’s maken en uitvoeren. 

![Data Explorer in Azure Portal](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>De Node.js-toepassing implementeren naar Azure

In deze stap implementeert u uw Node.js-toepassing naar Cosmos DB.

U hebt wellicht opgemerkt dat het configuratiebestand dat u eerder had gewijzigd, bestemd is voor de ontwikkelomgeving (`/config/env/local-development.js`). Wanneer u uw toepassing implementeert naar App Service, wordt deze standaard in de productieomgeving uitgevoerd. U moet nu dus dezelfde wijziging toepassing op het respectieve configuratiebestand.

Open `config/env/production.js` in uw MEAN.js-opslagplaats.

Vervang in het object `db` de waarde van `uri` zoals getoond in het volgende voorbeeld. Zorg dat u de plaatsaanduidingen vervangt zoals eerder beschreven.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> De optie `ssl=true` is belangrijk omdat [Cosmos DB SSL vereist](connect-mongodb-account.md#connection-string-requirements). 
>
>

Voer in de terminal al uw wijzigingen door in Git. U kunt beide opdrachten kopiëren om ze samen uit te voeren.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Cosmos DB MongoDB API-account maakt met behulp van de Azure Cloud Shell en een MEAN.js-app maken en uitvoeren om gebruikers aan het account toe te voegen. Nu kunt u aanvullende gegevens in uw Azure Cosmos DB-account importeren.

> [!div class="nextstepaction"]
> [MongoDB-gegevens importeren in Azure Cosmos DB](mongodb-migrate.md)
