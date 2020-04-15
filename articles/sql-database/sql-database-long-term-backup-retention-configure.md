---
title: 'Eén database: beheer het bewaren van back-ups op lange termijn'
description: Meer informatie over het opslaan en herstellen van geautomatiseerde back-ups voor een enkele of gepoolde Azure Database in Azure-opslag (tot 10 jaar) met behulp van de Azure Portal en PowerShell
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
ms.date: 04/14/2020
ms.openlocfilehash: 2564fd0ffd980dae4ca1835f4211fee0a0cf040c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380922"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database beheren voor het bewaren van back-ups op lange termijn

In Azure SQL Database u een enkele of een samengevoegde database configureren met een LTR [(Long Term Backup Retention](sql-database-long-term-retention.md) Policy) om de databaseback-ups automatisch maximaal 10 jaar in afzonderlijke Azure Blob-opslagcontainers te bewaren. U vervolgens een database herstellen met behulp van deze back-ups met behulp van de Azure-portal of PowerShell.

> [!IMPORTANT]
> [Azure SQL-databasebeheerde instantie](sql-database-managed-instance.md) biedt momenteel geen ondersteuning voor het behoud van back-ups op lange termijn.

## <a name="using-azure-portal"></a>Azure Portal gebruiken

In de volgende secties ziet u hoe u de Azure-portal gebruiken om de langetermijnretentie te configureren, back-ups te bekijken in langdurige retentie en back-ups te herstellen van langdurige retentie.

### <a name="configure-long-term-retention-policies"></a>Bewaarbeleid voor lange termijn configureren

U SQL Database configureren om [geautomatiseerde back-ups](sql-database-long-term-retention.md) langer te bewaren dan de bewaarperiode voor uw servicelaag.

1. Selecteer in de Azure-portal uw SQL-server en klik op **Back-ups beheren.** Schakel op het tabblad **Beleid configureren** het selectievakje in voor de database waarop u beleid voor het bewaren van back-ups op lange termijn wilt instellen of wijzigen. Als het selectievakje naast de database niet is ingeschakeld, zijn de wijzigingen voor het beleid niet van toepassing op die database.  

   ![back-upskoppeling beheren](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Selecteer in het deelvenster **Beleidsbeheer configureren** of u wekelijkse, maandelijkse of jaarlijkse back-ups wilt behouden en geef de bewaarperiode voor elk van deze gegevens op.

   ![beleid configureren](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Klik op **Toepassen**als u klaar bent.

> [!IMPORTANT]
> Wanneer u een beleid voor het bewaren van back-ups op lange termijn inschakelt, kan het tot 7 dagen duren voordat de eerste back-up zichtbaar en beschikbaar is om te herstellen. Zie voor meer informatie over de LTR-back-upcadance [voor het bewaren van back-ups op lange termijn.](sql-database-long-term-retention.md)

### <a name="view-backups-and-restore-from-a-backup"></a>Back-ups bekijken en herstellen vanuit een back-up

Bekijk de back-ups die worden bewaard voor een specifieke database met een LTR-beleid en herstel vanuit die back-ups.

1. Selecteer in de Azure-portal uw SQL-server en klik op **Back-ups beheren.** Selecteer op het tabblad **Beschikbare back-ups** de database waarvoor u beschikbare back-ups wilt zien.

   ![database selecteren](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. Bekijk in het deelvenster **Beschikbare back-ups** de beschikbare back-ups.

   ![back-ups weergeven](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Selecteer de back-up waarvan u wilt herstellen en geef de nieuwe databasenaam op.

   ![De pagina Restore](./media/sql-database-long-term-retention/ltr-restore.png)

1. Klik op **OK** om uw database te herstellen van de back-up in Azure SQL-opslag naar de nieuwe database.

1. Klik op de werkbalk op het meldingspictogram om de status van de hersteltaak weer te geven.

   ![voortgang hersteltaak](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. Wanneer de hersteltaak is voltooid, opent u de pagina **SQL-databases** om de onlangs herstelde database weer te geven.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals [een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

In de volgende secties ziet u hoe u PowerShell gebruiken om de langetermijnback-upretentie te configureren, back-ups in Azure SQL-opslag weer te geven en te herstellen vanuit een back-up in Azure SQL-opslag.

### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-rollen voor het beheren van langetermijnretentie

Voor **Get-AzSqlDatabaseLongTermRetentionBackup** en **Restore-AzSqlDatabase**moet u een van de volgende rollen hebben:

- Rol van abonnement-eigenaar of
- SQL Server-inzenderrol of
- Aangepaste rol met de volgende machtigingen:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Voor **Remove-AzSqlDatabaseLongTermRetentionBackup**moet u een van de volgende rollen hebben:

- Rol van abonnement-eigenaar of
- Aangepaste rol met de volgende machtiging:

   Microsoft.Sql/locaties/longTermRetentionServers/longtermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> De rol SQL Server-inzender heeft geen toestemming om LTR-back-ups te verwijderen.

RBAC-machtigingen kunnen worden verleend in het bereik van *een abonnement* of *resourcegroep.* Om toegang te krijgen tot LTR-back-ups die behoren tot een gedropte server, moet de toestemming echter worden verleend in het *abonnementsbereik* van die server.

- Microsoft.Sql/locaties/longTermRetentionServers/longtermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Een LTR-beleid maken

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR-beleid bekijken

In dit voorbeeld ziet u hoe u het LTR-beleid binnen een server weergeeft

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>Een LTR-beleid wissen

In dit voorbeeld ziet u hoe u een LTR-beleid uit een database wissen

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR-back-ups weergeven

In dit voorbeeld ziet u hoe u de LTR-back-ups binnen een server weergeven.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR-back-ups verwijderen

In dit voorbeeld ziet u hoe u een LTR-back-up verwijdert uit de lijst met back-ups.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Het verwijderen van LTR-back-up is niet omkeerbaar. Als u een LTR-back-up wilt verwijderen nadat de server is verwijderd, moet u toestemming hebben voor de abonnementsscope. U meldingen over elke verwijdering instellen in Azure Monitor door te filteren op bewerking 'Verwijdert een back-up voor retentie op lange termijn'. Het activiteitenlogboek bevat informatie over wie en wanneer het verzoek is ingediend. Zie [Waarschuwingen voor activiteitenlogboeken maken](../azure-monitor/platform/alerts-activity-log.md) voor gedetailleerde instructies.

### <a name="restore-from-ltr-backups"></a>Herstellen van LTR-back-ups

In dit voorbeeld ziet u hoe u herstelt vanuit een LTR-back-up. Let op, deze interface is niet gewijzigd, maar de parameter resource id vereist nu de LTR back-up resource id.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Als u wilt herstellen van een LTR-back-up nadat de server is verwijderd, moet u machtigingen hebben voor het abonnement van de server en moet dat abonnement actief zijn. U moet ook de optionele parameter ResourceGroupName weglaten.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database. Zie [punt in de tijd herstellen](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Volgende stappen

- Zie [Automatic backups](sql-database-automated-backups.md) (Automatische back-ups) voor meer informatie over door de service gegenereerde automatische back-ups.
- Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie over back-ups met langetermijnretentie.
