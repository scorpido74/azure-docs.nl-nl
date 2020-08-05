---
title: Verbindings bibliotheken-Azure Database for MySQL
description: In dit artikel vindt u een lijst met alle bibliotheken of stuur Programma's die door client Programma's kunnen worden gebruikt wanneer verbinding wordt gemaakt met Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/3/2020
ms.openlocfilehash: 9d4d862389a607b18ee5f2440069fa2eb6cf7bc4
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553033"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Verbindings bibliotheken voor Azure Database for MySQL
In dit artikel vindt u een lijst met alle bibliotheken of stuur Programma's die door client Programma's kunnen worden gebruikt wanneer verbinding wordt gemaakt met Azure Database for MySQL.

## <a name="client-interfaces"></a>Client interfaces
MySQL biedt standaard database stuur programma connectiviteit voor het gebruik van MySQL met toepassingen en hulpprogram ma's die compatibel zijn met de industrie normen ODBC en JDBC. Elk systeem dat werkt met ODBC of JDBC kan gebruikmaken van MySQL.

| **Taal** | **Platform** | **Aanvullende resource** | **Downloaden** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL native stuur programma voor PHP-mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Downloaden](https://secure.php.net/downloads.php) |
| ODBC | Windows-, Linux-, Mac OS X-en UNIX-platforms | [MySQL-Connector/ODBC-ontwikkelaars handleiding](https://dev.mysql.com/doc/connector-odbc/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL-Connector/NET-ontwikkelaars handleiding](https://dev.mysql.com/doc/connector-net/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Onafhankelijk van platform | [Hand leiding voor MySQL-Connector/J 5,1-ontwikkel aars](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Downloaden](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Hand leiding voor MySQL-Connector/python-ontwikkel aars](https://dev.mysql.com/doc/connector-python/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Naslag Gids voor MySQL-Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Hand leiding voor MySQL-Connector/C-ontwikkel aars](https://dev.mysql.com/doc/c-api/8.0/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, Mac OS X-en UNIX-platforms | [DBD:: MySQL](https://metacpan.org/pod/DBD::mysql) | [Downloaden](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Volgende stappen
Lees deze Snelstartgids voor het maken van verbinding met en het opvragen van Azure Database for MySQL met behulp van de taal van uw keuze:

- [PHP](./connect-php.md)
- [Java](./connect-java.md)
- [.NET (C#)](./connect-csharp.md)
- [Python](./connect-python.md)
- [Node.JS](./connect-nodejs.md)
- [Ruby](./connect-ruby.md)
- [C++](connect-cpp.md)
- [Go](./connect-go.md)
