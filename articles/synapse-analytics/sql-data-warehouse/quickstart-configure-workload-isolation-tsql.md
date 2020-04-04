---
title: 'Snelstart: workloadisolatie configureren - T-SQL'
description: Gebruik T-SQL om workloadisolatie te configureren.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: d3d1b9af0b26fa775beb78b313937890cb9287b3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633763"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Snelstart: workloadisolatie configureren met T-SQL

In deze snelle start maakt u snel een werkbelastinggroep en classificatie voor het reserveren van resources voor het laden van gegevens. De werkbelastinggroep wijst 20% van de systeembronnen toe aan een gegevensbelasting.  De workloadclassifier wijst aanvragen toe aan de groep werkbelasting voor gegevensbelasting.  Met 20% isolatie voor gegevensladingen, zijn ze gegarandeerd resources te raken SLA's.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

> [!NOTE]
> Als u een SQL Analytics-exemplaar maakt in Azure Synapse Analytics, kan dit resulteren in een nieuwe factureerbare service.  Zie [Azure Synapse Analytics-prijzen voor](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)meer informatie.

## <a name="prerequisites"></a>Vereisten

Met deze quickstart wordt ervan uitgegaan dat u al een SQL Analytics-exemplaar in Azure Synapse hebt en dat u controldatabasemachtigingen hebt. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Inloggen voor DataLoads maken

Maak een SQL Server-verificatielogin in de `master` database met CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor 'ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Gebruiker](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)"ELTLogin" maken in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Een werkbelastinggroep maken

Maak een [workloadgroep](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor DataLoads met een isolatie van 20%.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Een classificatie voor werkbelasting maken

Maak een [workloadclassificatie](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om ELTLogin in kaart te brengen in de groep DataLoads-workloads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Bestaande werkbelastinggroepen en classificaties en runtimewaarden weergeven

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

Er worden kosten in rekening gebracht voor gegevensmagazijnen en gegevens die zijn opgeslagen in uw gegevensmagazijn. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in opslag wilt houden, u de gegevensberekening onderbreken wanneer u de SQL-groep niet gebruikt. Door compute te pauzeren, worden alleen kosten in rekening gebracht voor gegevensopslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de gegevens.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om resources op te schonen.

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer in uw gegevensmagazijn.

    ![Resources opschonen](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. Als u de berekening wilt onderbreken, selecteert u de knop **Onderbreken.** Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Als u de gegevens wilt hervatten, selecteert u **Start**.

3. Als u het gegevensmagazijn wilt verwijderen zodat er geen kosten in rekening worden gebracht voor rekenkracht of opslag, selecteert u **Verwijderen**.

4. Als u de SQL-server wilt verwijderen die u hebt gemaakt, selecteert u **mynewserver-20180430.database.windows.net** in de vorige afbeelding en selecteert u **Verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de brongroep wilt verwijderen, selecteert u **myResourceGroup**en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- U hebt nu een werkbelastinggroep gemaakt. Voer een paar query's uit als ELTLogin om te zien hoe ze presteren. Zie [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om query's en de toegewezen werkbelastinggroep weer te geven.
- Zie [Workload management](sql-data-warehouse-workload-management.md) en [Workload Isolation](sql-data-warehouse-workload-isolation.md)voor meer informatie over Synapse SQL-workloadmanagement.
