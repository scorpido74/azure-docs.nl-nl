---
title: 'Zelfstudie: SQL Server online migreren naar een SQL-beheerde instantie'
titleSuffix: Azure Database Migration Service
description: Leer een online migratie uitvoeren van SQL Server on-premises naar een Azure SQL Database beheerd exemplaar met behulp van Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298905"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Zelfstudie: SQL Server migreren naar een Azure SQL Database beheerd exemplaar online met Behulp van DMS

U Azure Database Migration Service gebruiken om de databases te migreren van een on-premises SQL Server-instantie naar een [Azure SQL Database-beheerde instantie](../sql-database/sql-database-managed-instance.md) met minimale downtime. In het artikel [Een SQL Server-exemplaar migreren naar een beheerd Azure SQL Database-exemplaar](../sql-database/sql-database-managed-instance-migrate.md) vindt u aanvullende methoden, waarvoor enkele handmatige stappen nodig kunnen zijn.

In deze zelfstudie migreert u de **Adventureworks2012-database** van een on-premises instantie van SQL Server naar een SQL Database-beheerexemplaar met minimale downtime met behulp van Azure Database Migration Service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject en start online migratie met Azure Database Migration Service.
> * De migratie controleren.
> * Voer de migratiecutover uit wanneer u er klaar voor bent.

> [!IMPORTANT]
> Voor online migraties van SQL Server naar SQL Database beheerd exemplaar met Azure Database Migration Service, moet u de volledige databaseback-up en volgende logboekback-ups in het SMB-netwerkaandeel bieden die de service kan gebruiken om uw databases te migreren. Azure Database Migration Service start geen back-ups en gebruikt in plaats daarvan bestaande back-ups, die u mogelijk al hebt als onderdeel van uw noodherstelplan, voor de migratie.
> Zorg ervoor dat u [back-ups maakt met de optie MET CHECKSUM.](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017) Zorg er ook voor dat u niet meerdere back-ups (d.w.z. volledig en t-log) toetetoevoegen aan één back-upmedia; neem elke back-up op een apart back-upbestand. Ten slotte u gecomprimeerde back-ups gebruiken om de kans op mogelijke problemen in verband met het migreren van grote back-ups te verkleinen.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, moet u een instantie maken op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

