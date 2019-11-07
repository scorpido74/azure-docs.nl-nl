---
title: Een verwijderde datawarehouse herstellen
description: Instructies voor het herstellen van een verwijderde Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cb09b4808bd6d59d2f70e85d204ab8451d501cee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692608"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Een verwijderde Azure SQL Data Warehouse herstellen

In dit artikel leert u hoe u een verwijderde SQL Data Warehouse herstelt met behulp van Azure Portal en Power shell:

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elk SQL Data Warehouse wordt gehost door een SQL-Server (bijvoorbeeld myserver.database.windows.net) met een standaard-DTU-quotum.  Controleer of de SQL-Server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen][Request a DTU quota change]voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Een verwijderd Data Warehouse herstellen via Power shell

Als u een verwijderde SQL Data Warehouse wilt herstellen, gebruikt u de cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase] . Als de bijbehorende logische server ook is verwijderd, kunt u dat data warehouse niet herstellen.

1. Voordat u begint, moet u ervoor zorgen dat u [Azure PowerShell installeert][Install Azure PowerShell].
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat het verwijderde data warehouse bevat dat moet worden hersteld.
5. Het specifieke verwijderde data warehouse ophalen.
6. Het verwijderde data warehouse herstellen
    1. Als u de verwijderde SQL Data Warehouse wilt herstellen naar een andere logische server, moet u de naam van de andere logische server opgeven.  Deze logische server kan zich ook in een andere resource groep en regio bevinden.
    1. Als u wilt herstellen naar een ander abonnement, gebruikt u de knop [verplaatsen][Move] om de logische server naar een ander abonnement te verplaatsen.
1. Controleer of het herstelde data warehouse online is.
1. Nadat de herstel bewerking is voltooid, kunt u uw herstelde data warehouse configureren door [de data base na het herstel te configureren][Configure your database after recovery].

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

1. Meld u aan bij de [Azure Portal][Azure portal].
2. Ga naar de SQL-server waarop uw verwijderde data warehouse werd gehost.
3. Selecteer het pictogram **Verwijderde data bases** in de inhouds opgave.

    ![Verwijderde data bases](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecteer de verwijderde SQL Data Warehouse die u wilt herstellen.

    ![Verwijderde data bases selecteren](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Geef een nieuwe **database naam** op en klik op **OK**

    ![Database naam opgeven](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Volgende stappen
- [Een bestaand Data Warehouse herstellen][Restore an existing data warehouse]
- [Herstellen vanuit een geografisch back-updata Warehouse][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
