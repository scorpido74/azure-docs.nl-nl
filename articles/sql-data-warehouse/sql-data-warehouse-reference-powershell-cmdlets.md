---
title: Power shell-& REST-Api's
description: Zoek de bovenste Power shell-cmdlets voor de Azure Synapse Analytics SQL-groep, inclusief het onderbreken en hervatten van een Data Base.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198453"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Power shell & REST-Api's voor Azure Synapse Analytics SQL-groep

Veel beheer taken van de SQL-groep van Azure Synapse Analytics kunnen worden beheerd met behulp van Azure PowerShell-cmdlets of REST-Api's.  Hieronder ziet u enkele voor beelden van het gebruik van Power shell-opdrachten voor het automatiseren van algemene taken in uw SQL-groep.  Zie het artikel [schaal baarheid met rest beheren](sql-data-warehouse-manage-compute-rest-api.md)voor een aantal goede rest-voor beelden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Aan de slag met Azure PowerShell-cmdlets

1. Open Windows PowerShell.
2. Voer bij de Power shell-prompt deze opdrachten uit om u aan te melden bij de Azure Resource Manager en selecteer uw abonnement.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Voor beeld van data warehouse pauzeren
Een Data Base met de naam ' Database02 ' onderbreken die wordt gehost op een server met de naam ' Server01 '.  De server bevindt zich in een Azure-resource groep met de naam ' ResourceGroup1 '.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Een variant, in dit voor beeld wordt het opgehaalde object door sluizen naar [suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Als gevolg hiervan wordt de data base onderbroken. Met de laatste opdracht worden de resultaten weer gegeven.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Start Data Warehouse-voor beeld

Hervat de bewerking van een Data Base met de naam ' Database02 ' die wordt gehost op een server met de naam ' Server01 '. De server bevindt zich in een resource groep met de naam ' ResourceGroup1 '.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

In dit voor beeld wordt een Data Base met de naam ' Database02 ' opgehaald van een server met de naam ' Server01 ' die is opgenomen in een resource groep met de naam ' ResourceGroup1 '. Dit pipet het opgehaalde object om [AzSqlDatabase te hervatten](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Als uw server foo.database.windows.net is, gebruikt u Foo als de-server naam in de Power shell-cmdlets.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andere ondersteunde Power shell-cmdlets
Deze Power shell-cmdlets worden ondersteund met Azure Synapse Analytics Data Warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Volgende stappen
Zie voor meer Power shell-voor beelden:

* [Een Data Warehouse maken met behulp van Power shell](create-data-warehouse-powershell.md)
* [Data base terugzetten](sql-data-warehouse-restore-database-powershell.md)

Zie [Azure SQL database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql)voor andere taken die kunnen worden geautomatiseerd met Power shell. Niet alle Azure SQL Database-cmdlets worden ondersteund voor Azure Synapse Analytics Data Warehouse.  Zie [bewerkingen voor Azure SQL database](/rest/api/sql/)voor een lijst met taken die kunnen worden geautomatiseerd met rest.
