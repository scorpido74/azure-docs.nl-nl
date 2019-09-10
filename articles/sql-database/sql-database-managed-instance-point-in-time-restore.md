---
title: Restore-in-time herstel van SQL Database-exemplaar Microsoft Docs
description: Een data base in een SQL-beheerd exemplaar herstellen naar een eerder tijdstip.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862133"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Een SQL Managed instance-data base herstellen naar een eerder tijdstip

Met PITR (Point-in-time Restore) kunt u een Data Base op een bepaald moment in het verleden maken als een kopie van een andere data base. In dit artikel wordt beschreven hoe u een herstel bewerking van een data base in een beheerd exemplaar in een keer uitvoert.

Herstel naar een bepaald tijdstip kan worden gebruikt in herstel scenario's, zoals incidenten die worden veroorzaakt door fouten, onjuist geladen gegevens, het verwijderen van cruciale gegevens en andere problemen, evenals alleen voor test-en controle doeleinden. Afhankelijk van de data base-instellingen, worden de back-upbestanden gedurende een periode tussen 7 en 35 dagen bewaard.

Herstel naar een bepaald tijdstip kan worden gebruikt voor het volgende:

- Een Data Base herstellen vanuit een bestaande data base.
- Een Data Base herstellen vanuit een verwijderde data base.

Bovendien kan met een beheerd exemplaar herstel naar een bepaald tijdstip worden gebruikt voor het volgende: 

- Een Data Base herstellen naar hetzelfde beheerde exemplaar.
- Een Data Base herstellen naar een ander beheerd exemplaar.


> [!NOTE]
> Herstel naar een bepaald tijdstip van een volledig beheerd exemplaar is niet mogelijk. Wat mogelijk is, en in dit artikel wordt beschreven, is herstel naar een bepaald tijdstip van een Data Base die wordt gehost op een beheerd exemplaar.


## <a name="limitations"></a>Beperkingen

Bij het herstellen naar een ander beheerd exemplaar moeten beide instanties zich in hetzelfde abonnement en dezelfde regio bevinden. Herstel bewerkingen voor meerdere regio's en cross-abonnement worden momenteel niet ondersteund.

> [!WARNING]
> Wees voorzichtig met de opslag grootte van uw beheerde exemplaar. afhankelijk van de grootte van het herstellen van gegevens, is het mogelijk dat u geen exemplaar hebt van de opslag. Als er onvoldoende ruimte is voor de herstelde gegevens, gebruikt u een alternatieve benadering.

De volgende tabel bevat scenario's voor herstel naar een bepaald tijdstip voor een beheerd exemplaar:

|           |Bestaande data base herstellen| Bestaande data base herstellen|Verwijderde data base herstellen| Verwijderde data base herstellen|
|:----------|:----------|:----------|:----------|:----------|
|Bestemming| Dezelfde MI|Een andere MI |Dezelfde MI|Een andere MI |
|Azure Portal| Ja|Nee |Nee|Nee|
|Azure-CLI|Ja |Ja |Nee|Nee|
|PowerShell| Ja|Ja |Ja|Ja|


## <a name="restore-existing-database"></a>Bestaande data base herstellen

