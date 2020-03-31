---
title: Network Watcher - NSG-stroomlogboeken maken met een sjabloon Azure Resource Manager
description: Gebruik een Azure Resource Manager-sjabloon en PowerShell om eenvoudig NSG-stroomlogboeken in te stellen.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 35d185a625a81a259c366a45999769ecf76c6a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538155"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>NSG-stroomlogboeken configureren vanuit een azure resourcemanager-sjabloon

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) is de native en krachtige manier van Azure om uw [infrastructuur als code](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)te beheren.

In dit artikel ziet u hoe u [NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programmatisch inschakelen met behulp van een Azure Resource Manager-sjabloon en Azure PowerShell. We beginnen met een overzicht van de eigenschappen van het OBJECT NSG Flow Log, gevolgd door een paar voorbeeldsjablonen. Vervolgens implementeren we de deploy-sjabloon met behulp van een lokale PowerShell-instantie.


## <a name="nsg-flow-logs-object"></a>Object NSG-stroomlogboeken

Het object NSG Flow Logs met alle parameters wordt hieronder weergegeven.
Voor een volledig overzicht van de eigenschappen u de verwijzing naar de [sjabloon NSG Flow Logs](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)lezen.

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Als u een Bron voor Microsoft.Network/networkWatchers/flowLogs wilt maken, voegt u de bovenstaande JSON toe aan het gedeelte resources van uw sjabloon.


## <a name="creating-your-template"></a>Uw sjabloon maken

Als u azure resourcebeheersjablonen voor de eerste keer gebruikt, u meer informatie over deze sjablonen gebruiken via de onderstaande koppelingen.

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Zelfstudie: Uw eerste Azure Resource Manager-sjabloon maken en implementeren](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Hieronder vindt u twee voorbeelden van complete sjablonen voor het instellen van NSG Flow Logs.

**Voorbeeld 1**: De eenvoudigste versie van het bovenstaande met minimale parameters is geslaagd. De onderstaande sjabloon maakt NSG Flow Logs op een doel NSG en slaat ze op in een bepaald opslagaccount.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * De naam van de resource heeft de indeling 'Bovenliggende bron>/onderliggende bron'. Hier is de bovenliggende bron de regionale<RegionName>instantie Network Watcher (Indeling: NetworkWatcher_ . Voorbeeld: NetworkWatcher_centraluseuap)
> * targetResourceId is de resource-ID van de doel-NSG
> * storageId is de bron-id van het bestemmingsopslagaccount

**Voorbeeld 2**: De volgende sjablonen waarmee NSG Flow Logs (versie 2) met een retentie voor 5 dagen. Traffic Analytics inschakelen met een verwerkingsinterval van 10 minuten.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
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
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Uw Azure Resource Manager-sjabloon implementeren

In deze zelfstudie wordt ervan uitgegaan dat u een bestaande resourcegroep hebt en een NSG waarop Flow inloggen.
U een van de bovenstaande `azuredeploy.json`voorbeeldsjablonen lokaal opslaan als. Werk de eigenschapswaarden bij zodat ze wijzen op geldige bronnen in uw abonnement.

Als u de sjabloon wilt implementeren, voert u de volgende opdracht uit in PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Uw implementatie verifiëren

Er zijn een paar manieren om te controleren of uw implementatie is geslaagd. Op uw PowerShell-console moet "ProvisioningState" worden weergegeven als 'Geslaagd'. Daarnaast u de [portalpagina van NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) bezoeken om uw wijzigingen te bevestigen. Als er problemen zijn met de implementatie, bekijkt u [het oplossen van veelvoorkomende Azure-implementatiefouten met Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw NSG Flow-gegevens met behulp van:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Open source-hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure-verkeersanalyse](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
