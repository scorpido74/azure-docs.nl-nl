---
title: Een bestaande datawarehouse herstellen
description: Instructies voor het herstellen van een bestaande SQL-groep.
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
ms.openlocfilehash: bf83de481cf15711da45edf00e63262a05489247
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199519"
---
# <a name="restore-an-existing-sql-pool"></a>Een bestaande SQL-groep herstellen

In dit artikel leert u hoe u een bestaande SQL-groep in azure Synapse Analytics kunt herstellen met behulp van Azure Portal en Power shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elke pool wordt gehost door een SQL-Server (bijvoorbeeld myserver.database.windows.net) die een standaard DTU-quotum heeft. Controleer of de SQL-Server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="before-you-begin"></a>Voordat u begint

1. Zorg ervoor dat u [Azure PowerShell installeert](https://docs.microsoft.com/powershell/azure/overview).
2. Een bestaand herstel punt hebben waarvan u wilt herstellen. Als u een nieuwe herstel bewerking wilt maken, raadpleegt u [de zelf studie voor het maken van een nieuw door de gebruiker gedefinieerd herstel punt](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Een bestaande SQL-groep herstellen via Power shell

Als u een bestaande SQL-groep wilt herstellen vanaf een herstel punt, gebruikt u de Power shell [-cmdlet Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

1. Open PowerShell.

2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.

3. Selecteer het abonnement dat de Data Base bevat die u wilt herstellen.

4. De herstel punten voor de SQL-groep weer geven.

5. Kies het gewenste herstel punt met behulp van de RestorePointCreationDate.

6. Herstel de SQL-groep naar het gewenste herstel punt met de Power shell [-cmdlet Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .
        1. Als u de SQL-groep naar een andere logische server wilt herstellen, moet u de naam van de andere logische server opgeven.  Deze logische server kan zich ook in een andere resource groep en regio bevinden.
        2. Als u wilt herstellen naar een ander abonnement, gebruikt u de knop verplaatsen om de logische server te verplaatsen naar een ander abonnement.

7. Controleer of de herstelde SQL-groep online is.

8. Nadat de herstel bewerking is voltooid, kunt u de herstelde SQL-groep configureren door [de data base na het herstel te configureren](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Een bestaande SQL-groep herstellen via de Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar de SQL-groep waarvan u wilt herstellen.
3. Klik boven aan de Blade overzicht op **herstellen**.

    ![ Overzicht van Herstellen](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecteer **automatische herstel punten** of door de **gebruiker gedefinieerde herstel punten**. Als de SQL-groep geen automatische herstel punten heeft, wacht u enkele uren of maakt u een door de gebruiker gedefinieerd herstel punt voordat u het systeem herstelt. Voor door de gebruiker gedefinieerde herstel punten selecteert u een bestaande en maakt u een nieuwe. Voor- **Server**kunt u een logische server in een andere resource groep en regio kiezen of een nieuwe maken. Nadat u alle para meters hebt opgegeven, klikt u op **bekijken + herstellen**.

    ![Automatische herstel punten](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Volgende stappen
- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)
- [Een SQL-groep met geo-back-ups herstellen](sql-data-warehouse-restore-from-geo-backup.md)

 