---
title: 'Snelstartgids: een classificatie werk belasting maken-T-SQL '
description: T-SQL gebruiken om een werk belasting classificatie te maken met hoge urgentie.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 1375605b6dab385b53af9212023767003e686e60
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646290"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Snelstartgids: een classificatie van een werk belasting maken met T-SQL

In deze Quick Start maakt u snel een classificatie van werk belastingen met hoge prioriteit voor de CEO van uw organisatie. Met deze classificatie van werk belasting kunnen CEO query's voor rang krijgen op andere query's met een lagere urgentie in de wachtrij.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!NOTE]
> Het maken van een SQL Data Warehouse kan leiden tot een nieuwe factureerbare service.  Zie [Prijzen van SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.
>
>

## <a name="prerequisites"></a>Vereisten

In deze Quick Start wordt ervan uitgegaan dat u al een SQL Data Warehouse hebt en dat u beschikt over de machtigingen voor de beheer DATABASE. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Aanmelding maken voor TheCEO

Maak een SQL Server-verificatie aanmelding in de `master`-data base met behulp van de [aanmeldings gegevens](/sql/t-sql/statements/create-login-transact-sql) voor ' TheCEO '.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Gebruiker maken](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), ' TheCEO ', in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Een classificatie van een werk belasting maken

Maak een [werk belasting classificatie](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) voor ' TheCEO ' met hoge prioriteit.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Bestaande classificaties weer geven

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Resources opschonen

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Er worden kosten in rekening gebracht voor Data Warehouse-eenheden en gegevens die zijn opgeslagen in uw data warehouse. Deze compute- en opslagresources worden apart in rekening gebracht.

- Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Door Compute te onderbreken, worden er alleen kosten in rekening gebracht voor gegevens opslag. Wanneer u klaar bent om met de gegevens te werken, hervat u de compute.
- Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om resources op te schonen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer uw data warehouse.

    ![Resources opschonen](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Selecteer de knop **pause** om de berekening te onderbreken. Als het datawarehouse is onderbroken, ziet u een knop **Start**.  Selecteer **Start**om de compute te hervatten.

3. Als u het Data Warehouse wilt verwijderen zodat er geen kosten in rekening worden gebracht voor berekenen of opslaan, selecteert u **verwijderen**.

4. Als u de door u gemaakte SQL-Server wilt verwijderen, selecteert u **mynewserver-20180430.database.Windows.net** in de vorige installatie kopie en selecteert u vervolgens **verwijderen**.  Wees voorzichtig met verwijderen. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resource groep wilt verwijderen, selecteert u **myResourceGroup**en selecteert u **resource groep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- U hebt nu een classificatie voor de werk belasting gemaakt. Voer enkele query's uit als TheCEO om te zien hoe ze worden uitgevoerd. Zie [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) voor het weer geven van query's en de prioriteit die is toegewezen.
- Zie [urgentie van werk](sql-data-warehouse-workload-importance.md) belasting en [classificatie van werk](sql-data-warehouse-workload-classification.md)belasting voor meer informatie over het beheer van Azure SQL Data Warehouse workload.
- Zie de artikelen met procedures voor het [configureren van de urgentie van werk belastingen](sql-data-warehouse-how-to-configure-workload-importance.md) en het [beheren en bewaken van workload Management](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
