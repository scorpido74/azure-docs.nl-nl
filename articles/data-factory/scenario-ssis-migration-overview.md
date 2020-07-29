---
title: On-premises SSIS-workloads migreren naar SSIS in Azure Data Factory
description: Migratie van on-premises SSIS-workloads naar SSIS in ADF.
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
ms.openlocfilehash: eecf7ba1471e35e2d9ab26394c7295f324c4ca20
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116393"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>On-premises SSIS-workloads migreren naar SSIS in ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Wanneer u de werk belasting van uw data base migreert van SQL Server on-premises naar Azure Data Base-Services, dat wil zeggen Azure SQL Database of Azure SQL Managed instance, moeten uw ETL-workloads op SQL Server Integration Services (SSIS) als een van de services met primaire toegevoegde waarde ook worden gemigreerd.

Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) ondersteunt actieve SSIS-pakketten. Als Azure-SSIS IR is ingericht, kunt u vervolgens vertrouwde hulpprogram ma's gebruiken, zoals SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) en opdracht regel Programma's, zoals dtinstall/dtutil/dtexec, om uw pakketten te implementeren en uit te voeren in Azure. Zie [Azure SSIS Lift-and-Shift Overview (](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)Engelstalig) voor meer informatie.

In dit artikel wordt het migratie proces van de ETL-workloads van on-premises SSIS naar SSIS in ADF uitgelegd. Het migratie proces bestaat uit twee fasen: **evaluatie** en **migratie**.

## <a name="assessment"></a>Beoordeling

Een grondige evaluatie helpt bij het vaststellen van een volledig migratie plan voor het identificeren van problemen met de bron-SSIS-pakketten die een geslaagde migratie belemmeren.

Data Migration Assistant (DMA) is een vrij downloadbaar hulp programma voor dit doel dat lokaal kan worden geïnstalleerd en uitgevoerd. Een DMA-beoordelings project van het type **integratie Services** kan worden gemaakt om SSIS-pakketten in batches te beoordelen en compatibiliteits problemen te identificeren die worden weer gegeven in de volgende categorieën:

- Migratie blokkeringen: Dit zijn compatibiliteits problemen waarbij de migratie bron pakketten worden geblokkeerd om te worden uitgevoerd op Azure-SSIS IR. DMA biedt hulp bij het oplossen van deze problemen.

- Informatieve problemen: Dit zijn gedeeltelijk ondersteunde of afgeschafte functies die worden gebruikt in bron pakketten. DMA biedt een uitgebreide set aanbevelingen, alternatieve benaderingen die beschikbaar zijn in Azure en het beperken van de stappen voor het oplossen van problemen.

### <a name="four-storage-types-for-ssis-packages"></a>Vier opslag typen voor SSIS-pakketten

- SSIS-catalogus (SSISDB). Dit is geïntroduceerd in SQL Server 2012 en bevat een reeks opgeslagen procedures, weer gaven en functies voor tabel waarden die worden gebruikt voor het werken met SSIS-projecten/-pakketten.
- Bestands systeem.
- SQL Server systeem database (MSDB).
- SSIS-pakket opslag. Dit is een pakket Management-laag boven op twee subtypen:
  - MSDB, een systeem database in SQL Server gebruikt om SSIS-pakketten op te slaan.
  - Beheerd bestands systeem, een specifieke map in SQL Server installatiepad dat wordt gebruikt voor het opslaan van SSIS-pakketten.

DMA ondersteunt momenteel de batch-evaluatie van pakketten die zijn opgeslagen in **Bestands systeem**, **pakket opslag**en **SSIS-catalogus** sinds **DMA versie v 5.0**.

U kunt [DMA](https://docs.microsoft.com/sql/dma/dma-overview)ophalen en [uw pakket evaluatie met IT uitvoeren](https://docs.microsoft.com/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migratie

Afhankelijk van de [opslag typen](#four-storage-types-for-ssis-packages) van de bron-SSIS-pakketten en het migratie doel van data base-workloads, zijn de stappen voor het migreren van **SSIS-pakketten** en **SQL Server Agent taken** die de uitvoering van SSIS-pakket plannen kunnen variëren. Er zijn twee scenario's:

- [**Azure SQL Managed instance** as data base-werk belasting doel](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL database** als doel van de database werk belasting](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Azure SQL Managed instance** as data base-werk belasting doel

| **Type pakket opslag** |Hoe batch migratie SSIS-pakketten|Hoe kan ik SSIS-taken batchgewijs migreren|
|-|-|-|
|SSISDB|[**SSISDB** migreren](scenario-ssis-migration-ssisdb-mi.md)|[SSIS-taken migreren naar Azure SQL Managed instance agent](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent)|
|Bestandssysteem|Implementeer deze opnieuw naar bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar de bestands systemen via VNet/zelf-Hostende IR. Zie [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|<li> Migreren met de [wizard SSIS-taak migratie in SSMS](how-to-migrate-ssis-job-ssms.md) <li>Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|SQL Server (MSDB)|Exporteren naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil. Zie [SSIS-pakketten exporteren](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Pakket archief|Exporteer deze naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil of implementeer ze opnieuw op bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar ze in bestands systemen voor toegang via een VNet/zelf-Hostende IR. Zie dtutil Utility (Engelstalig) voor meer informatie. Zie [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL database** als doel van de database werk belasting

| **Type pakket opslag** |Hoe batch migratie SSIS-pakketten|Taken batchgewijs migreren|
|-|-|-|
|SSISDB|Implementeer opnieuw naar Azure-SSISDB via SSDT/SSMS. Zie [SSIS-pakketten implementeren in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Bestandssysteem|Implementeer deze opnieuw naar bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar de bestands systemen via VNet/zelf-Hostende IR. Zie [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|<li> Migreren met de [wizard SSIS-taak migratie in SSMS](how-to-migrate-ssis-job-ssms.md) <li> Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|SQL Server (MSDB)|Exporteren naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil. Zie [SSIS-pakketten exporteren](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|
|Pakket archief|Exporteer deze naar bestands systemen/bestands shares/Azure Files via SSMS/dtutil of implementeer ze opnieuw op bestands shares/Azure Files via dtinstall/dtutil/hand matig kopiëren of bewaar ze in bestands systemen voor toegang via een VNet/zelf-Hostende IR. Zie dtutil Utility (Engelstalig) voor meer informatie. Zie [dtutil Utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility)(Engelstalig) voor meer informatie.|Zet ze om in ADF-pijp lijnen/activiteiten/triggers via scripts/SSMS/ADF Portal. Zie [SSMS planning feature](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)(Engelstalig) voor meer informatie.|

## <a name="additional-resources"></a>Aanvullende bronnen

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [SSIS-workloads in de Cloud optillen en verplaatsen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS-pakketten naar Azure SQL Managed Instance migreren](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Pakketten opnieuw implementeren naar Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Volgende stappen

- [SSIS-pakketten valideren die zijn geïmplementeerd in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS Integration Runtime bewaken](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Uitvoeringen van SSIS-pakketten plannen in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
