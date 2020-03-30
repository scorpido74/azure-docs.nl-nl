---
title: 'PowerShell: SQL Server migreren naar SQL-beheerde instantie'
titleSuffix: Azure Database Migration Service
description: Lees of u migreren van on-premises SQL Server naar azure SQL Database-beheerde instantie met Azure PowerShell en de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650721"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>SQL Server migreren naar SQL Database-beheerde instantie met PowerShell-& Azure Database Migration Service

In dit artikel migreert u de **Adventureworks2016-database** die is hersteld naar een on-premises exemplaar van SQL Server 2005 of hoger naar een Azure SQL Database beheerd exemplaar met Behulp van Microsoft Azure PowerShell. U databases migreren van een on-premises SQL Server-instantie `Az.DataMigration` naar een Azure SQL Database beheerd exemplaar met behulp van de module in Microsoft Azure PowerShell.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Maak een resourcegroep.
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject in een instantie van Azure Database Migration Service.
> * De migratie uitvoeren.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Dit artikel bevat details over het uitvoeren van zowel online als offline migraties.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt uitvoeren, moet u het volgende doen:

* [SQL Server 2016 of hoger](https://www.microsoft.com/sql-server/sql-server-downloads) (elke editie).
* Een lokale kopie van de **AdventureWorks2016** database, die [hier](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)te downloaden is.
* Het TCP/IP-protocol inschakelen, dat standaard is uitgeschakeld met sql server express-installatie. Schakel het TCP/IP-protocol in door het artikel [Een ServerNetwerkprotocol in- of uitschakelen te](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)volgen.
* Uw Windows Firewall configureren [voor toegang tot database-engine.](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)
* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een azure SQL Database beheerde instantie. U een Azure SQL Database-beheerde instantie maken door de details in het artikel [Een azure SQL Database-beheerde instantie te volgen.](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)
* Om Data [Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 of hoger te downloaden en te installeren.
* Een Microsoft Azure Virtual Network dat is gemaakt met behulp van het Azure Resource Manager-implementatiemodel, dat de Azure Database Migration Service biedt met site-to-site-connectiviteit met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Een voltooide beoordeling van uw on-premises database en schemamigratie met behulp van Data Migration Assistant, zoals beschreven in het artikel [Het uitvoeren van een SQL Server-migratiebeoordeling](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* De `Az.DataMigration` module (versie 0.7.2 of hoger) downloaden en installeren vanuit de PowerShell Gallery met behulp van [de PowerShell-cmdlet van de installatiemodule.](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
* Om ervoor te zorgen dat de referenties die worden gebruikt om verbinding te maken met de sql server-instantie van de bron, de machtiging [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) hebben.
* Als u ervoor wilt zorgen dat de referenties die worden gebruikt om verbinding te maken met de beheerde instantie van de doelbeheerde Azure SQL Database de machtigingen voor de beheerde beheerde instantievan de controledatabase heeft op de beheerde instantiedatabases van de doelazure SQL Database.

    > [!IMPORTANT]
    > Voor online migraties moet u uw Azure Active Directory-referenties al hebben ingesteld. Zie het artikel [Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)voor meer informatie.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Aanmelden bij uw Microsoft Azure-abonnement

Meld u aan bij uw Azure-abonnement met PowerShell. Zie voor meer informatie het artikel [Aanmelden met Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-brongroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) met de opdracht.

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* in de regio *Oost-VS.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Een instantie van Azure Database Migration Service maken

U een nieuw exemplaar van Azure `New-AzDataMigrationService` Database Migration Service maken met behulp van de cmdlet.
Deze cmdlet verwacht de volgende vereiste parameters:

* *Naam Azure Resource Group*. U kunt [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) de opdracht gebruiken om een Azure Resource-groep te maken zoals eerder weergegeven en de naam als parameter op te geven.
* *Servicenaam*. Tekenreeks die overeenkomt met de gewenste unieke servicenaam voor Azure Database Migration Service.
* *Locatie*. Hiermee geeft u de locatie van de service op. Geef een Azure-datacenterlocatie op, zoals West-VS of Zuidoost-Azië.
* *Sku.* Deze parameter komt overeen met de naam DMS Sku. Momenteel worden ondersteunde Sku-namen *Basic_1vCore,* *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Virtuele subnet-id*. U de cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) gebruiken om een subnet te maken.

In het volgende voorbeeld wordt een service met de naam *MyDMS* gemaakt in de resourcegroep *MyDMSResourceGroup* in de regio *Oost-VS* met behulp van een virtueel netwerk met de naam *MyVNET* en een subnet met de naam *MySubnet*.

> [!IMPORTANT]
> Het onderstaande codefragment is voor een offlinemigratie, waarvoor geen instantie van Azure Database Migration Service vereist is op basis van een Premium SKU. Voor een online migratie moet de waarde van de parameter -Sku een Premium SKU bevatten.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Maak een migratieproject

Nadat u een instantie Azure Database Migration Service hebt gemaakt, maakt u een migratieproject. Een Azure Database Migration Service-project vereist verbindingsgegevens voor zowel de bron- als doelinstanties, evenals een lijst met databases die u wilt migreren als onderdeel van het project.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Een object Databaseverbindingsgegevens maken voor de bron- en doelverbindingen

U een object DatabaseConnection `New-AzDmsConnInfo` Info maken met behulp van de cmdlet, die de volgende parameters verwacht:

* *ServerType*. Het type databaseverbinding dat is aangevraagd, bijvoorbeeld SQL, Oracle of MySQL. SQL gebruiken voor SQL Server en Azure SQL.
* *DataSource*. De naam of het IP-exemplaar van een SQL Server-instantie of een Instantie Azure SQL Database.
* *AuthType*. Het verificatietype voor verbinding, dat sqlauthentication of WindowsAuthentication kan zijn.
* *TrustServerCertificaat*. Met deze parameter wordt een waarde ingesteld die aangeeft of het kanaal is versleuteld terwijl u de certificaatketen omzeilt om de vertrouwensrelatie te valideren. De waarde `$true` kan `$false`zijn of .

In het volgende voorbeeld wordt een verbindingsinfoobject gemaakt voor een bron SQL Server genaamd *MySourceSQLServer* met sql-verificatie:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

In het volgende voorbeeld wordt het maken van verbindingsgegevens voor een azure SQL Database-beheerde instantieserver met de naam 'targetmanagedinstance.database.windows.net' met sql-verificatie weergegeven:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Databases voor het migratieproject verstrekken

Maak een `AzDataMigrationDatabaseInfo` lijst met objecten die databases opgeeft als onderdeel van het Azure Database Migration Service-project, dat kan worden opgegeven als parameter voor het maken van het project. U de `New-AzDataMigrationDatabaseInfo` cmdlet `AzDataMigrationDatabaseInfo`gebruiken om te maken.

In het volgende `AzDataMigrationDatabaseInfo` voorbeeld wordt het project voor de **AdventureWorks2016-database** gemaakt en wordt het toegevoegd aan de lijst die moet worden opgegeven als parameter voor het maken van projecten.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Een projectobject maken

Ten slotte u een Azure Database Migration Service-project genaamd `New-AzDataMigrationProject` *MyDMSProject* in *Oost-VS* maken met behulp van de eerder gemaakte bron- en doelverbindingen en de lijst met databases toevoegen om te migreren.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Een migratietaak maken en starten

Maak en start vervolgens een Azure Database Migration Service-taak. Deze taak vereist verbindingsgegevens voor zowel de bron als het doel, evenals de lijst met databasetabellen die moeten worden gemigreerd en de informatie die al is verstrekt met het project dat als voorwaarde is gemaakt.

### <a name="create-credential-parameters-for-source-and-target"></a>Referentieparameters voor bron en doel maken

Verbindingsbeveiligingsreferenties maken als een [PSCredential-object.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

In het volgende voorbeeld wordt de creatie van *PSCredential-objecten* voor zowel de bron- als doelverbindingen weergegeven, met wachtwoorden als tekenreeksvariabelen *$sourcePassword* en *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Een back-up FileShare-object maken

Maak nu een FileShare-object dat het lokale SMB-netwerkaandeel vertegenwoordigt waarvoor Azure `New-AzDmsFileShare` Database Migration Service de brondatabaseback-ups met de cmdlet kan maken.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Geselecteerd databaseobject maken

De volgende stap is het selecteren van `New-AzDmsSelectedDB` de bron- en doeldatabases met behulp van de cmdlet.

Het volgende voorbeeld is voor het migreren van één database van SQL Server naar een azure SQL Database-beheerde instantie:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Als een hele SQL Server-instantie een lift-and-shift nodig heeft in een azure SQL Database-beheerde instantie, wordt hieronder een lus weergegeven om alle databases van de bron te verwijderen. Geef in het volgende voorbeeld voor $Server, $SourceUserName en $SourcePassword uw bronSQL Server-gegevens op.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>SAS URI voor Azure Storage Container

Maak variabele met de SAS URI die de Azure Database Migration Service toegang biedt tot de opslagaccountcontainer waarnaar de service de back-upbestanden uploadt.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Aanvullende configuratievereisten

Er zijn een paar aanvullende vereisten die u moet aanpakken, maar deze verschillen afhankelijk van of u een offline of online migratie uitvoert.

#### <a name="offline-migrations"></a>Offline migraties

Voer alleen de volgende extra configuratietaken uit voor offlinemigraties.

* **Selecteer aanmeldingen**. Maak een lijst met aanmeldingen die moeten worden gemigreerd, zoals in het volgende voorbeeld wordt weergegeven:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Momenteel ondersteunt Azure Database Migration Service alleen het migreren van SQL-aanmeldingen.

* **Selecteer agenttaken**. Maak een lijst met agenttaken die moeten worden gemigreerd, zoals in het volgende voorbeeld wordt weergegeven:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Momenteel ondersteunt Azure Database Migration Service alleen taken met de taakstappen van het T-SQL-subsysteem.

#### <a name="online-migrations"></a>Online migraties

Voer alleen de volgende extra configuratietaken uit voor alleen onlinemigraties.

* **Azure Active Directory-app configureren**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Opslagbron configureren**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>De migratietaak maken en starten

Gebruik `New-AzDataMigrationTask` de cmdlet om een migratietaak te maken en te starten.

#### <a name="specify-parameters"></a>Parameters opgeven

##### <a name="common-parameters"></a>Algemene parameters

Ongeacht of u een offline of online migratie `New-AzDataMigrationTask` uitvoert, de cmdlet verwacht de volgende parameters:

* *Taaktype*. Type migratietaak dat moet worden gemaakt voor SQL Server naar Azure SQL Database Managed Instance-migratietype *MigrateSqlServerSqlSqlSqlMi* wordt verwacht. 
* *Naam resourcegroep*. Naam van azure-resourcegroep waarin de taak moet worden gemaakt.
* *ServiceNaam*. Instantie Azure Database Migration Service waarin de taak kan worden gemaakt.
* *ProjectNaam*. Naam van azure database migration service project waarin de taak te maken. 
* *Taaknaam*. Naam van de taak die moet worden gemaakt. 
* *BronVerbinding*. AzDmsConnInfo-object dat de SQL Server-verbinding van de bron vertegenwoordigt.
* *Doelverbinding*. AzDmsConnInfo-object dat de verbinding met azure SQL Database Managed Instance van doelazure vertegenwoordigt.
* *SourceCred*. [PSCredential-object](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) voor verbinding met de bronserver.
* *TargetCred*. [PSCredential-object](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) voor verbinding met doelserver.
* *SelectedDatabase*. AzDataMigrationSelectedDB-object dat de bron- en doeldatabasetoewijzing vertegenwoordigt.
* *BackupFileShare*. FileShare-object dat het lokale netwerkaandeel vertegenwoordigt waar de Azure Database Migration Service de back-ups van de brondatabase naar kan brengen.
* *Back-upBlobSasUri*. De SAS URI die de Azure Database Migration Service toegang biedt tot de opslagaccountcontainer waarnaar de service de back-upbestanden uploadt. Lees hier meer over het verkrijgen van de SAS-URI voor de blob-container.
* *SelectedLogins*. Lijst met geselecteerde aanmeldingen om te migreren.
* *SelectedAgentJobs*. Lijst met geselecteerde agenttaken die moeten worden gemigreerd.

##### <a name="additional-parameters"></a>Aanvullende parameters

De `New-AzDataMigrationTask` cmdlet verwacht ook parameters die uniek zijn voor het type migratie, offline of online, dat u uitvoert.

* **Offline migraties**. Voor offline migraties `New-AzDataMigrationTask` verwacht de cmdlet ook de volgende parameters:

  * *SelectedLogins*. Lijst met geselecteerde aanmeldingen om te migreren.
  * *SelectedAgentJobs*. Lijst met geselecteerde agenttaken die moeten worden gemigreerd.

* **Online migraties**. Voor online migraties `New-AzDataMigrationTask` verwacht de cmdlet ook de volgende parameters.

* *AzureActiveDirectoryApp*. Active Directory-toepassing.
* *StorageResourceID*. Resource-id van opslagaccount.

#### <a name="create-and-start-an-offline-migration-task"></a>Een offlinemigratietaak maken en starten

In het volgende voorbeeld wordt een offlinemigratietaak met de naam **myDMSTask ge)** gesaneerd en gestart:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Een onlinemigratietaak maken en starten

In het volgende voorbeeld wordt een online migratietaak met de naam **myDMSTask ge)** gesaneerd en gestart:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Bewaak de migratie

Als u de migratie wilt controleren, voert u de volgende taken uit.

1. Consolideer alle migratiegegevens in een variabele die $CheckTask wordt genoemd.

    Als u migratiegegevens zoals eigenschappen, status en databasegegevens die aan de migratie zijn gekoppeld, wilt combineren, gebruikt u het volgende codefragment:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Gebruik `$CheckTask` de variabele om de huidige status van de migratietaak op te halen.

    Als u `$CheckTask` de variabele wilt gebruiken om de huidige status van de migratietaak op te halen, u de migratietaak controleren die wordt uitgevoerd door de eigenschap status van de taak op te vragen, zoals in het volgende voorbeeld wordt weergegeven:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>De cutover uitvoeren (alleen online migraties)

Met een online migratie wordt een volledige back-up en herstel van databases uitgevoerd en wordt vervolgens gewerkt aan het herstellen van de transactielogboeken die zijn opgeslagen in de BackupFileShare.

Wanneer de database in een azure SQL Database-beheerde instantie wordt bijgewerkt met de nieuwste gegevens en gesynchroniseerd is met de brondatabase, u een cutover uitvoeren.

In het volgende voorbeeld wordt de cutover\migration voltooid. Gebruikers beroepen zich op deze opdracht naar eigen inzicht.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>De instantie van Azure Database Migration Service verwijderen

Nadat de migratie is voltooid, u het exemplaar Azure Database Migration Service verwijderen:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Aanvullende bronnen

Zie de Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)voor informatie over aanvullende migratiescenario's (bron-/doelparen).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Database Migration Service in het artikel [Wat is de Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
