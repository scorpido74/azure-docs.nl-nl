---
title: Een verwijderde SQL-groep herstellen
description: Hoe te begeleiden voor het herstellen van een verwijderde SQL-groep.
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745128"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Een verwijderde SQL-groep herstellen met Azure Synapse Analytics

In dit artikel leert u een SQL te herstellen met behulp van de Azure-portal of PowerShell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Controleer uw DTU-capaciteit.** Elke SQL-groep wordt gehost door een SQL-server (bijvoorbeeld myserver.database.windows.net) met een standaard DTU-quotum.  Controleer of de SQL-server voldoende dtu-quotum heeft voor de database die wordt hersteld. Zie [Een DTU-quotumwijziging aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of het aanvragen van meer DTU-quota.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Een verwijderd gegevensmagazijn herstellen via PowerShell

Als u een verwijderde SQL-groep wilt herstellen, gebruikt u de cmdlet [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Als de bijbehorende logische server ook is verwijderd, u dat gegevensmagazijn niet herstellen.

1. Voordat u begint, moet u [Azure PowerShell installeren.](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat het verwijderde gegevensmagazijn bevat dat moet worden hersteld.
5. Download het specifieke verwijderde gegevensmagazijn.
6. Het magazijn voor verwijderde gegevens herstellen
    1. Als u het verwijderde SQL Data Warehouse wilt herstellen naar een andere logische server, moet u de andere logische servernaam opgeven.  Deze logische server kan zich ook in een andere brongroep en -regio bevinden.
    1. Als u wilt herstellen naar een ander abonnement, gebruikt u de knop [Verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) om de logische server naar een ander abonnement te verplaatsen.
7. Controleer of het herstelde gegevensmagazijn online is.
8. Nadat het herstel is voltooid, u het magazijn voor herstelde gegevens configureren door [uw database na herstel te configureren.](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

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

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Een verwijderde database herstellen met de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de SQL-server waarop uw verwijderde gegevensmagazijn is gehost.
3. Selecteer het pictogram **Verwijderde databases** in de inhoudsopgave.

    ![Verwijderde databases](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecteer het verwijderde SQL Data Warehouse dat u wilt herstellen.

    ![Verwijderde databases selecteren](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Een nieuwe **databasenaam** opgeven en op **OK** klikken

    ![Databasenaam opgeven](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Herstellen vanuit een SQL-groep met geoback-up](sql-data-warehouse-restore-from-geo-backup.md)
