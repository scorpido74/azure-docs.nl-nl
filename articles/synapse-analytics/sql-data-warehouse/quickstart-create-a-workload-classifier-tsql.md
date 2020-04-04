---
title: 'Snelstart: een workloadclassificatie maken - T-SQL'
description: Gebruik T-SQL om een workload classificatie met een hoog belang te maken.
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
ms.openlocfilehash: bcac6af9efd18ef8abeea7d82961fd8f2fe70ba3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633751"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Snelstart: een classificatie voor werkbelasting maken met T-SQL

In deze quickstart creëer je snel een workload classifier met een groot belang voor de CEO van je organisatie. Met deze classificatie voor werkbelasting kunnen CEO-query's voorrang krijgen op andere query's met een lager belang in de wachtrij.

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

> [!NOTE]
> Als u een Synapse SQL-poolinstantie maakt in Azure Synapse Analytics, kan dit resulteren in een nieuwe factureerbare service.  Zie [Azure Synapse Analytics-prijzen voor](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)meer informatie.
>
>

## <a name="prerequisites"></a>Vereisten

Deze quickstart gaat ervan uit dat u al een SQL Data Warehouse hebt en dat u control database-machtigingen hebt. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Login maken voor TheCEO

Maak een SQL Server-verificatielogin in de `master` database met CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor 'TheCEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Maak gebruiker](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)"TheCEO", in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Een classificatie voor werkbelasting maken

Maak een [workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor "TheCEO" met een hoog belang.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Bestaande classificaties weergeven

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Resources opschonen

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Er worden kosten in rekening gebracht voor gegevensmagazijnen en gegevens die zijn opgeslagen in uw gegevensmagazijn. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Door compute te pauzeren, worden alleen kosten in rekening gebracht voor gegevensopslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de gegevens.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om resources op te schonen.

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer in uw gegevensmagazijn.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u de berekening wilt onderbreken, selecteert u de knop **Onderbreken.** Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Als u de gegevens wilt hervatten, selecteert u **Start**.

3. Als u het gegevensmagazijn wilt verwijderen zodat er geen kosten in rekening worden gebracht voor rekenkracht of opslag, selecteert u **Verwijderen**.

4. Als u de SQL-server wilt verwijderen die u hebt gemaakt, selecteert u **mynewserver-20180430.database.windows.net** in de vorige afbeelding en selecteert u **Verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de brongroep wilt verwijderen, selecteert u **myResourceGroup**en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- U hebt nu een workload classificatie gemaakt. Voer een paar query's uit als TheCEO om te zien hoe ze presteren. Zie [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om query's en het toegewezen belang weer te geven.
- Zie [Workload importance](sql-data-warehouse-workload-importance.md) en [workload classification](sql-data-warehouse-workload-classification.md)voor meer informatie over Synapse SQL-workloadmanagement.
- Bekijk de how-to-artikelen om [het belang van workloads](sql-data-warehouse-how-to-configure-workload-importance.md) te configureren en hoe [u Workload Management beheren en bewaken.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
