---
title: 'Zelfstudie: Migreren Naar Azure DB naar Azure DB voor PostgreSQL online via de Azure-portal'
titleSuffix: Azure Database Migration Service
description: Leer een online migratie uitvoeren van PostgreSQL on-premises naar Azure Database voor PostgreSQL met behulp van Azure Database Migration Service via de Azure-portal.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114007"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Zelfstudie: Migreren Naar Azure DB naar Azure DB voor PostgreSQL online met Behulp van DMS via de Azure-portal

U Azure Database Migration Service gebruiken om de databases te migreren van een on-premises PostgreSQL-instantie naar [Azure Database voor PostgreSQL](https://docs.microsoft.com/azure/postgresql/) met minimale downtime naar de toepassing. In deze zelfstudie migreert u de **voorbeelddatabase voor dvd-verhuur** van een on-premises instantie van PostgreSQL 9.6 naar Azure Database voor PostgreSQL met behulp van de onlinemigratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeldschema met behulp van het pg_dump hulpprogramma.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject in Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.
> * Migratiecutover uitvoeren.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, moet u een instantie maken op basis van de prijscategorie Premium. We versleutelen schijf om diefstal van gegevens te voorkomen tijdens het migratieproces

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [PostgreSQL community edition](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 of 10. De bron-PostgreSQL Server-versie moet 9.4, 9.5, 9.6, 10 of 11 zijn. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) (Ondersteunde versies van de PostgreSQL-database).

    Houd er ook rekening mee dat de doel-Azure Database voor PostgreSQL-versie gelijk moet zijn aan of later dan de on-premises PostgreSQL-versie. PostgreSQL 9.6 kan bijvoorbeeld migreren naar Azure Database voor PostgreSQL 9.6, 10 of 11, maar niet naar Azure Database voor PostgreSQL 9.5.

* [Maak een Azure Database voor PostgreSQL-server](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) of [Maak een Azure Database voor PostgreSQL - Hyperscale (Citus) server](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).
* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

    > [!NOTE]
    > Als u ExpressRoute gebruikt met netwerkpeering naar Microsoft, voegt u tijdens de installatie van het virtuele netwerk ExpressRoute met netwerkpeering aan Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Eindpunt voor opslag
    > * Eindpunt van servicebus
    >
    > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.

* Zorg ervoor dat de NSG-regels (Network Security Group) voor uw virtuele netwerk niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van het virtuele netwerk.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows-firewall zodat Azure Database Migration Service toegang heeft tot de bron PostgreSQL Server, die standaard TCP-poort 5432 is.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor de Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.
* Maak een firewallregel op [serverniveau](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor Azure Database voor PostgreSQL om Azure Database Migration Service toegang te geven tot de doeldatabases. Geef het subnetbereik op van het virtuele netwerk dat wordt gebruikt voor Azure Database Migration Service.
* Schakel logische replicatie in het bestand postgresql.config in en stel de volgende parameters in:

  * wal_level = **logical**
  * max_replication_slots = [aantal slots], raden u aan om **op vijf slots** in te stellen
  * max_wal_senders = [aantal gelijktijdige taken]: met de parameter max_wal_senders stelt u het aantal taken in dat gelijktijdig kan worden uitgevoerd. De aanbevolen instelling is **10 taken**

> [!IMPORTANT]
> Alle tabellen in uw bestaande database hebben een primaire sleutel nodig om ervoor te zorgen dat wijzigingen kunnen worden gesynchroniseerd met de doeldatabase.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen.

1. Gebruik de opdracht pg_dump -s om een dumpbestand van het schema te maken voor een database.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Bijvoorbeeld om een schemadumpbestand voor de **dvdrental-database** te maken:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Zie voor meer informatie over het gebruik van het hulpprogramma pg_dump de voorbeelden in de zelfstudie [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Maak een lege database maken in uw doelomgeving, dit is Azure Database for PostgreSQL.

    Zie het artikel Een Azure Database voor [PostgreSQL-server maken in de Azure-portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) of [Een Azure Database voor PostgreSQL - Hyperscale (Citus) maken in de Azure-portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)voor meer informatie over het maken van een database.

    > [!NOTE]
    > Een exemplaar van Azure Database voor PostgreSQL - Hyperscale (Citus) heeft slechts één database: **citus**.

3. Importeer het schema in de doeldatabase die u hebt gemaakt, door het dumpbestand van het schema te herstellen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Bijvoorbeeld:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Als u het drop foreign key script wilt extraheren en toevoegen op de bestemming (Azure Database for PostgreSQL), voert u het volgende script uit in PgAdmin of in psql.

   > [!IMPORTANT]
   > Buitenlandse sleutels in uw schema zorgen ervoor dat de initiële belasting en continue synchronisatie van de migratie mislukken.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.

6. Voer het script hieronder uit om triggers in doeldatabase uit te schakelen.

   > [!IMPORTANT]
   > Triggers (invoegen of bijwerken) in de gegevens afdwingen gegevensintegriteit in het doel vóór de gegevens die worden gerepliceerd vanaf de bron. Als gevolg hiervan wordt aanbevolen triggers uit te schakelen in alle tabellen **op het doel** tijdens de migratie en de triggers opnieuw in te schakelen nadat de migratie is voltooid.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam, het abonnement, een nieuwe of bestaande brongroep en de locatie voor de service op.

4. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron PostgreSQL-server en de doelAzure Database voor PostgreSQL-instantie.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecteer **Controleren + maken** om de service te maken.

   Het maken van service is binnen ongeveer 10 tot 15 minuten voltooid.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Services** naar de naam van de instantie Azure Database Migration Service die u hebt gemaakt, selecteer de instantie en selecteer + Nieuw **migratieproject**.

3. Geef in het scherm **Nieuw migratieproject** een naam voor het project op, selecteer in het tekstvak **Bronservertype** De optie **PostgresSQL**, selecteer in het tekstvak **Doelservertype** de optie **Azure Database voor PostgreSQL**.

4. Selecteer online **gegevensmigratie**in de sectie **Type activiteit kiezen** .

    ![Azure Database Migration Service-project maken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > U afwisselend **project maken kiezen** om nu het migratieproject te maken en de migratie later uit te voeren.

5. Selecteer **Opslaan,** noteer de vereisten om Azure Database Migration Service te gebruiken om gegevens te migreren en selecteer **activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails toevoegen** de verbindingsdetails op voor de bron PostgreSQL-instantie.

    ![Scherm Brondetails toevoegen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Doeldetails** de verbindingsgegevens op voor de doelhyperscale-server (Citus), het vooraf ingerichte exemplaar van Hyperscale (Citus) waaraan het **DVD Rentals-schema** is geïmplementeerd met behulp van pg_dump.

    ![Scherm Doeldetails](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    ![Kaart om het scherm van databases te targeten](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Selecteer **Opslaan**en accepteer vervolgens in het scherm **Migratie-instellingen** de standaardwaarden.

    ![Scherm Migratie-instellingen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Scherm migratieoverzicht](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster migratieactiviteit wordt weergegeven en de **status** van de activiteit moet worden bijgewerkt om te worden weergegeven als **Back-up in uitvoering**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

     ![Migratieproces bewaken](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Wanneer de migratie is voltooid, selecteert u onder **Databasenaam**een specifieke database om de migratiestatus voor **Volledige gegevensbelasting** en **Incrementele gegevenssynchronisatiebewerkingen** te krijgen.

   > [!NOTE]
   > **Volledige gegevensbelasting** toont de status van de initiële migratie van de belasting, terwijl **incrementele gegevenssynchronisatie** de STATUS 'Change data capture(CDC) weergeeft.

     ![Volledige gegevensbelastingsgegevens](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Incrementele gegevenssynchronisatiedetails](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

2. Wacht totdat het in **behandeling zijnde wijzigingenteller** **0** weergeeft om ervoor te zorgen dat alle binnenkomende transacties naar de brondatabase zijn gestopt, schakel het selectievakje **Bevestigen** in en schakel vervolgens **Toepassen in.**

    ![Compleet cutover-scherm](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Wanneer de status van de databasemigratie **Voltooid**weergeeft, verbindt u uw toepassingen met de nieuwe doelinstantie van Azure Database voor PostgreSQL.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for PostgreSQL.
* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel [Wat is Azure Database voor PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
