---
title: SSIS-migratie met Azure SQL Database-beheerde instantie als doel van de databasewerkbelasting
description: SSIS-migratie met Azure SQL Database beheerd exemplaar als de database workload bestemming.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 38010e3aaa2d0544dfbfe19135d25250d2b021a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929785"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS-migratie met Azure SQL Database-beheerde instantie als doel van de databasewerkbelasting

Wanneer u databaseworkloads migreert van SQL Server on premises naar de door Azure SQL Database beheerde instantie, moet u bekend zijn met [Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) en de [netwerktopologieën voor Azure SQL Database beheerde instantiemigraties met DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Dit artikel richt zich op de migratie van SSIS-pakketten (SQL Server Integration Service) die zijn opgeslagen in SSIS-catalogus (SSISDB) en SQL Server Agent-taken die SSIS-pakketuitvoeringen plannen.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-catalogus migreren (SSISDB)

SSISDB-migratie kan worden uitgevoerd met DMS, zoals beschreven in het artikel: [SSIS-pakketten migreren naar een azure SQL Database-beheerde instantie.](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS-taken naar Azure SQL Database managed instance agent

Azure SQL Database managed instance has a native, first-class scheduler just like SQL Server Agent on premises.  Aangezien een migratietool voor SSIS-taken nog niet beschikbaar is, moeten deze worden gemigreerd van SQL Server Agent on-premises naar Azure SQL Database managed instance agent via scripts/handmatige kopiëren.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Azure-gegevensfabriek](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Netwerktopologieën voor Azure SQL Database beheerde instantiemigraties met DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-pakketten migreren naar een beheerd exemplaar van Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met SSISDB in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
