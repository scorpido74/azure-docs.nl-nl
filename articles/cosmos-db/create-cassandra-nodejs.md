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
ms.openlocfilehash: ffc2681e487a51ce630d9433d6ded86961b5276c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210344"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Quick Start: een Cassandra-app bouwen met de node. js-SDK en Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In deze Quick Start maakt u een Azure Cosmos DB Cassandra-API-account en gebruikt u een Cassandra node. js-app die is gekloond van GitHub om een Cassandra-data base en-container te maken. Azure Cosmos DB is een database service met meerdere modellen waarmee u snel documenten, tabellen, sleutel waarden en grafische data bases met globale distributie en mogelijkheden voor horizontale schaal kunt maken en er query's op uitvoert.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/) zonder Azure-abonnement, zonder kosten en zonder verplichtingen.

U hebt verder nodig:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) versie v0.10.29 of hoger
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
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Voer een query uit om alle sleutelwaarden op te halen.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Voer een query uit om een sleutelwaarde op te halen.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
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

1. Selecteer in uw Azure Cosmos DB-account [](https://portal.azure.com/)in de Azure Portal **verbindings reeks**. 

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

> [!NOTE]
> Als er een probleem is met het certificaat in de latere stappen en wordt uitgevoerd op een Windows-computer, moet u ervoor zorgen dat u het proces voor het correct omzetten van een CRT-bestand in de onderstaande micro soft. CER-indeling hebt gevolgd.
> 
> Dubbel klik op het. crt-bestand om het te openen in de certificaat weergave. 
>
> ![De uitvoer bekijken en controleren](./media/create-cassandra-nodejs/crtcer1.gif)
>
> Klik op volgende in de wizard Certificaat. Selecteer base-64 Encoded X. 509 (. CER) en vervolgens op volgende.
>
> ![De uitvoer bekijken en controleren](./media/create-cassandra-nodejs/crtcer2.gif)
>
> Selecteer Bladeren (om een doel locatie te vinden) en typ een bestands naam.
> Selecteer volgende.
>
> U hebt nu een juist opgemaakt CER-bestand. Zorg ervoor dat het pad in `uprofile.js` naar dit bestand verwijst.

## <a name="run-the-nodejs-app"></a>De Node.Js-app uitvoeren

1. Controleer in het git-Terminal venster of u zich in de voor beeld-Directory bevindt die u eerder hebt gekloond:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Voer `npm install` uit om de vereiste NPM-modules te installeren.

3. Voer `node uprofile.js` uit om de knooppunttoepassing te starten.

4. Controleer of de resultaten van de opdrachtregel aan de verwachting voldoen.

    ![De uitvoer bekijken en controleren](./media/create-cassandra-nodejs/output.png)

    Druk op CTRL + C om de uitvoering van het programma te stoppen en het console venster te sluiten. 

5. Open **Data Explorer** in de Azure-portal om deze nieuwe gegevens te bekijken, te wijzigen, een query erop uit te voeren of er iets anders mee te doen. 

    ![De gegevens bekijken in Data Explorer](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>SLA’s bekijken in Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Cosmos DB-account maakt met Cassandra-API en een Cassandra node. js-app uitvoert waarmee een Cassandra-data base en-container wordt gemaakt. U kunt nu aanvullende gegevens in uw Azure Cosmos DB-account importeren. 

> [!div class="nextstepaction"]
> [Cassandra-gegevens importeren in Azure Cosmos DB](cassandra-import-data.md)