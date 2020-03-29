---
title: Leesvoorkeur gebruiken met de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over het gebruik van de leesvoorkeur van MongoDB met de API van Azure Cosmos DB voor MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 10e6ed556abe8f8c438e5436fbb93c1b70b85d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445163"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Hoe leesbewerking wereldwijd te distribueren met behulp van Azure Cosmos DB's API voor MongoDB

In dit artikel ziet u hoe u leesbewerkingen wereldwijd distribueert met [mongoDB-leesvoorkeursinstellingen](https://docs.mongodb.com/manual/core/read-preference/) met behulp van de API van Azure Cosmos DB voor MongoDB.

## <a name="prerequisites"></a>Vereisten 
Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Raadpleeg dit [Quickstart-artikel](tutorial-global-distribution-mongodb.md) voor instructies over het gebruik van de Azure-portal om een Cosmos-account met globale distributie in te stellen en er vervolgens verbinding mee te maken.

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Open een git-terminalvenster zoals git bash en `cd` naar een werkmap.  

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen. Gebruik op basis van uw interesseplatform een van de volgende voorbeeldrepositories:

1. [.NET-voorbeeldtoepassing](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Voorbeeldtoepassing NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose monster toepassing](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-voorbeeldtoepassing](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Voorbeeldtoepassing SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Afhankelijk van het gebruikte platform installeerje de benodigde pakketten en start je de applicatie. Als u afhankelijkheden wilt installeren, volgt u de README die is opgenomen in de opslagplaats voor voorbeeldtoepassingen. Gebruik bijvoorbeeld in de voorbeeldtoepassing NodeJS de volgende opdrachten om de vereiste pakketten te installeren en de toepassing te starten.

```bash
cd mean
npm install
node index.js
```
De toepassing probeert verbinding te maken met een MongoDB-bron en mislukt omdat de verbindingstekenreeks ongeldig is. Volg de stappen in de README `url`om de verbindingstekenreeks bij te werken . Werk ook `readFromRegion` de naar een gelezen regio in uw Cosmos-account bij. De volgende instructies zijn afkomstig uit het voorbeeld NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Na het volgen van deze stappen wordt de voorbeeldtoepassing uitgevoerd en produceert de volgende uitvoer:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Lezen met de modus Leesvoorkeur

MongoDB-protocol biedt de volgende leesvoorkeursmodi voor clients om te gebruiken:

1. Primaire
2. PRIMARY_PREFERRED
3. Secundaire
4. SECONDARY_PREFERRED
5. Dichtstbijzijnde

Raadpleeg de gedetailleerde [mongoDB Leesvoorkeur-gedragsdocumentatie](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) voor meer informatie over het gedrag van elk van deze leesvoorkeursmodi. In Cosmos DB worden primaire kaarten voor het aantal borden en secundaire kaarten naar DE regio READ toegewezen.

Op basis van veelvoorkomende scenario's raden we u aan de volgende instellingen te gebruiken:

1. Als **er een lage latentie wordt gelezen,** gebruikt u de voorkeursmodus **NEAREST** lezen. Met deze instelling worden de leesbewerkingen naar de dichtstbijzijnde beschikbare regio doorgestoofd. Houd er rekening mee dat als het dichtstbijzijnde gebied het WRITE-gebied is, deze bewerkingen naar dat gebied zijn gericht.
2. Als **een hoge beschikbaarheid en geodistributie van reads** vereist zijn (latentie is geen beperking), gebruikt u de voorkeursmodus **SECUNDAIRe voorkeur** voor lezen. Met deze instelling worden leesbewerkingen naar een beschikbare LEES-regio geherleiden. Als er geen LEES-regio beschikbaar is, worden aanvragen doorgestuurd naar het WRITE-gebied.

In het volgende fragment van de voorbeeldtoepassing ziet u hoe u de voorkeur voor nearest lezen in NodeJS configureert:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

In het onderstaande fragment ziet u ook hoe u de SECONDARY_PREFERRED leesvoorkeur in NodeJS configureert:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

De leesvoorkeur kan ook `readPreference` worden ingesteld door als parameter in de URI-opties voor verbindingstekenreeksen door te geven:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Raadpleeg de bijbehorende voorbeeldtoepassingsrepo's voor andere platforms, zoals [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) en [Java.](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)

## <a name="read-using-tags"></a>Lezen met behulp van tags

Naast de leesvoorkeursmodus maakt het MongoDB-protocol het gebruik van tags mogelijk om leesbewerkingen te regisseren. In de API van Cosmos DB voor `region` MongoDB wordt de `isMaster` tag standaard opgenomen als onderdeel van het antwoord:

```json
"tags": {
         "region": "West US"
      }
```

Daarom kan MongoClient `region` de tag samen met de regionaam gebruiken om leesbewerkingen naar specifieke regio's te leiden. Voor Cosmos-accounts zijn regionamen te vinden in de Azure-portal aan de linkerkant onder **Instellingen->Replica-gegevens wereldwijd**. Deze instelling is handig voor het bereiken van **leesisolatie** - gevallen waarin clienttoepassing alleen leesbewerkingen naar een specifieke regio wilt leiden. Deze instelling is ideaal voor scenario's van het type niet-productie/analyse, die op de achtergrond worden uitgevoerd en geen productiekritieke services zijn.

In het volgende fragment van de voorbeeldtoepassing ziet u hoe u de leesvoorkeur configureert met tags in NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Raadpleeg de bijbehorende voorbeeldtoepassingsrepo's voor andere platforms, zoals [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) en [Java.](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)

In dit artikel hebt u geleerd hoe u leesbewerkingen wereldwijd distribueren met leesvoorkeur met de API van Azure Cosmos DB voor MongoDB.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze app niet blijft gebruiken, verwijdert u alle bronnen die door dit artikel in de Azure-portal zijn gemaakt met de volgende stappen:

1. Klik in het menu aan de linkerkant in Azure Portal op **Resourcegroepen** en klik vervolgens op de resource die u hebt gemaakt. 
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* [MongoDB-gegevens importeren in Azure Cosmos DB](mongodb-migrate.md)
* [Een wereldwijd gedistribueerde database instellen met Azure Cosmos DB's API voor MongoDB](tutorial-global-distribution-mongodb.md)
* [Lokaal ontwikkelen met de Azure Cosmos DB-emulator](local-emulator.md)
