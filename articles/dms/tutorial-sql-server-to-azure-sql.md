---
title: 'Zelfstudie: SQL Server offline migreren naar één SQL-database'
titleSuffix: Azure Database Migration Service
description: Lees of u offline migreren van SQL Server naar één database of gepoolde database in Azure SQL Database met Azure Database Migration Service.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298916"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Zelfstudie: SQL Server migreren naar één database of gepoolde database in Azure SQL Database offline met DMS

U Azure Database Migration Service gebruiken om de databases te migreren van een on-premises SQL Server-instantie naar [Azure SQL Database.](https://docs.microsoft.com/azure/sql-database/) In deze zelfstudie migreert u de **Adventureworks2012-database** die is hersteld naar een on-premises instantie van SQL Server 2016 (of hoger) naar één database of samengevoegde database in Azure SQL Database met azure databasemigratieservice.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - Uw on-premises database evalueren met behulp van de Data Migration Assistant.
> - Het voorbeeldschema migreren met behulp van de Data Migration Assistant.
> - Maak een exemplaar van de Azure Database Migration Service.
> - Maak een migratieproject met Azure Database Migration Service.
> - De migratie uitvoeren.
> - De migratie controleren.
> - Een migratierapport downloaden.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een offline migratie beschreven vanuit SQL Server naar een individuele of pooldatabase in Azure SQL Database. Zie [SQL Server online migreren naar Azure SQL Database met behulp van DMS](tutorial-sql-server-azure-sql-online.md) voor informatie over een online migratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- SQL [Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads)downloaden en installeren.
- Schakel het TCP/IP-protocol in, dat standaard is uitgeschakeld tijdens de installatie van SQL Server Express, door de instructies in het artikel [In- of uitschakelen van een Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) te volgen.
- Maak een individuele of pooldatabase in Azure SQL Database. Dit doet u door de stappen te volgen in het artikel [Een individuele database in Azure SQL Database maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Als u SQL Server Integration Services (SSIS) gebruikt en de catalogusdatabase voor uw SSIS-projecten/-pakketten (SSISDB) wilt migreren van SQL Server naar Azure SQL Database, wordt de doel-SSISDB automatisch namens u gemaakt en beheerd wanneer u SSIS in Azure Data Factory (ADF) inricht. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.
  
- Download en installeer de [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 of hoger.
- Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

    > [!NOTE]
    > Als u ExpressRoute gebruikt met netwerkpeering naar Microsoft, voegt u tijdens de installatie van het virtuele netwerk ExpressRoute met netwerkpeering aan Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > - Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > - Eindpunt voor opslag
    > - Eindpunt van servicebus
    >
    > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.
    >
    >Als u geen site-to-site-connectiviteit hebt tussen het on-premises netwerk en Azure of als er beperkte bandbreedte van site-to-site-connectiviteit is, u overwegen azure databasemigratieservice in hybride modus te gebruiken (Voorbeeld). De hybride modus maakt gebruik van een on-premises migratiewerknemer, samen met een instantie van Azure Database Migration Service die in de cloud wordt uitgevoerd. Als u een instantie van Azure Database Migration Service in hybride modus wilt maken, raadpleegt u het artikel [Een instantie van Azure Database Migration Service maken in hybride modus met behulp van de Azure-portal](https://aka.ms/dms-hybrid-create).

- Zorg ervoor dat de regels van uw beveiligingsgroep voor virtuele netwerken niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van Extern netwerknlnlnling voor het virtuele netwerk van Azure.
- Configureer uw [Windows Firewall voor toegang tot de database-engine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Open uw Windows-firewall zodat Azure Database Migration Service toegang heeft tot de bron SQL Server, die standaard TCP-poort 1433 is.
- Als u SQL Server-exemplaren met meerdere benoemde sql-servers uitvoert met dynamische poorten, u de SQL Browser Service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls, zodat azure databasemigratieservice verbinding kan maken met een benoemde instantie op uw bron Server.
- Wanneer u een firewalltoestel gebruikt voor uw brondatabase(s), moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.
- Maak een [IP-firewallregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) op serverniveau voor de Azure SQL Database-server om Azure Database Migration Service toegang te geven tot de doeldatabases. Geef het subnetbereik op van het virtuele netwerk dat wordt gebruikt voor Azure Database Migration Service.
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

Voer de volgende stappen uit om het **AdventureWorks2012-schema** te migreren naar één database of gepoolde database Azure SQL Database:

1. Selecteer het pictogram Nieuw (+) in de Data Migration Assistant, en selecteer vervolgens onder **Projecttype****Migratie**.
2. Geef een projectnaam op, selecteer in het tekstvak **Bronservertype****SQL Server**en in het tekstvak **Doelservertype****Azure SQL Database**.
3. Selecteer onder **Migratiebereik**, selecteer **Alleen schema**.

    Na het uitvoeren van de vorige stappen, moet de Data Migration Assistant-interface worden weergegeven zoals in de volgende afbeelding:

    ![Data Migration Assistant-Project maken](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecteer **Maken** om het project te maken.
5. Geef in de Data Migration Assistant de details van de bronverbinding voor uw SQL Server op, selecteer **Connect**, en selecteer vervolgens de **AdventureWorks2012** database.

    ![Details Data Migration Assistant Bronverbinding](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecteer **Volgende**, onder **Verbinding maken met doelserver**, geef de doelverbindingsgegevens voor de Azure SQL-database op, selecteer **Verbinding maken**en selecteer vervolgens de **AdventureWorksAzure-database** die u vooraf had ingericht in Azure SQL Database.

    ![Details Data Migration Assistant-doelverbinding](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecteer **Volgende** om door te gaan naar het scherm **Objecten selecteren** waarin u de schemaobjecten in de **AdventureWorks2012**-database kunt opgeven, die moeten worden geïmplementeerd naar Azure SQL Database.

    Standaard worden alle objecten geselecteerd.

    ![SQL-scripts genereren](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecteer **SQL-script genereren** voor het maken van de SQL-scripts en controleer vervolgens de scripts op fouten.

    ![Schemascript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecteer **Schema implementeren** om het schema te implementeren naar Azure SQL Database, en nadat het schema is geïmplementeerd, controleer de doelserver op eventuele afwijkingen.

    ![Schema implementeren](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij Azure Portal. Zoeken naar en selecteer **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer **Vervolgens Register** voor **Microsoft.DataMigration**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in het menu azure portal of op de **startpagina** de optie **Een bron maken**. Zoeken naar en selecteer **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waar u de instantie van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en de doelstelling Azure SQL Database instance.

    Zie het artikel Een virtueel netwerk maken met de [Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer alle services in het menu **Azure-portal**. Zoeken naar azure **databasemigratieservices**en selecteer deze .

     ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Selecteer in het scherm **Azure Database Migration Services** het exemplaar Azure Database Migration Service dat u hebt gemaakt.

3. Selecteer **Nieuw migratieproject**.

     ![Uw instantie van Azure Database Migration Service zoeken](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **bronservertype**, selecteer **SQL Server**, selecteer in het tekstvak **doelserver type****Azure SQL Database**, en kies vervolgens **Type activiteit kiezen**, selecteer **Offline gegevensmigratie**.

    ![Azure Database Migration Service-project maken](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Migratiebron** de details van de verbinding op voor het bron-SQL Server-exemplaar.

    Zorg ervoor dat u een Fully Qualified Domain Name (FQDN) gebruikt voor de naam van het bron-SQL Server-exemplaar. U kunt ook het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

2. Als u geen vertrouwd certificaat op de bronserver hebt geïnstalleerd, selecteert u het selectievakje **Servercertificaat vertrouwen**.

    Wanneer geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die zijn versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met het internet.

   ![Brondetails](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Als u SSIS gebruikt, biedt DMA momenteel geen ondersteuning voor de migratie van de bron-SSISDB, maar u kunt uw SSIS-projecten/-pakketten opnieuw implementeren naar de doel-SSISDB die wordt gehost door Azure SQL Database. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Selecteer **Opslaan**, en geef vervolgens in het scherm **Details migratiedoel** de details van de verbinding op voor de doel-Azure SQL Database-Server, dit is de vooraf ingerichte Azure SQL-Database waarnaar het ** AdventureWorks2012**-schema is geïmplementeerd met behulp van de Data Migration Assistant.

    ![Doel selecteren](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Selecteer **Opslaan**, en klik vervolgens in het scherm **Toewijzen aan doeldatabases**, wijs de bron- en de doeldatabase voor de migratie toe.

    Als de doeldatabase dezelfde databasenaam bevat als de brondatabase, selecteert Azure Database Migration Service standaard de doeldatabase.

    ![Toewijzen aan doeldatabases](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selecteer **Opslaan**in het scherm **Tabellen selecteren**, vouw de tabellenlijst uit en controleer de lijst met betrokken velden.

    Azure Database Migration Service selecteert automatisch alle lege brontabellen die aanwezig zijn op de doelstelling Azure SQL Database-instantie. Als u tabellen wilt terug migreren die al gegevens bevatten, moet u expliciet de tabellen op deze blade selecteren.

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

- [SQL-migratie met azure data migratieservice](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) in een praktijklab.
- Zie het artikel [Bekende problemen met en tijdelijke oplossingen voor online migraties naar Azure SQL Database](known-issues-azure-sql-online.md) voor informatie over bekende problemen en beperkingen bij het uitvoeren van online migraties naar Azure SQL Database.
- Zie het artikel [Wat is Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Zie het artikel [Wat is de service Azure SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) voor informatie over Azure SQL Database.
