---
title: Managed instance-in-time Restore (PITR)
description: Een SQL database in een beheerd exemplaar herstellen naar een eerder tijdstip.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197518"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Een SQL database in een beheerd exemplaar herstellen naar een eerder tijdstip

Gebruik PITR (Point-in-time Restore) om een Data Base van enige tijd in het verleden te maken als een kopie van een andere data base. In dit artikel wordt beschreven hoe u een herstel bewerking op een bepaald tijdstip van een data base in een Azure SQL Database beheerde instantie doet.

Herstel naar een bepaald tijdstip is handig in herstel scenario's, zoals incidenten die worden veroorzaakt door fouten, onjuist geladen gegevens of het verwijderen van cruciale gegevens. U kunt het ook alleen voor testen of controleren gebruiken. Back-upbestanden worden 7 tot 35 dagen bewaard, afhankelijk van de instellingen van uw data base.

Herstel naar een bepaald tijdstip kan een Data Base herstellen:

- uit een bestaande data base.
- uit een verwijderde data base.
- naar hetzelfde beheerde exemplaar of naar een ander beheerd exemplaar. 

## <a name="limitations"></a>Beperkingen

Herstel naar een beheerd exemplaar van een bepaald tijdstip heeft de volgende beperkingen:

- Wanneer u van het ene beheerde exemplaar naar het andere herstelt, moeten beide exemplaren zich in hetzelfde abonnement en dezelfde regio bevinden. Herstel van meerdere regio's en meerdere abonnementen wordt momenteel niet ondersteund.
- Herstel naar een bepaald tijdstip van een volledig beheerd exemplaar is niet mogelijk. In dit artikel wordt alleen uitgelegd wat er mogelijk is: herstel naar een bepaald tijdstip van een Data Base die wordt gehost op een beheerd exemplaar.

> [!WARNING]
> Houd rekening met de opslag grootte van uw beheerde exemplaar. Afhankelijk van de grootte van de gegevens die moeten worden hersteld, kunt u mogelijk geen exemplaar van de opslag uitvoeren. Als er onvoldoende ruimte is voor de herstelde gegevens, gebruikt u een andere methode.

De volgende tabel toont scenario's voor herstel naar een bepaald tijdstip voor beheerde instanties:

|           |Bestaande Data Base naar hetzelfde beheerde exemplaar herstellen| Een bestaande data base herstellen naar een ander beheerd exemplaar|Verwijderde data base naar hetzelfde beheerde exemplaar herstellen|Verwijderde data base herstellen naar een ander beheerd exemplaar|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| Ja|Nee |Ja|Nee|
|**Azure CLI**|Ja |Ja |Nee|Nee|
|**PowerShell**| Ja|Ja |Ja|Ja|

## <a name="restore-an-existing-database"></a>Een bestaande data base herstellen

