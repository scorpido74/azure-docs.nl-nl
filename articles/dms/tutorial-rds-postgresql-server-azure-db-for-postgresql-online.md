---
title: 'Zelfstudie: RDS PostgreSQL online migreren naar Azure Database voor PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Leer een online migratie uitvoeren van RDS PostgreSQL naar Azure Database voor PostgreSQL met behulp van de Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 12aa11aa5064b3a0a2ff18f88161f44f37208aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240683"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Zelfstudie: RDS PostgreSQL migreren naar Azure DB voor PostgreSQL online met DMS

U Azure Database Migration Service gebruiken om databases te migreren van een RDS PostgreSQL-instantie naar [Azure Database voor PostgreSQL,](https://docs.microsoft.com/azure/postgresql/) terwijl de brondatabase online blijft tijdens de migratie. Met andere woorden, migratie kan worden bereikt met minimale downtime naar de toepassing. In deze zelfstudie migreert u de **voorbeelddatabase voor dvd-verhuur** van een instantie van RDS PostgreSQL 9.6 naar Azure Database voor PostgreSQL met behulp van de onlinemigratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeldschema met behulp van het pg_dump hulpprogramma.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.
> * Migratiecutover uitvoeren.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie. We versleutelen schijf om diefstal van gegevens te voorkomen tijdens het migratieproces.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een onlinemigratie uitvoert van een on-premises instantie van PostgreSQL naar Azure Database voor PostgreSQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 of 10. De bronversie van PostgreSQL Server moet versie 9.5.11, 9.6.7, 10 of hoger zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) (Ondersteunde versies van de PostgreSQL-database).

    Bovendien moet de RDS PostgreSQL-versie overeenkomen met de Azure Database voor PostgreSQL-versie. RDS PostgreSQL 9.5.11.5 kan bijvoorbeeld alleen migreren naar Azure Database voor PostgreSQL 9.5.11 en niet naar versie 9.6.7.

