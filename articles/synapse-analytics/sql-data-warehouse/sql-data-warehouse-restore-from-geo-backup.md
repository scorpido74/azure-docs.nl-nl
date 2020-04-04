---
title: Een gegevensmagazijn herstellen vanuit een geoback-up
description: Handleiding voor het geo-herstellen van een SQL-pool.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 68d53d2a33b7ab705dfa88f03618a5d5a3d1bced
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633029"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-herstel voor SQL-pool

In dit artikel leert u uw SQL-pool te herstellen van een geoback-up via Azure-portal en PowerShell.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Controleer uw DTU-capaciteit.** Elke SQL-groep wordt gehost door een SQL-server (bijvoorbeeld myserver.database.windows.net) met een standaard DTU-quotum. Controleer of de SQL-server voldoende dtu-quotum heeft voor de database die wordt hersteld. Zie [Een DTU-quotumwijziging aanvragen](sql-data-warehouse-get-started-create-support-ticket.md)voor meer informatie over het berekenen van de benodigde DTU of het aanvragen van meer DTU-quota.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Herstellen vanuit een azure-geografische regio via PowerShell

Als u wilt herstellen vanuit een geoback-up, gebruikt u de cmdlet [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) en [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> U een geo-herstel uitvoeren naar Gen2! Geef hiervoor een Gen2 ServiceObjectiveName (bijvoorbeeld DW1000**c)** op als optionele parameter.
>

1. Voordat u begint, moet u [Azure PowerShell installeren.](https://docs.microsoft.com/powershell/azure/overview)
2. Open PowerShell.
3. Maak verbinding met uw Azure-account en vermeld alle abonnementen die aan uw account zijn gekoppeld.
4. Selecteer het abonnement dat het gegevensmagazijn bevat dat moet worden hersteld.
5. Haal het gegevensmagazijn dat u wilt herstellen.
6. Maak de herstelaanvraag voor het gegevensmagazijn.
7. Controleer de status van het geoherstelde gegevensmagazijn.
8. Zie [Uw database configureren na herstel]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)als u uw gegevensmagazijn wilt configureren nadat het herstel is voltooid.

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

De herstelde database is tde-ingeschakeld als de brondatabase tde-ingeschakeld is.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Herstellen vanuit een Azure-geografische regio via Azure-portal

Volg de onderstaande stappen om een SQL-groep te herstellen vanuit een geoback-up:

1. Meld u aan bij uw [Azure-portalaccount.](https://portal.azure.com/)
2. Klik **op + Een resource maken**.

   ![Nieuwe DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Klik **op Databases** en vervolgens **Azure Synapse Analytics (voorheen SQL DW) **.

   ![Nieuwe DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Vul de gevraagde informatie in op het tabblad **Basisbeginselen** en klik op **Volgende: Aanvullende instellingen**.

   ![Basisbeginselen](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Selecteer Back-up voor **Het gebruik van bestaande gegevensparameter** **back-up** en selecteer de juiste back-up in de opties voor omlaag schuiven. Klik **op Controleren + Maken**.

   ![Back-up](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Controleer na het herstel van het gegevensmagazijn of de **status** online is.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande SQL-groep herstellen](sql-data-warehouse-restore-active-paused-dw.md)
- [Een verwijderde SQL-groep herstellen](sql-data-warehouse-restore-deleted-dw.md)
