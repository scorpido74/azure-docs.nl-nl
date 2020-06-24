---
title: Door de gebruiker gedefinieerde herstel punten
description: Een herstel punt voor een SQL-groep maken.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 18d341db8538af12dbbabab4797459779a553e4e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204996"
---
# <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstel punten

In dit artikel leert u hoe u een nieuw, door de gebruiker gedefinieerd herstel punt maakt voor een SQL-groep in azure Synapse Analytics met behulp van Power shell en de Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Door de gebruiker gedefinieerde herstel punten maken via Power shell

Als u een door de gebruiker gedefinieerd herstel punt wilt maken, gebruikt u de Power shell [-cmdlet New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

1. Voordat u begint, moet u ervoor zorgen dat u [Azure PowerShell installeert](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat de Data Base bevat die u wilt herstellen.
5. Maak een herstel punt voor een onmiddellijke kopie van uw data warehouse.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Bekijk de lijst met alle bestaande herstel punten.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Door de gebruiker gedefinieerde herstel punten maken via de Azure Portal

Door de gebruiker gedefinieerde herstel punten kunnen ook worden gemaakt via Azure Portal.

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) -account.

2. Ga naar de SQL-groep waarvoor u een herstel punt wilt maken.

3. Selecteer **overzicht** in het linkerdeel venster en selecteer **+ Nieuw herstel punt**. Als de knop Nieuw herstel punt niet is ingeschakeld, moet u ervoor zorgen dat de SQL-groep niet wordt onderbroken.

    ![Nieuw herstel punt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Geef een naam op voor het herstel punt dat door de gebruiker is gedefinieerd en klik op **Toep assen**. Door de gebruiker gedefinieerde herstel punten hebben een standaard Bewaar periode van zeven dagen.

    ![Naam van herstel punt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)
- [Een SQL-groep met geo-back-ups herstellen](sql-data-warehouse-restore-from-geo-backup.md)

