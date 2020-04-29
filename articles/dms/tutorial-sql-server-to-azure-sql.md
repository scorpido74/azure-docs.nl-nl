---
title: 'Zelf studie: SQL Server offline migreren naar een SQL-data base'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren van SQL Server on-premises naar een enkele data base of gegroepeerde Data base in Azure SQL Database offline door gebruik te maken van Azure Database Migration Service.
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
ms.openlocfilehash: ff47246482bd0712ea4e741d44b12f2c6767380b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298916"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Zelf studie: SQL Server naar een enkele data base of gegroepeerde Data base in Azure SQL Database offline migreren met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de data bases te migreren van een on-premises SQL Server instantie naar [Azure SQL database](https://docs.microsoft.com/azure/sql-database/). In deze zelf studie migreert u de **Adventureworks2012** -data base die is hersteld naar een on-premises exemplaar van SQL Server 2016 (of hoger) naar een enkele data base of gegroepeerde Data base in Azure SQL database met behulp van Azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - Uw on-premises database evalueren met behulp van de Data Migration Assistant.
> - Het voorbeeldschema migreren met behulp van de Data Migration Assistant.
> - Maak een exemplaar van de Azure Database Migration Service.
> - Een migratie project maken met behulp van Azure Database Migration Service.
> - De migratie uitvoeren.
> - De migratie controleren.
> - Een migratierapport downloaden.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een offline migratie beschreven vanuit SQL Server naar een individuele of pooldatabase in Azure SQL Database. Zie [SQL Server online migreren naar Azure SQL Database met behulp van DMS](tutorial-sql-server-azure-sql-online.md) voor informatie over een online migratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Down load en Installeer [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads).
- Schakel het TCP/IP-protocol in, dat standaard is uitgeschakeld tijdens de installatie van SQL Server Express, door de instructies in het artikel [In- of uitschakelen van een Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) te volgen.
- Maak een individuele of pooldatabase in Azure SQL Database. Dit doet u door de stappen te volgen in het artikel [Een individuele database in Azure SQL Database maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Als u SQL Server Integration Services (SSIS) gebruikt en de catalogusdatabase voor uw SSIS-projecten/-pakketten (SSISDB) wilt migreren van SQL Server naar Azure SQL Database, wordt de doel-SSISDB automatisch namens u gemaakt en beheerd wanneer u SSIS in Azure Data Factory (ADF) inricht. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.
  
- Download en installeer de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 of hoger.
- Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Raadpleeg de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)voor meer informatie over het maken van een virtueel netwerk, met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de installatie van het virtuele netwerk ExpressRoute gebruikt met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > - Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > - Opslag eindpunt
    > - Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.
    >
    >Als u geen site-naar-site-verbinding hebt tussen het on-premises netwerk en Azure of als er beperkte band breedte voor de site-naar-site-verbinding is, kunt u overwegen Azure Database Migration Service te gebruiken in de hybride modus (preview). De hybride modus maakt gebruik van een on-premises migratie medewerker samen met een exemplaar van Azure Database Migration Service dat in de Cloud wordt uitgevoerd. Als u een exemplaar van Azure Database Migration Service in de hybride modus wilt maken, raadpleegt u het artikel [een exemplaar van Azure database Migration service maken in de hybride modus met behulp van de Azure Portal](https://aka.ms/dms-hybrid-create).

- Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk niet de volgende binnenkomende communicatie poorten blok keren tot Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het NSG van verkeer voor virtuele netwerken van Azure.
- Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron SQL Server, die standaard TCP-poort 1433 is.
- Als u meerdere benoemde exemplaren van SQL Server met dynamische poorten uitvoert, kunt u de SQL Browser-service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bron server.
- Wanneer u een firewall apparaat voor uw bron database (s) gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor de migratie.
- Maak een IP- [firewall regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op server niveau voor de Azure SQL database-server om Azure database Migration service toegang tot de doel databases toe te staan. Geef het subnet-bereik van het virtuele netwerk op dat wordt gebruikt voor Azure Database Migration Service.
- Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het SQL Server-bronexemplaar [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-machtigingen hebben.
- Zorg ervoor dat de referenties waarmee verbinding wordt gemaakt met het Azure SQL Database-doelexemplaar CONTROL DATABASE-machtiging hebben op de Azure SQL-doeldatabases.

## <a name="assess-your-on-premises-database"></a>Uw on-premises resources evalueren

Voordat u gegevens uit een on-premises SQL Server-exemplaar naar een individuele of pooldatabase in Azure SQL Database kunt migreren, moet u controleren of de SQL Server-database blokkerende problemen bevat die mogelijk de migratie verhinderen. Volg met behulp van de Data Migration Assistant v3.3 of hoger de stappen in het artikel [Uitvoeren van een SQL Server-migratie-evaluatie](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) uit om de on-premises database-evaluatie uit te voeren. Hier volgt een samenvatting van de vereiste stappen:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens het projecttype **Evaluatie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server**, in het tekstvak **Doelservertype****Azure SQL Database**, en selecteer vervolgens **Maken** om het project te maken.

    Wanneer u de SQL Server-brondatabase controleert die u naar een individuele of pooldatabase in Azure SQL Database wilt migreren, kunt u kiezen voor een of beide van de volgende typen evaluatierapport:

   - Databasecompatibiliteit controleren
   - Functiepariteit controleren

    Beide rapporttypen zijn standaard geselecteerd.

3. Selecteer in de Data Migration Assistant, in het scherm **Opties**, **Volgende**.
4. Geef in het scherm **Bronnen selecteren**, in het dialoogvenster **Verbinding maken met een server**, de verbindingsdetails met uw SQL Server op, en selecteer vervolgens **Verbinding maken**.
5. Selecteer in het dialoogvenster **Bronnen toevoegen****AdventureWorks2012**, selecteer **Toevoegen**, en selecteer vervolgens **Evaluatie starten**.

    > [!NOTE]
    > Als u SSIS gebruikt, wordt de evaluatie van de bron-SSISDB momenteel niet door DMA ondersteund. SSIS-projecten/-pakketten worden echter beoordeeld/gevalideerd als ze zijn geïmplementeerd in de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

    Wanneer de evaluatie voltooid is, worden de resultaten weergegeven zoals in de volgende afbeelding:

    ![Gegevensmigratie beoordelen](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Voor individuele databases of pooldatabases in Azure SQL Database, identificeren de beoordelingen problemen met betrekking tot functiepariteit en problemen die de migratie blokkeren bij een implementatie naar een individuele of pooldatabase.

    - De categorie **SQL Server-functiepariteit** biedt een uitgebreide set met aanbevelingen, alternatieve methoden die beschikbaar zijn in Azure, en beperkende stappen voor het plannen van de inzet in uw migratieprojecten.
    - De categorie **compatibiliteitsproblemen** identificeert gedeeltelijk ondersteunde of niet-ondersteunde functies die compatibiliteitsproblemen laten zien die mogelijk de migratie van on-premises SQL Server-databases naar Azure SQL Database blokkeren. Aanbevelingen om deze problemen op te lossen worden ook gegeven.

6. Bekijk de resultaten van de evaluatie voor migratieblokkerende problemen en problemen met de functiepariteit door de specifieke opties te selecteren.

## <a name="migrate-the-sample-schema"></a>Het voorbeeldschema migreren

Wanneer u tevreden bent over de evaluatie en overtuigd bent dat de geselecteerde database een goede kandidaat is voor migratie naar een individuele of pooldatabase in Azure SQL Database, kunt u de DMA gebruiken om het schema naar Azure SQL Database te migreren.

> [!NOTE]
> Voordat u een migratieproject in Data Migration Assistant maakt, moet u ervoor zorgen dat u al een Azure SQL-database hebt ingericht zoals vermeld in de vereisten. Voor deze zelfstudie wordt ervan uitgegaan dat de naam van de Azure SQL Database **AdventureWorksAzure** is, maar u kunt elke naam die u wenst opgeven.

> [!IMPORTANT]
> Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

Voer de volgende stappen uit om het **AdventureWorks2012** -schema te migreren naar een enkele data base of een gegroepeerde data base Azure SQL database:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens onder **Projecttype****Migratie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server**en in het tekstvak **Doelservertype****Azure SQL Database**.
3. Selecteer onder **Migratiebereik**, selecteer **Alleen schema**.

    Na het uitvoeren van de vorige stappen, moet de Data Migration Assistant-interface worden weergegeven zoals in de volgende afbeelding:

    ![Data Migration Assistant-Project maken](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecteer **Maken** om het project te maken.
5. Geef in de Data Migration Assistant de details van de bronverbinding voor uw SQL Server op, selecteer **Connect**, en selecteer vervolgens de **AdventureWorks2012** database.

    ![Details Data Migration Assistant Bronverbinding](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecteer **volgende**onder **verbinding maken met doel server**de doel verbindings gegevens voor de Azure SQL database, selecteer **verbinding maken**en selecteer vervolgens de **AdventureWorksAzure** -data base die u vooraf hebt ingericht in Azure SQL database.

    ![Details Data Migration Assistant-doelverbinding](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecteer **Volgende** om door te gaan naar het scherm **Objecten selecteren** waarin u de schemaobjecten in de **AdventureWorks2012**-database kunt opgeven, die moeten worden geïmplementeerd naar Azure SQL Database.

    Standaard worden alle objecten geselecteerd.

    ![SQL-scripts genereren](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecteer **SQL-script genereren** voor het maken van de SQL-scripts en controleer vervolgens de scripts op fouten.

    ![Schemascript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecteer **Schema implementeren** om het schema te implementeren naar Azure SQL Database, en nadat het schema is geïmplementeerd, controleer de doelserver op eventuele afwijkingen.

    ![Schema implementeren](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij Azure Portal. Zoek en selecteer **abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **resource providers**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer vervolgens **registreren** voor **micro soft. DataMigration**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in het menu Azure Portal of op de **Introductie** pagina de optie **een resource maken**. Zoek en selecteer **Azure database Migration service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarin u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuwe.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel Azure SQL Database exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in het menu Azure Portal **alle services**. Zoek en selecteer **Azure data base Migration Services**.

     ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Selecteer op het scherm **Azure data base Migration Services** het Azure database Migration service exemplaar dat u hebt gemaakt.

3. Selecteer **Nieuw migratie project**.

     ![Zoek uw exemplaar van Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **bronservertype**, selecteer **SQL Server**, selecteer in het tekstvak **doelserver type****Azure SQL Database**, en kies vervolgens **Type activiteit kiezen**, selecteer **Offline gegevensmigratie**.

    ![Azure Database Migration Service-project maken](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Migratiebron** de details van de verbinding op voor het bron-SQL Server-exemplaar.

    Zorg ervoor dat u een Fully Qualified Domain Name (FQDN) gebruikt voor de naam van het bron-SQL Server-exemplaar. U kunt ook het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

2. Als u geen vertrouwd certificaat op de bronserver hebt geïnstalleerd, selecteert u het selectievakje **Servercertificaat vertrouwen**.

    Wanneer geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die zijn versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met zelfondertekende certificaten in een productie omgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**, en geef vervolgens in het scherm **Details migratiedoel** de details van de verbinding op voor de doel-Azure SQL Database-Server, dit is de vooraf ingerichte Azure SQL-Database waarnaar het ** AdventureWorks2012**-schema is geïmplementeerd met behulp van de Data Migration Assistant.

    ![Doel selecteren](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doel database dezelfde database naam als de bron database bevat Azure Database Migration Service de doel database standaard geselecteerd.

    ![Toewijzen aan doeldatabases](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selecteer **Opslaan**in het scherm **Tabellen selecteren**, vouw de tabellenlijst uit en controleer de lijst met betrokken velden.

    Azure Database Migration Service automatisch selecteert alle lege bron tabellen die voor komen op het doel Azure SQL Database exemplaar. Als u tabellen wilt terug migreren die al gegevens bevatten, moet u expliciet de tabellen op deze blade selecteren.

    ![Selecteer tabellen](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Selecteer **Opslaan**in het scherm **Migratieoverzicht**, geef in het tekstvak **Activiteitennaam** een naam op voor de migratieactiviteit.

5. Vouw de sectie **Validatieoptie** uit om het scherm **Validatieoptie kiezen** weer te geven en geef op of de gemigreerde databases voor **Schemavergelijking**, **Gegevensconsistentie**, en **Query uitvoeren op juistheid**moeten worden gevalideerd.

    ![Validatieoptie kiezen](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Selecteer **Opslaan**, controleer de samenvatting om ervoor te zorgen dat de gegevens van de bron en het doel overeenkomen met wat u eerder hebt opgegeven.

    ![Migratieoverzicht](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

- Selecteer **Migratie uitvoeren**.

    Het venster van de migratieactiviteit wordt weergegeven, en de **Status** van de activiteit is **In behandeling**.

    ![Activiteitenstatus](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer op het scherm van de activiteitenmigratie **Vernieuwen** om de weergave bij te werken totdat de **Status** van de migratie als **Voltooid** wordt weergegeven.

    ![Activiteitenstatus voltooid](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Nadat de migratie is voltooid, selecteert u **Rapport downloaden** om een rapport op te halen met gegevens die zijn gekoppeld aan het migratieproces.

3. Controleer de doel-database(s) op de doel-Azure SQL Database-server.

### <a name="additional-resources"></a>Aanvullende bronnen

- [SQL-migratie met behulp van de Azure Data Migration service](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) -praktijk op Lab.
- Zie het artikel [Bekende problemen met en tijdelijke oplossingen voor online migraties naar Azure SQL Database](known-issues-azure-sql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure SQL Database.
- Zie het artikel [Wat is er Azure database Migration service?](https://docs.microsoft.com/azure/dms/dms-overview)voor informatie over Azure database Migration service.
- Zie het artikel [Wat is de service Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) voor informatie over Azure SQL Database.
