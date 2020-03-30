---
title: Contoso migratiereeks | Microsoft Documenten
description: Biedt een overzicht van de migratiestrategie en -scenario's die Contoso gebruikt om hun on-premises datacenter naar Azure te migreren.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: d37bee589eb7ee2e6e30c8dcea2531dd1f063481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78939182"
---
# <a name="contoso-migration-series"></a>Migratieserie Contoso


We hebben een reeks artikelen die laten zien hoe de fictieve organisatie Contoso on-premises infrastructuur migreert naar de [Microsoft Azure-cloud.](https://azure.microsoft.com/overview/what-is-azure/) 

De serie bevat informatie en scenario's die illustreren hoe u een migratie van infrastructuur instellen en verschillende soorten migraties uitvoeren. Scenario's groeien in complexiteit naarmate ze vorderen. De artikelen laten zien hoe het Bedrijf Contoso zijn migratiemissie voltooit, maar overal worden aanwijzingen gegeven voor algemene lectuur en specifieke instructies.

## <a name="migration-articles"></a>Migratieartikelen

De artikelen in de serie worden samengevat in de onderstaande tabel.  

- Elk migratiescenario wordt aangestuurd door enigszins verschillende bedrijfsdoelen die de migratiestrategie bepalen.
- Voor elk implementatiescenario bieden we informatie over zakelijke drijfveren en doelen, een voorgestelde architectuur, stappen voor het uitvoeren van de migratie en aanbevelingen voor opschoning en volgende stappen nadat de migratie is voltooid.

**Artikel** | **Details** 
--- | --- 
[Artikel 1: Overzicht](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Overzicht van de artikelreeks, de migratiestrategie van Contoso en de voorbeeld-apps die in de serie worden gebruikt. 
[Artikel 2: Azure-infrastructuur implementeren](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso bereidt zijn on-premises infrastructuur en azure-infrastructuur voor op migratie. Dezelfde infrastructuur wordt gebruikt voor alle migratieartikelen in de serie. 
[Artikel 3: On-premises resources beoordelen voor migratie naar Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso voert een beoordeling uit van de on-premises SmartHotel360-app die op VMware wordt uitgevoerd. Contoso beoordeelt app-VM's met behulp van de Azure Migrate-service en de SQL Server-database van de app met behulp van Data Migration Assistant.
[Artikel 4: Een app opnieuw hosten op een Azure VM- en SQL Database Managed Instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso voert een lift-and-shift-migratie uit naar Azure voor de on-premises SmartHotel360-app. Contoso migreert de front-end VM van de app met [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migreert de app-database naar een Azure SQL Database Managed Instance met behulp van de [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: Een app opnieuw hosten op Azure VM's](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migreert zijn SmartHotel360-app VM's naar Azure VM's met behulp van de Site Recovery-service. 
[Artikel 6: Een app opnieuw hosten op Azure VM's en in een SQL Server AlwaysOn-beschikbaarheidsgroep](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migreert de SmartHotel360-app. Contoso gebruikt Site Recovery om de VM's van de app te migreren. De databasemigratieservice wordt gebruikt om de app-database te migreren naar een SQL Server-cluster dat is beveiligd door een beschikbaarheidsgroep alwayson. 
[Artikel 7: Een Linux-app opnieuw hosten op Azure VM's](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso voltooit een lift-and-shift-migratie van zijn Linux-osTicket-app naar Azure VM's, met behulp van de Site Recovery-service.
[Artikel 8: Een Linux-app opnieuw hosten op Azure VM's en Azure Database voor MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migreert zijn Linux osTicket-app naar Azure VM's met behulp van Site Recovery. De app-database wordt gemigreerd naar Azure Database for MySQL met behulp van MySQL Workbench. 
[Artikel 9: Een app in een Azure-web-app en Azure SQL-database opnieuw incalculeren](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migreert zijn SmartHotel360-app naar een Azure-webapp en migreert de app-database naar een Azure SQL Server-exemplaar met de DatabaseMigratieassistent.     
[Artikel 10: Een Linux-app in een Azure-web-app en Azure Database voor MySQL refactor](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migreert zijn Linux-osTicket-app naar een Azure-web-app op meerdere Azure-regio's met Azure Traffic Manager, geïntegreerd met GitHub voor continue levering. Contoso migreert de app-database naar een Azure Database voor MySQL-instantie. 
[Artikel 11: Refactor Team Foundation Server op Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migreert de on-premises Team Foundation Server-implementatie naar Azure DevOps Services in Azure.
[Artikel 12: Een app opnieuw ontwerpen in Azure-containers en Azure SQL-database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migreert zijn SmartHotel-app naar Azure. Vervolgens wordt de weblaag van de app opnieuw ontworpen als een Windows-container die wordt uitgevoerd in Azure Service Fabric en de database met Azure SQL Database.
[Artikel 13: Een app opnieuw opbouwen in Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso herbouwt zijn SmartHotel-app met behulp van een reeks Azure-mogelijkheden en -services, waaronder Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services en Azure Cosmos DB.
[Artikel 14: Een migratie naar Azure schalen](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Na het uitproberen van migratiecombinaties bereidt Contoso zich voor om te schalen naar een volledige migratie naar Azure.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) cloudmigratie.
- Meer informatie over migratiestrategieën voor andere scenario's (bron-/doelparen) vindt u in de [databasemigratiehandleiding.](https://datamigration.microsoft.com/)
