---
title: Compatibiliteit met stuurprogramma's en hulpprogramma's - Azure Database voor MySQL
description: In dit artikel worden de MySQL-stuurprogramma's en beheerhulpprogramma's beschreven die compatibel zijn met Azure Database voor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e8917a0a5678c4c6b72352a0d4c1523bfea3c96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537207"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-stuurprogramma's en beheerhulpprogramma's die compatibel zijn met Azure Database voor MySQL
In dit artikel worden de stuurprogramma's en beheerhulpprogramma's beschreven die compatibel zijn met Azure Database voor MySQL.

## <a name="mysql-drivers"></a>MySQL-stuurprogramma's
Azure Database voor MySQL maakt gebruik van 's werelds populairste community-editie van MySQL-database. Daarom is het compatibel met een breed scala aan programmeertalen en stuurprogramma's. Het doel is om de drie meest recente versies MySQL drivers te ondersteunen, en inspanningen met auteurs uit de open source gemeenschap om voortdurend verbeteren van de functionaliteit en bruikbaarheid van MySQL drivers blijven. In de volgende tabel vindt u een lijst met stuurprogramma's die zijn getest en compatibel zijn bevonden met Azure Database voor MySQL 5.6 en 5.7:

| **Programmeertaal** | **Stuurprogramma** | **Links** | **Compatibele versies** | **Incompatibele versies** | **Opmerkingen** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Voor PHP 7.0-verbinding met SSL MySQLi voegt u MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT toe in de verbindingstekenreeks. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> BOB set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` optie om vals.|
| .NET | Async MySQL Connector voor .NET | https://github.com/mysql-net/MySqlConnector <br> [Installatiepakket van Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 en daarna | 0.26.5 en vóór | |
| .NET | MySQL-connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3 ,7.0 ,8.0 |  | Een coderingsbug kan ervoor zorgen dat verbindingen mislukken op sommige niet-UTF8 Windows-systemen. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Installatiepakket van NPM:<br> Uitvoeren `npm install mysql` vanaf NPM | 2.15 | 2.14.1 en vóór | |
| Node.js | node-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4+ | | |
| Aan de slag | Go MySQL-stuurprogramma | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 en vóór | Gebruik `allowNativePasswords=true` in de verbindingstekenreeks voor versie 1.3. Versie 1.4 bevat `allowNativePasswords=true` een oplossing en is niet meer nodig. |
| Python | MySQL-connector/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, gebruik 8.0.16+ met MySQL 8.0  | 1.2.2 en vóór | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0,7,11, 0,8,0, 0,8,1, 0,9,3+ | 0.9.0 - 0.9.2 (regressie in web2py) | |
| Java | MariaDB-connector/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 en vóór | | 
| Java | MySQL-connector/J | https://github.com/mysql/mysql-connector-j | 5.1.21+, gebruik 8.0.17+ met MySQL 8.0 | 5.1.20 en lager | |
| C | MySQL Connector/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2+ | | |
| C | MySQL Connector/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29+ | | |
| C++ | MySQL-connector/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9+ | 1.1.3 en lager | | 
| C++ | MySQL++| https://tangentsoft.net/mysql++ | 3.2.3+ | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0,4,10+ | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0,10.16+ | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0,7,2+ | | |
| Swift | damp/mysql | https://github.com/vapor/mysql-kit | 2,0,1+ | | |

## <a name="management-tools"></a>Beheerhulpprogramma's
Het compatibiliteitsvoordeel strekt zich ook uit tot databasebeheertools. Uw bestaande hulpprogramma's moeten blijven werken met Azure Database voor MySQL, zolang de databasemanipulatie binnen de grenzen van gebruikersmachtigingen werkt. Drie veelgebruikte databasebeheertools die zijn getest en compatibel zijn bevonden met Azure Database voor MySQL 5.6 en 5.7, worden in de volgende tabel weergegeven:

|                                     | **MySQL Workbench 6.x en omhoog** | **Navicat 12** | **PHPMyAdmin 4.x en meer** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Maken, bijwerken, lezen, schrijven, verwijderen | X | X | X |
| SSL-verbinding | X | X | X |
| Sql-query automatisch aanvullen | X | X |  |
| Gegevens importeren en exporteren | X | X | X | 
| Exporteren naar meerdere indelingen | X | X | X |
| Back-up en herstel |  | X |  |
| Serverparameters weergeven | X | X | X |
| Clientverbindingen weergeven | X | X | X |

## <a name="next-steps"></a>Volgende stappen

- [Verbindingsproblemen met Azure Databases for MySQL oplossen](howto-troubleshoot-common-connection-issues.md)