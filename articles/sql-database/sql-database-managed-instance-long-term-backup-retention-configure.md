---
title: 'Beheerde instantie: Back-upretentie op lange termijn (PowerShell)'
description: Meer informatie over het opslaan en herstellen van geautomatiseerde back-ups op afzonderlijke Azure Blob-opslagcontainers voor een Azure SQL Database beheerd exemplaar met PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/19/2020
ms.openlocfilehash: 24eacb555704593fe44bc2d949de44de163345bc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677103"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Azure SQL Database beheerde instantie voor langdurige back-upretentie (PowerShell)

In de instantie beheerd azure SQL Database u een LTR [(Long Term Backup Retention](sql-database-long-term-retention.md#managed-instance-support) Policy) configureren als een beperkte openbare preview-functie. Hiermee u databaseback-ups tot 10 jaar automatisch behouden in afzonderlijke Azure Blob-opslagcontainers. U vervolgens een database herstellen met behulp van deze back-ups met PowerShell.

   > [!IMPORTANT]
   > LTR voor beheerde exemplaren is momenteel in beperkte preview en beschikbaar voor EA- en CSP-abonnementen per geval. Als u inschrijving wilt aanvragen, maakt u een [Azure-ondersteuningsticket](https://azure.microsoft.com/support/create-ticket/) onder het ondersteuningsonderwerp **Back-up, Herstel en Ondersteuning voor bedrijfscontinuïteit/langetermijnback-upbehouden.** 


In de volgende secties ziet u hoe u PowerShell gebruiken om de langetermijnback-upretentie te configureren, back-ups in Azure SQL-opslag weer te geven en te herstellen vanuit een back-up in Azure SQL-opslag.

## <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-rollen voor het beheren van langetermijnretentie

Voor **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** en **Restore-AzSqlInstanceDatabase**moet u een van de volgende rollen hebben:

- Rol van abonnement-eigenaar of
- Rol van beheerde instantieinzender of
- Aangepaste rol met de volgende machtigingen:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Voor **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**moet u een van de volgende rollen hebben:

- Rol van abonnement-eigenaar of
- Aangepaste rol met de volgende machtiging:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> De rol Inzender van beheerde instantie heeft geen toestemming om LTR-back-ups te verwijderen.

RBAC-machtigingen kunnen worden verleend in het bereik van *een abonnement* of *resourcegroep.* Om toegang te krijgen tot LTR-back-ups die behoren tot een gevallen exemplaar, moet de toestemming echter worden verleend in het *abonnementsbereik* van die instantie.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>Een LTR-beleid maken

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>LTR-beleid bekijken

In dit voorbeeld ziet u hoe u het LTR-beleid in een instantie weergeven

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>Een LTR-beleid wissen

In dit voorbeeld ziet u hoe u een LTR-beleid uit een database wissen

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>LTR-back-ups weergeven

In dit voorbeeld ziet u hoe u de LTR-back-ups in een instantie weergeven.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>LTR-back-ups verwijderen

In dit voorbeeld ziet u hoe u een LTR-back-up verwijdert uit de lijst met back-ups.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Het verwijderen van LTR-back-up is niet omkeerbaar. Als u een LTR-back-up wilt verwijderen nadat de instantie is verwijderd, moet u toestemming voor het bereik van abonnement hebben. U meldingen over elke verwijdering instellen in Azure Monitor door te filteren op bewerking 'Verwijdert een back-up voor retentie op lange termijn'. Het activiteitenlogboek bevat informatie over wie en wanneer het verzoek is ingediend. Zie [Waarschuwingen voor activiteitenlogboeken maken](../azure-monitor/platform/alerts-activity-log.md) voor gedetailleerde instructies.

## <a name="restore-from-ltr-backups"></a>Herstellen van LTR-back-ups

In dit voorbeeld ziet u hoe u herstelt vanuit een LTR-back-up. Let op, deze interface is niet gewijzigd, maar de parameter resource id vereist nu de LTR back-up resource id.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Als u wilt herstellen van een LTR-back-up nadat de instantie is verwijderd, moet u machtigingen hebben voor het abonnement van de instantie en moet dat abonnement actief zijn. U moet ook de optionele parameter ResourceGroupName weglaten.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database. Zie [punt in de tijd herstellen](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Volgende stappen

- Zie [Automatic backups](sql-database-automated-backups.md) (Automatische back-ups) voor meer informatie over door de service gegenereerde automatische back-ups.
- Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie over back-ups met langetermijnretentie.
