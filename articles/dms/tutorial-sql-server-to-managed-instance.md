---
title: 'Zelf studie: DMS gebruiken om te migreren naar een beheerde instantie van Azure SQL Database | Microsoft Docs'
description: Meer informatie over het migreren van SQL Server on-premises naar een Azure SQL Database beheerd exemplaar met behulp van Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: ca6f94664ad07b15c9c0c6dada6d6824e97527d9
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903139"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Zelf studie: SQL Server naar een beheerde instantie van Azure SQL Database migreren met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de data bases van een on-premises SQL Server exemplaar te migreren naar een [Azure SQL database beheerd exemplaar](../sql-database/sql-database-managed-instance.md). In het artikel [Een SQL Server-exemplaar migreren naar een beheerd Azure SQL Database-exemplaar](../sql-database/sql-database-managed-instance-migrate.md) vindt u aanvullende methoden, waarvoor enkele handmatige stappen nodig kunnen zijn.

In deze zelf studie migreert u de **Adventureworks2012** -data base vanuit een on-premises exemplaar van SQL Server naar een SQL database beheerd exemplaar met behulp van Azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - Maak een instantie van Azure Database Migration Service.
> - Een migratie project maken met behulp van Azure Database Migration Service.
> - De migratie uitvoeren.
> - De migratie controleren.
> - Een migratierapport downloaden.