* Maak een exemplaar van [Azure Database voor PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) of [Azure Database voor PostgreSQL - Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) Raadpleeg dit [gedeelte](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) van het document voor meer informatie over hoe u verbinding maken met de PostgreSQL-server met pgAdmin.
* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.
* Zorg ervoor dat de regels van uw beveiligingsgroep voor virtuele netwerken niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445 en 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows-firewall zodat Azure Database Migration Service toegang heeft tot de bron PostgreSQL-server, die standaard TCP-poort 5432 is.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een firewallregel op [serverniveau](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor de Azure Database voor PostgreSQL-server om Azure Database Migration Service toegang te geven tot de doeldatabases. Geef het subnetbereik op van het virtuele netwerk dat wordt gebruikt voor Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>AWS RDS PostgreSQL instellen voor replicatie

1. Als u een nieuwe parametergroep wilt maken, volgt u de instructies van AWS in het artikel [Werken met DB-parametergroepen](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Gebruik de hoofdgebruikersnaam om verbinding te maken met de bron van Azure Database Migration Service. Als u een ander account dan het hoofdgebruikersaccount gebruikt, moet het account de rds_superuser rol en de rds_replication rol hebben. De rds_replication-rol verleent machtigingen voor het beheren van logische sleuven en het streamen van gegevens met behulp van logische sleuven.
3. Maak een nieuwe parametergroep met de volgende configuratie:

    a. Stel de parameter rds.logical_replication in uw DB-parametergroep in op 1.

    b. max_wal_senders =[aantal gelijktijdige taken] - De parameter max_wal_senders stelt het aantal gelijktijdige taken in dat kan worden uitgevoerd, beveelt 10 taken aan.

    c. max_replication_slots – = [aantal slots], raden ingesteld op vijf slots.

4. Koppel de parametergroep die u hebt gemaakt aan de instantie RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Het schema migreren

1. Haal het schema uit de brondatabase en pas de doeldatabase toe om de migratie van alle databaseobjecten, zoals tabelschema's, indexen en opgeslagen procedures, te voltooien.

    De eenvoudigste manier om alleen het schema te migreren, is door pg_dump te gebruiken met de optie -s. Zie voor meer informatie de [voorbeelden](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) in de Pg_dump-zelfstudie.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Als u bijvoorbeeld een schemabestand voor de **dvdrental-database wilt dumpen,** gebruikt u de volgende opdracht:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Maak een lege database in de doelservice, die Azure Database voor PostgreSQL is. Als u verbinding wilt maken en een database wilt maken, raadpleegt u een van de volgende artikelen:

    * [Een Azure-database voor PostgreSQL-server maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Een Azure-database voor PostgreSQL - Hyperscale -server (Citus) maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Importeer het schema naar doelservice, wat Azure Database voor PostgreSQL is. Voer de volgende opdracht uit om het schemadumpbestand te herstellen:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Als u het drop foreign key script wilt extraheren en een buitenlands sleutelscript op de bestemming wilt toevoegen (Azure Database for PostgreSQL), voert u het volgende script uit in PgAdmin of in psql:
  
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

5. Voer de drop-buitenlandse sleutel (de tweede kolom) uit in het queryresultaat om de buitenlandse sleutel te laten vallen.

6. Als u triggers (trigger invoegen of bijwerken) in de gegevens hebt, wordt de gegevensintegriteit in het doel afgedwongen voordat gegevens van de bron worden gerepliceerd. De aanbeveling is om triggers uit te schakelen in alle tabellen *op het doel* tijdens de migratie en vervolgens de triggers in te schakelen nadat de migratie is voltooid.

    Triggers in doeldatabase uitschakelen:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een instantie van Azure Database Migration Service maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waar u de instantie van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron PostgreSQL-instantie en de doel-Azure Database voor PostgreSQL-instantie.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijsniveau; Voor deze online migratie moet u de prijscategorie Premium: 4vCores selecteren.

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Services** naar de naam van de instantie Azure Database Migration Service die u hebt gemaakt, selecteer de instantie en selecteer + Nieuw **migratieproject**.
3. Geef in het scherm **Nieuw migratieproject** een naam voor het project op, selecteer in het tekstvak **Bronservertype** **AWS RDS voor PostgreSQL**en selecteer vervolgens in het tekstvak **Doelservertype** de optie **Azure Database voor PostgreSQL**.
4. Selecteer online **gegevensmigratie**in de sectie **Type activiteit kiezen** .

    > [!IMPORTANT]
    > Zorg ervoor dat u **Online gegevensmigratie**selecteert; offlinemigraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > U afwisselend **project maken kiezen** om nu het migratieproject te maken en de migratie later uit te voeren.

5. Selecteer **Opslaan**.

6. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    > [!NOTE]
    > Houd rekening met de vereisten die nodig zijn om online migratie in het projectcreatieblad in te stellen.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het scherm **Brondetails toevoegen** de verbindingsdetails op voor de bron PostgreSQL-instantie.

   ![Brondetails](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens op het scherm **Doeldetails** de verbindingsgegevens op voor de doelAzure-database voor PostgreSQL-server, die vooraf is ingericht en het **dvd-verhuurschema** heeft geïmplementeerd met pg_dump.

    ![Doelgegevens](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

    ![Activiteitsstatus - actief](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Selecteer **onder DATABASENAAM**een specifieke database om de migratiestatus voor **Volledige gegevensbelasting** en **Incrementele gegevenssynchronisatiebewerkingen** te krijgen.

    **Volledige gegevensbelasting** toont de status van de initiële migratie van de belasting, terwijl **incrementele gegevenssynchronisatie** de STATUS 'Change data capture(CDC) weergeeft.

    ![Voorraadscherm - volledige gegevensbelasting](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Voorraadscherm - incrementele gegevenssynchronisatie](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige belasting is voltooid, worden de databases gemarkeerd **met Ready to Cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht totdat het in **behandeling zijnde wijzigingenteller** **0** weergeeft om ervoor te zorgen dat alle binnenkomende transacties naar de brondatabase zijn gestopt, schakel het selectievakje **Bevestigen** in en schakel vervolgens **Toepassen in.**

    ![Compleet cutover-scherm](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Wanneer de status van de databasemigratie **Voltooid**weergeeft, verbindt u uw toepassingen met de nieuwe doelAzure-database voor PostgreSQL-database.

Uw online migratie van een on-premises instantie van RDS PostgreSQL naar Azure Database voor PostgreSQL is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel [Wat is Azure Database voor PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Voor andere vragen u een e-mail sturen naar de alias [Azure Database Migrations vragen.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
