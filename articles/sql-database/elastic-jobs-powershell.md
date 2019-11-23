---
title: Een elastische-taakagent maken met behulp van PowerShell
description: Ontdek hoe u een elastische-taakagent maakt met behulp van PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 74a72df9d8c0bc8a578fea57ab81fb496f8e6add
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420369"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Een elastische-taakagent maken met behulp van PowerShell

[Elastisc Jobs](sql-database-job-automation-overview.md#elastic-database-jobs-preview) maken het mogelijk om één of meerdere T-SQL-scripts (Transact-SQL) parallel in veel databases uit te voeren.

In this tutorial, you learn the steps required to run a query across multiple databases:

> [!div class="checklist"]
> * Een Elastic Jobs-agent maken
> * Taakreferenties maken zodat taken scripts kunnen uitvoeren op de doelen ervan
> * De doelen (servers, elastische pools, databases, shardtoewijzingen) definiëren waarop u de taak wilt uitvoeren
> * Databasereferenties maken in de doeldatabases zodat de agent verbinding kan maken en taken uitvoeren
> * Een taak maken
> * Taakstappen toevoegen aan een taak
> * Uitvoering van een taak starten
> * Een taak bewaken

## <a name="prerequisites"></a>Vereisten

The upgraded version of Elastic Database jobs has a new set of PowerShell cmdlets for use during migration. These new cmdlets transfer all of your existing job credentials, targets (including databases, servers, custom collections), job triggers, job schedules, job contents, and jobs over to a new Elastic Job agent.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Install the latest Elastic Jobs cmdlets

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

Install the **Az.Sql** module to get the latest Elastic Job cmdlets. Voer de volgende opdrachten met beheerderstoegang uit in PowerShell.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

In addition to the **Az.Sql** module, this tutorial also requires the *SqlServer* PowerShell module. Zie [SQL Server PowerShell-module installeren](/sql/powershell/download-sql-server-ps-module) voor meer informatie.

## <a name="create-required-resources"></a>Vereiste resources maken

Voor het maken van een Elastic Jobs-agent is een database (S0 of hoger) vereist om te gebruiken als [taakdatabase](sql-database-job-automation-overview.md#job-database).

The script below creates a new resource group, server, and database for use as the Job database. The second script creates a second server with two blank databases to execute jobs against.

Elastic Jobs heeft geen specifieke naamgevingsvereisten, zodat u elke naamgevingsconventie kunt gebruiken die u wilt, zolang deze maar voldoet aan de [Azure-vereisten](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>Use Elastic Jobs

To use Elastic Jobs, register the feature in your Azure subscription by running the following command. Run this command once for the subscription in which you intend to provision the Elastic Job agent. Subscriptions that only contain databases that are job targets don't need to be registered.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>De Elastic Jobs-agent maken

Een Elastic Jobs-agent is een Azure-resource voor het maken, uitvoeren en beheren van taken. De agent voert taken uit op basis van een planning of als eenmalige taak.

The **New-AzSqlElasticJobAgent** cmdlet requires an Azure SQL database to already exist, so the *resourceGroupName*, *serverName*, and *databaseName* parameters must all point to existing resources.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>Create the job credentials

Jobs use database scoped credentials to connect to the target databases specified by the target group upon execution and execute scripts. Deze databasereferenties worden ook gebruikt verbinding te maken met de hoofddatabase om alle databases in een server of elastische pool te inventariseren wanneer een van deze wordt gebruikt als doelgroeplidsoort.

De databasereferenties moeten worden gemaakt in de taakdatabase. Alle doeldatabases moeten een aanmelding met voldoende machtigingen hebben om de taak te kunnen voltooien.

![Referenties voor Elastic Jobs](media/elastic-jobs-overview/job-credentials.png)

Let behalve op de inloggegevens in de afbeelding op de toevoeging van de **GRANT**-opdrachten in het volgende script. Deze machtigingen zijn vereist voor het script dat we hebben gekozen voor deze voorbeeldtaak. Because the example creates a new table in the targeted databases, each target db needs the proper permissions to successfully run.

Voer het volgende script uit om de vereiste taakreferenties (in de taakdatabase) te maken:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>Define the target databases to run the job against

Een [doelgroep](sql-database-job-automation-overview.md#target-group) definieert een verzameling van een of meer databases waarop een taakstap wordt uitgevoerd.

The following snippet creates two target groups: *serverGroup*, and *serverGroupExcludingDb2*. *serverGroup* targets all databases that exist on the server at the time of execution, and *serverGroupExcludingDb2* targets all databases on the server, except *targetDb2*:

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Create a job and steps

This example defines a job and two job steps for the job to run. Met de eerste taakstap (*stap 1*) wordt een nieuwe tabel (*Step1Table*) gemaakt in elke database in de doelgroep *ServerGroup*. The second job step (*step2*) creates a new table (*Step2Table*) in every database except for *TargetDb2*, because the target group defined previously specified to exclude it.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>De taak uitvoeren

Voer de volgende opdracht uit om de taak onmiddellijk te starten:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

Na succesvolle voltooiing ziet u twee nieuwe tabellen in TargetDb1 en slechts één nieuwe tabel in TargetDb2:

   ![verificatie van nieuwe tabellen in SSMS](media/elastic-jobs-overview/job-execution-verification.png)

You can also schedule the job to run later. Voer de volgende opdracht uit om een ​​taak te plannen die op een specifiek tijdstip moet worden uitgevoerd:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>Status van taakuitvoeringen bewaken

Met de volgende codefragmenten worden gegevens opgehaald over de taakuitvoering:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

The following table lists the possible job execution states:

|Staat|Beschrijving|
|:---|:---|
|**Created** | The job execution was just created and is not yet in progress.|
|**InProgress** | The job execution is currently in progress.|
|**WaitingForRetry** | The job execution wasn’t able to complete its action and is waiting to retry.|
|**Geslaagd** | The job execution has completed successfully.|
|**SucceededWithSkipped** | The job execution has completed successfully, but some of its children were skipped.|
|**Mislukt** | The job execution has failed and exhausted its retries.|
|**TimedOut** | The job execution has timed out.|
|**Geannuleerd** | The job execution was canceled.|
|**Overgeslagen** | The job execution was skipped because another execution of the same job step was already running on the same target.|
|**WaitingForChildJobExecutions** | The job execution is waiting for its child executions to complete.|

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de Azure-bronnen die in deze zelfstudie zijn gemaakt door de resourcegroep te verwijderen.

> [!TIP]
> If you plan to continue to work with these jobs, you do not clean up the resources created in this article.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Transact-SQL-script uitgevoerd op een reeks databases. U hebt geleerd hoe u de volgende taken moet uitvoeren:

> [!div class="checklist"]
> * Een Elastic Jobs-agent maken
> * Taakreferenties maken zodat taken scripts kunnen uitvoeren op de doelen ervan
> * De doelen (servers, elastische pools, databases, shardtoewijzingen) definiëren waarop u de taak wilt uitvoeren
> * Databasereferenties maken in de doeldatabases zodat de agent verbinding kan maken en taken uitvoeren
> * Een taak maken
> * Een taakstap toevoegen aan de taak
> * Een uitvoering van de taak starten
> * De taak bewaken

> [!div class="nextstepaction"]
> [Elastic Jobs beheren met Transact-SQL](elastic-jobs-tsql.md)
