---
title: 'Zelf studie: RDS PostgreSQL online migreren naar Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het uitvoeren van een online migratie van RDS PostgreSQL naar Azure Database for PostgreSQL met behulp van de Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: 725b466a42e1e232d35bd7bd70c8943a254b026f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087693"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Zelf studie: RDS PostgreSQL migreren naar Azure DB for PostgreSQL online met behulp van DMS

U kunt Azure Database Migration Service gebruiken om data bases van een RDS PostgreSQL-exemplaar te migreren naar [Azure database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) terwijl de bron database online blijft tijdens de migratie. Met andere woorden, migratie kan worden gerealiseerd met minimale downtime voor de toepassing. In deze zelf studie migreert u de **gehuurde** voorbeeld database van de DVD vanuit een exemplaar van RDS postgresql 9,6 naar Azure database for PostgreSQL met behulp van de online migratie activiteit in azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeld schema met behulp van het hulp programma pg_dump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Een migratie project maken met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.
> * Migratie cutover uitvoeren.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie. De schijf wordt versleuteld om dief stal van gegevens tijdens het migratie proces te voor komen.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een online migratie uitvoert vanaf een on-premises exemplaar van PostgreSQL naar Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 of 10. De bronversie van PostgreSQL Server moet versie 9.5.11, 9.6.7, 10 of hoger zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) (Ondersteunde versies van de PostgreSQL-database).

   Houd er ook rekening mee dat de versie van het doel Azure Database for PostgreSQL moet gelijk zijn aan of hoger zijn dan de RDS PostgreSQL-versie. RDS PostgreSQL 9,6 kan bijvoorbeeld alleen migreren naar Azure Database for PostgreSQL 9,6, 10 of 11, maar niet naar Azure Database for PostgreSQL 9,5.

* Maak een instantie van [Azure database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) of [Azure database for PostgreSQL-grootschalige (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal). Raadpleeg deze [sectie](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) van het document voor meer informatie over het maken van verbinding met de postgresql-server met behulp van pgAdmin.
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Raadpleeg de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)voor meer informatie over het maken van een virtueel netwerk, met name de Quick Start-artikelen met stapsgewijze Details.
* Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk niet de volgende binnenkomende communicatie poorten blok keren tot Azure Database Migration Service: 443, 53, 9354, 445 en 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG verkeer van virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron PostgreSQL-server, die standaard TCP-poort 5432 is.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau voor de Azure database for postgresql-server om Azure database Migration service toegang tot de doel databases toe te staan. Geef het subnet-bereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>AWS RDS PostgreSQL instellen voor replicatie

