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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74420369"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Een elastische-taakagent maken met behulp van PowerShell

[Elastisc Jobs](sql-database-job-automation-overview.md#elastic-database-jobs-preview) maken het mogelijk om één of meerdere T-SQL-scripts (Transact-SQL) parallel in veel databases uit te voeren.

In deze zelf studie leert u de stappen die nodig zijn om een query uit te voeren voor meerdere data bases:

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

De bijgewerkte versie van taak voor Elastic Database heeft een nieuwe set Power shell-cmdlets voor gebruik tijdens de migratie. Met deze nieuwe cmdlets kunt u al uw bestaande taak referenties, doelen (inclusief data bases, servers, aangepaste verzamelingen), taak triggers, taak planningen, taak inhoud en taken naar een nieuwe elastische taak agent overdragen.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>De meest recente cmdlets voor elastische taken installeren

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

Installeer de **AZ. SQL** -module om de meest recente elastische taak-cmdlets te verkrijgen. Voer de volgende opdrachten met beheerderstoegang uit in PowerShell.

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

Naast de module **AZ. SQL** is in deze zelf studie ook de *sqlserver* Power shell-module vereist. Zie [SQL Server PowerShell-module installeren](/sql/powershell/download-sql-server-ps-module) voor meer informatie.

## <a name="create-required-resources"></a>Vereiste resources maken

Voor het maken van een Elastic Jobs-agent is een database (S0 of hoger) vereist om te gebruiken als [taakdatabase](sql-database-job-automation-overview.md#job-database).

Het onderstaande script maakt een nieuwe resourcegroep, server en database voor gebruik als de taakdatabase. Met het tweede script maakt u een tweede server met twee lege data bases om taken uit te voeren.

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

## <a name="use-elastic-jobs"></a>Elastische taken gebruiken

Als u elastische taken wilt gebruiken, moet u de functie in uw Azure-abonnement registreren door de volgende opdracht uit te voeren. Voer deze opdracht eenmaal uit voor het abonnement waarin u de elastische taak agent wilt inrichten. Abonnementen die alleen bestaan uit data bases die taak doelen bevatten, hoeven niet te worden geregistreerd.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>De Elastic Jobs-agent maken

Een Elastic Jobs-agent is een Azure-resource voor het maken, uitvoeren en beheren van taken. De agent voert taken uit op basis van een planning of als eenmalige taak.

De cmdlet **New-AzSqlElasticJobAgent** vereist dat er al een Azure-SQL database bestaat. de para meters *resourceGroupName*, *servername*en *DATABASENAME* moeten allemaal verwijzen naar bestaande resources.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>De taak referenties maken

-Taken maken gebruik van data base-scoped referenties voor verbinding met de doel databases die zijn opgegeven door de doel groep bij het uitvoeren en uitvoeren van scripts. Deze databasereferenties worden ook gebruikt verbinding te maken met de hoofddatabase om alle databases in een server of elastische pool te inventariseren wanneer een van deze wordt gebruikt als doelgroeplidsoort.

De databasereferenties moeten worden gemaakt in de taakdatabase. Alle doeldatabases moeten een aanmelding met voldoende machtigingen hebben om de taak te kunnen voltooien.

![Referenties voor elastische taken](media/elastic-jobs-overview/job-credentials.png)

Let behalve op de inloggegevens in de afbeelding op de toevoeging van de **GRANT**-opdrachten in het volgende script. Deze machtigingen zijn vereist voor het script dat we hebben gekozen voor deze voorbeeldtaak. Omdat in het voor beeld een nieuwe tabel wordt gemaakt in de doel databases, moeten voor elke doel database de juiste machtigingen worden uitgevoerd.

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

### <a name="define-the-target-databases-to-run-the-job-against"></a>Definieer de doel databases om de taak uit te voeren

Een [doelgroep](sql-database-job-automation-overview.md#target-group) definieert een verzameling van een of meer databases waarop een taakstap wordt uitgevoerd.

Met het volgende code fragment worden twee doel groepen gemaakt: *serverGroup*en *serverGroupExcludingDb2*. *serverGroup* streeft naar alle data bases die op de server aanwezig zijn op het moment dat ze worden uitgevoerd en *serverGroupExcludingDb2* streeft naar alle data bases op de server, met uitzonde ring van *targetDb2*:

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

### <a name="create-a-job-and-steps"></a>Een taak en stappen maken

In dit voor beeld worden een taak en twee taak stappen gedefinieerd om de taak uit te voeren. Met de eerste taakstap (*stap 1*) wordt een nieuwe tabel (*Step1Table*) gemaakt in elke database in de doelgroep *ServerGroup*. Met de tweede taakstap (*stap2*) wordt een nieuwe tabel (*Step2Table*) gemaakt in elke database behalve *TargetDb2*, omdat in de doelgroep die eerder is gedefinieerd is opgegeven dat deze moet worden uitgesloten.

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

U kunt ook plannen dat de taak later wordt uitgevoerd. Voer de volgende opdracht uit om een ​​taak te plannen die op een specifiek tijdstip moet worden uitgevoerd:

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

De volgende tabel bevat de mogelijke statussen voor taak uitvoering:

|Status|Beschrijving|
|:---|:---|
|**Gemaakt** | De taak uitvoering is zojuist gemaakt en wordt nog niet uitgevoerd.|
|**InProgress** | De taak wordt momenteel uitgevoerd.|
|**WaitingForRetry** | De uitvoering van de taak is niet voltooid en er wordt gewacht op het opnieuw proberen.|
|**Geslaagd** | De taak uitvoering is voltooid.|
|**SucceededWithSkipped** | De taak uitvoering is voltooid, maar sommige onderliggende items zijn overgeslagen.|
|**Mislukt** | De taak uitvoering is mislukt en de pogingen zijn uitgeput.|
|**Out** | Er is een time-out opgetreden voor de taak uitvoering.|
|**Geannuleerd** | De taak uitvoering is geannuleerd.|
|**Overgeslagen** | De taak uitvoering is overgeslagen omdat een andere uitvoering van dezelfde taak stap al werd uitgevoerd op hetzelfde doel.|
|**WaitingForChildJobExecutions** | De uitvoering van de taak wacht op het volt ooien van de onderliggende uitvoeringen.|

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de Azure-bronnen die in deze zelfstudie zijn gemaakt door de resourcegroep te verwijderen.

> [!TIP]
> Als u van plan bent om verder te gaan met deze taken, kunt u de resources die in dit artikel zijn gemaakt, niet opschonen.

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
