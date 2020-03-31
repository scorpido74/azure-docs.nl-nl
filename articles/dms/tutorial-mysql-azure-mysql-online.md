---
title: 'Zelfstudie: MySQL online migreren naar Azure Database voor MySQL'
titleSuffix: Azure Database Migration Service
description: Leer een online migratie uitvoeren van MySQL on-premises naar Azure Database voor MySQL met Behulp van Azure Database Migration Service.
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
ms.openlocfilehash: 7c8087a01bb71657e816be89b6a562dd4783b271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240744"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Zelfstudie: MySQL online migreren naar Azure Database for MySQL met behulp van DMS

U Azure Database Migration Service gebruiken om de databases te migreren van een on-premises MySQL-instantie naar [Azure Database voor MySQL](https://docs.microsoft.com/azure/mysql/) met minimale downtime. Met andere woorden, de migratie is mogelijk met minimale downtime van de toepassing. In deze zelfstudie migreert u de voorbeelddatabase **werknemers** van een on-premises instantie van MySQL 5.7 naar Azure Database voor MySQL met behulp van een onlinemigratieactiviteit in Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Het voorbeeldschema migreren met behulp van het hulpprogramma mysqldump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, moet u een instantie maken op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [MySQL-communityversie](https://dev.mysql.com/downloads/mysql/) 5.6 of 5.7. De on-premises MySQL-versie moet overeenkomen met de Azure Database for MySQL-versie. MySQL 5.6 kan bijvoorbeeld alleen migreren naar Azure Database for MySQL 5.6 en kan niet worden geüpgraded naar 5.7.
* [Maak een exemplaar in Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Raadpleeg het artikel [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen](https://docs.microsoft.com/azure/mysql/connect-workbench) voor informatie over hoe u een database verbindt en maakt met behulp van de Azure-portal.  
* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

    > [!NOTE]
    > Voeg tijdens de installatie van virtual networkNet ExpressRoute met netwerkpeering naar Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Eindpunt voor opslag
    > * Eindpunt van servicebus
    >
    > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.

* Zorg ervoor dat de regels van uw beveiligingsgroep voor virtuele netwerken niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van het virtuele netwerk.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows-firewall zodat Azure Database Migration Service toegang heeft tot de bron MySQL Server, die standaard TCP-poort 3306 is.
* Wanneer u een firewalltoestel gebruikt voor uw brondatabase(s), moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.
* Maak een firewallregel op [serverniveau](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor Azure Database voor MySQL om Azure Database Migration Service toegang te geven tot de doeldatabases. Geef het subnetbereik op van het virtuele netwerk dat wordt gebruikt voor Azure Database Migration Service.
* De brondatabase van MySQL moet op een ondersteunde MySQL-communityversie zijn. Om de versie van het MySQL-exemplaar te bepalen, voert u in het MySQL-hulpprogramma of MySQL Workbench de volgende opdracht uit:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Raadpleeg het artikel [Tabellen van MyISAM naar InnoDB converteren](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) om MyISAM-tabellen te converteren naar InnoDB.

* Schakel binaire logboekregistratie in het bestand my.ini (Windows) of my.cnf (Unix) in de brondatabase in met behulp van de volgende configuratie:

  * **server_id** = 1 of hoger (alleen relevant voor MySQL 5.6)
  * **logboekopslagpad** = \<> (alleen relevant voor MySQL 5.6) Bijvoorbeeld: logboekopslag = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (het wordt aanbevolen om geen nul te gebruiken; alleen relevant voor MySQL 5.6)
  * **Binlog_row_image** = full (alleen relevant voor MySQL 5.6)
  * **log_slave_updates** = 1

* De gebruiker moet beschikken over de rol ReplicationAdmin met de volgende bevoegdheden:

  * **REPLICATIECLIENT**: alleen vereist voor taken voor de verwerking van wijzigingen. Met andere woorden, voor taken voor volledig laden is deze bevoegdheid niet vereist.
  * **REPLICATIEREPLICA**: alleen vereist voor taken voor de verwerking van wijzigingen. Met andere woorden, voor taken voor volledig laden is deze bevoegdheid niet vereist.
  * **SUPER**: alleen vereist in eerdere versies dan MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen. Om het schema te extraheren, kunt u mysqldump gebruiken met de parameter `--no-data`.

Ervan uitgaande dat u een **MySQL** Employees-voorbeelddatabase in het on-premises systeem hebt, is de opdracht om schemamigratie uit te voeren met mysqldump:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Bijvoorbeeld:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Voer de volgende opdracht uit om het schema te importeren in de Azure Database for MySQL-doeldatabase:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Bijvoorbeeld:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie.  Voer het volgende script uit in MySQL Workbench om het drop foreign key script te extraheren en het externe sleutelscript toe te voegen.

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

Voer het script voor verwijdering van refererende sleutels (de tweede kolom) in het queryresultaat uit om de refererende sleutel te verwijderen.

> [!IMPORTANT]
> Als u gegevens importeert met een back-up, verwijdert u de opdrachten DEFINER MAKEN handmatig of gebruikt u de opdracht --overslaan-definer bij het uitvoeren van een mysqldump. DEFINER vereist superbevoegdheden om te maken en is beperkt in Azure Database voor MySQL.

Als u een trigger in de gegevens hebt (trigger invoegen of bijwerken), wordt de gegevensintegriteit in het doel afdwingd voor de gerepliceerde gegevens van de bron. Het is raadzaam triggers in alle tabellen in de doeldatabase uit te schakelen tijdens de migratie, en de triggers dan weer in te schakelen zodra de migratie is voltooid.

Als u triggers in de doeldatabase wilt uitschakelen, gebruikt u de volgende opdracht:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en de doelstelling Azure SQL Database instance.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Services** naar de naam van de instantie Azure Database Migration Service die u hebt gemaakt en selecteer vervolgens de instantie.

     ![Uw instantie van Azure Database Migration Service zoeken](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **bronservertype** de optie **MySQL** en selecteer in het tekstvak **doelservertype** de optie **AzureDbForMySQL**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**

    ![Azure Database Migration Service-project maken](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > U afwisselend **project maken kiezen** om nu het migratieproject te maken en de migratie later uit te voeren.

6. Selecteer **Opslaan**, noteer de vereisten om DMS succesvol te gebruiken om gegevens te migreren, en selecteer vervolgens **Activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor het bronexemplaar van MySQL.

    ![Scherm Brondetails toevoegen](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan** en geef vervolgens in het scherm **Doeldetails** de verbindingsgegevens op voor de Azure Database for MySQL-doelserver. Dit is het vooraf ingerichte exemplaar van Azure Database for MySQL waarnaar het **Werknemers**-schema is geïmplementeerd met behulp van mysqldump.

    ![Scherm Doeldetails](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    ![Toewijzen aan doeldatabases](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Hoewel u in deze stap meerdere databases selecteren, ondersteunt elk exemplaar van Azure Database Migration Service maximaal vier databases voor gelijktijdige migratie. Er is ook een limiet van twee exemplaren van Azure Database Migration Service per regio in een abonnement. Als u bijvoorbeeld 40 databases hebt om te migreren, u er slechts acht tegelijk migreren en alleen als u twee exemplaren van Azure Database Migration Service hebt gemaakt.

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster migratieactiviteit wordt weergegeven en de **status** van de activiteit **initialiseert**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de migratieactiviteit de optie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

     ![Activiteitenstatus: Voltooid](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Selecteer onder **Databasenaam** een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    ‘Alle gegevens worden geladen’ toont de migratiestatus van de eerste lading terwijl ‘Incrementele gegevenssynchronisatie’ de CDC-status (Change Data Capture) toont.

     ![Activiteitenstatus: Volledig geladen](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Activiteitenstatus: Incrementele gegevenssynchronisatie](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Cutover starten](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de databasemigratiestatus **Voltooid** toont, verbindt u uw toepassingen met de nieuwe doel-Azure SQL Database.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg het artikel [Bekende problemen/beperkingen met online migraties naar Azure Database for MySQL](known-issues-azure-mysql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure Database for MySQL.
* Zie het artikel [Wat is Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Zie het artikel Wat is Azure [Database voor MySQL?](https://docs.microsoft.com/azure/mysql/overview).
