---
title: 'Zelf studie: RDS SQL Server online migreren naar SQL Database'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het uitvoeren van een online migratie van RDS SQL Server naar Azure SQL Database enkele data base of een beheerd exemplaar met behulp van de Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 20ca7f1d9c8322fe9a4d5dd784768bdaaf7cd0d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314940"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Zelf studie: RDS-SQL Server migreren naar Azure SQL Database of een Azure SQL Managed instance online met behulp van DMS

U kunt de Azure Database Migration Service gebruiken om de data bases van een RDS SQL Server-exemplaar te migreren naar [Azure SQL database](https://docs.microsoft.com/azure/sql-database/) of een [Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) met minimale downtime. In deze zelf studie migreert u de **Adventureworks2012** -data base die is hersteld naar een RDS SQL Server exemplaar van SQL Server 2012 (of hoger) naar SQL database of een door SQL beheerd exemplaar met behulp van de Azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Maak een data base in Azure SQL Database of een door SQL beheerd exemplaar. 
> * Het voorbeeldschema migreren met behulp van de Data Migration Assistant.
> * De Azure-portal gebruiken om een Azure Database Migration Service-exemplaar te maken.
> * Een migratieproject maken met behulp van de Azure Database Migration Service.
> * De migratie uitvoeren.
> * De migratie controleren.
> * Een migratierapport downloaden.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van de Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een online migratie van RDS SQL Server naar SQL Database of een door SQL beheerd exemplaar beschreven.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Maak een [RDS SQL Server-database](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Maak een data base in Azure SQL database in het Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) of [Maak een data base in SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)en maak een lege data base met de naam **AdventureWorks2012**. 
* Download en installeer de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 of hoger.
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatie model. Als u migreert naar een beheerd exemplaar van SQL, moet u ervoor zorgen dat u het DMS-exemplaar maakt in hetzelfde virtuele netwerk dat wordt gebruikt voor het beheerde exemplaar van SQL, maar in een ander subnet.  Als u een ander virtueel netwerk gebruikt voor DMS, moet u ook een virtueel netwerk peering maken tussen de twee virtuele netwerken. Raadpleeg de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)voor meer informatie over het maken van een virtueel netwerk, met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de installatie van het virtuele netwerk ExpressRoute gebruikt met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > * Opslag eindpunt
    > * Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat de Azure Database Migration Service geen verbinding met internet heeft. 

* Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk niet de volgende binnenkomende communicatie poorten blok keren tot Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG verkeer van virtuele netwerken.
* Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows-firewall voor toegang voor de Azure Database Migration Service tot de SQL Server-bron, die standaard TCP-poort 1433 gebruikt.
* Maak voor SQL Database een [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau om de Azure database Migration service toegang tot de doel database toe te staan. Geef het subnet-bereik van het virtuele netwerk op dat wordt gebruikt voor de Azure Database Migration Service.
* Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het bronexemplaar voor RDS SQL Server gekoppeld zijn aan een account dat lid is van de serverrol 'Processadmin' en van de databaserollen 'db_owner' op alle databases die moeten worden gemigreerd.
* Zorg ervoor dat de referenties die worden gebruikt om verbinding te maken met de doel database, de machtiging beheer data base hebben voor de doel database in SQL Database en een lid van de rol sysadmin als deze wordt gemigreerd naar een data base in SQL Managed instance.
* De versie van de RDS SQL-bronserver moet SQL Server 2012 of hoger zijn. Zie het artikel [Hoe de versie, de editie en het updateniveau van de SQL-server en de bijbehorende onderdelen worden bepaald](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an) om te bepalen welke versie van uw SQL Server-exemplaar wordt uitgevoerd.
* Inschakelen van Change Data Capture (CDC) op de RDS SQL Server-database en alle gebruikerstabellen die geselecteerd zijn voor migratie.
    > [!NOTE]
    > U kunt het onderstaande script gebruiken om CDC in te schakelen op een RDS SQL Server-database.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > U kunt het onderstaande script gebruiken om CDC in te schakelen op alle tabellen.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Schakel database triggers uit in de doel database.
    > [!NOTE]
    > U kunt de database triggers vinden in de doel database met behulp van de volgende query:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Zie het artikel [TRIGGER UITSCHAKELEN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) voor meer informatie.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren
Gebruik DMA om het schema te migreren.

> [!NOTE]
> Voordat u een migratie project in DMA maakt, moet u ervoor zorgen dat u al een data base in SQL Database of SQL Managed instance hebt ingericht zoals vermeld in de vereisten. In deze zelf studie wordt ervan uitgegaan dat de naam van de Data Base **AdventureWorks2012**is, maar u kunt elke gewenste naam opgeven.

Voer de volgende stappen uit om het **AdventureWorks2012** -schema te migreren:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens onder **Projecttype****Migratie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server**en in het tekstvak **Doelservertype****Azure SQL Database**.

    > [!NOTE]
    > Selecteer **Azure SQL database** voor het doel server type om te migreren naar zowel Azure SQL database als en naar SQL Managed instance.

3. Selecteer onder **Migratiebereik**, selecteer **Alleen schema**.

    Na het uitvoeren van de vorige stappen, moet de DMA-interface worden weergegeven zoals in de volgende afbeelding:

    ![Data Migration Assistant-Project maken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Selecteer **Maken** om het project te maken.
5. Geef in DMA de details van de bronverbinding voor uw SQL Server op, selecteer **Verbinden** en selecteer vervolgens de database **AdventureWorks2012**.

    ![Details Data Migration Assistant Bronverbinding](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Selecteer **volgende**onder **verbinding maken met doel server**de doel verbindings gegevens voor de data base in SQL database of SQL Managed instance, selecteer **verbinden**en selecteer vervolgens de **AdventureWorksAzure** -data base die u vooraf hebt ingericht.

    ![Details Data Migration Assistant-doelverbinding](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Selecteer **volgende** om door te gaan naar het scherm **objecten selecteren** waarop u de schema objecten kunt opgeven in de **AdventureWorks2012** -data base die moet worden geïmplementeerd.

    Standaard worden alle objecten geselecteerd.

    ![SQL-scripts genereren](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Selecteer **SQL-script genereren** voor het maken van de SQL-scripts en controleer vervolgens de scripts op fouten.

    ![Schemascript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Selecteer **schema implementeren** om het schema te implementeren en Controleer na de implementatie van het schema het doel voor eventuele afwijkingen.

    ![Schema implementeren](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken. 

5. Selecteer een bestaand virtueel netwerk of maak een nieuwe.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en de doel-SQL Database of het door SQL beheerde exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

6. Selecteer een prijscategorie. Zorg dat u de prijscategorie Premium selecteert voor deze onlinemigratie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

     ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Zoek alle exemplaren van de Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Zoek in het scherm **Azure Database Migration service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens het exemplaar.

     ![Zoek uw exemplaar van de Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Selecteer + **Nieuw migratieproject**.
4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, selecteer in het tekstvak **Bronservertype** de optie **AWS RDS voor SQL Server** en selecteer in het tekstvak **Doelservertype** de optie **Azure SQL Database**.

    > [!NOTE]
    > Selecteer **Azure SQL database** voor het doel server type om te migreren naar zowel SQL database als en naar SQL Managed instance.

5. Selecteer in de sectie **type activiteit kiezen** de optie **Online gegevens migratie**.

    > [!IMPORTANT]
    > Zorg ervoor dat u **Online gegevens migratie**selecteert. offline migraties worden niet ondersteund voor dit scenario.

    ![Azure Database Migration Service-project maken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > U kunt ook **project maken** selecteren om het migratie project nu te maken en de migratie later uitvoeren.

6. Selecteer **Opslaan**.

7. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

    ![Een Azure Database Migration Service-activiteit maken en uitvoeren](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Migratiebron** de details van de verbinding op voor het bron-SQL Server-exemplaar.

    Zorg ervoor dat u een Fully Qualified Domain Name (FQDN) gebruikt voor de naam van het bron-SQL Server-exemplaar.

2. Als u geen vertrouwd certificaat op de bronserver hebt geïnstalleerd, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die zijn versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met zelfondertekende certificaten in een productie omgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**en geef vervolgens in het scherm **Details van migratie doel** de verbindings Details op voor de doel database in Azure.

    ![Doel selecteren](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in de Azure Database Migration Service de doeldatabase standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Selecteer **Opslaan**in het scherm **Tabellen selecteren**, vouw de tabellenlijst uit en controleer de lijst met betrokken velden.

    Met de Azure Database Migration Service automatisch worden alle lege bron tabellen geselecteerd die voor komen in de doel database. Als u tabellen die al gegevens bevatten terug wilt migreren, moet u expliciet de tabellen op dit scherm selecteren.

    ![Selecteer tabellen](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Selecteer **Opslaan** nadat u de volgende **Geavanceerde instellingen voor onlinemigratie** hebt ingesteld.

    | Instelling | Beschrijving |
    | ------------- | ------------- |
    | **Maximale aantal tabellen om parallel te laden** | Geeft het aantal tabellen aan dat DMS gelijktijdig tijdens de migratie uitvoert. De standaardwaarde is 5, maar deze kan worden ingesteld op een optimale waarde om te voldoen aan specifieke migratiebehoeften op basis van elke POC-migratie. |
    | **Wanneer de brontabel wordt afgekapt** | Geeft aan of DMS de doeltabel tijdens de migratie afkapt. Deze instelling kan nuttig zijn als een of meer tabellen worden afgekapt als onderdeel van het migratie proces. |
    | **Instellingen voor LOB-gegevens (Large Objects, grote objecten) configureren** | Geeft aan of DMS onbeperkte LOB-gegevens migreert of de gemigreerde LOB-gegevens tot een specifieke grootte beperkt.  Wanneer er een limiet is voor de LOB-gegevens die zijn gemigreerd, worden alle LOB-gegevens die groter zijn dan die limiet afgekapt. Voor productiemigraties is het raadzaam om **Onbeperkte LOB-grootte toestaan** te selecteren om gegevensverlies te voorkomen. Selecteer om onbeperkte LOB-grootte toe te staan het selectievakje **Migrate LOB data in a single block when the LOB size is less than (KB) specified** (LOB-gegevens in één blok migreren wanneer de LOB-grootte kleiner is dan het opgegeven aantal KB) voor betere prestaties. |

    ![Geavanceerde instellingen voor onlinemigratie instellen](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Selecteer **Opslaan**, geef op het scherm **Migratieoverzicht** in het tekstvak **Naam activiteit** een naam op voor de migratieactiviteit, en controleer vervolgens het overzicht om te verzekeren dat de bron- en doeldetails overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven en de **Status** van de activiteit is **Initialiseren**.

    ![Activiteitsstatus - initialiseren](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Actief** wordt weergegeven.

2. Klik op een specifieke database om de migratiestatus voor de bewerkingen **Alle gegevens worden geladen** en **Incrementele gegevenssynchronisatie** te bekijken.

    ![Activiteitsstatus - actief](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de eerste volledige lading is voltooid, worden de databases gemarkeerd als **Gereed voor cutover**.

1. Wanneer u klaar bent om de databasemigratie te voltooien, selecteert u **Cutover starten**.

    ![Cutover starten](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Zorg dat u alle transacties stopt die bij de brondatabase binnenkomen; wacht totdat de teller van **Wijzigingen in behandeling** op **0** staat.
3. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.
4. Wanneer de status van de database migratie **is voltooid**, verbindt u uw toepassingen met de nieuwe doel database.

    ![Activiteitsstatus - voltooid](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [bekende problemen en tijdelijke oplossingen met online migraties](known-issues-azure-sql-online.md)voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure.
* Zie het artikel [Wat is de database Migration service?](https://docs.microsoft.com/azure/dms/dms-overview)voor informatie over de database Migration service.
* Zie het artikel [Wat is de SQL database-service?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)voor informatie over SQL database.
* Zie het artikel [Wat is SQL Managed instance](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview)voor meer informatie over SQL Managed instances.
