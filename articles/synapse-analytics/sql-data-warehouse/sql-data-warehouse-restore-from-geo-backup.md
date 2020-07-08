---
title: Een Data Warehouse terugzetten vanuit een geo-back-up
description: Instructies voor het geo-herstellen van een SQL-groep.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2b7e980f2ffd31bd10b481fe4fc8e0617c40717a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85205081"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-herstel voor SQL-groep

In dit artikel leert u hoe u de SQL-groep kunt herstellen vanuit een geo-back-up via Azure Portal en Power shell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elke SQL-groep wordt gehost door een [logische SQL-Server](../../azure-sql/database/logical-servers.md) (bijvoorbeeld MyServer.database.Windows.net) die een standaard DTU-quotum heeft. Controleer of de SQL-Server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Herstellen vanuit een geografische regio van Azure via Power shell

Gebruik de cmdlet [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om een geo-back-up te herstellen.

> [!NOTE]
> U kunt een geo-herstel bewerking uitvoeren op Gen2. Als u dit wilt doen, geeft u een Gen2 ServiceObjectiveName op (bijvoorbeeld DW1000**c**) als een optionele para meter.
>

1. Voordat u begint, moet u ervoor zorgen dat u [Azure PowerShell installeert](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat het Data Warehouse bevat dat moet worden hersteld.
5. Haal het Data Warehouse op dat u wilt herstellen.
6. Maak de herstel aanvraag voor het Data Warehouse.
7. Controleer de status van het geografisch herstelde data warehouse.
8. Als u uw data warehouse wilt configureren nadat het herstel is voltooid, raadpleegt u [de data base configureren na herstel]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

De herstelde data base wordt TDE ingeschakeld als de bron database TDE is ingeschakeld.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Herstellen vanuit een geografische regio van Azure via Azure Portal

Volg de onderstaande stappen om een SQL-groep te herstellen vanuit een geo-back-up:

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) -account.
2. Klik op **+ een resource maken**.

   ![Nieuwe DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Klik op **data bases** en vervolgens op **Azure Synapse Analytics (voorheen SQL DW)**.

   ![Nieuwe DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Vul de gevraagde gegevens in op het tabblad **basis beginselen** en klik op **volgende: aanvullende instellingen**.

   ![Basisbeginselen](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Selecteer voor het gebruik van de para meter **bestaande gegevens** **back-up** en selecteer de juiste back-up in de opties omlaag schuiven. Klik op **beoordeling + maken**.

   ![Back-up](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Nadat het Data Warehouse is hersteld, controleert u of de **status** online is.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)
