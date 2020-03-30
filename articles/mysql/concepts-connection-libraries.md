---
title: Verbindingsbibliotheken - Azure Database voor MySQL
description: In dit artikel vindt u elke bibliotheek of stuurprogramma die clientprogramma's kunnen gebruiken wanneer u verbinding maakt met Azure Database voor MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5f83f937b8d9ec50ec7dc6ec781bff1f435eb45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537190"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Verbindingsbibliotheken voor Azure Database voor MySQL
In dit artikel vindt u elke bibliotheek of stuurprogramma die clientprogramma's kunnen gebruiken wanneer u verbinding maakt met Azure Database voor MySQL.

## <a name="client-interfaces"></a>Clientinterfaces
MySQL biedt standaard database driver connectiviteit voor het gebruik van MySQL met applicaties en tools die compatibel zijn met industriestandaarden ODBC en JDBC. Elk systeem dat werkt met ODBC of JDBC kan MySQL gebruiken.

| **Language** | **Platform** | **Aanvullende bron** | **Downloaden** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [MySQL native driver voor PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Downloaden](https://secure.php.net/downloads.php) |
| ODBC | Windows-, Linux-, Mac OS X- en Unix-platforms | [MySQL-connector/ODBC-ontwikkelaarshandleiding](https://dev.mysql.com/doc/connector-odbc/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL-connector/net-ontwikkelaarshandleiding](https://dev.mysql.com/doc/connector-net/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Platform onafhankelijk | [MySQL-connector/J 5.1-handleiding voor ontwikkelaars](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Downloaden](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [MySQL-connector/Python-ontwikkelaarshandleiding](https://dev.mysql.com/doc/connector-python/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [MySQL-connector/C++ handleiding voor ontwikkelaars](https://dev.mysql.com/doc/connector-cpp/en/) | [Downloaden](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [MySQL-connector/C-ontwikkelaarshandleiding](https://dev.mysql.com/doc/refman/8.0/en/c-api.html) | [Downloaden](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows-, Linux-, Mac OS X- en Unix-platforms | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Downloaden](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Volgende stappen
Lees deze snel starts over het maken van verbinding met Azure Database voor MySQL en query met behulp van uw taal naar keuze:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

