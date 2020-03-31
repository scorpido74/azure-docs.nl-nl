---
title: 'Zelfstudie: SQL Server migreren naar SQL-beheerde instantie'
titleSuffix: Azure Database Migration Service
description: Lees of u van SQL Server on-premises migreren naar een Azure SQL Database-exemplaar dat wordt beheerd met Azure Database Migration Service.
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
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297733"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Zelfstudie: SQL Server migreren naar een Azure SQL Database beheerd exemplaar offline met DMS

U Azure Database Migration Service gebruiken om de databases te migreren van een on-premises SQL Server-instantie naar een [azure SQL Database-beheerde instantie.](../sql-database/sql-database-managed-instance.md) In het artikel [Een SQL Server-exemplaar migreren naar een beheerd Azure SQL Database-exemplaar](../sql-database/sql-database-managed-instance-migrate.md) vindt u aanvullende methoden, waarvoor enkele handmatige stappen nodig kunnen zijn.

In deze zelfstudie migreert u de **Adventureworks2012-database** van een on-premises instantie van SQL Server naar een SQL Database-exemplaar dat wordt beheerd met behulp van Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> - Maak een exemplaar van de Azure Database Migration Service.
> - Maak een migratieproject met Azure Database Migration Service.
> - De migratie uitvoeren.
> - De migratie controleren.
> - Een migratierapport downloaden.

