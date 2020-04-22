---
title: On-premises SSIS-workloads migreren naar SSIS in Azure Data Factory
description: Migreren on-premises SSIS-workloads naar SSIS in ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 2b23ffec76de3fa644abe3b65876a60c65c05eb8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686010"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>On-premises SSIS-workloads migreren naar SSIS in ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Wanneer u uw databaseworkloads migreert van SQL Server on premises naar Azure-databaseservices, namelijk Azure SQL Database of Azure SQL Database managed instance, moeten uw ETL-workloads op SQL Server Integration Services (SSIS) als een van de primaire services met toegevoegde waarde ook worden gemigreerd.

Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) ondersteunt het uitvoeren van SSIS-pakketten. Zodra Azure-SSIS IR is ingericht, u vervolgens vertrouwde hulpprogramma's gebruiken, zoals SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) en command-line-hulpprogramma's, zoals dtinstall/dtutil/dtexec, om uw pakketten in Azure te implementeren en uit te voeren. Zie [Azure SSIS-lift-and-shift-overzicht voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)meer informatie.

In dit artikel wordt het migratieproces van uw ETL-workloads belicht, van on-premises SSIS naar SSIS in ADF. Het migratieproces bestaat uit twee fasen: **Assessment** and **Migration**.

## <a name="assessment"></a>Beoordeling

Om een volledig migratieplan op te stellen, zal een grondige beoordeling helpen bij het identificeren van problemen met de bron-SSIS-pakketten die een succesvolle migratie zouden voorkomen.

Data Migration Assistant (DMA) is een vrij downloadbare tool voor dit doel die lokaal kan worden geïnstalleerd en uitgevoerd. DMA-beoordelingsproject van type **Integration Services** kan worden gemaakt om SSIS-pakketten in batches te beoordelen en compatibiliteitsproblemen te identificeren die worden gepresenteerd in de volgende categorieën:

- Migratieblokkers: dit zijn compatibiliteitsproblemen die de migratiebronpakketten blokkeren die op Azure-SSIS IR moeten worden uitgevoerd. DMA biedt richtlijnen om u te helpen deze problemen aan te pakken.

- Informatieve problemen: dit zijn gedeeltelijk ondersteunde of afgeschafte functies die worden gebruikt in bronpakketten. DMA biedt een uitgebreide reeks aanbevelingen, alternatieve benaderingen die beschikbaar zijn in Azure en verzachtende stappen om op te lossen.

### <a name="four-storage-types-for-ssis-packages"></a>Vier opslagtypen voor SSIS-pakketten

- SSIS-catalogus (SSISDB). Dit is geïntroduceerd met SQL Server 2012 en bevat een reeks opgeslagen procedures, weergaven en functies met tabelwaarde die worden gebruikt voor het werken met SSIS-projecten/-pakketten.
- Bestandssysteem.
- SQL Server-systeemdatabase (MSDB).
- SSIS-pakketwinkel. Dit is een pakketbeheerlaag bovenop twee subtypen:
  - MSDB, een systeemdatabase in SQL Server die wordt gebruikt om SSIS-pakketten op te slaan.
  - Beheerd bestandssysteem, een specifieke map in SQL Server-installatiepad dat wordt gebruikt om SSIS-pakketten op te slaan.

DMA ondersteunt momenteel de batch-beoordeling van pakketten die zijn opgeslagen in **file system,** **Package Store**en **SSIS catalogus** sinds **DMA versie v5.0**.

Haal [DMA,](https://docs.microsoft.com/sql/dma/dma-overview)en [voer uw pakket beoordeling mee.](https://docs.microsoft.com/sql/dma/dma-assess-ssis)

## <a name="migration"></a>Migratie

Afhankelijk van de [opslagtypen](#four-storage-types-for-ssis-packages) van bron-SSIS-pakketten en de migratiebestemming van databaseworkloads, kunnen de stappen om **SSIS-pakketten** en **SQL Server Agent-taken** te migreren die SSIS-pakketuitvoeringen plannen, variëren. Er zijn twee scenario's:

- [**Azure SQL Database beheerd instantie** als database workload bestemming](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** als doel voor databaseworkload](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Azure SQL Database beheerd instantie** als database workload bestemming

| **Type pakketopslag** |SSIS-pakketten voor batchmigreren|SSIS-taken voor batchmigreren|
|-|-|-|
|SSISDB (SSISDB)|[**SSISDB migreren**](scenario-ssis-migration-ssisdb-mi.md)|[SSIS-taken migreren naar azure SQL Database managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|Bestandssysteem|Implementeer ze opnieuw naar bestandsshares/Azure-bestanden via dtinstall/dtutil/handmatige kopie, of om in bestandssystemen toegang te houden via VNet/Self-Hosted IR. Zie [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)voor meer informatie.|<li> Migreren met [wizard SSIS-taakmigratie in SSMS](how-to-migrate-ssis-job-ssms.md) <li>Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|
|SQL Server (MSDB)|Exporteer ze naar bestandssystemen/bestandsshares/Azure-bestanden via SSMS/dtutil. Zie [SSIS-pakketten exporteren](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)voor meer informatie .|Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|
|Pakketwinkel|Exporteer ze naar bestandssystemen/bestandsshares/Azure Files via SSMS/dtutil of implementeer ze opnieuw naar bestandsshares/Azure Files via dtinstall/dtutil/manual copy of bewaar ze in bestandssystemen om toegang te krijgen via VNet/Self-Hosted IR. Zie dtutil utility voor meer informatie. Zie [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)voor meer informatie.|Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** als doel voor databaseworkload

| **Type pakketopslag** |SSIS-pakketten voor batchmigreren|Taken voor batch-migreren|
|-|-|-|
|SSISDB (SSISDB)|Opnieuw implementeren naar Azure-SSISDB via SSDT/SSMS. Zie [SSIS-pakketten implementeren in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)voor meer informatie.|Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|
|Bestandssysteem|Implementeer ze opnieuw naar bestandsshares/Azure-bestanden via dtinstall/dtutil/handmatige kopie, of om in bestandssystemen toegang te houden via VNet/Self-Hosted IR. Zie [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)voor meer informatie.|<li> Migreren met [wizard SSIS-taakmigratie in SSMS](how-to-migrate-ssis-job-ssms.md) <li> Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|
|SQL Server (MSDB)|Exporteer ze naar bestandssystemen/bestandsshares/Azure-bestanden via SSMS/dtutil. Zie [SSIS-pakketten exporteren](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)voor meer informatie .|Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|
|Pakketwinkel|Exporteer ze naar bestandssystemen/bestandsshares/Azure Files via SSMS/dtutil of implementeer ze opnieuw naar bestandsshares/Azure Files via dtinstall/dtutil/manual copy of bewaar ze in bestandssystemen om toegang te krijgen via VNet/Self-Hosted IR. Zie dtutil utility voor meer informatie. Zie [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)voor meer informatie.|Converteer ze naar ADF-pijplijnen/activiteiten/triggers via scripts/SSMS/ADF-portal. Zie [SSMS-planningsfunctie voor](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)meer informatie .|

## <a name="additional-resources"></a>Aanvullende bronnen

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [SSIS-workloads naar de cloud tillen en verschuiven](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS-pakketten naar beheerd exemplaar van Azure SQL Database migreren](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Pakketten opnieuw implementeren in Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Volgende stappen

- [SSIS-pakketten valideren die zijn geïmplementeerd in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Runtime Azure-SSIS-integratie bewaken](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [SSIS-pakketuitvoeringen plannen in Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
