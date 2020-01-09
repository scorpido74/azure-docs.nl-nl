---
title: 'Zelf studie: PostgreSQL online migreren naar Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het uitvoeren van een online migratie van PostgreSQL on-premises naar Azure Database for PostgreSQL met behulp van Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/28/2019
ms.openlocfilehash: a710512d7063a73fde42e2b076a3bb67c5efbf7a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437358"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Zelfstudie: PostgreSQL online migreren naar Azure Database for PostgreSQL met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de data bases van een on-premises PostgreSQL-exemplaar te migreren naar [Azure database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) met minimale downtime. Met andere woorden, migratie kan worden gerealiseerd met minimale downtime voor de toepassing. In deze zelf studie migreert u de **gehuurde** voorbeeld database van de DVD van een on-premises exemplaar van postgresql 9,6 naar Azure database for PostgreSQL met behulp van de online migratie activiteit in azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeld schema met behulp van pg_dump-hulp programma.
> * De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> * Een migratieproject maken met behulp van de Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 of 10. De bronversie van PostgreSQL Server moet versie 9.5.11, 9.6.7, 10 of hoger zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) (Ondersteunde versies van de PostgreSQL-database).

    Bovendien moet de on-premises versie van PostgreSQL overeenkomen met de versie van Azure Database for PostgreSQL. PostgreSQL 9.5.11.5 kan bijvoorbeeld alleen migreren naar Azure Database for PostgreSQL 9.5.11 en niet naar versie 9.6.7.

* [Een exemplaar maken in Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* Maak een Azure Virtual Network (VNet) voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Voor meer informatie over het maken van een VNet raadpleegt u de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)en met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de VNet-installatie gebruikmaakt van ExpressRoute met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > * Opslag eindpunt
    > * Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat de Azure Database Migration Service geen verbinding met internet heeft.

