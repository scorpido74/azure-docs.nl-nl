---
title: Node. js gebruiken om verbinding te maken met Azure Database for PostgreSQL-één server
description: Deze Snelstartgids bevat een node. js-code voorbeeld dat u kunt gebruiken om verbinding te maken en gegevens op te vragen van Azure Database for PostgreSQL-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: seo-javascript-september2019
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 072e2fca4d7d0c90e9e4e66b9ba2b63ef45723db
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719943"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Quickstart: Node. js gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for PostgreSQL-één server
In deze Quick Start ziet u hoe u met behulp van een [Node.js](https://nodejs.org/)-toepassing verbinding maakt met een Azure Database for PostgreSQL. U ziet hier hoe u SQL-instructies gebruikt om gegevens in de database op te vragen, in te voegen, bij te werken en te verwijderen. In de stappen van dit artikel wordt ervan uitgegaan dat u bekend bent met het ontwikkelen met behulp van Node.js en geen ervaring hebt met het werken met Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten
In deze snelstartgids worden de resources die in een van deze handleidingen zijn gemaakt, als uitgangspunt gebruikt:
- [Database maken - Portal](quickstart-create-server-database-portal.md)
- [Database maken - CLI](quickstart-create-server-database-azure-cli.md)

U moet ook het volgende doen:
- [Node.js](https://nodejs.org) installeren

## <a name="install-pg-client"></a>pg-client installeren
Installeer [pg](https://www.npmjs.com/package/pg). Dit is een PostgreSQL-client voor Node.js.

Voer de Node Package Manager (NPM) voor JavaScript uit vanaf de opdrachtregel om de pg-client te installeren.
```bash
npm install pg
```

Controleer de installatie door de geïnstalleerde pakketten weer te geven.
```bash
npm list
```

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen
Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de Azure Database voor PostgreSQL. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in het menu aan de linkerkant in Azure Portal **alle resources**en zoek vervolgens naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Selecteer de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
 ![Servernaam Azure Database for PostgreSQL](./media/connect-nodejs/1-connection-string.png)

## <a name="running-the-javascript-code-in-nodejs"></a>De JavaScript-code in Node.js uitvoeren
U kunt Node.js starten vanuit de Bash-shell, Terminal of opdrachtprompt van Windows door `node` in te voeren. Voer vervolgens de JavaScript-voorbeeldcode interactief uit door deze te kopiëren en in de prompt te plakken. U kunt de JavaScript-code ook in een tekstbestand opslaan en `node filename.js` starten met de bestandsnaam als parameter om de code uit te voeren.

## <a name="connect-create-table-and-insert-data"></a>Verbinden, tabel maken en gegevens invoegen
Gebruik de volgende code om verbinding te maken en de gegevens te laden met de SQL-instructies **CREATE TABLE** EN **INSERT INTO**.
Het object [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) wordt gebruikt voor interactie met de PostgreSQL-server. De functie [pg.Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wordt gebruikt om verbinding met de server te maken. De functie [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wordt gebruikt om de SQL-query uit te voeren op de PostgreSQL-database. 

Vervang de parameters host, dbname, user en password door de waarden die u hebt opgegeven tijdens het maken van de server en database.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Gegevens lezen
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met de SQL-instructie **SELECT**. Het object [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) wordt gebruikt voor interactie met de PostgreSQL-server. De functie [pg.Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wordt gebruikt om verbinding met de server te maken. De functie [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wordt gebruikt om de SQL-query uit te voeren op de PostgreSQL-database. 

Vervang de parameters host, dbname, user en password door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Gegevens bijwerken
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met de SQL-instructie **UPDATE**. Het object [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) wordt gebruikt voor interactie met de PostgreSQL-server. De functie [pg.Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wordt gebruikt om verbinding met de server te maken. De functie [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wordt gebruikt om de SQL-query uit te voeren op de PostgreSQL-database. 

Vervang de parameters host, dbname, user en password door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Gegevens verwijderen
Gebruik de volgende code om verbinding te maken en de gegevens te lezen met de SQL-instructie **DELETE**. Het object [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) wordt gebruikt voor interactie met de PostgreSQL-server. De functie [pg.Client.Connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) wordt gebruikt om verbinding met de server te maken. De functie [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) wordt gebruikt om de SQL-query uit te voeren op de PostgreSQL-database. 

Vervang de parameters host, dbname, user en password door de waarden die u hebt opgegeven tijdens het maken van de server en database. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van Exporteren en importeren](./howto-migrate-using-export-and-import.md)