> [!IMPORTANT]
> Voor offline migraties van SQL Server naar SQL Database beheerde instantie, kunnen Azure Database Migration Service de back-upbestanden voor u maken. U kunt ook de laatste volledige back-up van de data base in de SMB-netwerk share opgeven die door de service wordt gebruikt om uw data bases te migreren. Voeg geen meerdere back-ups toe aan één back-upmedium; Maak elke back-up op een afzonderlijk back-upbestand. U kunt ook gecomprimeerde back-ups gebruiken om de kans te verkleinen dat er mogelijke problemen optreden bij het migreren van grote back-ups.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een offline migratie van SQL Server naar een SQL Database beheerd exemplaar beschreven. Zie [SQL Server naar een beheerde instantie van Azure SQL database migreren met behulp van DMS](tutorial-sql-server-managed-instance-online.md)voor een online migratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Een Azure Virtual Network (VNet) maken voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-connectiviteit met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Informatie over netwerktopologieën voor het Azure SQL database van beheerde exemplaar migraties met behulp van Azure database Migration service](https://aka.ms/dmsnetworkformi). Voor meer informatie over het maken van een VNet raadpleegt u de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)en met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de VNet-installatie gebruikmaakt van ExpressRoute met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    > - Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > - Opslag eindpunt
    > - Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.

- Zorg ervoor dat de regels van uw VNet-netwerk beveiligings groep niet de volgende binnenkomende communicatie poorten blok keren naar Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG-verkeer van Azure VNet.
- Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron SQL Server, die standaard TCP-poort 1433 is.
- Als u meerdere benoemde exemplaren van SQL Server met dynamische poorten uitvoert, kunt u de SQL Browser-service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar in uw bron naam.
- Als u voor uw bron databases een firewall apparaat gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor migratie, evenals bestanden via SMB-poort 445.
- Maak een beheerd Azure SQL Database-exemplaar aan de hand van de instructies in het artikel [Een beheerd exemplaar voor Azure SQL Database maken in de Azure-portal](https://aka.ms/sqldbmi).
- Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt voor verbinding met het bronexemplaar van SQL Server en het doelexemplaar van het beheerde exemplaar lid zijn van de serverrol sysadmin.
- Maak een netwerk share die Azure Database Migration Service kan gebruiken om een back-up te maken van de bron database.
- Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
- Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service imiteert de gebruikers referentie voor het uploaden van de back-upbestanden naar de Azure storage-container voor de herstel bewerking.
- Maak een blob-container en haal de SAS-URI van container op met behulp van de stappen in het artikel [Azure Blob Storage-resources beheren met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Vergeet niet om alle machtigingen (Lezen, Schrijven, Verwijderen, Lijst) te selecteren in het beleidsvenster terwijl u de SAS-URI maakt. Dit detail bevat Azure Database Migration Service met toegang tot uw opslag account container voor het uploaden van de back-upbestanden die worden gebruikt voor het migreren van data bases naar Azure SQL Database Managed instance.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **resource providers**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken**, zoek naar **Azure Database Migration Service** en selecteer vervolgens **Azure Database Migration Service** in de vervolgkeuzelijst.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service** **Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand VNet of maak een.

    Het VNet biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel Azure SQL Database beheerde exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een VNet in azure Portal.

    Zie het artikel [netwerk topologieën voor Azure SQL data base-migraties van beheerde exemplaren met Azure database Migration service](https://aka.ms/dmsnetworkformi)voor meer informatie.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **Bronservertype**, selecteer **SQL Server**, selecteer in het tekstvak **Type doelserver** **Azure SQL Database Managed Instance** en selecteer vervolgens **Offlinegegevensmigratie** voor **Type activiteit kiezen**.

   ![DMS-project maken](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecteer **Maken** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > SSL-verbindingen die worden versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op SSL met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecteer **Opslaan**.

4. Selecteer in het scherm **Brondatabases selecteren** de database **Adventureworks2012** voor migratie.

   ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Als u SSIS (SQL Server Integration Services) gebruikt, biedt DMS momenteel geen ondersteuning voor het migreren van de catalogusdatabase voor uw SSIS-projecten/pakketten (SSISDB) van SQL Server naar een beheerd Azure SQL Database-exemplaar. U kunt SSIS echter in Azure Data Factory (ADF) inrichten en uw SSIS-projecten/-pakketten opnieuw implementeren naar de bestemmings-SSISDB die wordt gehost door het beheerde Azure SQL Database-exemplaar. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

5. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Details migratiedoel** de verbindingsgegevens op voor het doel, dat bestaat uit het vooraf ingerichte beheerde Azure SQL Database-exemplaar waarnaar u de database **AdventureWorks2012** migreert.

    Als u het beheerde exemplaar van SQL Database niet al hebt ingericht, selecteert u de [koppeling](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) om u te helpen het exemplaar in te richten. U kunt door gaan met het maken van het project en vervolgens, wanneer het beheerde exemplaar van Azure SQL Database gereed is, terugkeren naar dit specifieke project om de migratie uit te voeren.

     ![Doel selecteren](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecteer **Opslaan**.

## <a name="select-source-databases"></a>Brondatabases selecteren

1. Selecteer in het scherm **Brondatabases selecteren** de brondatabase die u wilt migreren.

    ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecteer **Opslaan**.

## <a name="select-logins"></a>Aanmeldingen selecteren

1. Selecteer in het scherm **Aanmeldingen selecteren** de aanmeldingen die u wilt migreren.

    >[!NOTE]
    >Deze versie biedt alleen ondersteuning voor het migreren van de SQL-aanmeldingen.

    ![Aanmeldingen selecteren](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecteer **Opslaan**.

## <a name="configure-migration-settings"></a>Migratie-instellingen configureren

1. Geef in het scherm **Migratie-instellingen configureren** de volgende instellingen op:

    | | |
    |--------|---------|
    |**De back-upoptie voor de bron kiezen** | Kies de optie **Ik lever de recentste back-upbestanden** wanneer u al beschikt over een volledige set back-upbestanden die DMS kan gebruiken voor migratie van de database. Kies de optie **Ik laat Azure Database Migration Service back-upbestanden maken** als u wilt dat DMS de volledige back up van de brondatabase gebruikt voor migratie. |
    |**De netwerksharelocatie waarheen back-ups van databases kunnen worden verplaatst door Azure Database Migration Service** | De lokale SMB-netwerk share die Azure Database Migration Service, kan de back-ups van de bron database maken. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'.|
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service imiteert de gebruikers referentie voor het uploaden van de back-upbestanden naar de Azure storage-container voor de herstel bewerking. Als TDE-compatibele databases zijn geselecteerd voor migratie, moet de bovenstaande Windows-gebruiker het ingebouwde administratoraccount zijn en moet [Gebruikersaccountbeheer](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) zijn uitgeschakeld om Azure Database Migration Service in staat te stellen de certificaatbestanden te uploaden en verwijderen. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. |
    |**Opslagaccountinstellingen** | De SAS-URI waarmee Azure Database Migration Service toegang krijgt tot uw opslag account container waarnaar de service de back-upbestanden uploadt en die wordt gebruikt voor het migreren van data bases naar Azure SQL Database beheerde instantie. [Lees hier meer over het verkrijgen van de SAS-URI voor de blob-container](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-instellingen** | Als u de bron databases met Transparent Data Encryption (TDE) die zijn ingeschakeld, migreert, moet u schrijf bevoegdheden hebben op het doel-Azure SQL Database beheerde exemplaar.  Selecteer in de vervolgkeuzelijst het abonnement waarin het beheerde Azure SQL Database-exemplaar is ingericht.  Selecteer het **beheerde doelexemplaar voor Azure SQL Database** in de vervolgkeuzelijst. |

    ![Migratie-instellingen configureren](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Selecteer **Opslaan**.

## <a name="review-the-migration-summary"></a>Migratieoverzicht controleren

1. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

2. Vouw de sectie **Validatieoptie** uit om het scherm **Validatieoptie kiezen** weer te geven en geef op of de gemigreerde databases moeten worden gevalideerd op juistheid van de query's. Selecteer vervolgens **Opslaan**.

3. Bekijk en controleer de gegevens van het migratieproject.

    ![Overzicht van migratieproject](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Selecteer **Opslaan**.

## <a name="run-the-migration"></a>De migratie uitvoeren

- Selecteer **Migratie uitvoeren**.

  Het venster van de migratieactiviteit wordt weergegeven en de Status van de activiteit is **In behandeling**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de migratieactiviteit **Vernieuwen** om de weergave bij te werken.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    U kunt de databases en aanmeldingscategorieën verder uitvouwen om de migratiestatus van de respectieve serverobjecten te controleren.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Nadat de migratie is voltooid, selecteert u **Rapport downloaden** om een rapport op te halen met gegevens die zijn gekoppeld aan het migratieproces.

3. Controleer of de doeldatabase bestaat in de omgeving van de doeldatabase van het beheerde Azure SQL Database-exemplaar.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een back-up van de database herstellen voor een beheerd Azure SQL Database-exemplaar](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) voor een zelfstudie waarin wordt uitgelegd hoe u een database migreert naar een beheerd exemplaar met de opdracht T-SQL RESTORE.
- Zie [Wat is een beheerd exemplaar?](../sql-database/sql-database-managed-instance.md) voor meer informatie over beheerde exemplaren.
- Zie [Verbinding maken met toepassingen](../sql-database/sql-database-managed-instance-connect-app.md) voor meer informatie over het verbinden van apps met een beheerd exemplaar.
