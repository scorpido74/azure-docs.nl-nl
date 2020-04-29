---
title: Transparent Data Encryption (T-SQL)
description: Transparent Data Encryption (TDE) in azure Synapse Analytics (T-SQL)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745243"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)

> [!div class="op_single_selector"]
>
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Vereiste machtigingen

Als u Transparent Data Encryption (TDE) wilt inschakelen, moet u een beheerder of lid van de DBManager-rol zijn.

## <a name="enabling-encryption"></a>Versleuteling inschakelen

Volg deze stappen om TDE in te scha kelen:

1. Verbinding maken met de *hoofd* database op de server die als host fungeert voor de data base met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Versleuteling uitschakelen

Volg deze stappen om TDE uit te scha kelen:

1. Verbinding maken met de *hoofd* database met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Een onderbroken SQL-groep moet worden hervat voordat u wijzigingen aanbrengt in de TDE-instellingen.

## <a name="verifying-encryption"></a>Versleuteling controleren

Volg de onderstaande stappen om de versleutelings status te controleren:

1. Verbinding maken met de *Master* -of instance-data base met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Als gevolg van ```1``` een versleutelde data ```0``` base duidt dit op een niet-versleutelde data base.

## <a name="encryption-dmvs"></a>Versleuteling Dmv's

* [sys. data bases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys. dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
