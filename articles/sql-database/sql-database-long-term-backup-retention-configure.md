---
title: Azure SQL Database lange termijn retentie van back-ups beheren | Microsoft Docs
description: Meer informatie over het opslaan van automatische back-ups in de SQL Azure opslag en deze vervolgens herstellen
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
ms.date: 08/21/2019
ms.openlocfilehash: b90e364442e46269fc949ef4aecd9a756cff5595
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904626"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Azure SQL Database lange termijn retentie van back-ups beheren

In Azure SQL Database kunt u een enkele of een gegroepeerde Data Base configureren met een Bewaar beleid voor [lange termijn back-ups](sql-database-long-term-retention.md) (LTR) voor het automatisch bewaren van back-ups van de data base in afzonderlijke Azure Blob Storage-containers voor Maxi maal tien jaar. U kunt vervolgens met behulp van de Azure Portal of Power shell een Data Base herstellen met behulp van deze back-ups.

> [!IMPORTANT]
> [Azure SQL database beheerde instantie](sql-database-managed-instance.md) biedt momenteel geen ondersteuning voor het bewaren van back-ups op lange termijn.

## <a name="use-the-azure-portal-to-manage-long-term-backups"></a>De Azure Portal gebruiken voor het beheren van back-ups op lange termijn

In de volgende secties ziet u hoe u de Azure Portal kunt gebruiken voor het configureren van de lange termijn retentie, het weer geven van back-ups voor lange termijn retentie en het terugzetten van back-ups voor lange termijn retentie.

### <a name="configure-long-term-retention-policies"></a>Beleid voor lange termijn retentie configureren

U kunt SQL Database configureren om [automatische back-ups te bewaren](sql-database-long-term-retention.md) gedurende een periode die langer is dan de retentie periode voor uw servicelaag. 

