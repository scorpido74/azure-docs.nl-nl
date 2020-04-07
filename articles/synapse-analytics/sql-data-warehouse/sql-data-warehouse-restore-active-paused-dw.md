---
title: Een bestaande datawarehouse herstellen
description: Handleiding voor het herstellen van een bestaande SQL-groep.
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
ms.openlocfilehash: 6fa8bd42eb067124ab6ea1db77e2f3d6fba79638
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745209"
---
# <a name="restore-an-existing-sql-pool"></a>Een bestaande SQL-groep herstellen

In dit artikel leert u hoe u een bestaande SQL-groep in Azure Synapse Analytics herstelt met Azure-portal en PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Controleer uw DTU-capaciteit.** Elke groep wordt gehost door een SQL-server (bijvoorbeeld myserver.database.windows.net) met een standaard DTU-quotum. Controleer of de SQL-server voldoende dtu-quotum heeft voor de database die wordt hersteld. Zie [Een DTU-quotumwijziging aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of het aanvragen van meer DTU-quota.

## <a name="before-you-begin"></a>Voordat u begint

1. Zorg ervoor dat [Azure PowerShell wordt geïnstalleerd.](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
2. Een bestaand herstelpunt hebben waarvan u wilt herstellen. Als u een nieuw herstel wilt maken, raadpleegt u [de zelfstudie om een nieuw door de gebruiker gedefinieerd herstelpunt te maken.](sql-data-warehouse-restore-points.md)

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Een bestaande SQL-pool herstellen via PowerShell

Als u een bestaande SQL-groep vanaf een herstelpunt wilt herstellen, gebruikt u de cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell.

1. Open PowerShell.

2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.

3. Selecteer het abonnement dat de database bevat die moet worden hersteld.

4. Vermeld de herstelpunten voor de SQL-groep.

5. Kies het gewenste herstelpunt met de RestorePointCreationDate.

6. Herstel de SQL-pool naar het gewenste herstelpunt met [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell-cmdlet.
        1. Als u de SQL-groep wilt herstellen naar een andere logische server, moet u de andere logische servernaam opgeven.  Deze logische server kan zich ook in een andere brongroep en -regio bevinden.
        2. Als u wilt herstellen naar een ander abonnement, gebruikt u de knop 'Verplaatsen' om de logische server naar een ander abonnement te verplaatsen.

7. Controleer of de herstelde SQL-groep online is.

8. Nadat het herstel is voltooid, u de herstelde SQL-groep configureren door [uw database na herstel te configureren.](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Een bestaande SQL-groep herstellen via de Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Navigeer naar de SQL-groep waaruit u wilt herstellen.
3. Selecteer boven aan het overzichtsblad de optie **Herstellen**.

    ![ Overzicht van Herstellen](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecteer **Automatische herstelpunten** of **door de gebruiker gedefinieerde herstelpunten**. Als de SQL-groep geen automatische herstelpunten heeft, wacht u een paar uur of maakt u een door de gebruiker gedefinieerd herstelpunt voordat u herstelt. Selecteer een bestaande basisvoor door de gebruiker gedefinieerde herstelpunten of maak een nieuwe. Voor **Server**u een logische server kiezen in een andere resourcegroep en -regio of een nieuwe server maken. Nadat u alle parameters hebt opgemaakt, klikt u op **Controleren + Herstellen**.

    ![Automatische herstelpunten](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Volgende stappen

- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)
- [Herstellen vanuit een SQL-groep met geoback-up](sql-data-warehouse-restore-from-geo-backup.md)
