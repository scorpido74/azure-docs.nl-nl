---
title: Een database migreren - Azure Database voor PostgreSQL - Single Server
description: Beschrijft hoe een PostgreSQL-database in een scriptbestand worden geëxtraheerd en de gegevens in de doeldatabase uit dat bestand worden geïmporteerd.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: f7cf5d245383b8a58f03e2e3610750866a2f4b5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770200"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Uw PostgreSQL-database migreren met exporteren en importeren
U [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) gebruiken om een PostgreSQL-database in een scriptbestand en [psql](https://www.postgresql.org/docs/current/static/app-psql.html) te extraheren om de gegevens in de doeldatabase uit dat bestand te importeren.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Een [Azure Database voor PostgreSQL-server](quickstart-create-server-database-portal.md) met firewallregels om toegang en database eronder toe te staan.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) installatie van opdrachtregelhulpprogramma's
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) command-line utility geïnstalleerd

Volg deze stappen om uw PostgreSQL-database te exporteren en te importeren.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Een scriptbestand maken met pg_dump dat de te laden gegevens bevat
Voer de volgende opdracht uit in uw bestaande omgeving om uw bestaande PostgreSQL-database on-premises of in een VM naar een sql-scriptbestand uit te voeren:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Als u bijvoorbeeld een lokale server en een database met de naam **testdb** erin hebt:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>De gegevens importeren op doelAzure Database voor PostgreSQL
U de psql-opdrachtregel en de parameter --dbname (-d) gebruiken om de gegevens te importeren in de Azure Database voor PostgreSQL-server en gegevens uit het sql-bestand te laden.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
In dit voorbeeld wordt psql-hulpprogramma en een scriptbestand met de naam **testdb.sql** van vorige stap gebruikt om gegevens te importeren in de database **mypgsqldb** op de doelserver **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Volgende stappen
- Als u een PostgreSQL-database wilt migreren met dumpen en herstellen, [raadpleegt u Uw PostgreSQL-database migreren met dumpen en herstellen.](howto-migrate-using-dump-and-restore.md)
- Zie de [databasemigratiehandleiding](https://aka.ms/datamigration)voor meer informatie over het migreren van databases naar Azure Database voor PostgreSQL. 
