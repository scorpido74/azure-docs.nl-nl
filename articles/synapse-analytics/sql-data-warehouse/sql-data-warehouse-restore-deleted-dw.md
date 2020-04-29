---
title: Een verwijderde SQL-groep herstellen
description: Instructies voor het herstellen van een verwijderde SQL-groep.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d2e2fdb181b553d330368b043b75159e211dd0d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745128"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Een verwijderde SQL-groep herstellen met behulp van Azure Synapse Analytics

In dit artikel leert u hoe u een SQL herstelt met behulp van de Azure Portal of Power shell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elke SQL-groep wordt gehost door een SQL-Server (bijvoorbeeld myserver.database.windows.net) die een standaard DTU-quotum heeft.  Controleer of de SQL-Server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Een verwijderd Data Warehouse herstellen via Power shell

Als u een verwijderde SQL-groep wilt herstellen, gebruikt u de cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Als de bijbehorende logische server ook is verwijderd, kunt u dat data warehouse niet herstellen.

1. Voordat u begint, moet u ervoor zorgen dat u [Azure PowerShell installeert](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat het verwijderde data warehouse bevat dat moet worden hersteld.
5. Het specifieke verwijderde data warehouse ophalen.
6. Het verwijderde data warehouse herstellen
    1. Als u de verwijderde SQL Data Warehouse wilt herstellen naar een andere logische server, moet u de naam van de andere logische server opgeven.  Deze logische server kan zich ook in een andere resource groep en regio bevinden.
    1. Als u wilt herstellen naar een ander abonnement, gebruikt u de knop [verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) om de logische server naar een ander abonnement te verplaatsen.
7. Controleer of het herstelde data warehouse online is.
8. Nadat de herstel bewerking is voltooid, kunt u uw herstelde data warehouse configureren door [de data base na het herstel te configureren](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Een verwijderde data base herstellen met behulp van de Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar de SQL-server waarop uw verwijderde data warehouse werd gehost.
3. Selecteer het pictogram **Verwijderde data bases** in de inhouds opgave.

    ![Verwijderde data bases](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecteer de verwijderde SQL Data Warehouse die u wilt herstellen.

    ![Verwijderde databases selecteren](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Geef een nieuwe **database naam** op en klik op **OK**

    ![Database naam opgeven](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Een SQL-groep met geo-back-ups herstellen](sql-data-warehouse-restore-from-geo-backup.md)
