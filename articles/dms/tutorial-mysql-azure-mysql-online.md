---
title: 'Zelf studie: MySQL online migreren naar Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het uitvoeren van een online migratie van MySQL on-premises naar Azure Database for MySQL met behulp van Azure Database Migration Service.
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
ms.openlocfilehash: e9fc2913a526e01ea5279c476e3deab779db88c1
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609230"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Zelfstudie: MySQL online migreren naar Azure Database for MySQL met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de data bases van een on-premises MySQL-exemplaar te migreren naar [Azure database for MySQL](https://docs.microsoft.com/azure/mysql/) met minimale downtime. Met andere woorden, de migratie is mogelijk met minimale downtime van de toepassing. In deze zelf studie migreert u de voorbeeld database van **werk nemers** van een on-premises exemplaar van MySQL 5,7 naar Azure database for MySQL met behulp van een online migratie activiteit in azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Het voorbeeldschema migreren met behulp van het hulpprogramma mysqldump.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Een migratie project maken met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

> [!NOTE]
> Afwijking-vrije communicatie
>
> Micro soft biedt ondersteuning voor een gevarieerde en inbegrips omgeving. Dit artikel bevat verwijzingen naar het woord _Slave_. De micro soft- [stijl gids voor beschik bare communicatie](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) herkent deze als een uitsluitend woord. Het woord wordt in dit artikel gebruikt voor consistentie omdat het momenteel het woord is dat wordt weer gegeven in de software. Wanneer de software is bijgewerkt om het woord te verwijderen, wordt dit artikel zodanig bijgewerkt dat het in uitlijning is.
>


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Download en installeer [MySQL-communityversie](https://dev.mysql.com/downloads/mysql/) 5.6 of 5.7. De on-premises MySQL-versie moet overeenkomen met de Azure Database for MySQL-versie. MySQL 5.6 kan bijvoorbeeld alleen migreren naar Azure Database for MySQL 5.6 en kan niet worden geüpgraded naar 5.7.
* [Maak een exemplaar in Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Raadpleeg het artikel [MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen](https://docs.microsoft.com/azure/mysql/connect-workbench) voor informatie over hoe u een database verbindt en maakt met behulp van de Azure-portal.  
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van Azure Resource Manager implementatie model, waarmee u een site-naar-site-verbinding met uw on-premises bron servers kunt maken met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Raadpleeg de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)voor meer informatie over het maken van een virtueel netwerk, met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de installatie van een virtuele-networkNet gebruikmaakt van ExpressRoute met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > * Opslag eindpunt
    > * Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.

* Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk niet de volgende binnenkomende communicatie poorten blok keren tot Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van NSG verkeer van virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron MySQL-server, die standaard TCP-poort 3306 is.
* Wanneer u een firewall apparaat voor uw bron database (s) gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor de migratie.
* Maak een [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau voor Azure Database for MySQL om Azure database Migration service toegang tot de doel databases toe te staan. Geef het subnet-bereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
* De brondatabase van MySQL moet op een ondersteunde MySQL-communityversie zijn. Om de versie van het MySQL-exemplaar te bepalen, voert u in het MySQL-hulpprogramma of MySQL Workbench de volgende opdracht uit:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL ondersteunt alleen InnoDB-tabellen. Raadpleeg het artikel [Tabellen van MyISAM naar InnoDB converteren](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) om MyISAM-tabellen te converteren naar InnoDB.

* Schakel binaire logboekregistratie in het bestand my.ini (Windows) of my.cnf (Unix) in de brondatabase in met behulp van de volgende configuratie:

  * **server_id** = 1 of hoger (alleen relevant voor MySQL 5.6)
  * **logboek-bin** = \<path> (alleen relevant voor MySQL 5,6)    Bijvoorbeeld: log-bin = E:\ MySQL_logs \BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (het wordt aanbevolen geen nul te gebruiken; alleen relevant voor MySQL 5,6)
  * **Binlog_row_image** = full (alleen relevant voor MySQL 5.6)
  * **log_slave_updates** = 1

* De gebruiker moet beschikken over de rol ReplicationAdmin met de volgende bevoegdheden:

  * **REPLICATIECLIENT**: alleen vereist voor taken voor de verwerking van wijzigingen. Met andere woorden, voor taken voor volledig laden is deze bevoegdheid niet vereist.
  * **REPLICATIEREPLICA**: alleen vereist voor taken voor de verwerking van wijzigingen. Met andere woorden, voor taken voor volledig laden is deze bevoegdheid niet vereist.
  * **SUPER**: alleen vereist in eerdere versies dan MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Om alle databaseobjecten zoals tabelschema’s, indexen en opgeslagen procedures te voltooien, moeten we het schema uit de brondatabase extraheren en op de database toepassen. Om het schema te extraheren, kunt u mysqldump gebruiken met de parameter `--no-data`.

Ervan uitgaande dat u een voorbeeld database van MySQL- **werk nemers** in het on-premises systeem hebt, is de opdracht voor het uitvoeren van schema migratie met behulp van mysqldump:

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

Als u refererende sleutels in uw schema hebt, mislukken de eerste lading en doorlopende synchronisatie van de migratie.  Voer het volgende script uit in MySQL Workbench om het script voor refererende sleutels te extra heren en refererende-sleutel script toe te voegen.

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
> Als u gegevens importeert met behulp van een back-up, verwijdert u de opdrachten voor het maken van de opdracht hand matig of met behulp van de---overs laan voor het uitvoeren van een mysqldump. Voor het definiëren van de definitie moeten super bevoegdheden worden gemaakt en deze worden beperkt in Azure Database for MySQL.

Als u een trigger hebt in de gegevens (invoeg-of update-trigger), wordt de integriteit van gegevens in het doel vóór de gerepliceerde gegevens van de bron afgedwongen. Het is raadzaam triggers in alle tabellen in de doeldatabase uit te schakelen tijdens de migratie, en de triggers dan weer in te schakelen zodra de migratie is voltooid.

Als u triggers in de doel database wilt uitschakelen, gebruikt u de volgende opdracht:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Een DMS-exemplaar maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer een bestaand virtueel netwerk of maak een nieuwe.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel Azure SQL Database exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

5. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Op het scherm **Azure data base Migration Services** zoekt u de naam van Azure database Migration service exemplaar dat u hebt gemaakt en selecteert u vervolgens het exemplaar.

     ![Zoek uw exemplaar van Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **bronservertype** de optie **MySQL** en selecteer in het tekstvak **doelservertype** de optie **AzureDbForMySQL**.
5. Selecteer in de sectie **Het type activiteit kiezen** de optie **Onlinegegevensmigratie**

    ![Azure Database Migration Service-project maken](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > U kunt ook **project maken** selecteren om het migratie project nu te maken en de migratie later uitvoeren.

6. Selecteer **Opslaan**, noteer de vereisten om DMS succesvol te gebruiken om gegevens te migreren, en selecteer vervolgens **Activiteit maken en uitvoeren**.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails toevoegen** de verbindingsgegevens op voor het bronexemplaar van MySQL.

    ![Scherm Brondetails toevoegen](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan** en geef vervolgens in het scherm **Doeldetails** de verbindingsgegevens op voor de Azure Database for MySQL-doelserver. Dit is het vooraf ingerichte exemplaar van Azure Database for MySQL waarnaar het **Werknemers**-schema is geïmplementeerd met behulp van mysqldump.

    ![Scherm Doeldetails](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Hoewel u in deze stap meerdere data bases kunt selecteren, ondersteunt elk exemplaar van Azure Database Migration Service Maxi maal vier data bases voor gelijktijdige migratie. Er geldt ook een limiet van twee exemplaren van Azure Database Migration Service per regio in een abonnement. Als u bijvoorbeeld 40-data bases hebt om te migreren, kunt u alleen acht gelijktijdig migreren en alleen als u twee exemplaren van Azure Database Migration Service hebt gemaakt.

3. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster migratie activiteit wordt weer gegeven en de **status** van de activiteit wordt **geïnitialiseerd**.

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
* Zie het artikel [Wat is er Azure database Migration service?](https://docs.microsoft.com/azure/dms/dms-overview)voor informatie over Azure database Migration service.
* Zie het artikel [Wat is er Azure database for MySQL?](https://docs.microsoft.com/azure/mysql/overview)voor informatie over Azure database for MySQL.
