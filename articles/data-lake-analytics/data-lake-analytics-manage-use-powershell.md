---
title: Azure Data Lake Analytics beheren met Azure PowerShell
description: In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken voor het beheren van Data Lake Analytics-accounts, gegevensbronnen, gebruikers & taken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66156421"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure Data Lake Analytics beheren met Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics-accounts, gegevensbronnen, gebruikers en taken beheert met Azure PowerShell.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u PowerShell wilt gebruiken met Data Lake Analytics, verzamelt u de volgende gegevens: 

* **Abonnements-id**: de id van het Azure-abonnement dat uw Data Lake Analytics-account bevat.
* **Resourcegroep:** de naam van de Azure-brongroep die uw Data Lake Analytics-account bevat.
* **Data Lake Analytics-accountnaam:** de naam van uw Data Lake Analytics-account.
* **Standaard gegevens Lake Store-accountnaam**: elk Data Lake Analytics-account heeft een standaard Data Lake Store-account.
* **Locatie:** de locatie van uw Data Lake Analytics-account, zoals 'East US 2' of andere ondersteunde locaties.

De PowerShell-fragmenten in deze zelfstudie gebruiken deze variabelen om deze informatie op te slaan

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

### <a name="log-in-using-interactive-user-authentication"></a>Inloggen met interactieve gebruikersverificatie

Inloggen met een abonnements-id of op abonnementsnaam

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Verificatiecontext opslaan

De `Connect-AzAccount` cmdlet vraagt altijd om referenties. U voorkomen dat u wordt gevraagd door de volgende cmdlets te gebruiken:

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Inloggen met een Service Principal Identity (SPI)

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Accounts beheren


### <a name="list-accounts"></a>Lijst van accounts

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Een account maken

Elke Data Lake Analytics-account vereist een Data Lake Store-standaardaccount dat wordt gebruikt voor het opslaan van logboeken. U een bestaand account opnieuw gebruiken of een account aanmaken. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Accountgegevens opvragen

Meer informatie over een account.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Controleren of er een account bestaat

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Gegevensbronnen beheren
Azure Data Lake Analytics ondersteunt momenteel de volgende gegevensbronnen:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Elk Data Lake Analytics-account heeft een standaard Data Lake Store-account. Het standaard Data Lake Store-account wordt gebruikt om metagegevens en taakcontrolelogboeken op te slaan. 

### <a name="find-the-default-data-lake-store-account"></a>Het standaard GegevensLake Store-account zoeken

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

U het standaard Data Lake Store-account vinden door `IsDefault` de lijst met gegevensbronnen door de eigenschap te filteren:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Een gegevensbron toevoegen

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Gegevensbronnen weergeven

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL-taken verzenden

### <a name="submit-a-string-as-a-u-sql-job"></a>Een tekenreeks indienen als U-SQL-taak

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>Een bestand verzenden als een U-SQL-taak

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Taken weergeven

De uitvoer bevat de taken die momenteel worden uitgevoerd en de taken die onlangs zijn voltooid.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>De topN-taken weergeven

Standaard wordt de lijst met taken gesorteerd op verzendtijd. Dus de meest recent ingediende vacatures verschijnen eerst. Standaard onthoudt het ADLA-account taken gedurende 180 dagen, maar de Get-AdlJob-cmdlet retourneert standaard alleen de eerste 500. Gebruik de parameter Top om een specifiek aantal taken weer te geven.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Taken op taakstatus weergeven

Met `-State` behulp van de parameter. U een van deze waarden combineren:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>Taken op functieresultaat weergeven

Gebruik `-Result` de parameter om te detecteren of voltooide taken zijn voltooid. Het heeft deze waarden:

* Geannuleerd
* Mislukt
* Geen
* Geslaagd

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Taken aanbieden op vacatureindiener

Met `-Submitter` de parameter u bepalen wie een taak heeft ingediend.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Taken aanbieden op indieningstijd

Het `-SubmittedAfter` is handig bij het filteren naar een tijdsbereik.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>De status van een taak krijgen