1. Als u een nieuwe parameter groep wilt maken, volgt u de instructies van AWS in het artikel [werken met DB-parameter groepen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Gebruik de naam van de hoofd gebruiker om verbinding te maken met de bron vanuit Azure Database Migration Service. Als u een ander account dan het hoofd gebruikers account gebruikt, moet het account beschikken over de rds_superuser rol en de rds_replication rol. De functie rds_replication verleent machtigingen voor het beheren van logische sleuven en het streamen van gegevens met behulp van logische sleuven.
3. Maak een nieuwe parameter groep met de volgende configuratie:

    a. Stel de para meter RDS. logical_replication in de groep DB-para meter in op 1.

    b. max_wal_senders = [aantal gelijktijdige taken]: met de max_wal_senders-para meter wordt het aantal gelijktijdige taken ingesteld dat kan worden uitgevoerd, worden 10 taken aanbevolen.

    c. max_replication_slots – = [aantal sleuven], raadt aan om vijf sleuven in te stellen.

4. Koppel de parameter groep die u hebt gemaakt met het RDS PostgreSQL-exemplaar.

## <a name="migrate-the-schema"></a>Het schema migreren

1. Pak het schema uit van de bron database en pas deze toe op de doel database om de migratie van alle database objecten, zoals tabel schema's, indexen en opgeslagen procedures, te volt ooien.

    De eenvoudigste manier om alleen het schema te migreren, is door pg_dump te gebruiken met de optie-s. Zie de [voor beelden](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) in de post gres pg_dump-zelf studie voor meer informatie.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Als u bijvoorbeeld een schema bestand voor de **dvdrental** -Data Base wilt dumpen, gebruikt u de volgende opdracht:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Maak een lege data base in de doel service, die Azure Database for PostgreSQL is. Raadpleeg een van de volgende artikelen als u verbinding wilt maken en een Data Base wilt aanmaken:

    * [Een Azure Database for PostgreSQL-server maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Een Citus-server (Azure Database for PostgreSQL-grootschalige) maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Importeer het schema naar de doel service, die Azure Database for PostgreSQL is. Voer de volgende opdracht uit om het schema dump bestand te herstellen:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Als u het script voor refererende sleutels wilt extra heren en een extern sleutel script wilt toevoegen aan de bestemming (Azure Database for PostgreSQL), voert u het volgende script uit in PgAdmin of in psql:
  
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

5. Voer de refererende sleutel (die de tweede kolom is) in het query resultaat uit om de refererende sleutel te verwijderen.

6. Als u triggers hebt (invoeg-of update-trigger) in de gegevens, wordt de integriteit van gegevens in het doel afgedwongen voordat gegevens van de bron worden gerepliceerd. De aanbeveling is om tijdens de migratie triggers in alle tabellen *op het doel* uit te scha kelen en de triggers vervolgens in te scha kelen nadat de migratie is voltooid.

    Triggers uitschakelen in doel database:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een exemplaar maken van Azure Database Migration Service

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarin u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuwe.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot het bron PostgreSQL-exemplaar en het doel Azure Database for PostgreSQL exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

6. Selecteer een prijs categorie. Zorg ervoor dat u voor deze online migratie de prijs categorie Premium: 4vCores selecteert.

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Op het scherm **Azure data base Migration Services** zoekt u de naam van het Azure database Migration service exemplaar dat u hebt gemaakt, selecteert u het exemplaar en selecteert u vervolgens + **Nieuw migratie project**.
3. Geef in het scherm **Nieuw migratie project** een naam op voor het project, Selecteer in het tekstvak **type bron server** de optie **AWS RDS voor postgresql**, en selecteer in het tekstvak **doel server type** de optie **Azure database for PostgreSQL**.
4. Selecteer in de sectie **type activiteit kiezen** de optie **Online gegevens migratie**.

    > [!IMPORTANT]
    > Zorg ervoor dat u **Online gegevens migratie**selecteert. offline migraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > U kunt ook **project maken** selecteren om het migratie project nu te maken en de migratie later uitvoeren.

5. Selecteer **Opslaan**.

6. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    > [!NOTE]
    > Noteer de vereisten die nodig zijn om online migratie in te stellen op de Blade voor het maken van een project.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **bron Details toevoegen** de verbindings Details op voor het bron postgresql-exemplaar.

   ![Brondetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens in het scherm **doel Details** de verbindings gegevens op voor de doel Azure database for postgresql server, die vooraf is ingericht en waarop het schema voor de **DVD-huur** is geïmplementeerd met behulp van pg_dump.

    ![Doeldetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

    ![Activiteitsstatus-uitvoering](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Selecteer onder **database naam**een specifieke data base om de migratie status voor **volledige gegevens belasting** en **incrementele gegevens synchronisatie** te verkrijgen.

    Bij **volledige gegevens belasting** wordt de initiële status van de laad migratie weer gegeven, terwijl **incrementele gegevens synchronisatie** de status Change Data Capture (CDC) laat zien.

    ![Inventaris scherm-volledige belasting van gegevens](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Inventarisatie scherm-incrementele gegevens synchronisatie](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige belasting is voltooid, worden de data bases als **gereed voor Cutover**gemarkeerd.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht totdat de teller **in behandeling** is **0** weer gegeven om ervoor te zorgen dat alle inkomende trans acties voor de bron database zijn gestopt, schakel het selectie vakje **bevestigen** in en selecteer vervolgens **Toep assen**.

    ![Cutover-scherm volt ooien](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Wanneer de status van de database migratie **is voltooid**, verbindt u uw toepassingen met de nieuwe doel-Azure database for PostgreSQL data base.

Uw online migratie van een on-premises exemplaar van RDS PostgreSQL naar Azure Database for PostgreSQL is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel [Wat is er Azure database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)voor informatie over Azure database for PostgreSQL.
* Voor andere vragen moet u een e-mail sturen naar de [Azure data base-migratie](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
