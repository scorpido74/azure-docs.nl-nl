---
title: Transparante gegevensversleuteling (T-SQL)
description: Transparante gegevensversleuteling (TDE) in Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: ae751cc5b8e3ab67f3e65757724d0ebae1c45e02
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745243"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)

> [!div class="op_single_selector"]
>
> * [Overzicht van beveiliging](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Vereiste machtigingen

Als u Transparent Data Encryption (TDE) wilt inschakelen, moet u een beheerder of lid zijn van de rol dbmanager.

## <a name="enabling-encryption"></a>Versleuteling inschakelen

Volg de volgende stappen om TDE in te schakelen:

1. Verbinding maken met de *hoofddatabase* op de server die de database host met een aanmelding die een beheerder of een lid is van de **dbmanager-rol** in de hoofddatabase
2. Voer de volgende instructie uit om de database te versleutelen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Versleuteling uitschakelen

Volg de volgende stappen om TDE uit te schakelen:

1. Verbinding maken met de *hoofddatabase* met behulp van een aanmelding die een beheerder of een lid is van de **dbmanager-rol** in de hoofddatabase
2. Voer de volgende instructie uit om de database te versleutelen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Een onderbroken SQL-groep moet worden hervat voordat de TDE-instellingen worden gewijzigd.

## <a name="verifying-encryption"></a>Versleuteling verifiëren

Voer de onderstaande stappen uit om de versleutelingsstatus te verifiëren:

1. Verbinding maken met de *master-* of instance-database met behulp van een aanmelding die een beheerder of een lid is van de **dbmanager-rol** in de hoofddatabase
2. Voer de volgende instructie uit om de database te versleutelen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Een resultaat ```1``` van geeft een ```0``` versleutelde database aan, duidt op een niet-versleutelde database.

## <a name="encryption-dmvs"></a>Versleuteling DMVs

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
