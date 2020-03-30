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
ms.openlocfilehash: 14058a91ee21bd792f9be1a64d554092c4f5aedd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351508"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Overzicht van beveiliging](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

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
> 
> 

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
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