> [!IMPORTANT]
> Verminder de duur van het online migratieproces zoveel mogelijk om het risico op onderbreking als gevolg van de herconfiguratie van bijvoorbeelden of gepland onderhoud te minimaliseren. In het geval van een dergelijke gebeurtenis begint het migratieproces vanaf het begin. In het geval van gepland onderhoud is er een respijtperiode van 36 uur voordat het migratieproces opnieuw wordt gestart.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een onlinemigratie van SQL Server naar een SQL Database-beheerde instantie beschreven. Zie SQL Server [migreren naar een SQL Database-exemplaar offline migreren met DMS](tutorial-sql-server-to-managed-instance.md)voor een offlinemigratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) [Meer informatie over netwerktopologieën voor Azure SQL Database beheerde instantiemigraties met Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.

    > [!NOTE]
    > Als u ExpressRoute gebruikt met netwerkpeering naar Microsoft, voegt u tijdens de installatie van het virtuele netwerk ExpressRoute met netwerkpeering aan Microsoft de volgende [serviceeindpunten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Eindpunt voor opslag
    > * Eindpunt van servicebus
    >
    > Deze configuratie is nodig omdat azure database migratieservice geen internetverbinding heeft.
    >
    >Als u geen site-to-site-connectiviteit hebt tussen het on-premises netwerk en Azure of als er beperkte bandbreedte van site-to-site-connectiviteit is, u overwegen azure databasemigratieservice in hybride modus te gebruiken (Voorbeeld). De hybride modus maakt gebruik van een on-premises migratiewerknemer, samen met een instantie van Azure Database Migration Service die in de cloud wordt uitgevoerd. Als u een instantie van Azure Database Migration Service in hybride modus wilt maken, raadpleegt u het artikel [Een instantie van Azure Database Migration Service maken in hybride modus met behulp van de Azure-portal](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Met betrekking tot het opslagaccount dat als onderdeel van de migratie wordt gebruikt, moet u het:
    > * Kies ervoor om alle netwerken toegang te geven tot het opslagaccount.
    > * Schakel [subnetdelegatie](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) in op MI-subnet en werk de firewallregels voor opslagaccount bij om dit subnet toe te staan.

* Zorg ervoor dat de regels van uw beveiligingsgroep voor virtuele netwerken niet de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
* Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall zodat Azure Database Migration Service toegang heeft tot de bron SQL Server, die standaard TCP-poort 1433 is.
* Als u SQL Server-exemplaren met meerdere benoemde sql-servers uitvoert met dynamische poorten, u de SQL Browser Service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls, zodat azure databasemigratieservice verbinding kan maken met een benoemde instantie op uw bron Server.
* Als u een firewalltoestel gebruikt voor uw brondatabases, moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie en bestanden via SMB-poort 445.
* Maak een SQL Database-beheerde instantie door de details in het artikel Een Azure [SQL Database-beheerde instantie te volgen in de Azure-portal.](https://aka.ms/sqldbmi)
* Zorg ervoor dat de aanmeldingsreferenties die worden gebruikt om het bronexemplaar van SQL Server en het doelexemplaar van Managed Instance te verbinden, lid zijn van de serverrol sysadmin.
* Geef een SMB-netwerkshare op dat al uw databaseback-upbestanden en volgende back-upbestanden voor transactielogboeken bevat, die Azure Database Migration Service kan gebruiken voor databasemigratie.
* Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
* Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service doet zich voor als de gebruikersreferenties om de back-upbestanden te uploaden naar de Azure Storage-container voor herstelbewerking.
* Maak een Azure Active Directory Application Id die de Toepassings-id-sleutel genereert die Azure Database Migration Service kan gebruiken om verbinding te maken met de door Azure Database beheerde instantie en Azure Storage Container. Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) voor meer informatie.

  > [!NOTE]
  > Azure Database Migration Service vereist de inzendertoestemming voor het abonnement voor de opgegeven toepassings-id. U ook aangepaste rollen maken die de specifieke machtigingen verlenen waarvoor Azure Database Migration Service hiervoor is vereist. Zie het artikel [Aangepaste rollen voor SQL Server naar SQL Database beheerde instantie online migraties](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance)voor stapsgewijze richtlijnen voor het gebruik van aangepaste rollen.

* Maak of noteer een Azure Storage-account in de **Standard-prestatielaag** waarmee de DMS-service de back-upbestanden van de database kan uploaden en gebruiken voor het migreren van databases.  Zorg ervoor dat u het Azure Storage-account maakt in dezelfde regio als het exemplaar Azure Database Migration Service is gemaakt.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken,** zoeken naar **Azure Database Migration Service**en selecteer vervolgens Azure Database Migration **Service** in de vervolgkeuzelijst.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak er een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en doel SQL Database managed instance.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in Azure-portal.

    Zie voor meer informatie het artikel [Netwerktopologieën voor Azure SQL Database beheerde instantiemigraties met Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

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

   ![Azure Database Migration Service Project maken](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die worden versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met het internet.

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

2. Selecteer het **abonnement** met de doelinstantie van azure SQL Database managed instance en selecteer vervolgens de doelinstantie.

    Als u de beheerde instantie Azure SQL Database nog niet hebt ingericht, selecteert u de [koppeling](https://aka.ms/SQLDBMI) waarmee u de instantie inrichten. Wanneer het beheerde Azure SQL Database-exemplaar klaar is, gaat u terug naar dit specifieke project om de migratie uit te voeren.

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
    |**SMB-netwerksharelocatie** | De lokale SMB-netwerkshare of Azure-bestandsshare die de back-upbestanden van de volledige database en back-upbestanden voor transactielogboeken bevat die Azure Database Migration Service kan gebruiken voor migratie. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet lees-\schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'. Voor betere prestaties wordt aanbevolen om een afzonderlijke map te gebruiken voor elke database die moet worden gemigreerd. U het pad voor bestandsshare op databaseniveau opgeven met de optie **Geavanceerde instellingen.** |
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service doet zich voor als de gebruikersreferenties om de back-upbestanden te uploaden naar de Azure Storage-container voor herstelbewerking. Als u Azure File Share gebruikt, gebruikt u de naam van het opslagaccount vooraf met AZURE\ als gebruikersnaam. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. Als u Azure-bestandsshare gebruikt, gebruikt u een opslagaccountsleutel als wachtwoord. |
    |**Abonnement van het Azure Storage-account** | Selecteer het abonnement met het Azure Storage-account. |
    |**Azure-opslagaccount** | Selecteer het Azure Storage-account waarnaar DMS de back-upbestanden kan uploaden vanuit de SMB-netwerkshare en vervolgens kan gebruiken voor migratie van de database.  Het is raadzaam om het Storage-account te selecteren in dezelfde regio als de DMS-service voor optimale prestaties bij het uploaden van de bestanden. |

    ![Migratie-instellingen configureren](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Als azure databasemigratieservice fout 'Systeemfout 53' of 'Systeemfout 57' weergeeft, kan de oorzaak het gevolg zijn van het onvermogen van Azure Database Migration Service om toegang te krijgen tot Azure-bestandsshare. Als u een van deze fouten tegenkomt, u toegang verlenen tot het opslagaccount van het virtuele netwerk met behulp van de [instructies hier.](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)

    > [!IMPORTANT]
    > Als de functionaliteit van loopback-controle is ingeschakeld en de bron SQL Server en bestandsshare zich op dezelfde computer bevinden, heeft de bron geen toegang tot de bestanden met FQDN. Als u dit probleem wilt oplossen, schakelt u de functionaliteit van de lusbackcontrole uit met de instructies [hier](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

Nadat de volledige databaseback-up is hersteld op de doelinstantie van SQL Database-beheerde instantie, is de database beschikbaar voor het uitvoeren van een migratiecutover.

1. Wanneer u klaar bent om de onlinedatabasemigratie te voltooien, selecteert u **Cutover starten**.

2. Stop al het binnenkomende verkeer naar brondatabases.

3. Neem het bestand [tail-log backup], maak het back-upbestand beschikbaar in de SMB-netwerkshare en wacht totdat deze laatste transactielogboekback-up is hersteld.

    U ziet dat op dat moment **Wijzigingen in behandeling** wordt ingesteld op 0.

4. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.

    ![Voltooien van cutover voorbereiden](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Wanneer de status van de databasemigratie **Voltooid**weergeeft, verbindt u uw toepassingen met de nieuwe doelinstantie van azure SQL Database-beheerde instantie.

    ![Cutover voltooit](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Een back-up herstellen naar een beheerde instantie met de](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)opdracht Herstellen naar een beheerde instantie voor een zelfstudie waarin u laat zien hoe u een database migreert naar een beheerde instantie met de opdracht Herstellen.
* Zie [Wat is een beheerde instantie](../sql-database/sql-database-managed-instance.md)voor informatie over beheerde instantie .
* Zie [Toepassingen verbinden](../sql-database/sql-database-managed-instance-connect-app.md)voor informatie over het verbinden van apps met een beheerde instantie.