1. Selecteer in de Azure Portal uw SQL Server en klik vervolgens op **back-ups beheren**. Schakel op het tabblad **beleid configureren** *het selectie vakje in voor de data base waarvoor u het Bewaar beleid voor back-ups op lange termijn wilt instellen of wijzigen*. Als het selectie vakje naast de data base niet is ingeschakeld, zijn de wijzigingen voor het beleid niet van toepassing op die data base.  

   ![koppeling back-ups beheren](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Selecteer in het deel venster **beleid configureren** of u wekelijkse, maandelijkse of jaarlijkse back-ups wilt behouden en geef de Bewaar periode voor elk op. 

   ![beleid configureren](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Wanneer u klaar bent, klikt u op **Toep assen**.

> [!IMPORTANT]
> Wanneer u een lange termijn beleid voor het bewaren van back-ups inschakelt, kan het tot zeven dagen duren voordat de eerste back-up zichtbaar is en beschikbaar is voor herstel. Zie [lange termijn](sql-database-long-term-retention.md)retentie van back-ups voor meer informatie over de CADANCE voor LTR-back-ups.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Back-ups weer geven en terugzetten vanuit een back-up met behulp van Azure Portal

Bekijk de back-ups die worden bewaard voor een specifieke data base met een LTR-beleid en herstel van deze back-ups. 

1. Selecteer in de Azure Portal uw SQL Server en klik vervolgens op **back-ups beheren**. Selecteer de data base waarvoor u beschik bare back-ups wilt weer geven op het tabblad **beschik bare back-ups** .

   ![data base selecteren](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Bekijk de beschik bare back-ups in het deel venster **beschik bare back-ups** . 

   ![back-ups weer geven](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Selecteer de back-up van waaruit u wilt herstellen en geef vervolgens de naam van de nieuwe Data Base op.

   ![De pagina Restore](./media/sql-database-long-term-retention/ltr-restore.png)

5. Klik op **OK** om de data base terug te zetten van de back-up in Azure SQL Storage naar de nieuwe data base.

6. Klik op de werkbalk op het meldingspictogram om de status van de hersteltaak weer te geven.

   ![voortgang hersteltaak](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Wanneer de herstel taak is voltooid, opent u de pagina **SQL-data bases** om de zojuist herstelde data base weer te geven.

> [!NOTE]
> Hier kunt u verbinding maken met de herstelde database met behulp van SQL Server Management Studio om noodzakelijke taken uit te voeren, zoals [een deel van de gegevens uit de herstelde database extraheren om naar de bestaande database te kopiëren, of de bestaande database verwijderen en de naam van de herstelde database wijzigen in de naam van de bestaande database](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-manage-long-term-backups"></a>Power shell gebruiken voor het beheren van back-ups op lange termijn

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

In de volgende secties ziet u hoe u Power shell kunt gebruiken voor het configureren van de lange termijn retentie van back-ups, het weer geven van back-ups in Azure SQL Storage en het herstellen van een back-up in Azure SQL Storage.


### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-rollen voor het beheren van lange termijn retentie

Voor **Get-AzSqlDatabaseLongTermRetentionBackup** en **Restore-AzSqlDatabase**moet u een van de volgende rollen hebben:

- Rol van abonnements eigenaar of
- SQL Server rol Inzender of
- Aangepaste rol met de volgende machtigingen:

   Micro soft. SQL/locations/longTermRetentionBackups/Read micro soft. SQL/locations/longTermRetentionServers/longTermRetentionBackups/Read micro soft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/lezen
 
Voor **Remove-AzSqlDatabaseLongTermRetentionBackup**moet u een van de volgende rollen hebben:

- Rol van abonnements eigenaar of
- Aangepaste rol met de volgende machtiging:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


> [!NOTE]
> De rol SQL Server Inzender heeft geen machtiging voor het verwijderen van LTR-back-ups.

RBAC-machtigingen kunnen worden verleend in *abonnementen* of in het bereik van de *resource groep* . Voor toegang tot LTR-back-ups die deel uitmaken van een verwijderde server, moet de machtiging echter worden verleend in het *abonnements* bereik van die server.


### <a name="create-an-ltr-policy"></a>Een LTR-beleid maken

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR-beleid weer geven
Dit voor beeld laat zien hoe u het LTR-beleid in een server kunt weer geven

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Een LTR-beleid wissen
Dit voor beeld laat zien hoe u een LTR-beleid uit een Data Base wist

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR-back-ups weer geven

Dit voor beeld laat zien hoe u de LTR-back-ups in een server kunt weer geven. 

```powershell
# List all LTR backups under the current subscription in a specific Azure region 
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# List the LTR backups under a specific resource group in a specific Azure region 
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup

# List the LTR backups under an existing server
# The list includes backups from the existing servers only grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ResourceGroupName $resourceGroup -ServerName $serverName

# List the LTR backups for a specific database 
# The backups are ordered by the timestamp, the earliest backup first. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
# The list includes backups for existing servers and dropped servers grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest backup first.  
# Requires Subscription scope permission
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database under a server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR-back-ups verwijderen

In dit voor beeld ziet u hoe u een LTR-back-up verwijdert uit de lijst met back-ups.

```powershell
# Remove the earliest backup from the list of backups
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> LTR-back-up verwijderen is niet-omkeerbaar. Als u een LTR-back-up wilt verwijderen nadat de server is verwijderd, moet u een machtiging voor het abonnements bereik hebben. U kunt meldingen over elke verwijderings bewerking instellen in Azure Monitor door te filteren op een back-up van een lange termijn retentie verwijderen. Het activiteiten logboek bevat informatie over wie en wanneer de aanvraag is ingediend. Zie [waarschuwingen voor activiteiten logboeken maken](../azure-monitor/platform/alerts-activity-log.md) voor gedetailleerde instructies.
>

### <a name="restore-from-ltr-backups"></a>Herstellen vanuit LTR-back-ups
In dit voor beeld ziet u hoe u een LTR-back-up herstelt. Opmerking: deze interface is niet gewijzigd, maar voor de resource-id-para meter is nu de bron-id LTR backup vereist. 

```powershell
# Restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup 
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Als u een LTR-back-up wilt herstellen nadat de server is verwijderd, moet u machtigingen hebben die zijn afgestemd op het abonnement van de server en dat abonnement actief is. U moet ook de optionele para meter-ResourceGroupName weglaten.  
>

> [!NOTE]
> Vanaf hier kunt u verbinding maken met de herstelde data base met behulp van SQL Server Management Studio om de benodigde taken uit te voeren, zoals het extra heren van gegevens uit de herstelde data base om deze te kopiëren naar de bestaande data base of de bestaande Data Base te verwijderen en de herstelde naam te wijzigen Data Base naar de bestaande database naam. Zie [herstel punt in tijd](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Volgende stappen

- Zie [Automatic backups](sql-database-automated-backups.md) (Automatische back-ups) voor meer informatie over door de service gegenereerde automatische back-ups.
- Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie over back-ups met langetermijnretentie.
