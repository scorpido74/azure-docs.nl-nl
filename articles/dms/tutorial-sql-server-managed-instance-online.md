---
title: 'Zelf studie: Azure Database Migration Service gebruiken om een online migratie uit te voeren van SQL Server naar een beheerde instantie van Azure SQL Database | Microsoft Docs'
description: Meer informatie over het uitvoeren van een online migratie van SQL Server on-premises naar een Azure SQL Database beheerd exemplaar met behulp van Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 556fb2c1caf9c763cf5a63b71d3dd1e522104e1d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646949"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Zelf studie: SQL Server naar een beheerde instantie van Azure SQL Database migreren met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de data bases van een on-premises SQL Server exemplaar te migreren naar een [Azure SQL database beheerd exemplaar](../sql-database/sql-database-managed-instance.md) met minimale downtime. In het artikel [Een SQL Server-exemplaar migreren naar een beheerd Azure SQL Database-exemplaar](../sql-database/sql-database-managed-instance-migrate.md) vindt u aanvullende methoden, waarvoor enkele handmatige stappen nodig kunnen zijn.

In deze zelf studie migreert u de **Adventureworks2012** -data base vanuit een on-premises exemplaar van SQL Server naar een SQL database beheerd exemplaar met minimale downtime door gebruik te maken van Azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een instantie van Azure Database Migration Service.
> * Maak een migratie project en start online migratie met behulp van Azure Database Migration Service.
> * De migratie controleren.
> * Voer de migratie cutover uit wanneer u klaar bent.

