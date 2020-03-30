---
title: Transparante gegevensversleuteling inschakelen voor Stretch Database (T-SQL)
description: Transparent Data Encryption (TDE) inschakelen voor SQL Server Stretch Database in Azure TSQL
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033956"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Transparent Data Encryption (TDE) inschakelen voor Stretch Database op Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure-portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) helpt beschermen tegen de dreiging van kwaadaardige activiteiten door het uitvoeren van real-time encryptie en decryptie van de database, bijbehorende back-ups, en transactie log bestanden in rust zonder dat wijzigingen in de toepassing.

TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel (de zogeheten databaseversleutelingssleutel). De databaseversleutelingssleutel wordt beschermd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke Azure-server. Microsoft draait deze certificaten automatisch ten minste om de 90 dagen. Zie [Transparante gegevensversleuteling (TDE)]voor een algemene beschrijving van TDE.

## <a name="enabling-encryption"></a>Versleuteling inschakelen
Ga als volgt te werk om TDE in te schakelen voor een Azure-database die de gegevens opslaat die zijn gemigreerd uit een SQL Server-database met Stretch:

1. Verbinding maken met de *hoofddatabase* op de Azure-server die de database host met een aanmelding die een beheerder of een lid is van de **dbmanager-rol** in de hoofddatabase
2. Voer de volgende instructie uit om de database te versleutelen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Versleuteling uitschakelen
Ga als volgt te werk om TDE uit te schakelen voor een Azure-database die de gegevens opslaat die zijn gemigreerd uit een SQL Server-database met Stretch:

1. Verbinding maken met de *hoofddatabase* met behulp van een aanmelding die een beheerder of een lid is van de **dbmanager-rol** in de hoofddatabase
2. Voer de volgende instructie uit om de database te versleutelen.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Versleuteling verifiëren
Ga als volgt te werk om de versleutelingsstatus te verifiëren voor een Azure-database die de gegevens opslaat die zijn gemigreerd uit een SQL Server-database met Stretch:

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

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