Haal de status van een bepaalde taak op.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Een taak annuleren

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Wachten tot een taak is voltooid

In plaats `Get-AdlAnalyticsJob` van te herhalen tot een `Wait-AdlJob` taak is voltooid, u de cmdlet gebruiken om te wachten tot de taak is beëindigd.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Functiegeschiedenis analyseren

Het gebruik van Azure PowerShell om de geschiedenis van taken te analyseren die zijn uitgevoerd in Data Lake-analyses is een krachtige techniek. U het gebruiken om inzicht te krijgen in gebruik en kosten. U meer informatie vinden door te kijken naar de [Job History Analysis steekproef repo](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Taakpijplijnen en herhalingen weergeven

Gebruik `Get-AdlJobPipeline` de cmdlet om de eerder ingediende pijplijngegevens te bekijken.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Gebruik `Get-AdlJobRecurrence` de cmdlet om de herhalingsgegevens voor eerder ingediende taken te bekijken.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Rekenbeleid beheren

### <a name="list-existing-compute-policies"></a>Bestaande rekenbeleid weergeven

De `Get-AdlAnalyticsComputePolicy` cmdlet haalt informatie op over het rekenbeleid voor een Data Lake Analytics-account.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Een rekenbeleid maken

De `New-AdlAnalyticsComputePolicy` cmdlet maakt een nieuw rekenbeleid voor een Data Lake Analytics-account. In dit voorbeeld worden de maximale AU's die beschikbaar zijn voor de opgegeven gebruiker ingesteld op 50 en de minimale taakprioriteit op 250.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Bestanden beheren

### <a name="check-for-the-existence-of-a-file"></a>Controleer op de aanwezigheid van een bestand.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Uploaden en downloaden

Een bestand uploaden.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Upload een hele map recursief.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Download een bestand.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Download een hele map recursief.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Als het upload- of downloadproces wordt onderbroken, u proberen het ``-Resume`` proces te hervatten door de cmdlet opnieuw uit te voeren met de vlag.

## <a name="manage-the-u-sql-catalog"></a>De U-SQL-catalogus beheren

De U-SQL-catalogus wordt gebruikt om gegevens en code te structureren, zodat ze kunnen worden gedeeld door U-SQL-scripts. De catalogus biedt de hoogst mogelijke prestaties met gegevens in Azure Data Lake. Zie [U-SQL-catalogus gebruiken](data-lake-analytics-use-u-sql-catalog.md) voor meer informatie.

### <a name="list-items-in-the-u-sql-catalog"></a>Items in de U-SQL-catalogus aanbieden

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>Alle samenstellingen van de U-SQL-catalogus weergeven

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Meer informatie over een catalogusitem

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Referenties opslaan in de catalogus

Maak in een U-SQL-database een referentieobject voor een database die wordt gehost in Azure. Momenteel zijn U-SQL-referenties het enige type catalogusitem dat u maken via PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="manage-firewall-rules"></a>Firewallregels beheren

### <a name="list-firewall-rules"></a>Firewallregels weergeven

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Een firewallregel toevoegen

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Een firewallregel wijzigen

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Een firewallregel verwijderen

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Azure-IP-adressen toestaan

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Werken met Azure

### <a name="get-error-details"></a>Foutgegevens opvragen

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Controleren of u als administrator op uw Windows-machine werkt

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Een tenantid zoeken

Van een abonnementsnaam:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Vanaf een abonnements-ID:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Vanaf een domeinadres zoals 'contoso.com'

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Al uw abonnementen en tenant---geïdentificeerde gegevens weergeven

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Een Data Lake Analytics-account maken met behulp van een sjabloon

U ook een sjabloon voor Azure Resource Group gebruiken met het volgende voorbeeld: [Een Data Lake Analytics-account maken met een sjabloon](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Aan de slag met Data Lake Analytics met de [Azure-portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Azure CLI](data-lake-analytics-get-started-cli.md)
* Azure Data Lake Analytics beheren met [Azure-portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
