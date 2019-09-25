---
title: Een Data Base migreren met importeren en exporteren in Azure Database for PostgreSQL-één server
description: Hierin wordt beschreven hoe u een PostgreSQL-data base uitpakt in een script bestand en de gegevens uit dat bestand in de doel database importeert.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 0803f56312ca9b650987c2203c4271cff21df9f8
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260365"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Uw PostgreSQL-data base migreren met behulp van exporteren en importeren
U kunt [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) gebruiken om een postgresql-Data Base op te halen in een script bestand en [psql](https://www.postgresql.org/docs/current/static/app-psql.html) om de gegevens uit dat bestand in de doel database te importeren.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Een [Azure database for postgresql server](quickstart-create-server-database-portal.md) met firewall regels om toegang en data base daaronder toe te staan.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) -opdracht regel programma geïnstalleerd
- [psql](https://www.postgresql.org/docs/current/static/app-psql.html) -opdracht regel programma geïnstalleerd

Volg deze stappen voor het exporteren en importeren van uw PostgreSQL-data base.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Een script bestand maken met behulp van pg_dump dat de gegevens bevat die moeten worden geladen
Als u uw bestaande PostgreSQL-data base on-premises of in een virtuele machine naar een SQL-script bestand wilt exporteren, voert u de volgende opdracht uit in uw bestaande omgeving:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Als u bijvoorbeeld een lokale server en een Data Base met de naam **testdb** bevat:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>De gegevens op de doel Azure Database for PostgreSQL importeren
U kunt de psql-opdracht regel en de--dbname para meter (-d) gebruiken om de gegevens te importeren in de Azure Database for PostgreSQL-server en gegevens uit het SQL-bestand te laden.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
In dit voor beeld maakt gebruik van het psql-hulp programma en een script bestand met de naam **testdb. SQL** uit de vorige stap om gegevens te importeren in de Data Base **mypgsqldb** op de doel server **mydemoserver.postgres.database.Azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Volgende stappen
- Als u een PostgreSQL-Data Base wilt migreren met dump en herstellen, raadpleegt u [uw postgresql-data base migreren met dump en herstellen](howto-migrate-using-dump-and-restore.md).
- Zie de [hand leiding voor database migratie](https://aka.ms/datamigration)voor meer informatie over het migreren van data bases naar Azure database for PostgreSQL. 