> [!IMPORTANT]
> Voor online migraties van SQL Server naar SQL Database beheerd exemplaar met behulp van Azure Database Migration Service, moet u de volledige back-up van de data base en volgende logboek back-ups in de SMB-netwerk share opgeven die door de service kan worden gebruikt om uw data bases te migreren. Azure Database Migration Service initieert geen back-ups en maakt in plaats daarvan gebruik van bestaande back-ups, die u mogelijk al hebt als onderdeel van het nood herstel plan voor de migratie.
> Zorg ervoor dat u [back-ups maakt met behulp van de optie with checksum](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Zorg er ook voor dat u niet meerdere back-ups (dus volledig en t-Logboeken) toevoegt aan één back-upmedium. Maak elke back-up op een afzonderlijk back-upbestand. Ten slotte kunt u gecomprimeerde back-ups gebruiken om de kans te verkleinen dat er mogelijke problemen optreden bij het migreren van grote back-ups.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een online migratie van SQL Server naar een SQL Database beheerd exemplaar beschreven. Zie SQL Server voor een offline migratie [naar een SQL database Managed instance offline migreren met behulp van DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een Azure Virtual Network (VNet) maken voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-connectiviteit met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Informatie over netwerktopologieën voor het Azure SQL database van beheerde exemplaar migraties met behulp van Azure database Migration service](https://aka.ms/dmsnetworkformi). Voor meer informatie over het maken van een VNet raadpleegt u de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)en met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de VNet-installatie gebruikmaakt van ExpressRoute met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt, enzovoort)
    > * Opslag eindpunt
    > * Service Bus-eind punt
    >
    > Deze configuratie is nood zakelijk omdat Azure Database Migration Service geen verbinding met internet heeft.
    >
    >Als u geen site-naar-site-verbinding hebt tussen het on-premises netwerk en Azure of als er beperkte band breedte voor de site-naar-site-verbinding is, kunt u overwegen Azure Database Migration Service te gebruiken in de hybride modus (preview). De hybride modus maakt gebruik van een on-premises migratie medewerker samen met een exemplaar van Azure Database Migration Service dat in de Cloud wordt uitgevoerd. Als u een exemplaar van Azure Database Migration Service in de hybride modus wilt maken, raadpleegt u het artikel [een exemplaar van Azure database Migration service maken in de hybride modus met behulp van de Azure Portal](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Met betrekking tot het opslag account dat wordt gebruikt als onderdeel van de migratie, moet u:
    > * Kies ervoor te zorgen dat alle netwerken toegang hebben tot het opslag account.
    > * Stel Acl's in voor het VNet. Zie voor meer informatie het artikel [Azure Storage firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security).

* Zorg ervoor dat de regels van uw VNet-netwerk beveiligings groep niet de volgende binnenkomende communicatie poorten blok keren naar Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG-verkeer van Azure VNet.
* Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron SQL Server, die standaard TCP-poort 1433 is.
* Als u meerdere benoemde exemplaren van SQL Server met dynamische poorten uitvoert, kunt u de SQL Browser-service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar in uw bron naam.
* Als u voor uw bron databases een firewall apparaat gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor migratie, evenals bestanden via SMB-poort 445.
* Maak een beheerde SQL Database-exemplaar door de details in het artikel [een Azure SQL database beheerde instantie in de Azure portal te maken](https://aka.ms/sqldbmi).
* Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt om het bronexemplaar van SQL Server en het doelexemplaar van Managed Instance te verbinden, lid zijn van de serverrol sysadmin.
* Geef een SMB-netwerk share op met alle back-upbestanden van de data base en volgende back-upbestanden voor transactie logboeken, die Azure Database Migration Service kunnen gebruiken voor database migratie.
* Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
* Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service imiteert de gebruikers referentie voor het uploaden van de back-upbestanden naar de Azure storage-container voor de herstel bewerking.
* Maak een Azure Active Directory-toepassings-ID die de toepassings-ID-sleutel genereert die Azure Database Migration Service kan gebruiken om verbinding te maken met het doel-Azure data base Managed instance en Azure Storage container. Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) voor meer informatie.

  > [!NOTE]
  > Voor Azure Database Migration Service is de machtiging Inzender vereist voor het abonnement voor de opgegeven toepassings-ID. U kunt ook aangepaste rollen maken die de specifieke machtigingen verlenen die Azure Database Migration Service vereist. Zie voor stapsgewijze instructies over het gebruik van aangepaste rollen het artikel [aangepaste rollen voor SQL Server voor het SQL database van online migraties van beheerde exemplaren](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Maak of noteer een Azure Storage-account in de **Standard-prestatielaag** waarmee de DMS-service de back-upbestanden van de database kan uploaden en gebruiken voor het migreren van databases.  Zorg ervoor dat u het Azure Storage-account maakt in dezelfde regio als de Azure Database Migration Service instantie is gemaakt.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **resource providers**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken**, zoek naar **Azure Database Migration Service** en selecteer vervolgens **Azure Database Migration Service** in de vervolgkeuzelijst.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service** **Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand VNet of maak een.

    Het VNet biedt Azure Database Migration Service toegang tot de bron-SQL Server en het doel SQL Database beheerde exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een VNet in azure Portal.

    Zie voor meer informatie het artikel [Network-topologieën voor het Azure SQL database van beheerde-instantie migraties met behulp van Azure database Migration service](https://aka.ms/dmsnetworkformi).

6. Selecteer een SKU in de prijscategorie Premium.

    > [!NOTE]
    > Onlinemigraties worden alleen ondersteund bij gebruik van deze categorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project, in het tekstvak **Bronservertype**, selecteer **SQL Server**, in het tekstvak **Type doelserver**, selecteer **Azure SQL Database Managed Instance** en selecteer vervolgens **Onlinegegevensmigratie** voor **Type activiteit kiezen**.

   ![Azure Database Migration Service project maken](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > SSL-verbindingen die worden versleuteld met behulp van een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op SSL met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecteer **Opslaan**.

4. Selecteer in het scherm **Brondatabases selecteren** de database **Adventureworks2012** voor migratie.

   ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Als u SSIS (SQL Server Integration Services) gebruikt, biedt DMS momenteel geen ondersteuning voor het migreren van de catalogusdatabase voor uw SSIS-projecten/pakketten (SSISDB) van SQL Server naar een beheerd Azure SQL Database-exemplaar. U kunt SSIS echter in Azure Data Factory (ADF) inrichten en uw SSIS-projecten/-pakketten opnieuw implementeren naar de bestemmings-SSISDB die wordt gehost door het beheerde Azure SQL Database-exemplaar. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

5. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef op het scherm **Details migratiedoel** waarden op voor **Toepassings-id** en **Sleutel** die het DMS-exemplaar kan gebruiken om verbinding te maken met het doelexemplaar van een beheerd Azure SQL Database-exemplaar en het Azure Storage-account.

    Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) voor meer informatie.

2. Selecteer het **abonnement** met het doelexemplaar van Azure SQL Database Managed Instance en selecteer vervolgens het doelexemplaar.

    Als u het beheerde Azure SQL Database-exemplaar nog niet hebt ingericht, selecteert u de [koppeling](https://aka.ms/SQLDBMI) voor instructies voor het inrichten van het exemplaar. Wanneer het beheerde Azure SQL Database-exemplaar klaar is, gaat u terug naar dit specifieke project om de migratie uit te voeren.

3. Geef waarden op voor **SQL-gebruiker** en **Wachtwoord** om verbinding te maken met het beheerde Azure SQL Database-exemplaar.

    ![Doel selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Selecteer **Opslaan**.

## <a name="select-source-databases"></a>Brondatabases selecteren

1. Selecteer in het scherm **Brondatabases selecteren** de brondatabase die u wilt migreren.

    ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Selecteer **Opslaan**.

## <a name="configure-migration-settings"></a>Migratie-instellingen configureren

1. Geef in het scherm **Migratie-instellingen configureren** de volgende instellingen op:

    | | |
    |--------|---------|
    |**SMB-netwerksharelocatie** | De lokale SMB-netwerk share met de back-upbestanden van de volledige data base en de back-upbestanden van het transactie logboek die Azure Database Migration Service voor migratie kunnen gebruiken. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet lees-\schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'.|
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service imiteert de gebruikers referentie voor het uploaden van de back-upbestanden naar de Azure storage-container voor de herstel bewerking. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. |
    |**Abonnement van het Azure Storage-account** | Selecteer het abonnement met het Azure Storage-account. |
    |**Azure Storage-account** | Selecteer het Azure Storage-account waarnaar DMS de back-upbestanden kan uploaden vanuit de SMB-netwerkshare en vervolgens kan gebruiken voor migratie van de database.  Het is raadzaam om het Storage-account te selecteren in dezelfde regio als de DMS-service voor optimale prestaties bij het uploaden van de bestanden. |

    ![Migratie-instellingen configureren](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Selecteer **Opslaan**.

## <a name="review-the-migration-summary"></a>Migratieoverzicht controleren

1. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

2. Bekijk en controleer de gegevens van het migratieproject.

    ![Overzicht van migratieproject](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>De migratie uitvoeren en controleren

1. Selecteer **Migratie uitvoeren**.

2. Selecteer op het scherm met migratieactiviteit **Vernieuwen** om de weergave bij te werken.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    U kunt de databases en aanmeldingscategorieën verder uitvouwen om de migratiestatus van de respectieve serverobjecten te controleren.

   ![Migratieactiviteit wordt uitgevoerd](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Migratie-cutover uitvoeren

Nadat de volledige back-up van de data base is hersteld op het doel exemplaar van SQL Database beheerde exemplaar, is de data base beschikbaar voor het uitvoeren van een migratie cutover.

1. Wanneer u klaar bent om de onlinedatabasemigratie te voltooien, selecteert u **Cutover starten**.

2. Stop al het binnenkomende verkeer naar brondatabases.

3. Neem het bestand [tail-log backup], maak het back-upbestand beschikbaar in de SMB-netwerkshare en wacht totdat deze laatste transactielogboekback-up is hersteld.

    U ziet dat op dat moment **Wijzigingen in behandeling** wordt ingesteld op 0.

4. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.

    ![Voltooien van cutover voorbereiden](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Wanneer de migratie de status **Voltooid** heeft, verbindt u uw toepassingen met het nieuwe doelexemplaar van Azure SQL Database Managed Instance.

    ![Cutover voltooit](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Een back-up van de database herstellen voor een beheerd Azure SQL Database-exemplaar](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md) voor een zelfstudie waarin wordt uitgelegd hoe u een database migreert naar een beheerd exemplaar met de opdracht T-SQL RESTORE.
* Zie [Wat is een beheerd exemplaar?](../sql-database/sql-database-managed-instance.md) voor meer informatie over beheerde exemplaren.
* Zie [Verbinding maken met toepassingen](../sql-database/sql-database-managed-instance-connect-app.md) voor meer informatie over het verbinden van apps met een beheerd exemplaar.
