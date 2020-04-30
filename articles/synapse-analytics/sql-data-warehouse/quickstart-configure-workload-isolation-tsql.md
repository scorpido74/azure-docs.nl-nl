---
title: 'Snelstartgids: werk belasting isolatie configureren-T-SQL'
description: Gebruik T-SQL voor het configureren van isolatie van werk belastingen.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 99c64e703158c40c2cc110a18be7b8c8d3800ff0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207799"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Snelstartgids: werk belasting isolatie configureren met T-SQL

In deze Quick Start maakt u snel een werkbelasting groep en classificatie voor het reserveren van resources voor het laden van gegevens. Met de werkbelasting groep worden 20% van de systeem resources toegewezen aan een gegevens belasting.  Met de classificatie van de werk belasting worden aanvragen toegewezen aan de werkbelasting groep voor het laden van gegevens.  Met een isolatie van 20% voor het laden van gegevens, zijn er gegarandeerde bronnen om te voldoen aan de Sla's.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

> [!NOTE]
> Het maken van een SQL Analytics-exemplaar in azure Synapse Analytics kan resulteren in een nieuwe factureer bare service.  Zie [prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u al een exemplaar van SQL Analytics hebt in azure Synapse en dat u de machtigingen voor de data base beheert. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.

## <a name="create-login-for-dataloads"></a>Aanmelding maken voor DataLoads

Maak een SQL Server-verificatie aanmelding in `master` de data base met behulp van de [aanmeldings](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gegevens voor ' ELTLogin' '.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Gebruiker maken](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), ' ELTLogin ', in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Een werkbelasting groep maken

Maak een [werkbelasting groep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor DataLoads met een isolatie van 20%.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Een classificatie van een werk belasting maken

Maak een [classificatie van de werk belasting](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om ELTLogin toe te wijzen aan de werkbelasting groep DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Bestaande werkbelasting groepen en-classificaties en runtime waarden weer geven

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Resources opschonen

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en gegevens die zijn opgeslagen in uw data warehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag ruimte wilt bewaren, kunt u de reken proces onderbreken wanneer u de SQL-groep niet gebruikt. Door Compute te onderbreken, worden er alleen kosten in rekening gebracht voor gegevens opslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de compute.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

## <a name="next-steps"></a>Volgende stappen

- U hebt nu een werkbelasting groep gemaakt. Voer enkele query's uit als ELTLogin om te zien hoe ze worden uitgevoerd. Zie [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om query's en de toegewezen werkbelasting groep weer te geven.
- Zie [workload Management](sql-data-warehouse-workload-management.md) en [workload-isolatie](sql-data-warehouse-workload-isolation.md)voor meer informatie over Synapse voor SQL-workloads.
