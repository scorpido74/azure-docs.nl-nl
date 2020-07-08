---
title: Verbindings bibliotheken-Azure Database for PostgreSQL-één server
description: In dit artikel worden verschillende bibliotheken en stuur Programma's beschreven die u kunt gebruiken bij het coderen van toepassingen om verbinding te maken en query's uit te Azure Database for PostgreSQL-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768891"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Verbindings bibliotheken voor Azure Database for PostgreSQL-één server
Dit artikel bevat een lijst met bibliotheken en stuur Programma's die ontwikkel aars kunnen gebruiken voor het ontwikkelen van toepassingen om verbinding te maken met en query's uit te Azure Database for PostgreSQL.

## <a name="client-interfaces"></a>Client interfaces
De meeste taal-client bibliotheken die worden gebruikt om verbinding te maken met de PostgreSQL-server, zijn externe projecten en worden onafhankelijk gedistribueerd. De vermelde bibliotheken worden ondersteund op de Windows-, Linux-en Mac-platforms om verbinding te maken met Azure Database for PostgreSQL. In het gedeelte volgende stappen vindt u enkele voor beelden van Quick Start.

| **Taal** | **Client interface** | **Aanvullende informatie** | **Downloaden** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2,0-compatibel | [Downloaden](http://initd.org/psycopg/download/) |
| PHP | [PHP-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Data base-extensie | [Installeren](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [PG NPM-pakket](https://www.npmjs.com/package/pg) | Zuivere Java script-client die niet wordt geblokkeerd | [Installeren](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Type 4 JDBC-stuur programma | [Downloaden](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pagina Gem](https://deveiate.org/code/pg/) | Ruby-interface | [Downloaden](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Aan de slag | [Pakket pq](https://godoc.org/github.com/lib/pq) | Puur go post gres-stuur programma | [Installeren](https://github.com/lib/pq/blob/master/README.md) |
| C \# /.net | [Npgsql](https://www.npgsql.org/) | ADO.NET-gegevens provider | [Downloaden](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC-stuurprogramma | [Downloaden](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Primaire C-taal interface | Inbegrepen |
| C++ | [libpqxx](http://pqxx.org/) | Interface nieuw-stijl C++ | [Downloaden](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Volgende stappen
Lees deze Snelstartgids voor het maken van verbinding met en het opvragen van Azure Database for PostgreSQL met behulp van de taal van uw keuze:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [Php](./connect-php.md)  |  [.net (C#)](./connect-csharp.md)  |  [Ga](./connect-go.md)
