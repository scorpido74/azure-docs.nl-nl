---
title: NSG-stroom logboeken beheren-Azure REST API
titleSuffix: Azure Network Watcher
description: Deze pagina bevat informatie over het beheren van stroom logboeken voor netwerk beveiligings groepen in azure Network Watcher met REST API
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 636a422cd46dc6b6274766b92753f04195a829a0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277937"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Stroom logboeken voor netwerk beveiligings groepen configureren met REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Stroom logboeken van netwerk beveiligings groepen zijn een functie van Network Watcher waarmee u informatie kunt bekijken over binnenkomend en IP-verkeer via een netwerk beveiligings groep. Deze stroom logboeken worden geschreven in JSON-indeling en uitgaande en inkomende stromen per regel weer gegeven, de NIC waarop de stroom van toepassing is, 5-tuple informatie over de stroom (bron/doel-IP, bron/doel poort, Protocol) en of het verkeer is toegestaan of geweigerd.

## <a name="before-you-begin"></a>Voordat u begint

ARMclient wordt gebruikt om de REST API aan te roepen met behulp van Power shell. ARMClient is in Choco lade op [ARMClient op Choco lade](https://chocolatey.org/packages/ARMClient) gevonden

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd.

> [!Important]
> Voor Network Watcher REST API de naam van de resource groep in de aanvraag-URI is de resource groep die de Network Watcher bevat, niet de resources waarvoor u de diagnostische acties uitvoert.

## <a name="scenario"></a>Scenario

In het scenario dat in dit artikel wordt behandeld, wordt beschreven hoe u stroom Logboeken kunt inschakelen, uitschakelen en opvragen met behulp van de REST API. Ga voor meer informatie over het vastleggen van stroom logboeken voor netwerk beveiligings groepen naar [netwerk beveiligings groep Flow logging-overzicht](network-watcher-nsg-flow-logging-overview.md).

In dit scenario gaat u als volgt te werkt:

* Stroom logboeken inschakelen (versie 2)
* Stroom Logboeken uitschakelen
* Status van de query stroom logboeken

## <a name="log-in-with-armclient"></a>Aanmelden met ARMClient

Meld u aan bij armclient met uw Azure-referenties.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights-provider registreren

Voor een goede werking van de stroom registratie moet de **micro soft. Insights** -provider zijn geregistreerd. Als u niet zeker weet of de provider van **micro soft. Insights** is geregistreerd, voert u het volgende script uit.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Stroom logboeken van netwerk beveiligings groepen inschakelen

De opdracht voor het inschakelen van stroom logboeken versie 2 wordt weer gegeven in het volgende voor beeld. Vervang in versie 1 het veld ' versie ' door ' 1 ':

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

De reactie die wordt geretourneerd door het vorige voor beeld is als volgt:

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

## <a name="disable-network-security-group-flow-logs"></a>Stroom logboeken van netwerk beveiligings groep uitschakelen

Gebruik het volgende voor beeld om stroom Logboeken uit te scha kelen. De aanroep is hetzelfde als het inschakelen van stroom logboeken, behalve **False** is ingesteld voor de eigenschap Enabled.

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

De reactie die wordt geretourneerd door het vorige voor beeld is als volgt:

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

## <a name="query-flow-logs"></a>Query stroom logboeken

Met de volgende REST-aanroep wordt de status van stroom Logboeken in een netwerk beveiligings groep opgevraagd.

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

Hier volgt een voor beeld van het geretourneerde antwoord:

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

## <a name="download-a-flow-log"></a>Een stroom logboek downloaden

De opslag locatie van een stroom logboek wordt gedefinieerd bij het maken. Een handig hulp middel om toegang te krijgen tot deze stroom logboeken die zijn opgeslagen in een opslag account, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload: https://storageexplorer.com/

Als er een opslag account is opgegeven, worden pakket opname bestanden opgeslagen in een opslag account op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

> [!IMPORTANT]
> Er is momenteel een probleem waarbij de [NSG-stroom Logboeken (netwerk beveiligings groep)](network-watcher-nsg-flow-logging-overview.md) voor Network Watcher niet automatisch worden verwijderd uit Blob Storage op basis van de instellingen voor het Bewaar beleid. Als u een bestaand Bewaar beleid voor niet-nul hebt, raden we u aan om regel matig de opslag-blobs te verwijderen die de Bewaar periode hebben verstreken om te voor komen dat er kosten in rekening worden gebracht. Zie voor meer informatie over het verwijderen van de opslag blog van het NSG-stroom logboek de [opslag-blobs voor NSG stroom logboeken verwijderen](network-watcher-delete-nsg-flow-log-blobs.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het visualiseren van uw NSG-stroom logboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over hoe u [uw NSG-stroom logboeken visualiseren met open-source-hulpprogram ma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
