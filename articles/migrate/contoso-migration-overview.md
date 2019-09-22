---
title: Contoso-migratie reeks | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de migratie strategie en-scenario's die door contoso worden gebruikt om hun on-premises Data Center naar Azure te migreren.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: raynew
ms.openlocfilehash: d20c0be47f44cdce8ea895007494565d37f5923f
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179162"
---
# <a name="contoso-migration-series"></a>Migratieserie Contoso


We hebben een aantal artikelen waarin wordt getoond hoe de fictieve organisatie Contoso een on-premises infra structuur migreert naar de [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) Cloud. 

De serie bevat informatie en scenario's waarin wordt uitgelegd hoe u een migratie van een infra structuur instelt en verschillende typen migraties uitvoert. Scenario's groeien in complexiteit naarmate ze worden uitgevoerd. In de artikelen ziet u hoe het contoso-bedrijf haar migratie missie heeft voltooid, maar verwijzingen naar algemene lees-en specifieke instructies worden weer gegeven.

## <a name="migration-articles"></a>Migratie artikelen

De artikelen in de reeks worden in de onderstaande tabel samenvatten.  

- Elk migratiescenario wordt aangestuurd door enigszins verschillende bedrijfsdoelen die de migratiestrategie bepalen.
- Voor elk implementatiescenario bieden we informatie over zakelijke drijfveren en doelen, een voorgestelde architectuur, stappen voor het uitvoeren van de migratie en aanbevelingen voor opschoning en volgende stappen nadat de migratie is voltooid.

**Artikel** | **Details** 
--- | --- 
[Artikel 1: Krijgt](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Overzicht van de artikel reeks, de migratie strategie van Contoso en de voor beeld-apps die in de reeks worden gebruikt. 
[Artikel 2: Azure-infra structuur implementeren](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso bereidt zijn on-premises infra structuur en de Azure-infra structuur voor migratie voor. Dezelfde infra structuur wordt gebruikt voor alle migratie artikelen in de reeks. 
[Artikel 3: On-premises resources evalueren voor migratie naar Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso voert een evaluatie uit van de on-premises SmartHotel360-app die wordt uitgevoerd op VMware. Contoso evalueert app-Vm's met behulp van de Azure Migrate-service en de app SQL Server-Data Base met behulp van Data Migration Assistant.
[Artikel 4: Een app opnieuw hosten op een Azure-VM en een SQL Database beheerd exemplaar](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso voert een lift-en-Shift-migratie naar Azure uit voor de on-premises SmartHotel360-app. Contoso migreert de front-end-app-VM met behulp van [Azure site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migreert de app-Data Base naar een door Azure SQL Database beheerd exemplaar met behulp van de [Azure database Migration service](https://docs.microsoft.com/azure/dms/dms-overview).
[Artikel 5: Een app op Azure-Vm's opnieuw hosten](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migreert de SmartHotel360-app-Vm's naar Azure-Vm's met behulp van de Site Recovery-service. 
[Artikel 6: Een app opnieuw hosten op virtuele machines in Azure en in een SQL Server AlwaysOn-beschikbaarheids groep](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso migreert de SmartHotel360-app. Contoso maakt gebruik van Site Recovery om de app-Vm's te migreren. De Database Migration Service wordt gebruikt om de app-data base te migreren naar een SQL Server-cluster dat wordt beveiligd door een AlwaysOn-beschikbaarheids groep. 
[Artikel 7: Een Linux-app op Azure-Vm's opnieuw hosten](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso voltooit een lift-en-Shift-migratie van de Linux osTicket-app naar Azure-Vm's met behulp van de Site Recovery-service.
[Artikel 8: Een Linux-app op Azure-Vm's en-Azure Database for MySQL opnieuw hosten](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migreert de Linux osTicket-app naar Azure-Vm's met behulp van Site Recovery. De app-database wordt gemigreerd naar Azure Database for MySQL met behulp van MySQL Workbench. 
[Artikel 9: Een app refactoriseren in een Azure-web-app en Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migreert de SmartHotel360-app naar een Azure-web-app en migreert de app-Data Base naar een Azure SQL Server-exemplaar met de data base-Migration Assistant.     
[Artikel 10: Een Linux-app refactoriseren in een Azure-web-app en Azure Database for MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migreert de Linux osTicket-app naar een Azure-web-app op meerdere Azure-regio's met Azure Traffic Manager, geïntegreerd met GitHub voor continue levering. Contoso migreert de app-Data Base naar een Azure Database for MySQL-exemplaar. 
[Artikel 11: Refactorion Team Foundation Server op Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migreert de on-premises Team Foundation Server implementatie naar Azure DevOps Services in Azure.
[Artikel 12: Een app opnieuw architecten in azure-containers en Azure SQL Database](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso migreert de SmartHotel-app naar Azure. Vervolgens wordt de app-weblaag opnieuw ontworpen als een Windows-container die wordt uitgevoerd in azure Service Fabric en de data base met Azure SQL Database.
[Artikel 13: Een app opnieuw samen stellen in azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso bouwt de SmartHotel-app opnieuw met behulp van een scala aan Azure-functies en-services, waaronder Azure App Service, Azure Kubernetes service (AKS), Azure Functions, Azure Cognitive Services en Azure Cosmos DB.
[Artikel 14: Een migratie naar Azure schalen](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Na het uitvoeren van de migratie combinaties, bereidt contoso voor om te schalen naar een volledige migratie naar Azure.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) Cloud migratie.
- Meer informatie over migratie strategieën voor andere scenario's (bron/doel paren) vindt u in de [hand leiding voor database migratie](https://datamigration.microsoft.com/).
