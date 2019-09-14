---
title: SSIS-migratie met Azure SQL Database Managed instance als de data base-werkbelasting bestemming | Microsoft Docs
description: SSIS-migratie met Azure SQL Database Managed instance als de werkbelasting bestemming voor de data base.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c1bb056909641ad0b8654e84f35c4fbd5b38db82
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968521"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>SSIS-migratie met Azure SQL Database Managed instance als de werk belasting bestemming van de data base

Bij het migreren van data base-workloads van SQL Server on-premises naar Azure SQL Database beheerde instantie, moet u bekend zijn met de [Azure Data Migration service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) en de [netwerktopologieën voor Azure SQL database beheerde exemplaar migraties met behulp van DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Dit artikel is gericht op de migratie van de SSIS-pakketten (SQL Server Integration service) die zijn opgeslagen in SSIS Catalog (SSISDB) en SQL Server Agent Jobs waarmee SSIS-pakket uitvoeringen worden gepland.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-catalogus migreren (SSISDB)

SSISDB-migratie kan worden uitgevoerd met behulp van DMS, zoals beschreven in het artikel: [SSIS-pakketten migreren naar een door Azure SQL database beheerd exemplaar](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>SSIS-taken voor het Azure SQL Database van Managed instance agent

Azure SQL Database beheerde instantie heeft een systeem eigen, eersteklas scheduler, net als SQL Server Agent on-premises.  Omdat er nog geen migratie hulpprogramma voor SSIS-taken beschikbaar is, moeten deze worden gemigreerd van SQL Server Agent on-premises naar Azure SQL Database beheerde exemplaar agent via scripts/hand matig kopiëren.

## <a name="additional-resources"></a>Aanvullende resources

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database migratieservice](https://docs.microsoft.com/azure/dms/dms-overview)
- [Netwerktopologieën voor het Azure SQL Database van beheerde-exemplaar migraties met behulp van DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-pakketten migreren naar een door Azure SQL Database beheerd exemplaar](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met SSISDB in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
