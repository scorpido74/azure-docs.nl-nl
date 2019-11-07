---
title: Een Data Warehouse terugzetten vanuit een geo-back-up
description: Instructies voor het geo-herstellen van een Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69eb1221686da61868df8b06ed80664ae76d1627
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685496"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Geo-herstel Azure SQL Data Warehouse

In dit artikel leert u hoe u uw data warehouse kunt herstellen met behulp van een geo-back-up via Azure Portal en Power shell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elk SQL Data Warehouse wordt gehost door een SQL-Server (bijvoorbeeld myserver.database.windows.net) met een standaard-DTU-quotum. Controleer of de SQL-Server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen][Request a DTU quota change]voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Herstellen vanuit een geografische regio van Azure via Power shell

Gebruik de cmdlet [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] en [Restore-AzSqlDatabase][Restore-AzSqlDatabase] om een geo-back-up te herstellen.

> [!NOTE]
> U kunt een geo-herstel bewerking uitvoeren op Gen2. Als u dit wilt doen, geeft u een Gen2 ServiceObjectiveName op (bijvoorbeeld DW1000**c**) als een optionele para meter.
>

1. Voordat u begint, moet u ervoor zorgen dat u [Azure PowerShell installeert][Install Azure PowerShell].
2. Open PowerShell.
2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
3. Selecteer het abonnement dat het Data Warehouse bevat dat moet worden hersteld.
4. Haal het Data Warehouse op dat u wilt herstellen.
5. Maak de herstel aanvraag voor het Data Warehouse.
6. Controleer de status van het geografisch herstelde data warehouse.
7. Als u uw data warehouse wilt configureren nadat het herstel is voltooid, raadpleegt u [de data base configureren na herstel][Configure your database after recovery].

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
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

Volg de onderstaande stappen om een Azure SQL Data Warehouse van een geo-back-up te herstellen:

1. Meld u aan bij uw [Azure Portal][Azure portal] -account.
1. Klik op **+ een resource maken** en zoek naar SQL Data Warehouse en klik op **maken**.

    ![Nieuwe DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Vul de gevraagde gegevens in op het tabblad **basis beginselen** en klik op **volgende: aanvullende instellingen**.

    ![Basisbeginselen](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Selecteer voor het gebruik van de para meter **bestaande gegevens** **back-up** en selecteer de juiste back-up in de opties omlaag schuiven. Klik op **beoordeling + maken**.
 
   ![Back-up](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Nadat het Data Warehouse is hersteld, controleert u of de **status** online is.

## <a name="next-steps"></a>Volgende stappen
- [Een bestaand Data Warehouse herstellen][Restore an existing data warehouse]
- [Een verwijderd Data Warehouse herstellen][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
