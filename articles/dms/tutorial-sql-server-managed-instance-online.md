---
title: 'Zelf studie: SQL Server online migreren naar een beheerd exemplaar van SQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het uitvoeren van een online migratie van SQL Server naar een door Azure SQL beheerd exemplaar met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 4bd6c3dc1f3cd1ef553efc6ac3cd3c4e558afc97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087659"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Zelf studie: SQL Server naar een Azure SQL Managed instance online migreren met behulp van DMS

U kunt Azure Database Migration Service gebruiken om de data bases van een SQL Server exemplaar te migreren naar een [beheerd exemplaar van Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) met minimale downtime. Zie het artikel [SQL Server exemplaar migratie naar Azure SQL Managed instance](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)(Engelstalig) voor aanvullende methoden waarvoor mogelijk hand matige inspanningen nodig zijn.

In deze zelf studie migreert u de **Adventureworks2012** -data base vanuit een on-premises exemplaar van SQL Server naar een SQL Managed instance met minimale downtime door gebruik te maken van Azure database Migration service.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratie project en start online migratie met behulp van Azure Database Migration Service.
> * Houd de migratie in de gaten.
> * Voer de migratie cutover uit wanneer u klaar bent.

> [!IMPORTANT]
> Voor online migraties van SQL Server naar een door SQL beheerd exemplaar met behulp van Azure Database Migration Service, moet u de volledige back-up van de data base en volgende logboek back-ups in de SMB-netwerk share opgeven die door de service kan worden gebruikt om uw data bases te migreren. Azure Database Migration Service initieert geen back-ups en maakt in plaats daarvan gebruik van bestaande back-ups, die u mogelijk al hebt als onderdeel van het nood herstel plan voor de migratie.
> Zorg ervoor dat u [back-ups maakt met behulp van de optie with checksum](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Zorg er ook voor dat u niet meerdere back-ups (dus volledig en t-Logboeken) toevoegt aan één back-upmedium. Maak elke back-up op een afzonderlijk back-upbestand. Ten slotte kunt u gecomprimeerde back-ups gebruiken om de kans te verkleinen dat er mogelijke problemen optreden bij het migreren van grote back-ups.

> [!NOTE]
> Als u Azure Database Migration Service voor het uitvoeren van een online migratie wilt gebruiken, moet u een instantie maken op basis van de prijs categorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt micro soft aan om een instantie van Azure Database Migration Service te maken in dezelfde Azure-regio als de doel database. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen en fouten veroorzaken.

> [!IMPORTANT]
> Verminder zo veel mogelijk de duur van het online migratie proces om het risico van de onderbreking te minimaliseren die wordt veroorzaakt door herconfiguratie van exemplaren of gepland onderhoud. In het geval van een dergelijke gebeurtenis wordt het migratie proces vanaf het begin gestart. In het geval van gepland onderhoud geldt een respijt periode van 36 uur voordat het migratie proces opnieuw wordt gestart.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een online migratie van SQL Server naar een beheerd exemplaar van SQL beschreven. Zie [SQL Server naar een SQL Managed instance migreren met behulp van DMS](tutorial-sql-server-to-managed-instance.md)voor een offline migratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager implementatie model, dat site-naar-site-verbinding met uw on-premises bron servers biedt met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Leer netwerk topologieën voor migraties van SQL Managed instances met behulp van Azure database Migration service](https://aka.ms/dmsnetworkformi). Raadpleeg de [documentatie van Virtual Network](https://docs.microsoft.com/azure/virtual-network/)voor meer informatie over het maken van een virtueel netwerk, met name de Quick Start-artikelen met stapsgewijze Details.

    > [!NOTE]
    > Als u tijdens de installatie van het virtuele netwerk ExpressRoute gebruikt met Network-peering voor micro soft, voegt u de volgende service- [eind punten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) toe aan het subnet waarin de service wordt ingericht:
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
    > * Schakel [subnet delegering](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) op mi-subnet in en werk de firewall regels van het opslag account om dit subnet toe te staan.

* Zorg ervoor dat de regels voor de netwerk beveiligings groep van uw virtuele netwerk niet de volgende binnenkomende communicatie poorten blok keren tot Azure Database Migration Service: 443, 53, 9354, 445, 12000. Zie het artikel [netwerk verkeer filteren met netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van NSG verkeer van virtuele netwerken.
* Configureer uw [Windows-firewall voor toegang tot de engine van de brondatabase](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Open uw Windows Firewall om Azure Database Migration Service toegang te geven tot de bron SQL Server, die standaard TCP-poort 1433 is.
* Als u meerdere benoemde exemplaren van SQL Server met dynamische poorten uitvoert, kunt u de SQL Browser-service inschakelen en toegang tot UDP-poort 1434 toestaan via uw firewalls zodat Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bron server.
* Als u voor uw bron databases een firewall apparaat gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database (s) voor migratie, evenals bestanden via SMB-poort 445.
* Maak een door SQL beheerd exemplaar door de details in het artikel [een SQL Managed instance maken in de Azure Portal](https://aka.ms/sqldbmi)te volgen.
* Zorg ervoor dat de aanmeldingen die worden gebruikt om verbinding te maken met de bron SQL Server en het beheerde exemplaar van SQL Managed instance lid zijn van de serverrol sysadmin.
* Geef een SMB-netwerk share op met alle back-upbestanden van de data base en volgende back-upbestanden voor transactie logboeken, die Azure Database Migration Service kunnen gebruiken voor database migratie.
* Zorg ervoor dat het serviceaccount van het bronexemplaar van SQL Server schrijfbevoegdheid heeft voor de netwerkshare die u hebt gemaakt en dat het computeraccount voor de bronserver lees-/schrijftoegang heeft tot de share.
* Maak een notitie van een Windows-gebruiker (en wachtwoord) die volledig beheer heeft over de netwerkshare die u eerder hebt gemaakt. Azure Database Migration Service imiteert de gebruikers referentie voor het uploaden van de back-upbestanden naar Azure Storage container voor de herstel bewerking.
* Maak een Azure Active Directory-toepassings-ID die de toepassings-ID-sleutel genereert die Azure Database Migration Service kan gebruiken om verbinding te maken met het doel-Azure data base Managed instance en Azure Storage container. Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) voor meer informatie.

  > [!NOTE]
  > Voor Azure Database Migration Service is de machtiging Inzender vereist voor het abonnement voor de opgegeven toepassings-ID. U kunt ook aangepaste rollen maken die de specifieke machtigingen verlenen die Azure Database Migration Service vereist. Zie voor stapsgewijze instructies over het gebruik van aangepaste rollen het artikel [aangepaste rollen voor SQL Server naar online migraties van SQL Managed instance](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Maak of noteer een Azure Storage-account in de **Standard-prestatielaag** waarmee de DMS-service de back-upbestanden van de database kan uploaden en gebruiken voor het migreren van databases.  Zorg ervoor dat u het Azure Storage-account maakt in dezelfde regio als de Azure Database Migration Service instantie is gemaakt.

  > [!NOTE]
  > Wanneer u een Data Base migreert die wordt beveiligd door [transparent Data Encryption](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview) naar een beheerd exemplaar met behulp van een online migratie, moet het bijbehorende certificaat van de on-premises of de Azure VM-SQL Server instantie worden gemigreerd voordat de data base wordt hersteld. Zie [een TDe-certificaat migreren naar een beheerd exemplaar](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview)voor gedetailleerde stappen.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure Portal + **een resource maken**, zoek naar **Azure database Migration service**en selecteer vervolgens **Azure database Migration service** in de vervolg keuzelijst.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en het doel-SQL beheerde exemplaar.

    Zie het artikel [een virtueel netwerk maken met behulp van de Azure Portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in azure Portal.

    Zie het artikel [netwerk topologieën voor SQL Managed instance-migraties met Azure database Migration service](https://aka.ms/dmsnetworkformi)voor meer informatie.

6. Selecteer een SKU in de prijscategorie Premium.

    > [!NOTE]
    > Onlinemigraties worden alleen ondersteund bij gebruik van deze categorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Alle exemplaren van Azure Database Migration Service zoeken](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratie project** een naam op voor het project, Selecteer in het tekstvak **type bron server** de optie **SQL Server**, Selecteer in het tekstvak **doel server type** de optie **Azure SQL Managed instance**en selecteer vervolgens bij **type activiteit**de optie **Online gegevens migratie**.

   ![Azure Database Migration Service project maken](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die zijn versleuteld met een zelfondertekend certificaat, bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met zelfondertekende certificaten in een productie omgeving of op servers die zijn verbonden met internet.

   ![Brondetails](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selecteer **Opslaan**.

4. Selecteer in het scherm **Brondatabases selecteren** de database **Adventureworks2012** voor migratie.

   ![Brondatabases selecteren](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Als u SQL Server Integration Services (SSIS) gebruikt, biedt DMS momenteel geen ondersteuning voor het migreren van de catalogus database voor uw SSIS-projecten/-pakketten (SSISDB) van SQL Server naar een SQL Managed instance. U kunt echter SSIS inrichten in Azure Data Factory (ADF) en uw SSIS-projecten/-pakketten opnieuw implementeren op de doel-SSISDB die worden gehost door een door SQL beheerd exemplaar. Zie het artikel [Pakketten van SQL Server Integration Services migreren naar Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) voor meer informatie over het migreren van SSIS-pakketten.

5. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **doel Details van migratie** de **toepassings-id** en de **sleutel** op die het DMS-exemplaar kan gebruiken om verbinding te maken met het doel exemplaar van SQL Managed instance en het Azure Storage-account.

    Zie het artikel [Portal gebruiken voor het maken van een Azure Active Directory-toepassing en een -service-principal die toegang hebben tot resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) voor meer informatie.

2. Selecteer het **abonnement** met het doel exemplaar van SQL Managed instance en selecteer vervolgens het doel exemplaar.

    Als u het SQL Managed instance nog niet hebt ingericht, selecteert u de [koppeling](https://aka.ms/SQLDBMI) om u te helpen het exemplaar in te richten. Wanneer het beheerde exemplaar van SQL is voltooid, keert u terug naar dit specifieke project om de migratie uit te voeren.

3. Geef een **SQL-gebruiker** en- **wacht woord** op om verbinding te maken met het door SQL beheerde exemplaar.

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
    |**SMB-netwerksharelocatie** | De lokale SMB-netwerk share of de Azure-bestands share met de back-upbestanden van de volledige data base en de back-upbestanden voor transactie logboeken die Azure Database Migration Service voor migratie kunnen gebruiken. Het serviceaccount waarmee het SQL Server-bronexemplaar wordt uitgevoerd, moet lees-\schrijfbevoegdheid op deze netwerkshare hebben. Geef een FQDN-naam of IP-adressen op van de server in de netwerkshare, bijvoorbeeld '\\\servernaam.domeinnaam.com\back-upmap' of '\\\IP-adres\back-upmap'. Voor betere prestaties kunt u het beste afzonderlijke mappen gebruiken voor elke Data Base die moet worden gemigreerd. U kunt het pad naar de bestands share op database niveau opgeven met de optie **Geavanceerde instellingen** . |
    |**Gebruikersnaam** | Zorg ervoor dat de Windows-gebruiker volledig beheer heeft over de netwerkshare die u hierboven hebt opgegeven. Azure Database Migration Service imiteert de gebruikers referentie voor het uploaden van de back-upbestanden naar Azure Storage container voor de herstel bewerking. Als u Azure-bestands share gebruikt, gebruikt u de naam van het opslag account pre-pended met AZURE \ als de gebruikers naam. |
    |**Wachtwoord** | Het wachtwoord voor de gebruiker. Als u Azure-bestands share gebruikt, gebruikt u een sleutel voor het opslag account als wacht woord. |
    |**Abonnement van het Azure Storage-account** | Selecteer het abonnement met het Azure Storage-account. |
    |**Azure Storage-account** | Selecteer het Azure Storage-account waarnaar DMS de back-upbestanden kan uploaden vanuit de SMB-netwerkshare en vervolgens kan gebruiken voor migratie van de database.  Het is raadzaam om het Storage-account te selecteren in dezelfde regio als de DMS-service voor optimale prestaties bij het uploaden van de bestanden. |

    ![Migratie-instellingen configureren](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Als Azure Database Migration Service fout ' systeem fout 53 ' of ' systeem fout 57 ' wordt weer gegeven, kan dit tot gevolg hebben dat Azure Database Migration Service geen toegang meer heeft tot de Azure-bestands share. Als u een van deze fouten tegen komt, moet u via [de instructies in](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)het virtuele netwerk toegang verlenen tot het opslag account.

    > [!IMPORTANT]
    > Als de functie loop back-controle is ingeschakeld en de bron SQL Server en de bestands share zich op dezelfde computer bevinden, is de bron niet in staat om toegang te krijgen tot de bestanden harenaa die gebruikmaken van FQDN. Als u dit probleem wilt oplossen, schakelt u de functie loop back-controle uit met behulp van de instructies [hier](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

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

Nadat de volledige back-up van de data base is hersteld op het doel exemplaar van het SQL Managed instance, is de data base beschikbaar voor het uitvoeren van een migratie cutover.

1. Wanneer u klaar bent om de onlinedatabasemigratie te voltooien, selecteert u **Cutover starten**.

2. Stop al het binnenkomende verkeer naar brondatabases.

3. Neem het bestand [tail-log backup], maak het back-upbestand beschikbaar in de SMB-netwerkshare en wacht totdat deze laatste transactielogboekback-up is hersteld.

    U ziet dat op dat moment **Wijzigingen in behandeling** wordt ingesteld op 0.

4. Selecteer **Bevestigen** en selecteer vervolgens **Toepassen**.

    ![Voltooien van cutover voorbereiden](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Wanneer de status van de database migratie **is voltooid**, verbindt u uw toepassingen met het nieuwe doel exemplaar van SQL Managed instance.

    ![Cutover voltooit](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [een back-up herstellen naar een SQL-beheerd exemplaar met behulp van de opdracht herstellen](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)voor een zelf studie waarin wordt beschreven hoe u een Data Base naar een beheerd exemplaar van SQL migreert met de opdracht voor het terugzetten van T-SQL.
* Zie [What is SQL Managed](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)instance (Engelstalig) voor meer informatie over SQL Managed instance.
* Zie [toepassingen verbinden](../azure-sql/managed-instance/connect-application-instance.md)voor meer informatie over het verbinden van apps met een SQL Managed instance.
