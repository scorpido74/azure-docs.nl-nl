---
title: 'Snelstartgids: werk belasting isolatie configureren-T-SQL'
description: Gebruik T-SQL voor het configureren van isolatie van werk belastingen.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 10/29/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 92f8aaad1cc3279142d419faa2852406c2956595
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685974"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Snelstartgids: werk belasting isolatie configureren met T-SQL

In deze Quick Start maakt u snel een werkbelasting groep en classificatie voor het reserveren van resources voor het laden van gegevens. Met de werkbelasting groep worden 20% van de systeem resources toegewezen aan een gegevens belasting.  Met de classificatie van de werk belasting worden aanvragen toegewezen aan de werkbelasting groep voor het laden van gegevens.  Met een isolatie van 20% voor het laden van gegevens, zijn er gegarandeerde bronnen om te voldoen aan de Sla's.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!NOTE]
> Het maken van een SQL Data Warehouse kan leiden tot een nieuwe factureerbare service.  Zie [Prijzen van SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) voor meer informatie.
>
>

## <a name="prerequisites"></a>Vereisten
 
In deze Quick Start wordt ervan uitgegaan dat u al een SQL Data Warehouse hebt en dat u beschikt over de machtigingen voor de beheer DATABASE. Gebruik [Maken en verbinden - portal](create-data-warehouse-portal.md) om een datawarehouse met de naam **mySampleDataWarehouse** te maken.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Aanmelding maken voor DataLoads

Maak een SQL Server-verificatie aanmelding in de `master`-data base met behulp van [aanmeldings gegevens maken](/sql/t-sql/statements/create-login-transact-sql) voor ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Gebruiker maken

[Gebruiker maken](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), ' ELTLogin ', in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Een werkbelasting groep maken
Maak een [werkbelasting groep](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) voor DataLoads met een isolatie van 20%.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Een classificatie van een werk belasting maken

Maak een [classificatie van de werk belasting](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) om ELTLogin toe te wijzen aan de werkbelasting groep DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'ELTLogin'
      ,MEMBERNAME = 'DataLoads')
;
```

## <a name="view-existing-workload-groups-and-classifiers"></a>Bestaande werkbelasting groepen en classificaties weer geven

```sql
SELECT * FROM 
sys.workload_management_workload_groups

SELECT * FROM 
sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Resources opschonen

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
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

- U hebt nu een werkbelasting groep gemaakt. Voer enkele query's uit als ELTLogin om te zien hoe ze worden uitgevoerd. Zie [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) om query's en de toegewezen werkbelasting groep weer te geven.
- Zie [workload Management](sql-data-warehouse-workload-management.md) en [workload-isolatie](sql-data-warehouse-workload-isolation.md)voor meer informatie over het beheer van Azure SQL Data Warehouse workload.
