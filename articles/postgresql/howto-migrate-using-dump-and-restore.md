---
title: Dumpen en herstellen - Azure Database voor PostgreSQL - Single Server
description: Beschrijft hoe u een PostgreSQL-database in een dumpbestand extraheren en herstellen uit een bestand dat is gemaakt door pg_dump in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 4365338efa56593e80edcc19cba5944b213d2b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770234"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Uw PostgreSQL-database migreren met dumpen en herstel
U [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) gebruiken om een PostgreSQL-database in een dumpbestand te extraheren en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) om de PostgreSQL-database te herstellen uit een archiefbestand dat door pg_dump is gemaakt.

## <a name="prerequisites"></a>Vereisten
Om deze handleiding door te nemen, hebt u het volgende nodig:
- Een [Azure Database voor PostgreSQL-server](quickstart-create-server-database-portal.md) met firewallregels om toegang en database eronder toe te staan.
- [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) installatie van de opdrachtregel

Volg deze stappen om uw PostgreSQL-database te dumpen en te herstellen:

## <a name="create-a-dump-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Een dumpbestand maken met pg_dump dat de te laden gegevens bevat
Als u een on-premises of in een vm een back-up wilt maken van een bestaande PostgreSQL-database, voert u de volgende opdracht uit:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> -f <database>.dump
```
Als u bijvoorbeeld een lokale server en een database met de naam **testdb** in zich hebt
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb -f testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postgresql-using-pg_restore"></a>De gegevens herstellen in de doelazure-database voor PostgreSQL met pg_restore
Nadat u de doeldatabase hebt gemaakt, u de opdracht pg_restore en de parameter -d, --dbname gebruiken om de gegevens vanuit het dumpbestand in de doeldatabase te herstellen.
```bash
pg_restore -v --no-owner --host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Inclusief de parameter --geen-eigenaar zorgt ervoor dat alle objecten die tijdens het herstel zijn gemaakt, eigendom zijn van de gebruiker die is opgegeven met --gebruikersnaam. Zie voor meer informatie de officiële PostgreSQL-documentatie over [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Als uw PostgreSQL-server SSL-verbindingen vereist (standaard ingeschakeld in Azure Database `PGSSLMODE=require` voor PostgreSQL-servers), stelt u een omgevingsvariabele in zodat het pg_restore-hulpprogramma verbinding maakt met SSL. Zonder SSL kan de fout`FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Voer de opdracht `SET PGSSLMODE=require` uit in de opdrachtregel van Windows voordat u de opdracht pg_restore uitvoert. Voer in Linux of `export PGSSLMODE=require` Bash de opdracht uit voordat u de opdracht pg_restore uitvoert.
>

In dit voorbeeld u de gegevens van het dumpbestand **testdb.dump** herstellen in de database **mypgsqldb** op doelserver **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Het migratieproces optimaliseren

Een manier om uw bestaande PostgreSQL-database te migreren naar Azure Database voor PostgreSQL-service is door een back-up te maken van de database op de bron en deze te herstellen in Azure. Als u de tijd wilt minimaliseren die nodig is om de migratie te voltooien, u overwegen de volgende parameters te gebruiken met de back-up- en herstelopdrachten.

> [!NOTE]
> Zie de artikelen [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)voor gedetailleerde syntaxisinformatie.
>

### <a name="for-the-backup"></a>Voor de back-up
- Neem de back-up met de -Fc-schakelaar, zodat u het herstel parallel uitvoeren om het te versnellen. Bijvoorbeeld:

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName -f Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Voor het herstel
- We raden u aan het back-upbestand naar een Azure VM te verplaatsen in dezelfde regio als de Azure Database voor PostgreSQL-server waarnaar u migreert en de pg_restore van die VM uit te brengen om de netwerklatentie te verminderen. We raden ook aan dat de VM is gemaakt met [versnelde netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) ingeschakeld.

- Dit moet al standaard worden gedaan, maar open het dumpbestand om te controleren of de indexinstructies voor maken na het invoegen van de gegevens zijn. Als dit niet het geval is, verplaatst u de indexinstructies maken nadat de gegevens zijn ingevoegd.

- Herstel met de schakelaars -Fc en -j *#* om het herstel te paralleleren. *#* is het aantal cores op de doelserver. U ook *#* proberen met ingesteld op twee keer het aantal cores van de doelserver om de impact te zien. Bijvoorbeeld:

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- U het dumpbestand ook bewerken door de *opdrachtset synchronous_commit = uit* te voegen; aan het begin en de *opdrachtset synchronous_commit = aan;* aan het einde. Als u deze aan het einde niet inschakelt, kan dit leiden tot verlies van gegevens voordat de apps de gegevens wijzigen.

- Overweeg op de doelazure-database voor PostgreSQL-server het volgende te doen voordat het herstel wordt hersteld:
    - Schakel queryprestatietracking uit, omdat deze statistieken niet nodig zijn tijdens de migratie. U dit doen door pg_stat_statements.track, pg_qs.query_capture_mode en pgms_wait_sampling.query_capture_mode in te stellen op GEEN.

    - Gebruik een hoge rekenkracht en een hoge geheugensku, zoals 32 vCore Memory Optimized, om de migratie te versnellen. U eenvoudig terugschalen naar uw gewenste sku nadat het herstel is voltooid. Hoe hoger de sku, hoe meer parallellisme `-j` u bereiken door de overeenkomstige parameter in de opdracht pg_restore te verhogen. 

    - Meer IOPS op de doelserver kan de herstelprestaties verbeteren. U meer IOPS inrichten door de opslaggrootte van de server te vergroten. Deze instelling is niet omkeerbaar, maar overweeg of een hogere IOPS uw werkelijke werklast in de toekomst ten goede zou komen.

Vergeet niet om deze opdrachten in een testomgeving te testen en te valideren voordat u ze in productie gebruikt.

## <a name="next-steps"></a>Volgende stappen
- Zie [Uw PostgreSQL-database migreren met exporteren en importeren](howto-migrate-using-export-and-import.md)als u een PostgreSQL-database wilt migreren met exporteren en importeren.
- Zie de [databasemigratiehandleiding](https://aka.ms/datamigration)voor meer informatie over het migreren van databases naar Azure Database voor PostgreSQL.
