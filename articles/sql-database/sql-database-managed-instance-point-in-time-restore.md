---
title: Beheerde instantie - Point-in-time restore (PITR)
description: Een SQL-database in een beheerde instantie herstellen naar een eerder tijdstip.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268806"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Een SQL-database in een beheerde instantie herstellen naar een eerder tijdstip

Gebruik point-in-time restore (PITR) om een database te maken als een kopie van een andere database uit enige tijd in het verleden. In dit artikel wordt beschreven hoe u een point-in-time-herstel van een database in een azure SQL Database-beheerde instantie uitvoeren.

Point-in-time restore is handig in herstelscenario's, zoals incidenten veroorzaakt door fouten, onjuist geladen gegevens of verwijdering van cruciale gegevens. U het ook gewoon gebruiken voor het testen of controleren. Back-upbestanden worden 7 tot 35 dagen bewaard, afhankelijk van uw database-instellingen.

Point-in-time restore kan een database herstellen:

- uit een bestaande database.
- uit een verwijderde database.
- naar dezelfde beheerde instantie of naar een andere beheerde instantie. 

## <a name="limitations"></a>Beperkingen

Point in time restore to a managed instance has the following limitations:

- Wanneer u van de ene beheerde instantie naar de andere herstelt, moeten beide instanties zich in hetzelfde abonnement en dezelfde regio bevinden. Herstel van verschillende regio's en kruisabonnementen worden momenteel niet ondersteund.
- Point-in-time herstel van een hele beheerde instantie is niet mogelijk. In dit artikel wordt alleen uitgelegd wat mogelijk is: point-in-time herstellen van een database die wordt gehost op een beheerde instantie.

> [!WARNING]
> Wees je bewust van de opslaggrootte van je beheerde exemplaar. Afhankelijk van de grootte van de te herstellen gegevens, u geen instantieopslag meer hebben. Als er niet genoeg ruimte is voor de herstelde gegevens, gebruikt u een andere aanpak.

In de volgende tabel worden point-in-time herstelscenario's voor beheerde instanties weergegeven:

|           |Bestaande DB terugzetten naar dezelfde beheerde instantie| Bestaande DB terugzetten naar een ander beheerd exemplaar|Herstel van de neergedaalde DB naar hetzelfde beheerde exemplaar|Herstel van gedaaldDB naar een andere beheerde instantie|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portal**| Ja|Nee |Ja|Nee|
|**Azure-CLI**|Ja |Ja |Nee|Nee|
|**Powershell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Een bestaande database herstellen

Een bestaande database herstellen naar dezelfde instantie met behulp van de Azure-portal, PowerShell of Azure CLI. Als u een database wilt herstellen naar een andere instantie, gebruikt u PowerShell of de Azure CLI, zodat u de eigenschappen voor de doelbeheerde instantie en resourcegroep opgeven. Als u deze parameters niet opgeeft, wordt de database standaard hersteld naar de bestaande instantie. De Azure-portal ondersteunt momenteel geen herstel naar een andere instantie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Ga naar uw beheerde instantie en selecteer de database die u wilt herstellen.
3. Selecteer **Herstellen** op de databasepagina:

    ![Een database herstellen met behulp van de Azure-portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Selecteer **op** de pagina Herstellen het punt voor de datum en tijd waarop u de database wilt herstellen.
5. Selecteer **Bevestigen** om uw database te herstellen. Met deze actie wordt het herstelproces gestart, waarbij een nieuwe database wordt gemaakt en deze wordt gevuld met gegevens uit de oorspronkelijke database op het opgegeven tijdstip. Zie [Hersteltijd](sql-database-recovery-using-backups.md#recovery-time)voor meer informatie over het herstelproces.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Zie [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)als u Azure PowerShell nog niet hebt geïnstalleerd.

Als u de database wilt herstellen met PowerShell, geeft u uw waarden op voor de parameters in de volgende opdracht. Voer vervolgens de opdracht uit:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Als u de database wilt herstellen naar een andere beheerde instantie, geeft u ook de namen op van de doelgroep en doelbeheerde instantie:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Zie [Restore-AzSqlInstanceDatabase voor](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)meer informatie.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest)als u de Azure CLI nog niet hebt geïnstalleerd.

Als u de database wilt herstellen met de Azure CLI, geeft u uw waarden op voor de parameters in de volgende opdracht. Voer vervolgens de opdracht uit:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Als u de database wilt herstellen naar een andere beheerde instantie, geeft u ook de namen van de doelgroep en beheerde instantie op:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Zie de [CLI-documentatie voor het herstellen](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)van een database in een beheerde instantie voor een gedetailleerde uitleg van de beschikbare parameters.

---

## <a name="restore-a-deleted-database"></a>Een verwijderde database herstellen

Het herstellen van een verwijderde database kan met PowerShell of Azure portal. Als u een verwijderde database wilt herstellen naar dezelfde instantie, gebruikt u de Azure-portal of PowerShell. Als u een verwijderde database wilt herstellen naar een andere instantie, gebruikt u PowerShell. 

### <a name="portal"></a>Portal 


Als u een beheerde database wilt herstellen met de Azure-portal, opent u de overzichtspagina beheerde instantie en selecteert u **Verwijderde databases**. Kies een verwijderde database die u wilt herstellen en typ de naam voor de nieuwe database die wordt gemaakt met gegevens die zijn hersteld vanuit de back-up.

  ![Schermafbeelding van de verwijderde Azure SQL-instantiedatabase herstellen](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Als u een database naar dezelfde instantie wilt herstellen, werkt u de parameterwaarden bij en voert u de volgende PowerShell-opdracht uit: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Als u de database wilt herstellen naar een andere beheerde instantie, geeft u ook de namen op van de doelgroep en doelbeheerde instantie:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Een bestaande database overschrijven

Als u een bestaande database wilt overschrijven, moet u het:

1. Laat de bestaande database vallen die u wilt overschrijven.
2. Wijzig de naam van de point-in-time-herstelde database naar de naam van de database die u hebt laten vallen.

### <a name="drop-the-original-database"></a>De oorspronkelijke database laten vallen

U de database laten vallen met behulp van de Azure-portal, PowerShell of Azure CLI.

U de database ook laten vallen door rechtstreeks verbinding te maken met de beheerde instantie, SQL Server Management Studio (SSMS) te starten en vervolgens de volgende opdracht Transact-SQL (T-SQL) uit te voeren:

```sql
DROP DATABASE WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met uw database in het beheerde exemplaar:

- [SSMS/Azure Data Studio via een virtuele Azure-machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punt-naar-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Openbaar eindpunt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de Azure-portal de database in de beheerde instantie en selecteer **Verwijderen**.

   ![Een database verwijderen met behulp van de Azure-portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Gebruik de volgende PowerShell-opdracht om een bestaande database uit een beheerde instantie te laten vallen:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht Azure CLI om een bestaande database uit een beheerde instantie te laten vallen:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>De nieuwe databasenaam wijzigen in de oorspronkelijke databasenaam

Maak rechtstreeks verbinding met de beheerde instantie en start SQL Server Management Studio. Voer vervolgens de volgende Transact-SQL -query (T-SQL) uit. De query wijzigt de naam van de herstelde database in die van de database die u wilt overschrijven.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met uw database in het beheerde exemplaar:

- [Azure virtuele machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punt-naar-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Openbaar eindpunt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [geautomatiseerde back-ups](sql-database-automated-backups.md).
