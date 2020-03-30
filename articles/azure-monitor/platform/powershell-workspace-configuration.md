---
title: Logboekanalyses & configureren met PowerShell
description: Log Analytics-werkruimten in Azure Monitor slaan gegevens op van servers in uw on-premises of cloudinfrastructuur. U machinegegevens uit Azure-opslag verzamelen wanneer u wordt gegenereerd door Azure-diagnose.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054698"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Logboekanalysewerkruimte beheren in Azure Monitor met PowerShell

U de [PowerShell-cmdlets log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) gebruiken om verschillende functies uit te voeren op een Log Analytics-werkruimte in Azure Monitor vanuit een opdrachtregel of als onderdeel van een script.  Voorbeelden van de taken die u met PowerShell uitvoeren, zijn:

* Een werkruimte maken
* Een oplossing toevoegen of verwijderen
* Opgeslagen zoekopdrachten importeren en exporteren
* Een computergroep maken
* Verzameling van IIS-logboeken inschakelen vanaf computers waarop de Windows-agent is geïnstalleerd
* Prestatiemeteritems verzamelen van Linux- en Windows-computers
* Gebeurtenissen verzamelen van syslog op Linux-computers
* Gebeurtenissen verzamelen uit Logboeken van Windows-gebeurtenissen
* Aangepaste gebeurtenislogboeken verzamelen
* De logboekanalyseagent toevoegen aan een virtuele Azure-machine
* Logboekanalyses configureren om gegevens te indexeren die zijn verzameld met Azure-diagnose

In dit artikel worden twee codevoorbeelden gegeven die een aantal functies illustreren die u vanuit PowerShell uitvoeren.  U verwijzen naar de [cmdlet-verwijzing log analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) voor andere functies.

> [!NOTE]
> Log Analytics heette voorheen Operational Insights, daarom is het de naam die in de cmdlets wordt gebruikt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Deze voorbeelden werken met versie 1.0.0 of hoger van de az.operationalinsights module.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Een logboekanalysewerkruimte maken en configureren
In het volgende scriptvoorbeeld wordt uitgelegd hoe u:

1. Een werkruimte maken
2. De beschikbare oplossingen weergeven
3. Oplossingen toevoegen aan de werkruimte
4. Opgeslagen zoekopdrachten importeren
5. Opgeslagen zoekopdrachten exporteren
6. Een computergroep maken
7. Verzameling van IIS-logboeken inschakelen vanaf computers waarop de Windows-agent is geïnstalleerd
8. Logische schijfperftellers verzamelen van Linux-computers (% Gebruikte Inodes; Gratis Megabytes; % gebruikte ruimte; Schijfoverdrachten per seconde; Schijfleest/sec; Schijfschrijft/sec)
9. Syslog-gebeurtenissen verzamelen vanaf Linux-computers
10. Fout- en waarschuwingsgebeurtenissen verzamelen in het logboek van toepassingsgebeurtenis van Windows-computers
11. Beschikbare Mbytes-prestatiemeter items verzamelen vanaf Windows-computers
12. Een aangepast logboek verzamelen

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> De indeling voor de parameter **CustomLogRawJson** die de configuratie voor een aangepast logboek definieert, kan complex zijn. Gebruik [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) om de configuratie voor een bestaand aangepast logboek op te halen. De eigenschap **Eigenschappen** is de configuratie die nodig is voor de parameter **CustomLogRawJson.**

In het bovenstaande voorbeeld regexDelimiter\\werd gedefinieerd als " n" voor newline. De logscheidingsscheiding kan ook een tijdstempel zijn.  Dit zijn de ondersteunde indelingen:

| Indeling | Json RegEx formaat \\ maakt gebruik van twee voor elke \ in \\ een standaard RegEx dus als het testen in een RegEx app te verminderen tot \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> twee spaties na MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> waar + + of a - <br> waar zzzz tijd offset | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> De T is een letterlijke letter T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Logboekanalyse configureren om Azure-diagnose te verzenden
Voor agentless monitoring van Azure-resources moeten de resources Azure-diagnose hebben ingeschakeld en geconfigureerd om naar een Log Analytics-werkruimte te schrijven. Deze aanpak stuurt gegevens rechtstreeks naar de werkruimte en vereist niet dat gegevens naar een opslagaccount worden geschreven. Ondersteunde bronnen zijn onder andere:

| Resourcetype | Logboeken | Metrische gegevens |
| --- | --- | --- |
| Toepassingsgateways    | Ja | Ja |
| Automation-accounts     | Ja | |
| Batch-accounts          | Ja | Ja |
| Data Lake-analyses     | Ja | |
| Data Lake-winkel         | Ja | |
| Elastische SQL-pool        |     | Ja |
| Event hub-naamruimte     |     | Ja |
| IoT Hubs                |     | Ja |
| Key Vault               | Ja | |
| Load balancers          | Ja | |
| Logic Apps              | Ja | Ja |
| Netwerkbeveiligingsgroepen | Ja | |
| Azure Cache voor Redis             |     | Ja |
| Zoekservices         | Ja | Ja |
| Naamruimte servicebus   |     | Ja |
| SQL (v12)               |     | Ja |
| Websites               |     | Ja |
| Webserverfarms        |     | Ja |

Raadpleeg voor de details van de beschikbare statistieken [ondersteunde statistieken met Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Raadpleeg voor de details van de beschikbare logboeken [ondersteunde services en schema voor bronlogboeken](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

U de vorige cmdlet ook gebruiken om logboeken te verzamelen van bronnen die zich in verschillende abonnementen bevinden. De cmdlet kan werken tussen abonnementen, omdat u de id van zowel de bronlogboeken als de werkruimte waarnaar de logboeken worden verzonden, verstrekt.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Logboekanalysewerkruimte configureren om Azure-diagnoses uit de opslag te verzamelen
Als u logboekgegevens wilt verzamelen vanuit een lopende instantie van een klassieke cloudservice of een cluster van servicefabric, moet u de gegevens eerst naar Azure-opslag schrijven. Een werkruimte loganalytics wordt vervolgens geconfigureerd om de logboeken van het opslagaccount te verzamelen. Ondersteunde bronnen zijn onder andere:

* Klassieke cloudservices (web- en werknemersrollen)
* Clusters van servicefabric

In het volgende voorbeeld ziet u hoe u:

1. De bestaande opslagaccounts en -locaties weergeven waarvan de werkruimte gegevens indexeert
2. Een configuratie maken om te lezen vanuit een opslagaccount
3. De nieuw gemaakte configuratie bijwerken om gegevens van extra locaties te indexeren
4. De nieuw gemaakte configuratie verwijderen

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

U het vorige script ook gebruiken om logboeken van opslagaccounts in verschillende abonnementen te verzamelen. Het script kan werken tussen abonnementen, omdat u de bron-id voor opslagaccount en een bijbehorende toegangssleutel verstrekt. Wanneer u de toegangssleutel wijzigt, moet u het opslaginzicht bijwerken om de nieuwe sleutel te hebben.


## <a name="next-steps"></a>Volgende stappen
* [Bekijk de PowerShell-cmdlets van Log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) voor meer informatie over het gebruik van PowerShell voor de configuratie van Log Analytics.

