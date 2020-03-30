---
title: Compatibiliteit met stuurprogramma's en hulpprogramma's - Azure-database voor MariaDB
description: In dit artikel worden de MariaDB-stuurprogramma's en beheertools beschreven die compatibel zijn met Azure Database voor MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a0cee198f028fd90e04dac15e98d7cd33aee9201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532345"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB-stuurprogramma's en beheertools die compatibel zijn met Azure Database voor MariaDB

In dit artikel worden de stuurprogramma's en beheerhulpprogramma's beschreven die compatibel zijn met Azure Database voor MariaDB.

## <a name="mariadb-drivers"></a>MariaDB-stuurprogramma's

Azure Database voor MariaDB maakt gebruik van de community-editie van MariaDB-server. Daarom is het compatibel met een breed scala aan programmeertalen en stuurprogramma's. MariaDB's API en protocol zijn compatibel met die gebruikt door MySQL. Dit betekent dat connectors die werken met MySQL ook met MariaDB moeten werken.

Het doel is om de drie meest recente versies MariaDB drivers te ondersteunen, en inspanningen met auteurs uit de open source gemeenschap om voortdurend verbeteren van de functionaliteit en bruikbaarheid van MariaDB drivers blijven. In de volgende tabel vindt u een lijst met stuurprogramma's die zijn getest en compatibel zijn bevonden met Azure Database voor MariaDB 10.2:

**Stuurprogramma** | **Links** | **Compatibele versies** | **Incompatibele versies** | **Opmerkingen**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | Voor PHP 7.0-verbinding met SSL MySQLi voegt u MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT toe in de verbindingstekenreeks. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> BOB set: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` optie om vals.
.NET | [MySqlConnector op GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Installatiepakket van Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0,27 en daarna | 0.26.5 en vóór |
MySQL-connector/NET | [MySQL-connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Een coderingsbug kan ervoor zorgen dat verbindingen mislukken op sommige niet-UTF8 Windows-systemen.
Node.js |  [MySQLjs op GitHub](https://github.com/mysqljs/mysql/) <br> Installatiepakket van NPM:<br> Uitvoeren `npm install mysql` vanaf NPM | 2.15 | 2.14.1 en vóór
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 en vóór | Gebruik `allowNativePasswords=true` in de verbindingstekenreeks voor versie 1.3. Versie 1.4 bevat `allowNativePasswords=true` een oplossing en is niet meer nodig.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 en vóór |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 en vóór |

## <a name="management-tools"></a>Beheerhulpprogramma's

Het compatibiliteitsvoordeel strekt zich ook uit tot databasebeheertools. Uw bestaande hulpprogramma's moeten blijven werken met Azure Database voor MariaDB, zolang de databasemanipulatie binnen de grenzen van gebruikersmachtigingen werkt. Drie veelgebruikte databasebeheertools die zijn getest en compatibel zijn bevonden met Azure Database voor MariaDB 10.2, worden in de volgende tabel weergegeven:

| | **MySQL Workbench 6.x en omhoog** | **Navicat 12** | **PHPMyAdmin 4.x en meer**
---|---|---|---
Maken, bijwerken, lezen, schrijven, verwijderen | X | X | X
SSL-verbinding | X | X | X
Sql-query automatisch aanvullen | X | X |
Gegevens importeren en exporteren | X | X | X
Exporteren naar meerdere indelingen | X | X | X
Back-up en herstel |  | X |
Serverparameters weergeven | X | X | X
Clientverbindingen weergeven | X | X | X

## <a name="next-steps"></a>Volgende stappen

- [Verbindingsproblemen met Azure Database for MariaDB oplossen](howto-troubleshoot-common-connection-issues.md)