---
title: Upgraden met dump en Restore-Azure Database for PostgreSQL-één server
description: Beschrijft een aantal methoden om data bases te dumpen en te herstellen om te migreren naar een hogere versie Azure Database for PostgreSQL-één server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 6dfcf0b2ec1d46821007123908a8e7ba8df29744
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421761"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Een upgrade uitvoeren van uw PostgreSQL-data base met dump en herstel

In Azure Database for PostgreSQL-één server is het raadzaam om uw PostgreSQL-data base-engine bij te werken naar een hogere primaire versie met behulp van een van de volgende methoden:
* Offline methode met PostgreSQL [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). In deze methode voert u eerst de dump van de bron server uit en herstelt u deze dump op de doel server.
* Online methode met behulp van [**database Migration service**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Met deze methode blijft de doel database synchroon met met de bron en kunt u kiezen wanneer u wilt knippen. Er zijn echter enkele vereisten en beperkingen die moeten worden verholpen. 

U kunt de volgende aanbeveling gebruiken bij het bepalen van de online-en offline methoden om upgrades van belang rijke versies uit te voeren.

| **Database** | **Dump/herstellen (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| U hebt een kleine data base en u kunt de uitval tijd voor de upgrade veroorloven  | X | |
| Kleine data bases (< 10 GB)  | X | X | 
| Kleine, middel grote Db's (10 GB – 100 GB) | X | X |
| Grote data bases (> 100 GB) |  | X |
| Kan de uitval tijd voor de upgrade veroorloven (ongeacht de grootte van de data base) | X |  |
| Kan de [voorbereidings vereisten](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) van een DMS worden geadresseerd, inclusief opnieuw opstarten? |  | X |
| Kunt u DDLs en niet-geregistreerde tabellen voor komen tijdens het upgrade proces? | |  X |

Deze hand leiding bevat twee voorbeeld methoden voor het bijwerken van uw data bases met behulp van PostgreSQL pg_dump en pg_restore opdrachten. Het proces in dit document wordt een **upgrade** genoemd, maar de data base wordt  **gemigreerd** van de bron server naar de doel server. 

> [!NOTE]
> PostgreSQL dump en Restore kunnen op verschillende manieren worden uitgevoerd. U kunt ervoor kiezen om verschillende methoden te gebruiken dan in dit document wordt genoemd. Als u bijvoorbeeld een dump wilt doen, gevolgd door een herstel bewerking vanaf een PostgreSQL-client, raadpleegt u de [documentatie](./howto-migrate-using-dump-and-restore.md) voor gedetailleerde stapsgewijze procedures en aanbevolen procedures. Zie de artikelen [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)voor gedetailleerde syntaxis voor dump en herstel met aanvullende para meters. 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Vereisten voor het gebruik van dump en herstellen met Azure PostgreSQL
 
U hebt het volgende nodig om deze stapsgewijze hand leiding te door lopen:
- Een bron database met 9,5, 9,6 of 10 (Azure Database for PostgreSQL – één server)
- Doel database server met de gewenste PostgreSQL primaire versie [Azure database for postgresql server](quickstart-create-server-database-portal.md). 
- Een client systeem (Linux) met PostgreSQL geïnstalleerd en heeft [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) en [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) opdracht regel Programma's geïnstalleerd. 
- U kunt ook [Azure Cloud shell](https://shell.azure.com) gebruiken of door te klikken op de Azure Cloud shell in de menu balk in de rechter bovenhoek van de [Azure Portal](https://portal.azure.com). U moet zich aanmelden bij uw account `az login` voordat u de opdrachten dump en herstellen uitvoert.
- De locatie van uw PostgreSQL-client, zoals een virtuele machine, wordt bij voor keur uitgevoerd in dezelfde regio als de bron-en doel server. 

## <a name="additional-details-and-considerations"></a>Meer informatie en overwegingen
- U kunt de connection string vinden in de bron-en doel database door te klikken op de verbindings reeksen in de portal. 
- Mogelijk wordt er meer dan één Data Base op de server uitgevoerd. U kunt de lijst met data bases vinden door verbinding te maken met de bron server en uit te voeren `\l` .
- De bijbehorende data bases maken in de doel database server.
- U kunt de upgrade- `azure_maintenance` of sjabloon databases overs Laan.
- Raadpleeg de bovenstaande tabellen om te bepalen of de data base geschikt is voor deze migratie modus.
- Als u Azure Cloud Shell wilt gebruiken, wordt de sessie na 20 minuten getimed. Als de grootte van de data base < 10 GB is, is het mogelijk dat u de upgrade uitvoert zonder time-out. Als dat niet het geval is, moet u de sessie mogelijk op een andere manier openen, zoals het drukken op een <Enter> toets in 10-15 minuten. 

> [!TIP] 
> - Als u hetzelfde wacht woord gebruikt voor de bron-en doel database, kunt u de `PGPASSWORD=yourPassword` omgevings variabele instellen.  U hoeft geen wacht woord op te geven wanneer u opdrachten zoals psql, pg_dump en pg_restore uitvoert.  Op dezelfde manier kunt u aanvullende variabelen instellen `PGUSER` , zoals, `PGSSLMODE` enzovoort. Zie [postgresql-omgevings variabelen](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Als uw PostgreSQL-server TLS/SSL-verbindingen (standaard op Azure Database for PostgreSQL servers) vereist, stelt u een omgevings variabele in, `PGSSLMODE=require` zodat het hulp programma pg_restore verbinding maakt met TLS. Zonder TLS kan de fout worden gelezen  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - Voer in de Windows-opdracht regel de opdracht uit `SET PGSSLMODE=require` voordat u de opdracht pg_restore uitvoert. Voer in Linux of bash de opdracht uit `export PGSSLMODE=require` voordat u de opdracht pg_restore uitvoert.

## <a name="example-database-used-in-this-guide"></a>Voorbeeld database die in deze hand leiding wordt gebruikt

 | **Beschrijving** | **Waarde** |
 | ------- | ------- |
 | Bron server (v 9.5) | pg-95.postgres.database.azure.com |
 | Bron database | bench5gb |
 | Grootte van de bron database | 5 GB |
 | Gebruikers naam van de bron | pg@pg-95 |
 | Doel server (V11) | pg-11.postgres.database.azure.com |
 | Doel database | bench5gb |
 | Gebruikers naam voor doel | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Methode 1: upgraden met streaming-back-ups naar het doel 

Bij deze methode wordt de volledige database dump rechtstreeks naar de doel database server gestreamd en wordt de dump niet opgeslagen in de client. Dit kan daarom worden gebruikt met een client met beperkte opslag en kan zelfs worden uitgevoerd vanaf de Azure Cloud Shell. 

1. Zorg ervoor dat de data base bestaat op de doel server met behulp van de `\l` opdracht. Als de data base niet bestaat, maakt u de data base.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Voer de dump en herstel uit als één opdracht regel met behulp van een pipe. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Bijvoorbeeld:

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Zodra het proces voor de upgrade (migratie) is voltooid, kunt u de toepassing testen met de doel server. 
4. Herhaal dit proces voor alle data bases op de server.

 De volgende tabel illustreert de tijd die nodig is om een upgrade uit te kunnen maken met behulp van deze methode. De gegevens worden ingevuld met behulp van [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Aangezien uw data base een ander aantal objecten kan hebben met verschillende grootten dan pgbench gegenereerde tabellen en indexen, wordt het ten zeerste aanbevolen de dump en herstel bewerking van uw data base te testen om inzicht te krijgen in de werkelijke tijd die nodig is om uw data base bij te werken. 

| **Database grootte** | **Ong. gebruikte tijd** | 
| ----- | ------ |
| 1 GB  | 1-2 minuten |
| 5 GB | 8-10 minuten |
| 10 GB | 15-20 minuten |
| 50 GB | 1-1,5 uur |
| 100 GB | 2,5-3 uur|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Methode 2: upgraden met parallelle dump en herstel 

Deze methode is handig als u weinig grotere tabellen in de Data Base hebt en u het dump-en herstel proces voor die Data Base wilt parallelliseren. U hebt voldoende lokale schijf ruimte nodig om back-updumps voor uw data bases te kunnen bieden. Met dit parallelle dump en herstel proces wordt het tijd verbruik van de volledige migratie/upgrade verkort. Bijvoorbeeld: de pgbench-data base van 50 GB die 1 tot 1,5 uur duurde om te migreren, is in minder dan 30 minuten voltooid.

1. Maak voor elke data base in de bron server een bijbehorende data base op de doel server.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Bijvoorbeeld:
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Voer de pg_dump opdracht uit in een directory-indeling met het aantal taken = 4 (het aantal tabellen in de data base). Met een grotere Compute-laag en met meer tabellen kunt u deze verg Roten tot een hoger getal. Met deze pg_dump wordt een directory gemaakt voor het opslaan van gecomprimeerde bestanden voor elke taak.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Bijvoorbeeld:
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Herstel vervolgens de back-up op de doel server.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Bijvoorbeeld:
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Het proces dat in dit document wordt vermeld, kan ook worden gebruikt voor het bijwerken van uw Azure Database for PostgreSQL flexibele server, die in preview is. Het belangrijkste verschil is het connection string voor het flexibele server doel is zonder de `@dbName` .  Als de gebruikers naam bijvoorbeeld is, is `pg` de naam van de enkele server in de verbindings reeks `pg@pg-95` , terwijl u met een flexibele server werkt, u gewoon kunt gebruiken `pg` .

## <a name="next-steps"></a>Volgende stappen

- Zodra u tevreden bent met de functie van de doel database, kunt u de oude database server verwijderen. 
- Als u hetzelfde database eindpunt wilt gebruiken als de bron server en u de oude bron database server hebt verwijderd, kunt u een lees replica maken met de oude database server naam. Zodra de constante status is ingesteld, kunt u de replica stoppen, waardoor de replica server wordt gepromoveerd tot een onafhankelijke server. Zie [replicatie](./concepts-read-replicas.md) voor meer informatie.
- Vergeet niet om deze opdrachten te testen en te valideren in een test omgeving voordat u ze in productie gebruikt.
