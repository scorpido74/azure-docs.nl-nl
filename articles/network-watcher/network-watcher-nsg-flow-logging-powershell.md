---
title: NSG-stroomlogboeken beheren - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe logboeken van netwerkbeveiliginggroepsstroom kunnen worden beheren in Azure Network Watcher met PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 9612afdb63c6988c0027f003caeacd456b5e50e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840941"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Netwerkbeveiligingsgroepstroomlogboeken configureren met PowerShell

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Netwerkbeveiligingsgroepstroomlogboeken zijn een functie van Network Watcher waarmee u informatie over binnendringend en uitgangs-IP-verkeer bekijken via een netwerkbeveiligingsgroep. Deze stroomlogboeken worden geschreven in json-indeling en tonen uitgaande en binnenkomende stromen op basis van een regel, de NIC waarop de stroom van toepassing is, 5-tuple-informatie over de stroom (Bron/Bestemming-IP, Bron/Bestemmingspoort, Protocol) en als het verkeer is toegestaan of geweigerd.

## <a name="register-insights-provider"></a>Insights-provider registreren

Om de logboekregistratie van de stroom succesvol te laten werken, moet de **Microsoft.Insights-provider** worden geregistreerd. Als u niet zeker weet of de **Microsoft.Insights-provider** is geregistreerd, voert u het volgende script uit.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Netwerkbeveiligingsgroepstroomlogboeken en Traffic Analytics inschakelen

De opdracht om stroomlogboeken in te schakelen wordt weergegeven in het volgende voorbeeld:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Het opslagaccount dat u opgeeft, kan geen netwerkregels configureren die de netwerktoegang beperken tot alleen Microsoft-services of specifieke virtuele netwerken. Het opslagaccount kan zich in hetzelfde of een ander Azure-abonnement bevinden dan het NSG waarvoor u het stroomlogboek inschakelt. Als u verschillende abonnementen gebruikt, moeten deze beide zijn gekoppeld aan dezelfde Azure Active Directory-tenant. Het account dat u voor elk abonnement gebruikt, moet over de [vereiste machtigingen](required-rbac-permissions.md)beschikken.

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Logboeken van traffic-analyses en netwerkbeveiligingsgroepstroom uitschakelen

Gebruik het volgende voorbeeld om verkeersanalyses en stroomlogboeken uit te schakelen:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Een stroomlogboek downloaden

De opslaglocatie van een stroomlogboek wordt gedefinieerd bij het maken. Een handig hulpmiddel om toegang te krijgen tot deze stroomlogboeken die zijn opgeslagen in een opslagaccount, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload:https://storageexplorer.com/

Als een opslagaccount is opgegeven, worden stroomlogboekbestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Voor informatie over de structuur van het logboek bezoek [Network Security Group Flow Overzicht](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met open source-hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
