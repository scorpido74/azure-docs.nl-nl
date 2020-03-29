---
title: Verbindingsbibliotheken - Azure Database voor PostgreSQL - Single Server
description: In dit artikel worden verschillende bibliotheken en stuurprogramma's beschreven die u gebruiken bij het coderen van toepassingen om verbinding te maken en Azure Database voor PostgreSQL - Single Server op te vragen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768891"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Verbindingsbibliotheken voor Azure Database voor PostgreSQL - Single Server
In dit artikel worden bibliotheken en stuurprogramma's weergegeven die ontwikkelaars kunnen gebruiken om toepassingen te ontwikkelen om verbinding te maken met En azure database voor PostgreSQL op te vragen.

## <a name="client-interfaces"></a>Clientinterfaces
De meeste taalclientbibliotheken die worden gebruikt om verbinding te maken met de PostgreSQL-server zijn externe projecten en worden onafhankelijk gedistribueerd. De vermelde bibliotheken worden ondersteund op de Windows-, Linux- en Mac-platforms voor verbinding maken met Azure Database voor PostgreSQL. Verschillende voorbeelden van snelstart worden weergegeven in de sectie Volgende stappen.

| **Language** | **Clientinterface** | **Aanvullende informatie** | **Downloaden** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [Psycopg](http://initd.org/psycopg/) | DB API 2.0-compatibel | [Downloaden](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Database-extensie | [Installeren](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm-pakket](https://www.npmjs.com/package/pg) | Pure JavaScript-niet-blokkerende client | [Installeren](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Type 4 JDBC-stuurprogramma | [Downloaden](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby-interface | [Downloaden](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Aan de slag | [Pakket pq](https://godoc.org/github.com/lib/pq) | Pure Go postgres driver | [Installeren](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | ADO.NET gegevensprovider | [Downloaden](https://www.microsoft.com/net/) |
| ODBC | [Psqlodbc](https://odbc.postgresql.org/) | ODBC-stuurprogramma | [Downloaden](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [Libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Taalinterface primair C | Inbegrepen |
| C++ | [Libpqxx](http://pqxx.org/) | C++-interface in nieuwe stijl | [Downloaden](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Volgende stappen
Lees deze snel starts over het maken van verbinding met Azure Database voor PostgreSQL en query met behulp van uw taal naar keuze:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
