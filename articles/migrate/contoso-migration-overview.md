---
title: Contoso migratiereeks | Microsoft Documenten
description: Koppelingen naar voorbeeldmigratiescenario's van Contoso voor migratie naar Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676328"
---
# <a name="contoso-migration-series"></a>Migratieserie Contoso


We hebben een reeks artikelen die laten zien hoe de fictieve organisatie Contoso zijn on-premises infrastructuur migreert naar de [Microsoft Azure-cloud.](https://azure.microsoft.com/overview/what-is-azure/) 

De serie bevat scenario's die illustreren hoe u een infrastructuurmigratie instellen en hoe u verschillende soorten migraties uitvoeren. Scenario's groeien in complexiteit naarmate ze vorderen. De artikelen laten zien hoe het Bedrijf Contoso omgaat met migratie, maar algemene instructies en aanwijzingen worden overal verstrekt.

## <a name="migration-articles"></a>Migratieartikelen

De artikelen in de serie worden samengevat in de onderstaande tabel.  

- Elk migratiescenario wordt aangestuurd door iets andere bedrijfsvereisten, die de migratiestrategie bepalen.
- Voor elk implementatiescenario bieden we informatie over zakelijke stuurprogramma's en doelen, een voorgestelde architectuur, stappen om de migratie uit te voeren en aanbevelingen voor het opruimen en de volgende stappen nadat de migratie is voltooid.


**Artikel** | **Details** 
--- | --- 
[Artikel 1: Overzicht](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Overzicht van de artikelreeks, de migratiestrategie van Contoso en de voorbeeld-apps die in de serie worden gebruikt. 
[Artikel 2: Azure-infrastructuur implementeren](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso bereidt zijn on-premises infrastructuur en Azure-infrastructuur voor op migratie. Dezelfde infrastructuur wordt gebruikt voor alle artikelen in de serie. 
[Artikel 3: On-premises resources beoordelen voor migratie naar Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso voert een beoordeling uit van de on-premises SmartHotel360-app die op VMware wordt uitgevoerd. Contoso beoordeelt app-VM's met behulp van de Azure Migrate-service en de SQL Server-database van de app met behulp van Data Migration Assistant.
[Artikel 4: Een app opnieuw hosten op een Azure VM- en SQL Database Managed Instance](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso voert een lift-and-shift-migratie uit naar Azure voor de on-premises SmartHotel360-app. Contoso migreert de front-end VM van de app met [Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso migreert de app-database naar een Azure SQL Database Managed Instance, met behulp van de [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: Een app opnieuw hosten op Azure VM's](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migreert zijn SmartHotel360-app VM's naar Azure VM's met behulp van de Azure Migrate-service. 
[Artikel 6: Een app opnieuw hosten op Azure VM's en in een SQL Server AlwaysOn-beschikbaarheidsgroep](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso migreert de SmartHotel360-app. Contoso gebruikt Azure Migrate om de VM's van de app te migreren. De databasemigratieservice wordt gebruikt om de app-database te migreren naar een SQL Server-cluster dat is beveiligd door een beschikbaarheidsgroep alwayson. 
[Artikel 7: Een Linux-app opnieuw hosten op Azure VM's](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso voltooit een lift-and-shift-migratie van zijn Linux-osTicket-app naar Azure VM's met Azure Migrate.
[Artikel 8: Een Linux-app opnieuw hosten op Azure VM's en Azure Database voor MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migreert zijn Linux osTicket-app naar Azure VM's met Azure Migrate. De app-database wordt gemigreerd naar Azure Database for MySQ, met azure database migration service (bevat een alternatieve optie met MySQL Workbench).
[Artikel 9: Een app in een Azure-web-app en Azure SQL-database opnieuw incalculeren](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migreert zijn SmartHotel360-app naar een Azure-web-app en migreert de app-database naar een Azure SQL Server-exemplaar met behulp van de Azure Database Migration Service.
[Artikel 10: Een Windows-app opnieuw inerachten met Azure App Services en SQL Managed Instance](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migreert een on-premises Windows-app naar een Azure-web-app en migreert de app-database naar een Azure SQL Managed Instance, met behulp van de Azure Database Migration Service.
[Artikel 11: Een Linux-app in een Azure-web-app en Azure Database voor MySQL refactor](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migreert zijn Linux-osTicket-app naar een Azure-web-app in meerdere Azure-regio's, met Azure Traffic Manager, geïntegreerd met GitHub voor continue levering. Contoso migreert de app-database naar een Azure Database voor MySQL-instantie. 
[Artikel 12: Refactor Team Foundation Server op Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migreert de on-premises Team Foundation Server-implementatie naar Azure DevOps Services in Azure.
[Artikel 13: Een app opnieuw opbouwen in Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso herbouwt zijn SmartHotel-app met behulp van een reeks Azure-mogelijkheden en -services, waaronder Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services en Azure Cosmos DB.
[Artikel 14: Een migratie naar Azure schalen](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Na het uitproberen van migratiecombinaties bereidt Contoso zich voor om te schalen naar een volledige migratie naar Azure.



## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) cloudmigratie.
- Meer informatie over migratiestrategieën voor andere scenario's (bron-/doelparen) vindt u in de [databasemigratiehandleiding.](https://datamigration.microsoft.com/)
