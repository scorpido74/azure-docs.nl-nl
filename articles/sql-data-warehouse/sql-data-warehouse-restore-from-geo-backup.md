---
title: Een Data Warehouse terugzetten vanuit een geo-back-up
description: Instructies voor het geo-herstellen van een SQL-groep.
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
ms.openlocfilehash: 624c6665e70802907be8a41015b78d36cca7df1c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198336"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-herstel voor SQL-groep

In dit artikel leert u hoe u de SQL-groep kunt herstellen vanuit een geo-back-up via Azure Portal en Power shell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Controleer de DTU-capaciteit.** Elke SQL-groep wordt gehost door een SQL-Server (bijvoorbeeld myserver.database.windows.net) die een standaard DTU-quotum heeft. Controleer of de SQL-Server voldoende resterende DTU-quota heeft voor de data base die wordt hersteld. Zie [een wijziging in een DTU-quotum aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of om meer DTU aan te vragen.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Herstellen vanuit een geografische regio van Azure via Power shell

Gebruik de cmdlet [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) en [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) om een geo-back-up te herstellen.

> [!NOTE]
> U kunt een geo-herstel bewerking uitvoeren op Gen2. Als u dit wilt doen, geeft u een Gen2 ServiceObjectiveName op (bijvoorbeeld DW1000**c**) als een optionele para meter.
>

1. Voordat u begint, moet u ervoor zorgen dat u [Azure PowerShell installeert](https://docs.microsoft.com/powershell/azure/overview).
2. Open PowerShell.
2. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
3. Selecteer het abonnement dat het Data Warehouse bevat dat moet worden hersteld.
4. Haal het Data Warehouse op dat u wilt herstellen.
5. Maak de herstel aanvraag voor het Data Warehouse.
6. Controleer de status van het geografisch herstelde data warehouse.
7. Als u uw data warehouse wilt configureren nadat het herstel is voltooid, raadpleegt u [de data base configureren na herstel]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

Volg de onderstaande stappen om een SQL-groep te herstellen vanuit een geo-back-up:

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) -account.
1. Klik op **+ een resource maken**. 

![Nieuwe DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Klik op **data bases** en vervolgens * * Azure Synapse Analytics (voorheen SQL DW) * *.

![Nieuwe DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Vul de gevraagde gegevens in op het tabblad **basis beginselen** en klik op **volgende: aanvullende instellingen**.

![Basisbeginselen](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Selecteer voor het gebruik van de para meter **bestaande gegevens** **back-up** en selecteer de juiste back-up in de opties omlaag schuiven. Klik op **beoordeling + maken**.
 
![Back-up](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Nadat het Data Warehouse is hersteld, controleert u of de **status** online is.

## <a name="next-steps"></a>Volgende stappen
- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)