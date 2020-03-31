---
title: NSG-stroomlogboeken beheren - Azure REST API
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u stroomlogboeken van netwerkbeveiliginggroepen beheert in Azure Network Watcher met REST API
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
ms.openlocfilehash: 7cc47414dc985f6fc2fff3c57d809f307b142e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840924"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Stroomlogboeken voor netwerkbeveiliginggroep configureren met rest-API

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Netwerkbeveiligingsgroepstroomlogboeken zijn een functie van Network Watcher waarmee u informatie over binnendringend en uitgangs-IP-verkeer bekijken via een netwerkbeveiligingsgroep. Deze stroomlogboeken worden geschreven in json-indeling en tonen uitgaande en binnenkomende stromen op basis van een regel, de NIC waarop de stroom van toepassing is, 5-tuple-informatie over de stroom (Bron/Bestemming-IP, Bron/Bestemmingspoort, Protocol) en als het verkeer is toegestaan of geweigerd.

## <a name="before-you-begin"></a>Voordat u begint

ARMclient wordt gebruikt om de REST API aan te roepen met PowerShell. ARMClient is te vinden op chocolatey bij [ARMClient op Chocolatey](https://chocolatey.org/packages/ARMClient)

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken.

> [!Important]
> Voor Network Watcher REST API roept de naam van de brongroep in de aanvraag URI is de resourcegroep die de Network Watcher bevat, niet de resources waarop u de diagnostische acties uitvoert.

## <a name="scenario"></a>Scenario

In het scenario in dit artikel ziet u hoe u logboeken voor het inschakelen, uitschakelen en queryquery's in- en uitschakelen met behulp van de REST-API. Ga voor meer informatie over stroomlogboekregistraties van netwerkbeveiligingsgroepen naar [Netwerkbeveiligingsgroep stroomlogboekregistratie - Overzicht](network-watcher-nsg-flow-logging-overview.md).

In dit scenario zult u:

* Stroomlogboeken inschakelen (versie 2)
* Stroomlogboeken uitschakelen
* Status van querystroomlogboeken

## <a name="log-in-with-armclient"></a>Inloggen met ARMClient

Meld u aan bij de client met uw Azure-referenties.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights-provider registreren

Om de logboekregistratie van de stroom succesvol te laten werken, moet de **Microsoft.Insights-provider** worden geregistreerd. Als u niet zeker weet of de **Microsoft.Insights-provider** is geregistreerd, voert u het volgende script uit.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Stroomlogboeken voor netwerkbeveiliginggroeps inschakelen

De opdracht om stroomlogboeken versie 2 in te schakelen wordt in het volgende voorbeeld weergegeven. Vervang voor versie 1 het veld 'versie' door '1':

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Het antwoord dat wordt geretourneerd uit het voorgaande voorbeeld is als volgt:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Stroomlogboeken voor netwerkbeveiliginguitschakelen

Gebruik het volgende voorbeeld om stroomlogboeken uit te schakelen. De aanroep is hetzelfde als het inschakelen van stroomlogboeken, behalve **dat false** is ingesteld voor de ingeschakelde eigenschap.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Het antwoord dat wordt geretourneerd uit het voorgaande voorbeeld is als volgt:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Querystroomlogboeken

Met de volgende REST-aanroep wordt de status van stroomlogboeken op een netwerkbeveiligingsgroep opgevraagd.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Het volgende is een voorbeeld van het geretourneerde antwoord:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Een stroomlogboek downloaden

De opslaglocatie van een stroomlogboek wordt gedefinieerd bij het maken. Een handig hulpmiddel om toegang te krijgen tot deze stroomlogboeken die zijn opgeslagen in een opslagaccount, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload:https://storageexplorer.com/

Als een opslagaccount is opgegeven, worden pakketopnamebestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met open source-hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