Een bestaande data base herstellen naar hetzelfde exemplaar met behulp van de Azure Portal, Power shell of de Azure CLI. Een Data Base herstellen naar een ander exemplaar met behulp van Power shell of Azure CLI door het beoogde beheerde exemplaar en de eigenschappen van de resource groep op te geven. Als deze para meters niet zijn opgegeven, wordt de data base standaard teruggezet naar het bestaande exemplaar. Herstellen naar een ander exemplaar wordt momenteel niet ondersteund via de Azure Portal. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar uw beheerde exemplaar en selecteer de data base die u wilt herstellen. 
1. Selecteer **herstellen** op de pagina Data Base. 

    ![Bestaande data base herstellen](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. Selecteer op de pagina **herstellen** het punt voor de datum en tijd in de geschiedenis waarnaar u de Data Base wilt herstellen.
1. Selecteer **bevestigen** om uw data base te herstellen. Hiermee wordt het herstel proces gestart, waarmee een nieuwe Data Base wordt gemaakt en die op het gewenste tijdstip wordt gevuld met gegevens uit de oorspronkelijke data base. Zie [herstel tijd](sql-database-recovery-using-backups.md#recovery-time)voor meer informatie over het herstel proces. 

1. Beheerd exemplaar zoeken
1. Selecteer de data base die u wilt herstellen
1. Klik op het scherm Data Base op actie herstellen
1. Selecteer op het scherm herstellen de datum en tijd van het punt in de geschiedenis waarnaar u de Data Base wilt herstellen
1. Na de bevestiging wordt het herstel proces gestart en wordt, afhankelijk van de grootte van de data base, een nieuwe data base gemaakt en gevuld met gegevens uit de oorspronkelijke Data Base op het gewenste moment. Voor de duur van het herstel proces met behulp van back-ups.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Als Azure PowerShell nog niet is geïnstalleerd, raadpleegt u [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps).

Als u de Data Base wilt herstellen met Power shell, werkt u de para meters bij met de waarden en voert u de volgende opdracht uit:

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

Als u de Data Base wilt herstellen naar een ander beheerd exemplaar, stelt u de naam van de doel resource groep en de naam van het doel beheerde exemplaar in.  

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

Zie [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)voor meer informatie.


# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).

Als u de Data Base wilt herstellen met behulp van de Azure CLI, werkt u de para meters bij met de waarden en voert u de volgende opdracht uit:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Als u de Data Base wilt herstellen naar een ander beheerd exemplaar, stelt u de naam van de doel resource groep en de naam van het doel beheerde exemplaar in.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Zie [Managed instance cli](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)(Engelstalig) voor gedetailleerde uitleg van de beschik bare para meters. 

---

## <a name="restore-a-deleted-database"></a>Een verwijderde database herstellen 
 
Het herstellen van een verwijderde data base kan alleen worden uitgevoerd met Power shell. De data base kan worden hersteld naar hetzelfde exemplaar of een ander exemplaar. 

Als u een verwijderde data base wilt herstellen met Power shell, werkt u de para meters bij met de waarden en voert u de volgende opdracht uit:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Als u de verwijderde data base naar een ander exemplaar wilt herstellen, wijzigt u de naam van de resource groep en de naam van het beheerde exemplaar.

De locatie parameter moet overeenkomen met de locatie van de resource groep en het beheerde exemplaar.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Bestaande data base overschrijven 
 
Als u een bestaande Data Base wilt overschrijven, moet u ook het volgende doen:

1. Verwijder de bestaande data base die u wilt overschrijven.
1. Wijzig de naam van de herstelde data base op een bepaald tijdstip in de data base die is verwijderd. 


### <a name="drop-original-database"></a>Oorspronkelijke data base verwijderen 
 
Het verwijderen van de data base kan worden uitgevoerd met de Azure Portal, Power shell of de Azure CLI. 

U kunt de data base ook verwijderen door rechtstreeks verbinding te maken met het beheerde exemplaar, door SQL Server Management Studio (SSMS) te starten en de onderstaande opdracht Transact-SQL (T-SQL) uit te voeren.

```sql
DROP DATABASE WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met de data base van uw beheerde instantie: 

- [Virtuele SQL-machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punt-naar-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Openbaar eindpunt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Selecteer in de Azure Portal de data base uit het beheerde exemplaar en selecteer **verwijderen**.

   ![Bestaande data base herstellen](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende Power shell-opdracht om een bestaande Data Base uit een beheerd exemplaar te verwijderen: 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende Azure CLI-opdracht om een bestaande Data Base uit een beheerd exemplaar te verwijderen: 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>Nieuwe database naam wijzigen in origineel

Maak rechtstreeks verbinding met het beheerde exemplaar, start SQL Server Management Studio en voer vervolgens de volgende Transact-SQL-query (T-SQL) uit om de naam van de herstelde data base te wijzigen in die van de verwijderde data base die u wilt overschrijven. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Gebruik een van de volgende methoden om verbinding te maken met de data base van uw beheerde instantie: 

- [Virtuele SQL-machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punt-naar-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Openbaar eindpunt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [lange termijn retentie](sql-database-long-term-retention.md) en [automatische back-ups](sql-database-automated-backups.md). 