> [!IMPORTANT]
> Voor offline migraties van SQL Server naar SQL Database beheerde instantie, kan Azure Database Migration Service de back-upbestanden voor u maken. U ook de nieuwste volledige databaseback-up bieden in het SMB-netwerkaandeel dat de service zal gebruiken om uw databases te migreren. Niet meerdere back-ups toevoegen aan één back-upmedium. neem elke back-up op een apart back-upbestand. Houd er rekening mee dat u ook gecomprimeerde back-ups gebruiken, om de kans op mogelijke problemen met het migreren van grote back-ups te verkleinen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een offlinemigratie van SQL Server naar een SQL Database-beheerde instantie beschreven. Zie SQL Server [migreren naar een Azure SQL Database beheerd exemplaar online met DMS](tutorial-sql-server-managed-instance-online.md)voor een online migratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) [Meer informatie over netwerktopologieën voor Azure SQL Database beheerde instantiemigraties met Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

    > [!NOTE]
    > Als u ExpressRoute gebruikt met netwerkpeering naar Microsoft, voegt u tijdens de installatie van het virtuele netwerk ExpressRoute met netwerkpeering aan Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    > - Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > - Eindpunt voor opslag
    > - Eindpunt van servicebus
    >
    > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.

- Zorg ervoor dat de regels van uw beveiligingsgroep voor virtuele netwerken niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
- Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Open uw Windows Firewall zodat Azure Database Migration Service toegang heeft tot de bron SQL Server, die standaard TCP-poort 1433 is.
- Als u SQL Server-exemplaren met meerdere benoemde sql-servers uitvoert met dynamische poorten, u de SQL Browser Service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls, zodat azure databasemigratieservice verbinding kan maken met een benoemde instantie op uw bron Server.
- Als u een firewalltoestel gebruikt voor uw brondatabases, moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie en bestanden via SMB-poort 445.
- Maak een Azure SQL Database-beheerde instantie door de details in het artikel [Te volgen Een azure SQL Database-beheerde instantie maken in de Azure-portal.](https://aka.ms/sqldbmi)
- Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt voor verbinding met het bronexemplaar van SQL Server en het doelexemplaar van het beheerde exemplaar lid zijn van de serverrol sysadmin.

    >[!NOTE]
    >Azure Database Migration Service ondersteunt standaard alleen migrerende SQL-aanmeldingen. U echter de mogelijkheid inschakelen om Windows-aanmeldingen te migreren door:
    >
    >- Ervoor zorgen dat de doelSQL-databasebeheerde instantie AAD leestoegang heeft, die via de Azure-portal kan worden geconfigureerd door een gebruiker met de **bedrijfsbeheerder**of een rol **van globale beheerder.**
    >- Uw azure database migration service-instantie configureren om windows-aanmeldingsmigraties voor gebruikers/groepen, die is ingesteld via de Azure-portal, in te schakelen op de pagina Configuratie. Nadat u deze instelling hebt inschakelt, start u de service opnieuw om de wijzigingen van kracht te laten worden.
    >
    > Nadat de service opnieuw is opgestart, worden windows-gebruikers-/groepsaanmeldingen weergegeven in de lijst met aanmeldingen die beschikbaar zijn voor migratie. Voor alle windows-gebruikers/groepsaanmeldingen die u migreert, wordt u gevraagd de bijbehorende domeinnaam op te geven. Servicegebruikersaccounts (account met domeinnaam NT AUTHORITY) en virtuele gebruikersaccounts (accountnaam met domeinnaam NT SERVICE) worden niet ondersteund.

- Maak een netwerkshare dat Azure Database Migration Service kan gebruiken om een back-up van de brondatabase te maken.
- Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
- Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service doet zich voor als de gebruikersreferenties om de back-upbestanden te uploaden naar de Azure Storage-container voor herstelbewerking.
- Maak een blob-container en haal de SAS-URI van container op met behulp van de stappen in het artikel [Azure Blob Storage-resources beheren met Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Vergeet niet om alle machtigingen (Lezen, Schrijven, Verwijderen, Lijst) te selecteren in het beleidsvenster terwijl u de SAS-URI maakt. Dit detail biedt Azure Database Migration Service toegang tot uw opslagaccountcontainer voor het uploaden van de back-upbestanden die worden gebruikt voor het migreren van databases naar het beheerde exemplaar van Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken,** zoeken naar **Azure Database Migration Service**en selecteer vervolgens Azure Database Migration **Service** in de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak er een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en het doel Azure SQL Database beheerde exemplaar.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in Azure-portal.

    Zie voor meer informatie het artikel [Netwerktopologieën voor Azure SQL DB-beheerde instantiemigraties met Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

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

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **Bronservertype**, selecteer **SQL Server**, selecteer in het tekstvak **Type doelserver****Azure SQL Database Managed Instance** en selecteer vervolgens **Offlinegegevensmigratie** voor **Type activiteit kiezen**.

   ![DMS-project maken](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selecteer **Maken** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die worden versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met het internet.

   ![Brondetails](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selecteer **Opslaan**.

4. Selecteer in het scherm **Brondatabases selecteren** de database **Adventureworks2012** voor migratie.

   ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Als u SSIS (SQL Server Integration Services) gebruikt, biedt DMS momenteel geen ondersteuning voor het migreren van de catalogusdatabase voor uw SSIS-projecten/pakketten (SSISDB) van SQL Server naar een beheerd Azure SQL Database-exemplaar. U kunt SSIS echter in Azure Data Factory (ADF) inrichten en uw SSIS-projecten/-pakketten opnieuw implementeren naar de bestemmings-SSISDB die wordt gehost door het beheerde Azure SQL Database-exemplaar. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

5. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Details migratiedoel** de verbindingsgegevens op voor het doel, dat bestaat uit het vooraf ingerichte beheerde Azure SQL Database-exemplaar waarnaar u de database **AdventureWorks2012** migreert.

    Als u de sql-databasebeheerde instantie nog niet hebt ingericht, selecteert u de [koppeling](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) waarmee u de instantie inrichten. U nog steeds doorgaan met het maken van projecten en vervolgens, wanneer de door Azure SQL Database beheerde instantie gereed is, terugkeren naar dit specifieke project om de migratie uit te voeren.

    ![Doel selecteren](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selecteer **Opslaan**.

## <a name="select-source-databases"></a>Brondatabases selecteren

1. Selecteer in het scherm **Brondatabases selecteren** de brondatabase die u wilt migreren.

    ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selecteer **Opslaan**.

## <a name="select-logins"></a>Aanmeldingen selecteren

1. Selecteer in het scherm **Aanmeldingen selecteren** de aanmeldingen die u wilt migreren.

    >[!NOTE]
    >Azure Database Migration Service ondersteunt standaard alleen migrerende SQL-aanmeldingen. Zie het gedeelte **Voorwaarden** van deze zelfstudie om ondersteuning voor het migreren van Windows-aanmeldingen in te schakelen.

    ![Aanmeldingen selecteren](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selecteer **Opslaan**.

## <a name="configure-migration-settings"></a>Migratie-instellingen configureren

1. Geef in het scherm **Migratie-instellingen configureren** de volgende instellingen op:

    | | |
    |--------|---------|
    |**De back-upoptie voor de bron kiezen** | Kies de optie **Ik lever de recentste back-upbestanden** wanneer u al beschikt over een volledige set back-upbestanden die DMS kan gebruiken voor migratie van de database. Kies de optie **Ik laat Azure Database Migration Service back-upbestanden maken** als u wilt dat DMS de volledige back up van de brondatabase gebruikt voor migratie. |
    |**De netwerksharelocatie waarheen back-ups van databases kunnen worden verplaatst door Azure Database Migration Service** | Het lokale SMB-netwerkaandeel waar azure database migration service de brondatabaseback-ups naar kan brengen. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'.|
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service doet zich voor als de gebruikersreferenties om de back-upbestanden te uploaden naar de Azure Storage-container voor herstelbewerking. Als TDE-compatibele databases zijn geselecteerd voor migratie, moet de bovenstaande Windows-gebruiker het ingebouwde administratoraccount zijn en moet [Gebruikersaccountbeheer](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) zijn uitgeschakeld om Azure Database Migration Service in staat te stellen de certificaatbestanden te uploaden en verwijderen. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. |
    |**Opslagaccountinstellingen** | De SAS URI die Azure Database Migration Service toegang biedt tot uw opslagaccountcontainer waarnaar de service de back-upbestanden uploadt en die wordt gebruikt voor het migreren van databases naar azure SQL Database beheerd exemplaar. [Lees hier meer over het verkrijgen van de SAS-URI voor de blob-container](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-instellingen** | Als u de brondatabases migreert met TDE (Transparent Data Encryption) ingeschakeld, moet u schrijfrechten hebben op de beheerde instantie van de doelbeheervan Azure SQL Database.  Selecteer in de vervolgkeuzelijst het abonnement waarin het beheerde Azure SQL Database-exemplaar is ingericht.  Selecteer het **beheerde doelexemplaar voor Azure SQL Database** in de vervolgkeuzelijst. |

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

  Het venster migratieactiviteit wordt weergegeven en de status van de activiteit is **in behandeling**.

## <a name="monitor-the-migration"></a>Bewaak de migratie

1. Selecteer in het scherm van de migratieactiviteit **Vernieuwen** om de weergave bij te werken.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    U kunt de databases en aanmeldingscategorieën verder uitvouwen om de migratiestatus van de respectieve serverobjecten te controleren.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Nadat de migratie is voltooid, selecteert u **Rapport downloaden** om een rapport op te halen met gegevens die zijn gekoppeld aan het migratieproces.

3. Controleer of de doeldatabase bestaat in de omgeving van de doeldatabase van het beheerde Azure SQL Database-exemplaar.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een back-up herstellen naar een beheerde instantie met de](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)opdracht Herstellen naar een beheerde instantie voor een zelfstudie waarin u laat zien hoe u een database migreert naar een beheerde instantie met de opdracht Herstellen.
- Zie [Wat is een beheerde instantie](../sql-database/sql-database-managed-instance.md)voor informatie over beheerde instantie .
- Zie [Toepassingen verbinden](../sql-database/sql-database-managed-instance-connect-app.md)voor informatie over het verbinden van apps met een beheerde instantie.
