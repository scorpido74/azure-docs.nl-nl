---
title: Transparent Data Encryption inschakelen voor Stretch Database (T-SQL)
description: Transparent Data Encryption (TDE) inschakelen voor SQL Server Stretch Database op Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74033956"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Transparent Data Encryption (TDE) inschakelen voor Stretch Database op Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Met Transparent Data Encryption (TDE) kunt u zich beschermen tegen de dreiging van schadelijke activiteiten door real-time versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden in rust te brengen zonder dat de toepassing hoeft te worden gewijzigd.

TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel (de zogeheten databaseversleutelingssleutel). De database versleutelings sleutel wordt beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke Azure-server. Micro soft roteert deze certificaten ten minste elke 90 dagen automatisch. Zie [transparent Data Encryption (TDE)]voor een algemene beschrijving van TDe.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Ga als volgt te werk om TDE in te scha kelen voor een Azure-Data Base waarin de gegevens worden opgeslagen die zijn gemigreerd vanuit een SQL Server Data Base met stretch-functionaliteit:

1. Verbinding maken met de *hoofd* database op de Azure-server die als host fungeert voor de data base met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Ga als volgt te werk om TDE uit te scha kelen voor een Azure-Data Base waarin de gegevens worden opgeslagen die zijn gemigreerd vanuit een SQL Server Data Base met stretch-functionaliteit:

1. Verbinding maken met de *hoofd* database met behulp van een aanmelding die een beheerder is of een lid van de **DBManager** -rol in de hoofd database
2. Voer de volgende instructie uit om de data base te versleutelen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Versleuteling controleren
Ga als volgt te werk om de versleutelings status te controleren voor een Azure-Data Base waarin de gegevens worden opgeslagen die zijn gemigreerd vanuit een SQL Server Data Base met stretch-functionaliteit:

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

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