Een bestaande data base herstellen naar hetzelfde exemplaar met behulp van de Azure Portal, Power shell of de Azure CLI. Als u een Data Base naar een ander exemplaar wilt herstellen, gebruikt u Power shell of de Azure CLI zodat u de eigenschappen voor het beheerde exemplaar van het doel en de resource groep kunt opgeven. Als u deze para meters niet opgeeft, wordt de data base standaard teruggezet naar het bestaande exemplaar. Het Azure Portal biedt momenteel geen ondersteuning voor het herstellen naar een ander exemplaar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Ga naar uw beheerde exemplaar en selecteer de data base die u wilt herstellen.
3. Selecteer **herstellen** op de pagina Data Base:

    ![Een Data Base herstellen met behulp van de Azure Portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Selecteer op de pagina **herstellen** het punt voor de datum en tijd waarop u de Data Base wilt terugzetten.
5. Selecteer **bevestigen** om uw data base te herstellen. Met deze actie wordt het herstel proces gestart, waarmee een nieuwe Data Base wordt gemaakt en gevuld met gegevens uit de oorspronkelijke Data Base op het opgegeven tijdstip. Zie [herstel tijd](sql-database-recovery-using-backups.md#recovery-time)voor meer informatie over het herstel proces.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als Azure PowerShell nog niet is geïnstalleerd, raadpleegt u [de module Azure PowerShell installeren](https://docs.microsoft.com/powershell/azure/install-az-ps).

Als u de Data Base wilt herstellen met behulp van Power shell, geeft u de waarden voor de para meters op in de volgende opdracht. Voer vervolgens de opdracht uit:

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

Als u de Data Base wilt herstellen naar een ander beheerd exemplaar, geeft u ook de namen op van de doel resource groep en het beheerde exemplaar van het doel:  

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).

Als u de Data Base wilt herstellen met behulp van de Azure CLI, geeft u de waarden voor de para meters op in de volgende opdracht. Voer vervolgens de opdracht uit:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Als u de Data Base wilt herstellen naar een ander beheerd exemplaar, geeft u ook de namen van de doel resource groep en het beheerde exemplaar op:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Zie de [cli-documentatie voor het herstellen van een data base in een beheerd exemplaar](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)voor een gedetailleerde uitleg van de beschik bare para meters.

---

## <a name="restore-a-deleted-database"></a>Een verwijderde database herstellen

Het herstellen van een verwijderde data base kan worden uitgevoerd met behulp van Power shell of Azure Portal. Als u een verwijderde data base naar hetzelfde exemplaar wilt herstellen, gebruikt u de Azure Portal of Power shell. Gebruik Power shell om een verwijderde data base te herstellen naar een ander exemplaar. 

### <a name="portal"></a>Portal 


Als u een beheerde Data Base wilt herstellen met behulp van de Azure Portal, opent u de overzichts pagina van het beheerde exemplaar en selecteert u **Verwijderde data bases**. Kies een verwijderde data base die u wilt herstellen en typ de naam voor de nieuwe Data Base die wordt gemaakt met de gegevens die worden teruggezet vanuit de back-up.

  ![Scherm opname van verwijderde Azure SQL-exemplaar database](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Als u een Data Base naar hetzelfde exemplaar wilt herstellen, werkt u de parameter waarden bij en voert u vervolgens de volgende Power shell-opdracht uit: 

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

Als u de Data Base wilt herstellen naar een ander beheerd exemplaar, geeft u ook de namen op van de doel resource groep en het beheerde exemplaar van het doel:

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

## <a name="overwrite-an-existing-database"></a>Een bestaande data base overschrijven

Als u een bestaande Data Base wilt overschrijven, moet u het volgende doen:

1. Verwijder de bestaande data base die u wilt overschrijven.
2. Wijzig de naam van de herstelde data base naar een Data Base die u hebt verwijderd.

### <a name="drop-the-original-database"></a>De oorspronkelijke data base verwijderen

U kunt de data base verwijderen met behulp van de Azure Portal, Power shell of de Azure CLI.

U kunt de data base ook verwijderen door rechtstreeks verbinding te maken met het beheerde exemplaar, te beginnen met SQL Server Management Studio (SSMS) en vervolgens de volgende Transact-SQL-opdracht (T-SQL) uit te voeren:

```sql
DROP DATABASE WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met uw data base in het beheerde exemplaar:

- [SSMS/Azure Data Studio via een virtuele machine van Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punt-naar-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Openbaar eind punt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de Azure Portal de data base uit het beheerde exemplaar en selecteer vervolgens **verwijderen**.

   ![Een Data Base verwijderen met behulp van de Azure Portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende Power shell-opdracht om een bestaande Data Base uit een beheerd exemplaar te verwijderen:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende Azure CLI-opdracht om een bestaande Data Base uit een beheerd exemplaar te verwijderen:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Wijzig de naam van de nieuwe data base zodat deze overeenkomt met de naam van de oorspronkelijke data base

Maak rechtstreeks verbinding met het beheerde exemplaar en start SQL Server Management Studio. Voer vervolgens de volgende Transact-SQL-query (T-SQL) uit. De query wijzigt de naam van de herstelde data base in die van de verwijderde data base die u wilt overschrijven.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Gebruik een van de volgende methoden om verbinding te maken met uw data base in het beheerde exemplaar:

- [Virtuele Azure-machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Punt-naar-site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Openbaar eind punt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [automatische back-ups](sql-database-automated-backups.md).