* Zorg ervoor dat de regels van uw VNet-netwerk beveiligings groep (NSG) niet de volgende binnenkomende communicatie poorten blok keren naar Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van NSG-verkeer van Azure VNet.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron PostgreSQL-server, die standaard TCP-poort 5432 is.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau voor Azure Database for PostgreSQL om Azure database Migration service toegang te geven tot de doel databases. Geef het subnet-bereik van het VNet op dat wordt gebruikt voor Azure Database Migration Service.
* Er zijn twee methoden voor het aanroepen van de CLI:

  * Selecteer de knop Cloud Shell in de rechter bovenhoek van de Azure Portal:

       ![Knop Cloud Shell in de Azure Portal](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Installeer de CLI lokaal en voer deze uit. CLI 2.0 is het opdrachtregelprogramma voor het beheer van Azure-resources.

       Volg de instructies in het artikel [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt downloaden. In het artikel worden ook de platforms genoemd die ondersteuning bieden voor CLI 2.0.

       Volg als u Windows-subsysteem voor Linux (WSL) wilt instellen de instructies in de [installatiehandleiding voor Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)

* Schakel logische replicatie in het bestand postgresql.config in en stel de volgende parameters in:

  * wal_level = **logical**
  * max_replication_slots = [aantal sleuven], raadt u aan **vijf sleuven** in te stellen
  * max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. De aanbevolen instelling is **10 taken**

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen.

1. Gebruik de opdracht pg_dump -s om een dumpbestand van het schema te maken voor een database. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Ga bijvoorbeeld als volgt te werk als u een dump wilt maken van het schemabestand van de dvdrental-database:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Zie voor meer informatie over het gebruik van het hulpprogramma pg_dump de voorbeelden in de zelfstudie [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Maak een lege database maken in uw doelomgeving, dit is Azure Database for PostgreSQL.

    Raadpleeg het artikel [Create an Azure Database for PostgreSQL server in the Azure portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) (Een Azure Database for PostgreSQL-server maken in Azure Portal) voor meer informatie over hoe u een database maakt en verbindt.

3. Importeer het schema in de doeldatabase die u hebt gemaakt, door het dumpbestand van het schema te herstellen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Voer het volgende script uit in PgAdmin of in psql om het 'drop foreign key'-script uit te pakken en voeg het 'foreign key'-script toe op de doeldatabase (Azure Database for PostgreSQL).
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.

5. Triggers in de gegevens (invoeg- of bijwerktriggers) dwingen de gegevensintegriteit in de doeldatabase af vóór de gerepliceerde gegevens uit de brondatabase. Het is raadzaam om tijdens de migratie triggers in alle tabellen **op het doel** uit te scha kelen en de triggers opnieuw in te scha kelen nadat de migratie is voltooid.

    Gebruik de volgende opdracht om triggers in de doeldatabase uit te schakelen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Als er in tabellen een ENUM-gegevens type is, is het raadzaam dat u dit tijdelijk bijwerkt naar het gegevens type ' character varieerde ' in de doel tabel. Wanneer de gegevensreplicatie is voltooid, wijzigt u het gegevenstype weer in ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Een exemplaar van DMS inrichten met behulp van de CLI

1. Installeer als volgt de DMS-synchronisatie-extensie:
   * Meld u aan bij Azure door de volgende opdracht uit te voeren:
       ```
       az login
       ```

   * Wanneer dit wordt gevraagd, opent u een webbrowser en voert u een code in om uw apparaat te verifiëren. Volg de aanwijzingen.
   * Voeg de DMS-extensie toe:
       * Voer de volgende opdracht uit om de beschikbare extensies weer te geven:

           ```
           az extension list-available –otable
           ```

       * Voer de volgende opdracht uit om de extensie te installeren:

           ```
           az extension add –n dms-preview
           ```

   * Voer de volgende opdracht uit om te controleren of de DMS-extensie correct is geïnstalleerd:

       ```
       az extension list -otable
       ```
       In dat geval moet de volgende uitvoer worden weergegeven:

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * U kunt op elk gewenst moment alle opdrachten bekijken die in DMS worden ondersteund door de volgende opdracht uit te voeren:

       ```
       az dms -h
       ```

   * Als u meerdere Azure-abonnementen hebt, voert u de volgende opdracht uit om het abonnement in te stellen waarmee u een exemplaar van de DMS-service wilt inrichten.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Voer de volgende opdracht uit om een exemplaar van DMS in te richten:

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Met de volgende opdracht bijvoorbeeld maakt u een service in:

   * Locatie: US - oost 2
   * Abonnement: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Naam resourcegroep: PostgresDemo
   * Naam DMS-service: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   Het duurt ongeveer 10-12 minuten om het exemplaar van de DMS-service te maken.

3. Voer de volgende opdracht uit om het IP-adres van de DMS-agent te identificeren, zodat u het kunt toevoegen aan het Postgres-bestand pg_hba.conf:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Bijvoorbeeld:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Als het goed is, lijkt het resultaat op het volgende adres: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Voeg het IP-adres van de DMS-agent toe aan het Postgres-bestand pg_hba.conf.

    * Noteer het IP-adres in DMS wanneer u klaar bent met het inrichten in DMS.
    * Voeg het IP-adres toe aan het bestand pg_hba.conf op de bron, vergelijkbaar met de volgende vermelding:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Voer vervolgens de volgende opdracht uit om een PostgreSQL-migratieproject te maken:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Zo maakt u met de volgende opdracht een project met deze parameters:

   * Locatie: US - west-centraal
   * Naam resourcegroep: PostgresDemo
   * Servicenaam: PostgresCLI
   * Projectnaam: PGMigration
   * Bronplatform: PostgreSQL
   * Doelplatform: AzureDbForPostgreSql

     ```
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Voer de volgende stappen uit om een PostgreSQL-migratietaak te maken.

    Deze stap omvat het gebruik van de bron-IP, gebruikers-id en -wachtwoord, doel-IP, gebruikers-id en -wachtwoord en taaktype om verbinding te maken.

   * Voer de volgende opdracht uit als u een volledige lijst van opties wilt bekijken:

       ```
       az dms project task create -h
       ```

       Voor zowel de bron- als de doelverbinding verwijst de invoerparameter naar een json-bestand dat de lijst met objecten bevat.

       De indeling van het JSON-object van de verbinding voor PostgreSQL-verbindingen.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Er is ook een JSON-bestand van de data base-optie met een lijst met JSON-objecten. Hieronder ziet u de indeling van het JSON-object van de database-optie voor PostgreSQL:

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Maak een json-bestand in Kladblok, kopieer de volgende opdrachten en plak ze in het bestand. Sla het bestand op in C:\DMS\source.json.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Maak een ander bestand met de naam target.json en sla het op als C:\DMS\target.json. Neem daarin de volgende opdrachten op:

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Maak een json-bestand voor de database-optie dat de inventaris weergeeft als de te migreren database:

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Voer de volgende opdracht, waarbij het bronbestand, het doelbestand en het json-bestand voor de database-optie worden gebruikt.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     U hebt nu een migratietaak ingediend.

7. Voer de volgende opdracht uit om de voortgang van de taak weer te geven:

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   OF

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. U kunt ook de migrationState opvragen met de expand-parameter:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Inzicht in de status van de migratietaak

Het uitvoerbestand bevat diverse parameters die de voortgang van de migratie aangeven. Zie bijvoorbeeld onderstaande uitvoerbestand:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Cutover-migratietaak

De database is gereed voor cutover wanneer de database volledig is geladen. Afhankelijk van hoe druk de bronserver is met de nieuwe transacties die binnenkomen, is het mogelijk dat de DMS-taak nog steeds wijzigingen toepast nadat het volledig laden is voltooid.

Valideer het aantal rijen in de bron- en doeldatabases om te controleren of alle gegevens zijn bijgewerkt. U kunt hiervoor bijvoorbeeld de volgende opdracht gebruiken:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. Voer de cutover-databasemigratietaak uit met behulp van de volgende opdracht:

    ```
    az dms project task cutover -h
    ```

    Bijvoorbeeld:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. Voer de volgende opdracht uit als u de voortgang van de cutover wilt bijhouden:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Service, project, taak opschonen

Als u een DMS-taak, -project of -service wilt annuleren of verwijderen, voert u de annulering uit in de volgende volgorde:

* Een actieve taak annuleren
* De taak verwijderen
* Het project verwijderen
* De DMS-service verwijderen

1. Gebruik de volgende opdracht als u een actieve taak wilt annuleren:

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Gebruik de volgende opdracht als u een actieve taak wilt verwijderen:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Gebruik de volgende opdracht als u een actief project wilt annuleren:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Gebruik de volgende opdracht als u een actief project wilt verwijderen:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Gebruik de volgende opdracht als u een DMS-service wilt verwijderen:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Zie het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel [Wat is er Azure database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)voor informatie over Azure database for PostgreSQL.
