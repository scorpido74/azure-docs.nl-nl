---
title: Log Analytics maken & configureren met Power shell
description: Log Analytics werk ruimten in Azure Monitor gegevens opslaan van servers in uw on-premises of Cloud infrastructuur. U kunt computer gegevens uit Azure Storage verzamelen wanneer deze worden gegenereerd door Azure Diagnostics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80054698"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Log Analytics werk ruimte beheren in Azure Monitor met behulp van Power shell

U kunt de [log Analytics Power shell-cmdlets](https://docs.microsoft.com/powershell/module/az.operationalinsights/) gebruiken om verschillende functies uit te voeren op een log Analytics werk ruimte in azure monitor vanaf een opdracht regel of als onderdeel van een script.  Voor beelden van de taken die u kunt uitvoeren met Power shell zijn:

* Een werkruimte maken
* Een oplossing toevoegen of verwijderen
* Opgeslagen Zoek opdrachten importeren en exporteren
* Een computer groep maken
* Verzamelen van IIS-logboeken inschakelen op computers waarop de Windows-agent is geïnstalleerd
* Prestatie meter items verzamelen van Linux-en Windows-computers
* Gebeurtenissen verzamelen van syslog op Linux-computers
* Gebeurtenissen verzamelen van Windows-gebeurtenis logboeken
* Aangepaste gebeurtenis logboeken verzamelen
* De log Analytics-agent toevoegen aan een virtuele machine van Azure
* Log Analytics configureren voor het indexeren van gegevens die zijn verzameld met Azure Diagnostics

Dit artikel bevat twee code voorbeelden die een aantal functies illustreren die u vanuit Power shell kunt uitvoeren.  U kunt verwijzen naar de [log Analytics Power shell-cmdlet-verwijzing](https://docs.microsoft.com/powershell/module/az.operationalinsights/) voor andere functies.

> [!NOTE]
> Log Analytics eerder Operational Insights is aangeroepen. Dit is de reden waarom het de naam is die wordt gebruikt in de cmdlets.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Deze voor beelden werken met versie 1.0.0 of hoger van de module AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte maken en configureren
In het volgende voorbeeld script ziet u hoe u:

1. Een werkruimte maken
2. De beschik bare oplossingen weer geven
3. Oplossingen toevoegen aan de werk ruimte
4. Opgeslagen Zoek opdrachten importeren
5. Opgeslagen Zoek opdrachten exporteren
6. Een computer groep maken
7. Verzamelen van IIS-logboeken inschakelen op computers waarop de Windows-agent is geïnstalleerd
8. Prestatie meter items voor logische schijven verzamelen van Linux-computers (% used inodes; Beschik bare mega bytes; Percentage gebruikte ruimte; Schijf overdrachten per seconde; Schijf lezen per seconde; Schrijf bewerkingen per seconde)
9. Syslog-gebeurtenissen verzamelen van Linux-computers
10. Fout-en waarschuwings gebeurtenissen verzamelen uit het toepassings gebeurtenis logboek van Windows-computers
11. Het prestatie meter item geheugen beschik bare Mbytes verzamelen van Windows-computers
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
> De notatie voor de **CustomLogRawJson** -para meter waarmee de configuratie voor een aangepast logboek wordt gedefinieerd, kan ingewikkeld zijn. Gebruik [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) om de configuratie van een bestaand aangepast logboek op te halen. De eigenschap **Properties** is de configuratie die vereist is voor de para meter **CustomLogRawJson** .

In het bovenstaande voor beeld is regexDelimiter gedefinieerd als\\' n ' voor een nieuwe regel. Het logboek scheidings teken kan ook een tijds tempel zijn.  Dit zijn de ondersteunde indelingen:

| Indeling | JSON RegEx-indeling gebruikt \\ twee voor elke \ in een standaard regex, dus als de test in een \\ regex-app verkort tot \ | | |
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
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> waar + + of a- <br> zzzz tijd verschuiving | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> De T is een letterlijke letter T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Log Analytics configureren voor het verzenden van Azure Diagnostics
Voor bewaking zonder agents van Azure-resources moeten de resources Azure Diagnostics hebben ingeschakeld en geconfigureerd om te schrijven naar een Log Analytics-werk ruimte. Deze benadering verzendt gegevens rechtstreeks naar de werk ruimte en vereist niet dat gegevens naar een opslag account worden geschreven. Ondersteunde bronnen zijn:

| Resourcetype | Logboeken | Metrische gegevens |
| --- | --- | --- |
| Toepassingsgateways    | Ja | Ja |
| Automation-accounts     | Ja | |
| Batch-accounts          | Ja | Ja |
| Data Lake Analytics     | Ja | |
| Data Lake Store         | Ja | |
| Elastische SQL-pool        |     | Ja |
| Event hub-naamruimte     |     | Ja |
| IoT Hubs                |     | Ja |
| Key Vault               | Ja | |
| Load balancers          | Ja | |
| Logic Apps              | Ja | Ja |
| Netwerkbeveiligingsgroepen | Ja | |
| Azure Cache voor Redis             |     | Ja |
| Services zoeken         | Ja | Ja |
| Service Bus naam ruimte   |     | Ja |
| SQL (V12)               |     | Ja |
| Websites               |     | Ja |
| Webserver farms        |     | Ja |

Raadpleeg voor meer informatie over de beschik bare metrische gegevens de [ondersteunde metrische gegevens met Azure monitor](../../azure-monitor/platform/metrics-supported.md).

Raadpleeg voor meer informatie over de beschik bare logboeken [ondersteunde services en schema's voor bron logboeken](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

U kunt ook de voor gaande cmdlet gebruiken om logboeken te verzamelen van bronnen die zich in verschillende abonnementen bevinden. De cmdlet kan worden gebruikt voor verschillende abonnementen, omdat u de ID opgeeft van zowel de resource waarmee logboeken worden gemaakt als de werk ruimte waarnaar de logboeken worden verzonden.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Log Analytics werkruimte configureren voor het verzamelen van Azure-diagnose van opslag
Als u logboek gegevens wilt verzamelen vanuit een actief exemplaar van een klassieke Cloud service of een service Fabric-cluster, moet u eerst de gegevens naar Azure Storage schrijven. Er wordt dan een Log Analytics-werk ruimte geconfigureerd voor het verzamelen van de logboeken van het opslag account. Ondersteunde bronnen zijn:

* Klassieke Cloud Services (web-en werk rollen)
* Service Fabric-clusters

In het volgende voor beeld ziet u hoe u:

1. Geef een lijst van de bestaande opslag accounts en locaties waarvan de werk ruimte index gegevens bevat
2. Een configuratie maken om te lezen van een opslag account
3. De zojuist gemaakte configuratie bijwerken om gegevens te indexeren van extra locaties
4. De zojuist gemaakte configuratie verwijderen

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

U kunt ook het voor gaande script gebruiken voor het verzamelen van logboeken van opslag accounts in verschillende abonnementen. Het script kan worden gebruikt voor verschillende abonnementen sinds u de resource-ID van het opslag account en een bijbehorende toegangs sleutel opgeeft. Wanneer u de toegangs sleutel wijzigt, moet u de opslag inzicht bijwerken zodat deze de nieuwe sleutel heeft.


## <a name="next-steps"></a>Volgende stappen
* [Raadpleeg log Analytics Power shell-cmdlets](https://docs.microsoft.com/powershell/module/az.operationalinsights/) voor meer informatie over het gebruik van Power shell voor configuratie van log Analytics.

