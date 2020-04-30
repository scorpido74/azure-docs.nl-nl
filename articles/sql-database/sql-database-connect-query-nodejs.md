---
title: Node. js gebruiken om een query uit te zoeken op een Data Base
description: Node.js gebruiken om een programma te maken dat is verbonden met een Azure SQL-database, en een query voor deze database uitvoeren met behulp van T-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76768609"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Snelstart: Node.js gebruiken om een query uit te voeren voor een Azure SQL-database

In deze Quick Start gebruikt u node. js om verbinding te maken met een Azure-SQL database en kunt u T-SQL-instructies gebruiken om gegevens op te vragen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een [Azure-SQL database](sql-database-single-database-get-started.md)
- [Node. js](https://nodejs.org)-gerelateerde software

  # <a name="macos"></a>[macOS](#tab/macos)

  Installeer homebrew en node. js, installeer het ODBC-stuur programma en SQLCMD met behulp van de stappen **1,2** en **1,3** in [node. js-apps maken met behulp van SQL Server op macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installeer node. js, installeer het ODBC-stuur programma en SQLCMD met behulp van de stappen **1,2** en **1,3** in [node. js-apps maken met behulp van SQL Server op Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installeer chocolade en node. js, installeer het ODBC-stuur programma en SQLCMD met behulp van de stappen **1,2** en **1,3** in [node. js-apps maken met behulp van SQL Server in Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven om de **Adventure Works** -data base te gebruiken.

> [!NOTE]
> U kunt desgewenst kiezen voor het gebruik van een Azure SQL Managed instance.
>
> Als u de [Azure Portal](sql-database-managed-instance-get-started.md), [Power shell](scripts/sql-database-create-configure-managed-instance-powershell.md)of [cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)wilt maken en configureren, gebruikt u de [on-site-](sql-database-managed-instance-configure-p2s.md) of [VM](sql-database-managed-instance-configure-vm.md) -verbinding instellen.
>
> Als u gegevens wilt laden, raadpleegt u [Restore with BACPAC](sql-database-import.md) with the [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) file of raadpleegt u [de Data Base voor Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Ga naar de pagina **SQL-data bases** of **SQL Managed instances** .

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**. 

## <a name="create-the-project"></a>Het project maken

Open een opdrachtprompt en maak een map met de naam *sqltest*. Open de map die u hebt gemaakt en voer de volgende opdracht uit:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Code toevoegen om een query uit te voeren op de database

1. Maak in uw favoriete teksteditor een nieuw bestand *sqltest.js*.

1. Vervang de inhoud ervan door de volgende code. Voeg vervolgens de juiste waarden toe voor uw server, database, gebruiker en wachtwoord.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> In het codevoorbeeld wordt de voorbeelddatabase **AdventureWorksLT** voor Azure SQL gebruikt.

## <a name="run-the-code"></a>De code uitvoeren

1. Voer het programma uit bij de opdrachtprompt.

    ```bash
    node sqltest.js
    ```

1. Controleer de bovenste 20 rijen die worden geretourneerd en sluit het toepassingsvenster.

## <a name="next-steps"></a>Volgende stappen

- [Microsoft-stuurprogramma Node.js voor SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Verbinding maken en query's uitvoeren in Windows/Linux/macOS met [.NET core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) of [SSMS](sql-database-connect-query-ssms.md) (alleen Windows)

- [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel](/dotnet/core/tutorials/using-with-xplat-cli)

- Uw eerste Azure SQL-database ontwerpen met [.NET](sql-database-design-first-database-csharp.md) of [SSMS](sql-database-design-first-database.md)
