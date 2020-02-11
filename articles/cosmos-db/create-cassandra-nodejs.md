---
title: 'Snelstart: Cassandra-API met Node.js - Azure Cosmos DB'
description: In deze quickstart ziet u hoe u de Cassandra-API in Azure Cosmos DB gebruikt om een profieltoepassing te maken met Node.js
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: cea504393ba1735298bf04b3e9a2d55a9fa42c66
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117927"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Quick Start: een Cassandra-app bouwen met de node. js-SDK en Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

Deze quickstart laat zien hoe u Node.js en de [Cassandra-API](cassandra-introduction.md) van Azure Cosmos DB gebruikt voor het compileren van een profiel-app door een voorbeeld uit GitHub te klonen. In deze snelstart ziet u ook hoe u de webportal van Azure gebruikt om een Azure Cosmos DB-account te maken.

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt snel databases maken van documenten, sleutel/waarde-paren en grafieken en hier query's op uitvoeren. Deze databases genieten allemaal het voordeel van de wereldwijde distributie en horizontale schaalmogelijkheden die ten grondslag liggen aan Azure Cosmos DB. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, zonder kosten en zonder verplichtingen.

U hebt verder nodig:
* [Node.js](https://nodejs.org/en/) versie v0.10.29 of hoger
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Een databaseaccount maken

Voordat u een documentdatabase kunt maken, moet u een Cassandra-account maken met Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

We gaan nu een Cassandra API-app klonen vanuit GitHub, de verbindingsreeks instellen en de app uitvoeren. U zult zien hoe gemakkelijk het is om op een programmatische manier met gegevens te werken. 

1. Open een opdrachtprompt. Maak een nieuwe map met de naam `git-samples`. Sluit de opdrachtprompt.

    ```bash
    md "C:\git-samples"
    ```

2. Open een venster in een git-terminal zoals git bash. Gebruik de opdracht `cd` om de nieuwe map te selecteren voor het installeren van de voorbeeld-app.

    ```bash
    cd "C:\git-samples"
    ```

3. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>De code bekijken

Deze stap is optioneel. Als u wilt weten hoe de code de databaseresources maakt, kunt u de volgende codefragmenten bekijken. De fragmenten zijn allemaal afkomstig uit het bestand `uprofile.js` in de map `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Als u deze stap wilt overslaan, kunt u verdergaan naar [Uw verbindingsgegevens bijwerken](#update-your-connection-string). 

* De waarden van de gebruikersnaam en het wachtwoord zijn ingesteld met behulp van de pagina Verbindingsreeks in de Azure-portal. `path\to\cert` bevat een pad naar een X509-certificaat. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* De `client` wordt geïnitialiseerd met contactPoint-informatie. Het contactPoint wordt opgehaald uit Azure Portal.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* De `client` maakt verbinding met de Cassandra-API van Azure Cosmos DB.

    ```javascript
    client.connect(next);
    ```

* Er wordt een nieuwe keyspace gemaakt.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Er wordt een nieuwe tabel gemaakt.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Sleutel/waarde-entiteiten worden ingevoegd.

    ```javascript
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Voer een query uit om alle sleutelwaarden op te halen.

    ```javascript
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Voer een query uit om een sleutelwaarde op te halen.

    ```javascript
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Uw verbindingsreeks bijwerken

Ga nu terug naar Azure Portal om de verbindingsreeksinformatie op te halen en kopieer deze in de app. De verbindingsreeks stelt uw app in staat om te communiceren met de gehoste database.

1. Selecteer [Verbindingsreeks](https://portal.azure.com/) in de **Azure-portal**. 

    Gebruik de ![knop Kopiëren](./media/create-cassandra-nodejs/copy.png) aan de rechterkant van het scherm om de bovenste waarde (het CONTACT POINT) te kopiëren.

    ![Kopieer de waarden voor CONTACT POINT, USERNAME en PASSWORD uit Azure Portal, op de pagina Verbindingsreeks](./media/create-cassandra-nodejs/keys.png)

2. Open het `config.js`-bestand. 

3. Plak de waarde van CONTACT POINT uit de portal over `<FillMEIN>` op regel 4.

    Regel 4 moet er nu ongeveer als volgt uitzien: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. Kopieer de waarde van USERNAME uit de portal en plak deze over `<FillMEIN>` op regel 2.

    Regel 2 moet er nu ongeveer als volgt uitzien: 

    `config.username = 'cosmos-db-quickstart';`
    
5. Kopieer de waarde van PASSWORD uit de portal en plak deze over `<FillMEIN>` op regel 3.

    Regel 3 moet nu ongeveer als volgt uitzien:

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Sla het bestand `config.js` op.
    
## <a name="use-the-x509-certificate"></a>Het X509-certificaat gebruiken

1. Download het Baltimore CyberTrust Root-certificaat lokaal van [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Wijzig de naam van het bestand met de bestandsextensie `.cer`.

   Het certificaat heeft serienummer `02:00:00:b9` en SHA1-vingerafdruk `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Open `uprofile.js` en wijzig het `path\to\cert` zo dat het naar uw nieuwe certificaat verwijst.

3. Sla `uprofile.js` op.

## <a name="run-the-nodejs-app"></a>De Node.Js-app uitvoeren

1. Voer `npm install` uit in het git-terminalvenster om de vereiste npm-modules te installeren.

2. Voer `node uprofile.js` uit om de knooppunttoepassing te starten.

3. Controleer of de resultaten van de opdrachtregel aan de verwachting voldoen.

    ![De uitvoer bekijken en controleren](./media/create-cassandra-nodejs/output.png)

    Druk op Ctrl+C om de uitvoering van het programma te stoppen en het consolevenster te sluiten. 

4. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen. 

    ![De gegevens bekijken in Data Explorer](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Cosmos DB-account maakt, een container maakt met Data Explorer en een app uitvoert. Nu kunt u aanvullende gegevens in uw Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)


