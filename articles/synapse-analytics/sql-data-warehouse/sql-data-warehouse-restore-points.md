---
title: Door de gebruiker gedefinieerde herstelpunten
description: Een herstelpunt maken voor SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745055"
---
# <a name="user-defined-restore-points"></a>Door de gebruiker gedefinieerde herstelpunten

In dit artikel leert u een nieuw door de gebruiker gedefinieerd herstelpunt voor een SQL-groep in Azure Synapse Analytics te maken met PowerShell en de Azure-portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Door de gebruiker gedefinieerde herstelpunten maken via PowerShell

Als u een door de gebruiker gedefinieerd herstelpunt wilt maken, gebruikt u de cmdlet [Nieuw-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell.

1. Voordat u begint, moet u [Azure PowerShell installeren.](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat de database bevat die moet worden hersteld.
5. Maak een herstelpunt voor een onmiddellijke kopie van uw gegevensmagazijn.

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

6. Bekijk de lijst met alle bestaande herstelpunten.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Door de gebruiker gedefinieerde herstelpunten maken via de Azure-portal

Door de gebruiker gedefinieerde herstelpunten kunnen ook worden gemaakt via azure-portal.

1. Meld u aan bij uw [Azure-portalaccount.](https://portal.azure.com/)

2. Navigeer naar de SQL-groep waarvoor u een herstelpunt wilt maken.

3. Selecteer **Overzicht** in het linkerdeelvenster en selecteer **+ Nieuw herstelpunt**. Als de knop Nieuw herstelpunt niet is ingeschakeld, controleert u of de SQL-groep niet wordt onderbroken.

    ![Nieuw herstelpunt](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Geef een naam op voor het door de gebruiker gedefinieerde herstelpunt en klik op **Toepassen**. Door de gebruiker gedefinieerde herstelpunten hebben een standaardbewaarperiode van zeven dagen.

    ![Naam van herstelpunt](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)
- [Herstellen vanuit een SQL-groep met geoback-up](sql-data-warehouse-restore-from-geo-backup.md)

