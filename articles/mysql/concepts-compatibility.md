---
title: Compatibiliteit met stuur Programma's en beheer Programma's Azure Database for MySQL
description: In dit artikel worden de MySQL-Stuur Programma's en-beheer hulpprogramma's beschreven die compatibel zijn met Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/06/2019
ms.openlocfilehash: 916c02c30f6d54aef44459775a7a437fe96a4ff9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720134"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>MySQL-Stuur Programma's en-beheer hulpprogramma's die compatibel zijn met Azure Database for MySQL
In dit artikel worden de Stuur Programma's en beheer Programma's beschreven die compatibel zijn met Azure Database for MySQL.

## <a name="mysql-drivers"></a>MySQL-Stuur Programma's
Azure Database for MySQL maakt gebruik van de populairste Community-editie van de wereld van de MySQL-data base. Daarom is het compatibel met een groot aantal programmeer talen en-stuur Programma's. Het doel is om de drie meest recente versies MySQL-Stuur Programma's te ondersteunen en inspanningen te doen met auteurs van de open source-community om de functionaliteit en de bruikbaarheid van MySQL-Stuur Programma's voortdurend te verbeteren. In de volgende tabel vindt u een lijst met stuur Programma's die zijn getest en die compatibel zijn met Azure Database for MySQL 5,6 en 5,7.

| **Programmeer taal** | **Stuurprogrammamodel** | **Koppelen** | **Compatibele versies** | **Incompatibele versies** | **Opmerkingen** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5,5, 5,6, 7. x | 5,3 | Voeg MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT in het connection string toe voor PHP 7,0-verbinding met SSL-MySQLi. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> Bob ingesteld: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` optie op ONWAAR.|
| .NET | Asynchrone MySQL-Connector voor .NET | https://github.com/mysql-net/MySqlConnector <br> [Installatie pakket van Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 en na | 0.26.5 en vóór | |
| .NET | MySQL-Connector/NET | https://github.com/mysql/mysql-connector-net | 8,0, 7,0, 6,10 |  | Een coderings fout kan ertoe leiden dat verbindingen mislukken op sommige niet-UTF8 Windows-systemen. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Installatie pakket van NPM:<br> `npm install mysql` uitvoeren vanuit NPM | 2,15 | 2.14.1 en vóór | |
| Node.js | knoop punt-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Ga naar MySQL-stuur programma | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1,2 en vóór | Gebruik `allowNativePasswords=true` in de connection string voor versie 1,3. Versie 1,4 bevat een oplossings-en `allowNativePasswords=true` is niet meer vereist. |
| Python | MySQL-Connector/python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, 8.0.16 + gebruiken met MySQL 8,0  | 1.2.2 en voor | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regressie in web2py) | |
| Java | MariaDB-Connector/J | https://downloads.mariadb.org/connector-java/ | 2,1, 2,0, 1,6 | 1.5.5 en vóór | | 
| Java | MySQL-Connector/J | https://github.com/mysql/mysql-connector-j | 5.1.20 +, 8.0.17 + gebruiken met MySQL 8,0 | 5.1.9 en lager | |
| C | MySQL-Connector/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL-Connector/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL-Connector/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 en lager | | 
| C++ | MySQL + +| https://tangentsoft.net/mysql++ | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL-Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | Vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Beheerhulpprogramma's
Het compatibiliteits voordeel kan ook worden uitgebreid naar beheer hulpprogramma's voor data bases. De bestaande hulpprogram ma's moeten blijven werken met Azure Database for MySQL, zolang de data base is gemanipuleerd binnen de gebruikers machtigingen. De volgende tabel bevat drie algemene hulpprogram ma's voor database beheer die zijn getest en die compatibel zijn met Azure Database for MySQL 5,6 en 5,7.

|                                     | **MySQL Workbench 6. x en Maxi maal** | **Navicat 12** | **PHPMyAdmin 4. x en Maxi maal** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| Maken, bijwerken, lezen, schrijven, verwijderen | X | X | X |
| SSL-verbinding | X | X | X |
| SQL-query automatisch aanvullen | X | X |  |
| Gegevens importeren en exporteren | X | X | X | 
| Exporteren naar meerdere indelingen | X | X | X |
| Back-up en herstel |  | X |  |
| Server parameters weer geven | X | X | X |
| Client verbindingen weer geven | X | X | X |

## <a name="next-steps"></a>Volgende stappen

- [Verbindingsproblemen met Azure Databases for MySQL oplossen](howto-troubleshoot-common-connection-issues.md)