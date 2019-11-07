---
title: Transparent Data Encryption (T-SQL)
description: Transparent Data Encryption (TDE) in SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 98695aa7578e90be9ada2f86640e68b05a9812ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692745"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Aan de slag met Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Beveiligings overzicht](sql-data-warehouse-overview-manage-security.md)
> * [Verificatie](sql-data-warehouse-authentication.md)
> * [Versleuteling (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Versleuteling (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Vereiste machtigingen
Als u Transparent Data Encryption (TDE) wilt inschakelen, moet u een beheerder of lid van de DBManager-rol zijn.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Volg deze stappen om TDE in te scha kelen voor een SQL Data Warehouse:

1. Verbinding maken met de *hoofd* database op de server die als host fungeert voor de data base met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Volg deze stappen om TDE uit te scha kelen voor een SQL Data Warehouse:

1. Verbinding maken met de *hoofd* database met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> U moet een onderbroken SQL Data Warehouse hervatten voordat u de TDE-instellingen wijzigt.
> 
> 

## <a name="verifying-encryption"></a>Versleuteling controleren
Volg de onderstaande stappen om de versleutelings status voor een SQL Data Warehouse te controleren:

1. Verbinding maken met de *Master* -of instance-data base met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Een resultaat van ```1``` geeft een versleutelde data base aan ```0``` duidt op een niet-versleutelde data base.

## <a name="encryption-dmvs"></a>Versleuteling Dmv's
* [sys. data bases][sys.databases] 
* [sys. DM _pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
