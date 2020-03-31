---
title: 'Zelfstudie: RDS MySQL online migreren naar Azure Database voor MySQL'
titleSuffix: Azure Database Migration Service
description: Leer een online migratie uitvoeren van RDS MySQL naar Azure Database voor MySQL met behulp van de Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255166"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Zelfstudie: RDS MySQL migreren naar Azure Database voor MySQL online met DMS

U Azure Database Migration Service gebruiken om databases te migreren van een RDS MySQL-exemplaar naar [Azure Database voor MySQL,](https://docs.microsoft.com/azure/mysql/) terwijl de brondatabase online blijft tijdens migratie. Met andere woorden, migratie kan worden bereikt met minimale downtime naar de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **werknemers** van een instantie van RDS MySQL naar Azure Database voor MySQL met behulp van de online migratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeldschema met behulp van de mysqldump- en mysql-hulpprogramma's.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, moet u een instantie maken op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een onlinemigratie uitvoert van een exemplaar van RDS MySQL naar Azure Database voor MySQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Controleer of de bron-MySQL-server een ondersteunde MySQL-community-editie uitvoert. Voer de opdracht uit om de versie van uw MySQL-exemplaar te bepalen in het hulpprogramma mysql of MySQL Workbench:

    ```
    SELECT @@version;
    ```

    Zie het artikel [Ondersteunde Azure Database voor MySQL-versies voor](https://docs.microsoft.com/azure/mysql/concepts-supported-versions)meer informatie.

* Download en installeer de [MySQL **Employees** sample database](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Een exemplaar van [Azure Database voor MySQL maken](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.
* Zorg ervoor dat de regels van uw beveiligingsgroep voor virtuele netwerken niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445 en 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
* Configureer uw [Windows Firewall](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (of uw Linux-firewall) om toegang tot database-engine toe te staan. Voor MySQL-server u poort 3306 toestaan voor connectiviteit.

> [!NOTE]
> Azure Database voor MySQL ondersteunt alleen InnoDB-tabellen. Als u MyISAM-tabellen wilt converteren naar InnoDB, raadpleegt u het artikel [Tabellen converteren van MyISAM naar InnoDB.](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)

### <a name="set-up-aws-rds-mysql-for-replication"></a>AWS RDS MySQL instellen voor replicatie

1. Als u een nieuwe parametergroep wilt maken, volgt u de instructies van AWS in het artikel [MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)in de sectie **Binaire logboekindeling.**
2. Maak een nieuwe parametergroep met de volgende configuratie:
    * binlog_format = row
    * binlog_checksum = NONE
3. Sla de nieuwe parametergroep op.
4. Koppel de nieuwe parametergroep aan de instantie RDS MySQL. Een reboot kan nodig zijn.

## <a name="migrate-the-schema"></a>Het schema migreren

1. Haal het schema uit de brondatabase en pas de doeldatabase toe om de migratie van alle databaseobjecten, zoals tabelschema's, indexen en opgeslagen procedures, te voltooien.

    De eenvoudigste manier om alleen het schema te migreren, is door mysqldump te gebruiken met de parameter --no-data. De opdracht om het schema te migreren is:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Als u bijvoorbeeld een schemabestand voor de database Werknemers wilt **dumpen,** gebruikt u de volgende opdracht:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importeer het schema naar doelservice, wat Azure Database voor MySQL is. Voer de volgende opdracht uit om het schemadumpbestand te herstellen:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Bijvoorbeeld om het schema voor **de** werknemersdatabase te importeren:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Als u het drop foreign key script wilt extraheren en een buitenlands sleutelscript op de bestemming wilt toevoegen (Azure Database for MySQL), voert u het volgende script uit in MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Voer de drop-buitenlandse sleutel (de tweede kolom) uit in het queryresultaat om de buitenlandse sleutel te laten vallen.

5. Als u triggers (trigger invoegen of bijwerken) in de gegevens hebt, wordt de gegevensintegriteit in het doel afgedwongen voordat gegevens van de bron worden gerepliceerd. De aanbeveling is om triggers uit te schakelen in alle tabellen *op het doel* tijdens de migratie en vervolgens de triggers in te schakelen nadat de migratie is voltooid.

    Triggers in doeldatabase uitschakelen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Als er exemplaren van het ENUM-gegevenstype in tabellen staan, raden we u aan tijdelijk bij te werken naar het gegevenstype 'tekenwisselen' in de doeltabel. WHen-gegevensreplicatie is voltooid en zet het gegevenstype terug naar ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een instantie van Azure Database Migration Service maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waar u de instantie van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron MySQL-instantie en de doelAzure Database voor MySQL-instantie.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijsniveau; Voor deze online migratie moet u de prijscategorie Premium: 4vCores selecteren.

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

     ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam voor het project op, selecteer in het tekstvak **Bronservertype** De optie **MySQL**en selecteer vervolgens in het tekstvak **Doelservertype** de optie **AzureDbForMySQL**.
5. Selecteer online **gegevensmigratie**in de sectie **Type activiteit kiezen** .

    > [!IMPORTANT]
    > Zorg ervoor dat u **Online gegevensmigratie**selecteert; offlinemigraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > U afwisselend **project maken kiezen** om nu het migratieproject te maken en de migratie later uit te voeren.

6. Selecteer **Opslaan**.

7. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    > [!NOTE]
    > Houd rekening met de vereisten die nodig zijn om online migratie in het projectcreatieblad in te stellen.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het detailscherm **Migratiebron** de verbindingsdetails op voor de bron MySQL-instantie.

   ![Brondetails](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens op het scherm **Doeldetails** de verbindingsgegevens op voor de doelAzure-database voor MySQL-server, die vooraf is ingericht en het **werknemersschema** heeft geïmplementeerd met MySQLDump.

    ![Doel selecteren](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

    ![Activiteitsstatus - actief](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Selecteer **onder DATABASENAAM**een specifieke database om de migratiestatus voor **Volledige gegevensbelasting** en **Incrementele gegevenssynchronisatiebewerkingen** te krijgen.

    **Volledige gegevensbelasting** toont de status van de initiële migratie van de belasting, terwijl **incrementele gegevenssynchronisatie** de STATUS 'Change data capture(CDC) weergeeft.

    ![Voorraadscherm - volledige gegevensbelasting](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Voorraadscherm - incrementele gegevenssynchronisatie](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige belasting is voltooid, worden de databases gemarkeerd **met Ready to Cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Begin te snijden over](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de status van de databasemigratie **Voltooid**weergeeft, verbindt u uw toepassingen met de nieuwe doelAzure Database voor MySQL-database.

Uw online migratie van een on-premises instantie van MySQL naar Azure Database voor MySQL is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel Wat is Azure [Database voor MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Voor andere vragen u een e-mail sturen naar de alias [Azure Database Migrations vragen.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
