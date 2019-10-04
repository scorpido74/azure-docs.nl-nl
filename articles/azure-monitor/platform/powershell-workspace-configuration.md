---
title: PowerShell gebruiken om te maken en configureren van een Log Analytics-werkruimte | Microsoft Docs
description: Log Analytics werk ruimten in Azure Monitor gegevens opslaan van servers in uw on-premises of Cloud infrastructuur. U kunt gegevens van de machine verzamelen uit Azure storage wanneer die worden gegenereerd door Azure diagnostics.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 16cad34290ecc518e95ec1a0ce0950722cfe0780
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836145"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Log Analytics werk ruimte beheren in Azure Monitor met behulp van Power shell

U kunt de [log Analytics Power shell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) -cmdlets gebruiken om verschillende functies uit te voeren op een log Analytics werk ruimte in azure monitor vanaf een opdracht regel of als onderdeel van een script.  Voorbeelden van de taken die u met PowerShell uitvoeren kunt zijn:

* Een werkruimte maken
* Toevoegen of verwijderen van een oplossing
* Importeren en exporteren van opgeslagen zoekopdrachten
* Een computergroep maken
* Verzamelen van IIS-logboeken van computers met de Windows-agent is geïnstalleerd
* Verzamelen van prestatiemeteritems van Linux en Windows-computers
* Gebeurtenissen verzamelen van syslog op Linux-computers
* Gebeurtenissen verzamelen van Windows-gebeurtenislogboeken
* Aangepaste logboeken verzamelen
* De log analytics-agent toevoegen aan een virtuele machine van Azure
* Log analytics om gegevens te indexeren die zijn verzameld met behulp van Azure diagnostics configureren

In dit artikel bevat twee codevoorbeelden van die illustratie van enkele van de functies die u vanuit PowerShell uitvoeren kunt.  U kunt verwijzen naar de [Log Analytics PowerShell-cmdlet-verwijzing](https://docs.microsoft.com/powershell/module/az.operationalinsights/) voor andere functies.

> [!NOTE]
> Log Analytics werd voorheen Operational Insights, die daarom is het de naam die wordt gebruikt in de cmdlets genoemd.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Deze voor beelden werken met versie 1.0.0 of hoger van de module AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Maken en configureren van een Log Analytics-werkruimte
Het volgende voorbeeldscript laat zien hoe u:

1. Een werkruimte maken
2. Lijst van de beschikbare oplossingen
3. Oplossingen toevoegen aan de werkruimte
4. Importeren opgeslagen zoekopdrachten
5. Export opgeslagen zoekopdrachten
6. Een computergroep maken
7. Verzamelen van IIS-logboeken van computers met de Windows-agent is geïnstalleerd
8. Verzamelen van prestatiemeteritems voor logische schijf van Linux-computers (% gebruikte Inodes Beschikbare Megabytes; Percentage gebruikte ruimte; Schijfoverdrachten per seconde; Schijf lezen per seconde; Schijf schrijven per seconde)
9. Syslog-gebeurtenissen verzamelen van Linux-computers
10. Fout- en waarschuwingsberichten gebeurtenissen verzamelen van het logboek voor toepassingsgebeurtenissen van Windows-computers
11. Beschikbaar geheugen in megabytes-prestatiemeteritem verzamelen van Windows-computers
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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
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
In het bovenstaande voor beeld is regexDelimiter gedefinieerd als\\' n ' voor een nieuwe regel. Het logboek scheidings teken kan ook een tijds tempel zijn.  Dit zijn de ondersteunde indelingen:

| Indeling | JSON regex-indeling gebruikt \\ twee voor elke \ in een standaard regex, dus als de test in een \\ regex-app verkort tot \ | | |
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
Voor bewaking zonder agent van Azure-resources, moeten de resources hebben van Azure diagnostics ingeschakeld en geconfigureerd voor het schrijven naar Log Analytics-werkruimte. Deze benadering verzendt gegevens rechtstreeks naar de werk ruimte en vereist niet dat gegevens naar een opslag account worden geschreven. Ondersteunde resources zijn onder andere:

| Resourcetype | Logboeken | Metrische gegevens |
| --- | --- | --- |
| Toepassingsgateways    | Ja | Ja |
| Automation-accounts     | Ja | |
| Batch-accounts          | Ja | Ja |
| Data Lake analytics     | Ja | |
| Data Lake store         | Ja | |
| Elastische SQL-groep        |     | Ja |
| Event hub-naamruimte     |     | Ja |
| IoT Hubs                |     | Ja |
| Key Vault               | Ja | |
| Load balancers          | Ja | |
| Logic Apps              | Ja | Ja |
| Netwerkbeveiligingsgroepen | Ja | |
| Azure Cache voor Redis             |     | Ja |
| Services zoeken         | Ja | Ja |
| Service Bus-naamruimte   |     | Ja |
| SQL (v12)               |     | Ja |
| Web Sites               |     | Ja |
| Web Server-farms        |     | Ja |

Raadpleeg voor de details van de beschikbare metrische gegevens, [ondersteunde metrische gegevens met Azure Monitor](../../azure-monitor/platform/metrics-supported.md).

Raadpleeg voor de details van de beschikbare logboeken, [ondersteunde services en -schema voor diagnostische logboeken](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

U kunt ook de voorgaande cmdlet gebruiken om Logboeken te verzamelen uit resources die zich in verschillende abonnementen. De cmdlet kan worden gebruikt voor verschillende abonnementen, omdat u de ID opgeeft van zowel de resource waarmee logboeken worden gemaakt als de werk ruimte waarnaar de logboeken worden verzonden.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Log Analytics werkruimte configureren voor het verzamelen van Azure-diagnose van opslag
Voor het verzamelen van logboekgegevens van binnen een actief exemplaar van een klassieke cloudservice of een service fabric-cluster, moet u eerst de gegevens schrijven naar Azure storage. Er wordt dan een Log Analytics-werk ruimte geconfigureerd voor het verzamelen van de logboeken van het opslag account. Ondersteunde resources zijn onder andere:

* Klassieke cloudservices (web- en werkrollen rollen)
* Service fabric-clusters

Het volgende voorbeeld wordt getoond hoe u:

1. Geef een lijst van de bestaande opslag accounts en locaties waarvan de werk ruimte index gegevens bevat
2. Een configuratie lezen van een storage-account maken
3. Werk de zojuist gemaakte configuratie om gegevens te indexeren vanaf meer locaties
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

U kunt dit script ook gebruiken voor het verzamelen van Logboeken van de storage-accounts in verschillende abonnementen. Het script kan worden gebruikt voor verschillende abonnementen sinds u de resource-ID van het opslag account en een bijbehorende toegangs sleutel opgeeft. Wanneer u de toegangssleutel wijzigt, moet u het opslaginzicht als u de nieuwe sleutel wilt bijwerken.


## <a name="next-steps"></a>Volgende stappen
* [Controleer de Log Analytics PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.operationalinsights/) voor meer informatie over het gebruik van PowerShell voor de configuratie van Log Analytics.

