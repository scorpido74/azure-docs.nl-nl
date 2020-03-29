---
title: NSG-stroomlogboeken beheren - Azure CLI
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe logboeken van netwerkbeveiliginggroepsstroom worden beheren in Azure Network Watcher met Azure CLI
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
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840958"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Netwerkbeveiligingsgroepstroomlogboeken configureren met Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Netwerkbeveiligingsgroepstroomlogboeken zijn een functie van Network Watcher waarmee u informatie over binnendringend en uitgangs-IP-verkeer bekijken via een netwerkbeveiligingsgroep. Deze stroomlogboeken worden geschreven in json-indeling en tonen uitgaande en binnenkomende stromen op basis van een regel, de NIC waarop de stroom van toepassing is, 5-tuple-informatie over de stroom (Bron/Bestemming-IP, Bron/Bestemmingspoort, Protocol) en als het verkeer is toegestaan of geweigerd.

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure-command line-interface voor Mac, Linux en Windows (CLI) installeren.](/cli/azure/install-azure-cli)

## <a name="register-insights-provider"></a>Insights-provider registreren

Om de logboekregistratie van de stroom succesvol te laten werken, moet de **Microsoft.Insights-provider** worden geregistreerd. Als u niet zeker weet of de **Microsoft.Insights-provider** is geregistreerd, voert u het volgende script uit.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Logboeken van netwerkbeveiliginggroepstroom inschakelen

De opdracht om stroomlogboeken in te schakelen wordt weergegeven in het volgende voorbeeld:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Het opslagaccount dat u opgeeft, kan geen netwerkregels configureren die de netwerktoegang beperken tot alleen Microsoft-services of specifieke virtuele netwerken. Het opslagaccount kan zich in hetzelfde of een ander Azure-abonnement bevinden dan het NSG waarvoor u het stroomlogboek inschakelt. Als u verschillende abonnementen gebruikt, moeten deze beide zijn gekoppeld aan dezelfde Azure Active Directory-tenant. Het account dat u voor elk abonnement gebruikt, moet over de [vereiste machtigingen](required-rbac-permissions.md)beschikken. 

Als het opslagaccount zich in een andere brongroep of abonnement bevindt dan de netwerkbeveiligingsgroep, geeft u de volledige id van het opslagaccount op in plaats van de naam ervan. Als het opslagaccount zich bijvoorbeeld in een brongroep met de naam *RG-Storage*bevindt, in plaats van *opslagAccountName* op te geven in de vorige opdracht, geeft u */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*op.

## <a name="disable-network-security-group-flow-logs"></a>Logboeken van netwerkbeveiliginggroepstroom uitschakelen

Gebruik het volgende voorbeeld om stroomlogboeken uit te schakelen:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Een stroomlogboek downloaden

De opslaglocatie van een stroomlogboek wordt gedefinieerd bij het maken. Een handig hulpmiddel om toegang te krijgen tot deze stroomlogboeken die zijn opgeslagen in een opslagaccount, is Microsoft Azure Storage Explorer, dat hier kan worden gedownload:https://storageexplorer.com/

Als een opslagaccount is opgegeven, worden stroomlogboekbestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Meer informatie over het [visualiseren van uw NSG-stroomlogboeken met open source-hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
