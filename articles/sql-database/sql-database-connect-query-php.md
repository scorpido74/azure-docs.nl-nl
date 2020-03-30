---
title: PHP gebruiken om te vragen
description: PHP gebruiken om een programma te maken dat is verbonden met een Azure SQL-database, en een query op deze database uitvoeren met behulp van T-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: ae119dd23da670f16c0239b14119519c431e6326
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827003"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Snelstart: PHP gebruiken om een query uit te voeren voor een Azure SQL-database

In dit artikel ziet u hoe u [PHP](https://php.net/manual/en/intro-whatis.php) gebruikt om verbinding te maken met een Azure SQL-database. Vervolgens kunt u T-SQL-instructies gebruiken om een query uit te voeren voor de gegevens.

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan de volgende vereisten voldoet als u dit voorbeeld wilt uitvoeren:

- Een Azure SQL-database. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

  || Individuele database | Beheerd exemplaar |
  |:--- |:--- |:---|
  | Maken| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)| [Connectiviteit vanaf een VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connectiviteit vanaf locatie](sql-database-managed-instance-configure-p2s.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](sql-database-managed-instance-get-started-restore.md)
  |||Adventure Works uit [BACPAC-bestand](sql-database-import.md) herstellen of importeren van [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een beheerd exemplaar moet u de Adventure Works-database importeren in een exemplaardatabase of de scripts in dit artikel wijzigen zodat deze de Wide World Importers-database gebruiken.

- U moet aan PHP verwante software hebben geïnstalleerd voor het besturingssysteem:

  - **MacOS**: installeer PHP, het ODBC-stuurprogramma en installeer vervolgens het PHP-stuurprogramma voor SQL Server. Zie [stap 1, 2 en 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**: installeer PHP, het ODBC-stuurprogramma en installeer vervolgens het PHP-stuurprogramma voor SQL Server. Zie [stap 1, 2 en 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**: installeer PHP voor IIS Express en Chocolatey, en installeer vervolgens het ODBC-stuurprogramma en SQLCMD. Zie [Stap 1.2 en 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-database** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een individuele database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="add-code-to-query-database"></a>Code toevoegen om een query uit te voeren op de database

1. Maak een nieuw bestand in uw favoriete teksteditor *sqltest.php*.  

1. Vervang de inhoud ervan door de volgende code. Voeg vervolgens de juiste waarden toe voor uw server, database, gebruiker en wachtwoord.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de app uit vanaf de opdrachtprompt.

   ```bash
   php sqltest.php
   ```

1. Controleer de bovenste 20 rijen die worden geretourneerd en sluit het app-venster.

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste Azure SQL-database ontwerpen](sql-database-design-first-database.md)

- [Microsoft PHP-stuurprogramma's voor SQL Server](https://github.com/Microsoft/msphpsql/)

- [Problemen melden of vragen stellen](https://github.com/Microsoft/msphpsql/issues)

- [Voorbeeld logica voor opnieuw proberen: flexibel verbinding maken met SQL via PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
