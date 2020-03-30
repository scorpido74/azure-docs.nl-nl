---
title: SSIS-pakketten migreren naar SQL-beheerde instantie
titleSuffix: Azure Database Migration Service
description: Meer informatie over het migreren van SSIS-pakketten en -projecten (SQL Server Integration Services) naar een Azure SQL Database-beheerde instantie met behulp van de Azure Database Migration Service of de Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 97a466ab033a42016c0d82465d1f98e2dcae8080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297187"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>SQL Server Integration Services-pakketten migreren naar een beheerd exemplaar van Azure SQL Database
Als u SQL Server Integration Services (SSIS) gebruikt en uw SSIS-projecten/-pakketten wilt migreren van de bron SSISDB die door SQL Server wordt gehost naar de bestemming SSISDB die wordt gehost door een azure SQL Database-beheerde instantie, u Azure Database Migration Service gebruiken.

Als de versie van SSIS die u gebruikt eerder is dan 2012 of als u niet-SSISDB-pakketopslagtypen gebruikt, moet u deze converteren voordat u uw SSIS-projecten/-pakketten migreert, deze converteren met behulp van de wizard Projectconversie van Integration Services, die ook vanaf SSMS kan worden gestart. Zie voor meer informatie het artikel [Projecten converteren naar het projectimplementatiemodel](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) ondersteunt momenteel geen Azure SQL Database als doelmigratiebestemming. Zie het artikel [SQL Server Integration Services-pakketten](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)opnieuw implementeren in Azure SQL Database als u SSIS-projecten/-pakketten opnieuw wilt implementeren in Azure SQL Database.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Beoordeel bron SSIS-projecten/-pakketten.
> * SSIS-projecten/-pakketten migreren naar Azure.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, moet u het volgende doen:

* Als u een Microsoft Azure Virtual Network voor de Azure Database Migration Service wilt maken met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Zie het artikel [Netwerktopologieën voor Azure SQL Database-beheerde instantiemigraties met Azure Database Migration Service voor]( https://aka.ms/dmsnetworkformi)meer informatie. Zie de [virtual network documentation](https://docs.microsoft.com/azure/virtual-network/)en vooral de quickstart-artikelen met stapsgewijze details voor meer informatie over het maken van een virtueel netwerk.
* U ervoor zorgen dat de regels van uw netwerknetwerkbeveiligingsgroep de volgende binnenkomende communicatiepoorten naar Azure Database Migration Service niet blokkeren: 443, 53, 9354, 445, 12000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)voor meer informatie over het filteren van het virtuele netwerk.
* Uw Windows Firewall configureren [voor toegang tot brondatabase-engine.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)
* Als u uw Windows Firewall wilt openen, zodat de Azure Database Migration Service toegang krijgt tot de bron SQL Server, die standaard TCP-poort 1433 is.
* Als u meerdere benoemde SQL Server-exemplaren uitvoert met behulp van dynamische poorten, kunt u desgewenst de SQL Browser Service inschakelen en toegang tot de UDP-poort 1434 via uw firewalls toestaan, zodat de Azure Database Migration Service verbinding kan maken met een benoemd exemplaar op uw bronserver.
* Als u een firewallapparaat gebruikt vóór de brondatabases, moet u mogelijk firewallregels toevoegen om de Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie. Hetzelfde geldt voor bestanden via SMB-poort 445.
* Een Azure SQL Database beheerde instantie om SSISDB te hosten. Als u er een wilt maken, volgt u de details in het artikel [Een Azure SQL Database Managed Instance maken](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Om ervoor te zorgen dat de aanmeldingen die worden gebruikt om de bron SQL Server en doelbeheerde instantie met elkaar te verbinden, lid zijn van de sysadmin-serverrol.
* Controleren of SSIS is ingericht in Azure Data Factory (ADF) met Azure-SSIS Integration Runtime (IR) met de bestemming SSISDB die wordt gehost door een azure SQL Database-beheerde instantie (zoals beschreven in het artikel [De runtime van Azure-SSIS-integratie maken in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Beoordeel bron SSIS-projecten/-pakketten

Hoewel de beoordeling van bron SSISDB nog niet is geïntegreerd in de Database Migration Assistant (DMA), worden uw SSIS-projecten/-pakketten beoordeeld/gevalideerd wanneer ze opnieuw worden geïmplementeerd op de bestemming SSISDB die wordt gehost op een azure SQL-database beheerde instantie.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

    ![Portal-abonnementen weergeven](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecteer het abonnement waarin u de instantie van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer Vervolgens rechts van **Microsoft.DataMigration**de optie **Register**.

    ![Resourceprovider registreren](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Een Azure Database Migration Service-exemplaar maken

1. Selecteer in de Azure-portal + **Een resource maken,** zoeken naar **Azure Database Migration Service**en selecteer vervolgens Azure Database Migration **Service** in de vervolgkeuzelijst.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van DMS wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak er een.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de bron SQL Server en het doel Azure SQL Database beheerde exemplaar.

    Zie het artikel [Een virtueel netwerk maken met de Azure-portal](https://aka.ms/DMSVnet)voor meer informatie over het maken van een virtueel netwerk in Azure-portal.

    Zie het artikel [Netwerktopologieën voor migraties van Azure SQL Database Managed Instance met behulp van de Azure Database Migration Service](https://aka.ms/dmsnetworkformi) voor aanvullende informatie.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![DMS-service starten](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat er een exemplaar van de service is gemaakt, zoekt u het exemplaar in de Azure-portal, opent u het en maakt u vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van de Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van het exemplaar dat u hebt gemaakt en selecteer vervolgens het exemplaar.

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam voor het project op, selecteer in het tekstvak **Bronservertype** **SQL Server**, selecteer in het tekstvak **Doelservertype,** selecteer **Azure SQL Database Managed Instance**en selecteer vervolgens voor Type **activiteit**kiezen de **optie SSIS-pakketmigratie**.

   ![DMS-project maken](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selecteer **Maken** om het project te maken.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brongegevens** de verbindingsgegevens op voor de brondatabase van SQL Server.

2. Als u geen vertrouwd certificaat hebt geïnstalleerd op de server, schakelt u het selectievakje **Servercertificaat vertrouwen** in.

    Wanneer er geen vertrouwd certificaat is geïnstalleerd, genereert SQL Server een zelfondertekend certificaat wanneer het exemplaar wordt gestart. Dit certificaat wordt gebruikt voor het versleutelen van de referenties voor clientverbindingen.

    > [!CAUTION]
    > TLS-verbindingen die worden versleuteld met een zelfondertekend certificaat bieden geen sterke beveiliging. Ze zijn vatbaar voor man-in-the-middle-aanvallen. U moet niet vertrouwen op TLS met behulp van zelfondertekende certificaten in een productieomgeving of op servers die zijn verbonden met het internet.

   ![Brondetails](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Migratiedoeldetails** de verbindingsdetails voor het doel op.

     ![Doelgegevens](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecteer **Opslaan**.

## <a name="review-the-migration-summary"></a>Migratieoverzicht controleren

1. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

2. Geef voor de **optie Overschrijven van SSIS-projecten(en) en omgevingen**de optie of bestaande SSIS-projecten en -omgevingen moeten worden overschreven of genegeerd.

    ![Overzicht van migratieproject](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Bekijk en controleer de gegevens van het migratieproject.

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de migratierichtlijnen in de [Migratiehandleiding voor Microsoft-database](https://datamigration.microsoft.com/).
