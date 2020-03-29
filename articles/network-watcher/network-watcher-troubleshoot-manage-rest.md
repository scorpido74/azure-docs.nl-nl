---
title: Problemen met VNET-gateway en -verbindingen oplossen - Azure REST API
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u problemen oplossen met virtuele netwerkgateways en -verbindingen met Azure Network Watcher met BEHULP van REST
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: ab9f7fd95d7081b66e05dfd3d6a5ef47eb3c4053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840669"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Problemen met de gateway en verbindingen van virtuele netwerken oplossen met Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher biedt veel mogelijkheden als het gaat om het begrijpen van uw netwerkbronnen in Azure. Een van deze mogelijkheden is het oplossen van problemen met resources. Resourceprobleemoplossing kan worden aangeroepen via de portal, PowerShell, CLI of REST API. Wanneer network watcher wordt opgeroepen, inspecteert hij de status van een Virtual Network Gateway of een Connection en retourneert het zijn bevindingen.

In dit artikel vindt u de verschillende beheertaken die momenteel beschikbaar zijn voor het oplossen van problemen met resources.

- [**Problemen met een gateway voor virtueel netwerk oplossen**](#troubleshoot-a-virtual-network-gateway)
- [**Een verbinding oplossen**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Voordat u begint

ARMclient wordt gebruikt om de REST API aan te roepen met PowerShell. ARMClient is te vinden op chocolatey bij [ARMClient op Chocolatey](https://chocolatey.org/packages/ARMClient)

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken.

Voor een lijst met ondersteunde gatewaytypen [u ondersteunde gatewaytypen bezoeken.](network-watcher-troubleshoot-overview.md#supported-gateway-types)

## <a name="overview"></a>Overzicht

Network Watcher probleemoplossing biedt de mogelijkheid om problemen op te lossen die zich voordoen met gateways en verbindingen van virtuele netwerken. Wanneer een aanvraag wordt ingediend bij het oplossen van problemen met de bron, worden logboeken opgevraagd en geïnspecteerd. Wanneer de inspectie is voltooid, worden de resultaten geretourneerd. De problemen met API-aanvragen zijn langlopende aanvragen, wat meerdere minuten kan duren om een resultaat te retourneren. Logboeken worden opgeslagen in een container op een opslagaccount.

## <a name="log-in-with-armclient"></a>Inloggen met ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Problemen met een gateway voor virtueel netwerk oplossen


### <a name="post-the-troubleshoot-request"></a>Post de probleemoplossing aanvraag

In het volgende voorbeeld wordt de status van een gateway voor een virtueel netwerk opgevraagd.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Aangezien deze bewerking lang is uitgevoerd, wordt de URI voor het opvragen van de bewerking en de URI voor het resultaat geretourneerd in de antwoordkop, zoals weergegeven in het volgende antwoord:

**Belangrijke waarden**

* **Azure-AsyncOperation** - Deze eigenschap bevat de URI om de async-probleemoplossende bewerking op te vragen
* **Locatie** - Deze eigenschap bevat de URI waar de resultaten zijn wanneer de bewerking is voltooid

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>De async-bewerking opvragen voor voltooiing

Gebruik de bewerkingen URI om te zoeken naar de voortgang van de bewerking, zoals in het volgende voorbeeld wordt gezien:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Terwijl de bewerking aan de gang is, wordt in het antwoord **InProgress** weergegeven zoals te zien is in het volgende voorbeeld:

```json
{
  "status": "InProgress"
}
```

Wanneer de bewerking is voltooid, worden de statuswijzigingen **in Geslaagd .**

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>De resultaten ophalen

Zodra de status is **geretourneerd, roept**u een GET-methode aan op de operationResult URI om de resultaten op te halen.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

De volgende antwoorden zijn voorbeelden van een typische gedegradeerdantwoord dat wordt geretourneerd bij het opvragen van de resultaten van het oplossen van problemen met een gateway. Zie [Inzicht in de resultaten](#understanding-the-results) om duidelijkheid te krijgen over wat de eigenschappen in de reactie betekenen.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Problemen met verbindingen oplossen

In het volgende voorbeeld wordt de status van een verbinding opgevraagd.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> De probleemoplossingsbewerking kan niet parallel worden uitgevoerd op een verbinding en de bijbehorende gateways. De bewerking moet worden voltooid voordat deze wordt uitgevoerd op de vorige resource.

Aangezien dit een langlopende transactie is, wordt in de antwoordkop de URI voor het opvragen van de bewerking en de URI voor het resultaat geretourneerd, zoals in het volgende antwoord wordt weergegeven:

**Belangrijke waarden**

* **Azure-AsyncOperation** - Deze eigenschap bevat de URI om de async-probleemoplossende bewerking op te vragen
* **Locatie** - Deze eigenschap bevat de URI waar de resultaten zijn wanneer de bewerking is voltooid

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>De async-bewerking opvragen voor voltooiing

Gebruik de bewerkingen URI om te zoeken naar de voortgang van de bewerking, zoals in het volgende voorbeeld wordt gezien:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Terwijl de bewerking aan de gang is, wordt in het antwoord **InProgress** weergegeven zoals te zien is in het volgende voorbeeld:

```json
{
  "status": "InProgress"
}
```

Wanneer de bewerking is voltooid, wordt de status **gewijzigd in Geslaagd**.

```json
{
  "status": "Succeeded"
}
```

De volgende antwoorden zijn voorbeelden van een standaardantwoord dat wordt geretourneerd bij het opvragen van de resultaten van het oplossen van problemen met een verbinding.

### <a name="retrieve-the-results"></a>De resultaten ophalen

Zodra de status is **geretourneerd, roept**u een GET-methode aan op de operationResult URI om de resultaten op te halen.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

De volgende antwoorden zijn voorbeelden van een standaardantwoord dat wordt geretourneerd bij het opvragen van de resultaten van het oplossen van problemen met een verbinding.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Inzicht in de resultaten

De actietekst bevat algemene richtlijnen voor het oplossen van het probleem. Als er actie kan worden ondernomen voor het probleem, wordt een koppeling voorzien van aanvullende richtlijnen. In het geval dat er geen aanvullende richtlijnen zijn, biedt het antwoord de url om een ondersteuningsaanvraag te openen.  Ga voor meer informatie over de eigenschappen van het antwoord en wat er is inbegrepen naar [Het Overzicht problemen met problemen met netwerkwatcher](network-watcher-troubleshoot-overview.md)

Raadpleeg aan [de slag met Azure Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor instructies voor het downloaden van bestanden uit azure-opslagaccounts. Een ander hulpmiddel dat kan worden gebruikt is Storage Explorer. Meer informatie over Storage Explorer vindt u hier via de volgende link: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Volgende stappen

Zie [Netwerkbeveiligingsgroepen beheren](../virtual-network/manage-network-security-group.md) om de netwerkbeveiligingsgroep en beveiligingsregels op te sporen die mogelijk in het geding zijn, als de instellingen die vpn-connectiviteit stoppen, zijn gewijzigd.
