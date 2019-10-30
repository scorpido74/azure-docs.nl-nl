---
title: 'Zelf studie: Azure Database Migration Service gebruiken voor een online migratie van RDS MySQL naar Azure Database for MySQL | Microsoft Docs'
description: Meer informatie over het uitvoeren van een online migratie van RDS MySQL naar Azure Database for MySQL met behulp van de Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/28/2019
ms.openlocfilehash: 2df76c5906037fc5ce35e0c3a6558b0240c4b2be
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043307"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Zelf studie: RDS MySQL migreren naar Azure Database for MySQL online met behulp van DMS

U kunt Azure Database Migration Service gebruiken om data bases te migreren van een RDS MySQL-exemplaar naar [Azure database for MySQL](https://docs.microsoft.com/azure/mysql/) terwijl de bron database online blijft tijdens de migratie. Met andere woorden, migratie kan worden gerealiseerd met minimale downtime voor de toepassing. In deze zelf studie migreert u de voorbeeld database van **werk nemers** van een exemplaar van RDS MySQL naar Azure database for MySQL met behulp van de online migratie activiteit in azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Migreer het voorbeeld schema met behulp van de mysqldump-en MySQL-hulpprogram ma's.
> * Maak een instantie van Azure Database Migration Service.
> * Een migratie project maken met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt beschreven hoe u een online migratie uitvoert vanaf een exemplaar van RDS MySQL naar Azure Database for MySQL.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Zorg ervoor dat op de MySQL-bron server een ondersteunde MySQL-Community-editie wordt uitgevoerd. Als u de versie van uw MySQL-exemplaar wilt bepalen, voert u in het hulp programma MySQL of MySQL Workbench de volgende opdracht uit:

    ```
    SELECT @@version;
    ```

    Zie voor meer informatie het artikel [Azure database for mysql versies ondersteund](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Down load en installeer de [voorbeeld database van MySQL- **werk nemers** ](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Maak een instantie van [Azure database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Een Azure Virtual Network (VNet) maken voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-connectiviteit met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Voor meer informatie over het maken van een VNet raadpleegt u de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)en met name de Quick Start-artikelen met stapsgewijze Details.
* Zorg ervoor dat de regels van uw VNet-netwerk beveiligings groep niet de volgende binnenkomende communicatie poorten blok keren naar Azure Database Migration Service: 443, 53, 9354, 445 en 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG-verkeer van Azure VNet.
* Configureer uw [Windows Firewall](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (of uw Linux-firewall) om toegang tot de data base-engine mogelijk te maken. Voor MySQL-server is poort 3306 voor connectiviteit toegestaan.

> [!NOTE]
> Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Als u MyISAM-tabellen wilt converteren naar InnoDB, raadpleegt u het artikel over het [converteren van tabellen van MyISAM naar InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>AWS RDS MySQL instellen voor replicatie

1. Als u een nieuwe parameter groep wilt maken, volgt u de instructies van AWS in het artikel [MySQL-database logboek bestanden](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), in de sectie **binaire logboek registratie-indeling** .
2. Maak een nieuwe parameter groep met de volgende configuratie:
    * binlog_format = rij
    * binlog_checksum = geen
3. Sla de nieuwe parameter groep op.
4. Koppel de nieuwe parameter groep aan het exemplaar van het RDS MySQL. Opnieuw opstarten is mogelijk vereist.

## <a name="migrate-the-schema"></a>Het schema migreren

1. Pak het schema uit van de bron database en pas deze toe op de doel database om de migratie van alle database objecten, zoals tabel schema's, indexen en opgeslagen procedures, te volt ooien.

    De eenvoudigste manier om alleen het schema te migreren, is door mysqldump te gebruiken met de para meter--no-data. De opdracht voor het migreren van het schema is:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Als u bijvoorbeeld een schema bestand voor de Data Base **werk nemers** wilt dumpen, gebruikt u de volgende opdracht:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importeer het schema naar de doel service, die Azure Database for MySQL is. Voer de volgende opdracht uit om het schema dump bestand te herstellen:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Als u bijvoorbeeld het schema voor de Data Base **werk nemers** wilt importeren:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie. Als u het script voor refererende sleutels wilt extra heren en een refererende-sleutel script wilt toevoegen aan de bestemming (Azure Database for MySQL), voert u het volgende script uit in MySQL Workbench:

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

4. Voer de refererende sleutel (die de tweede kolom is) in het query resultaat uit om de refererende sleutel te verwijderen.

5. Als u triggers hebt (invoeg-of update-trigger) in de gegevens, wordt de integriteit van gegevens in het doel afgedwongen voordat gegevens van de bron worden gerepliceerd. De aanbeveling is om tijdens de migratie triggers in alle tabellen *op het doel* uit te scha kelen en de triggers vervolgens in te scha kelen nadat de migratie is voltooid.

    Triggers uitschakelen in doel database:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Als er exemplaren van het ENUM-gegevens type in tabellen zijn, raden we u aan om het data type ' character varieerde ' in de doel tabel tijdelijk bij te werken. Wanneer de gegevens replicatie is voltooid, herstelt u het gegevens type in ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **resource providers**.

    ![Resourceproviders weergeven](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een instantie van Azure Database Migration Service maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service** **Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarin u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand VNet of maak een nieuw account.

    Het VNet biedt Azure Database Migration Service toegang tot het bron-MySQL-exemplaar en het doel-Azure Database for MySQL exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een VNet in de Azure Portal.

6. Selecteer een prijs categorie. Zorg ervoor dat u voor deze online migratie de prijs categorie Premium: 4vCores selecteert.

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

     ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratie project** een naam op voor het project, selecteer **MySQL**in het tekstvak **type bron server** en selecteer vervolgens in het tekstvak **doel server type** **AzureDbForMySQL**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**.

    > [!IMPORTANT]
    > Zorg ervoor dat u **Online gegevens migratie**selecteert. offline migraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > U kunt ook **project maken** selecteren om het migratie project nu te maken en de migratie later uitvoeren.

6. Selecteer **Opslaan**.

7. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    > [!NOTE]
    > Noteer de vereisten die nodig zijn om online migratie in te stellen op de Blade voor het maken van een project.

## <a name="specify-source-details"></a>Geef brondetails op

* Geef in het detail scherm van de **migratie bron** de verbindings Details op voor het bron-mysql-exemplaar.

   ![Brondetails](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens in het scherm **doel Details** de verbindings gegevens op voor de doel Azure database for mysql server, die vooraf is ingericht en waarvoor het schema **Employees** is geïmplementeerd met behulp van MySQLDump.

    ![Doel selecteren](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

    ![Activiteitsstatus-uitvoering](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Selecteer onder **database naam**een specifieke data base om de migratie status voor **volledige gegevens belasting** en **incrementele gegevens synchronisatie** te verkrijgen.

    Bij **volledige gegevens belasting** wordt de initiële status van de laad migratie weer gegeven, terwijl **incrementele gegevens synchronisatie** de status Change Data Capture (CDC) laat zien.

    ![Inventaris scherm-volledige belasting van gegevens](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Inventarisatie scherm-incrementele gegevens synchronisatie](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige belasting is voltooid, worden de data bases als **gereed voor Cutover**gemarkeerd.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Beginnen met knippen](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de status van de database migratie **is voltooid**, verbindt u uw toepassingen met de nieuwe doel-Azure database for MySQL data base.

Uw online migratie van een on-premises exemplaar van MySQL naar Azure Database for MySQL is nu voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) voor informatie over de Azure Database Migration Service.
* Zie het artikel [Wat is er Azure database for MySQL?](https://docs.microsoft.com/azure/mysql/overview)voor informatie over Azure database for MySQL.
* Voor andere vragen moet u een e-mail sturen naar de [Azure data base-migratie](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
