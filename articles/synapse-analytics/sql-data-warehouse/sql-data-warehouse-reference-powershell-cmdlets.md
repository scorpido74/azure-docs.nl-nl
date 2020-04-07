---
title: PowerShell & REST API's
description: Zoek de beste PowerShell-cmdlets voor Azure Synapse Analytics SQL-groep, inclusief hoe u een database onderbreken en hervatten.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f3d6d0c1f71e2262e943998cdc08717291903365
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743815"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell-& REST-API's voor Azure Synapse Analytics SQL-pool

Veel sql-poolbeheertaken van Azure Synapse Analytics kunnen worden beheerd met Azure PowerShell-cmdlets of REST API's.  Hieronder vindt u enkele voorbeelden van het gebruik van PowerShell-opdrachten om veelvoorkomende taken in uw SQL-groep te automatiseren.  Zie voor een aantal goede REST-voorbeelden het artikel [Schaalbaarheid beheren met REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Aan de slag met Azure PowerShell-cmdlets

1. Open Windows PowerShell.
2. Voer deze opdrachten uit om bij de PowerShell-prompt deze opdrachten uit te voeren om u aan te melden bij Azure Resource Manager en uw abonnement te selecteren.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Voorbeeld van gegevensmagazijn onderbreken

Pauzeer een database met de naam 'Database02' gehost op een server met de naam 'Server01'.  De server bevindt zich in een Azure-brongroep met de naam 'ResourceGroup1'.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Een variatie, dit voorbeeld leidt het opgehaalde object naar [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Als gevolg hiervan wordt de database onderbroken. De laatste opdracht toont de resultaten.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Voorbeeld van het gegevensmagazijn starten

De werking hervatten van een database met de naam 'Database02' gehost op een server met de naam 'Server01'. De server is opgenomen in een resourcegroep met de naam 'ResourceGroup1'.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

In een variant wordt in dit voorbeeld een database met de naam 'Database02' opgehaald van een server met de naam 'Server01' die is opgenomen in een brongroep met de naam 'ResourceGroup1'. Het leidingen van de opgehaalde object [naar Cv-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Als uw server foo.database.windows.net is, gebruikt u 'foo' als de servernaam in de PowerShell-cmdlets.

## <a name="other-supported-powershell-cmdlets"></a>Andere ondersteunde PowerShell-cmdlets

Deze PowerShell-cmdlets worden ondersteund met Azure Synapse Analytics-gegevensmagazijn.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Nieuwe AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Verwijder-azsqldatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase]/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Cv-azsqldatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspend-azsqldatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer PowerShell-voorbeelden:

* [Een gegevensmagazijn maken met PowerShell](create-data-warehouse-powershell.md)
* [Database herstellen](sql-data-warehouse-restore-points.md)

Zie [Azure SQL Database cmdlets]/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.js&on voor andere taken die met PowerShell kunnen worden geautomatiseerd.' Niet alle Azure SQL Database-cmdlets worden ondersteund voor Azure Synapse Analytics-gegevensmagazijn. Zie [Operations for Azure SQL Database voor](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)een lijst met taken die met REST kunnen worden geautomatiseerd.
