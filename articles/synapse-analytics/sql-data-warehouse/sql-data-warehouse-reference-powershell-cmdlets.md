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
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351391"
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

Een variatie, dit voorbeeld leidt het opgehaalde object naar [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Als gevolg hiervan wordt de database onderbroken. De laatste opdracht toont de resultaten.

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

In een variant wordt in dit voorbeeld een database met de naam 'Database02' opgehaald van een server met de naam 'Server01' die is opgenomen in een brongroep met de naam 'ResourceGroup1'. Het leidingen van de opgehaalde object [naar Cv-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Als uw server foo.database.windows.net is, gebruikt u 'foo' als de servernaam in de PowerShell-cmdlets.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Andere ondersteunde PowerShell-cmdlets
Deze PowerShell-cmdlets worden ondersteund met Azure Synapse Analytics-gegevensmagazijn.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Nieuwe AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Verwijder-azsqldatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Herstel-azsqldatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Cv-azsqldatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzAbonnement](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-azsqldatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Volgende stappen
Zie voor meer PowerShell-voorbeelden:

* [Een gegevensmagazijn maken met PowerShell](create-data-warehouse-powershell.md)
* [Database herstellen](sql-data-warehouse-restore-points.md)

Zie [cmdlets azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql)voor andere taken die met PowerShell kunnen worden geautomatiseerd. Niet alle Azure SQL Database-cmdlets worden ondersteund voor Azure Synapse Analytics-gegevensmagazijn.  Zie [Operations for Azure SQL Database voor](/rest/api/sql/)een lijst met taken die met REST kunnen worden geautomatiseerd.
