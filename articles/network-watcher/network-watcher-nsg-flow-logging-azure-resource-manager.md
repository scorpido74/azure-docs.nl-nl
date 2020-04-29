---
title: Network Watcher-NSG-stroom logboeken maken met behulp van een Azure Resource Manager sjabloon
description: Een Azure Resource Manager sjabloon en Power shell gebruiken om eenvoudig NSG-stroom Logboeken in te stellen.
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
ms.openlocfilehash: 104311904b99cadbbc8c0267a98f2709443608ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80891455"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>NSG-stroom logboeken van een Azure Resource Manager sjabloon configureren

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Zo](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) is de systeem eigen en krachtige manier waarop u uw [infra structuur kunt beheren als code](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

Dit artikel laat u zien hoe u [NSG-stroom logboeken](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programmatisch kunt inschakelen met behulp van een Azure Resource Manager sjabloon en Azure PowerShell. We beginnen met het bieden van een overzicht van de eigenschappen van het NSG-flow logboek object, gevolgd door een paar voorbeeld sjablonen. Vervolgens wordt de sjabloon implementeren met een lokaal Power shell-exemplaar.


## <a name="nsg-flow-logs-object"></a>Object voor NSG-stroom logboeken

Hieronder ziet u het object NSG flow-logboeken met alle para meters.
Voor een volledig overzicht van de eigenschappen kunt u de referentie van de [sjabloon NSG-stroom logboeken](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)lezen.

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
Als u een micro soft. Network/networkWatchers/flowLogs-resource wilt maken, voegt u de bovenstaande JSON toe aan de sectie resources van uw sjabloon.


## <a name="creating-your-template"></a>Uw sjabloon maken

Als u Azure Resource Manager-sjablonen voor de eerste keer gebruikt, kunt u meer informatie hierover vinden met behulp van de onderstaande koppelingen.

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Zelf studie: uw eerste Azure Resource Manager sjabloon maken en implementeren](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Hieronder vindt u twee voor beelden van volledige sjablonen voor het instellen van NSG-stroom Logboeken.

**Voor beeld 1**: de eenvoudigste versie van de bovenstaande met de minimale para meters die worden door gegeven. Met de onderstaande sjabloon schakelt u NSG-stroom Logboeken in op een doel-NSG en slaat u deze op in een opgegeven opslag account.

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
> * De naam van de resource heeft de indeling ' bovenliggende resource>/Child-resource '. Hier is de bovenliggende resource het regionale Network Watcher exemplaar (indeling: NetworkWatcher_<RegionName>. Voor beeld: NetworkWatcher_centraluseuap)
> * targetResourceId is de resource-ID van de doel-NSG
> * Id is de resource-ID van het doel-opslag account

**Voor beeld 2**: met de volgende sjablonen worden NSG-stroom Logboeken (versie 2) ingeschakeld met een Bewaar periode van vijf dagen. Het inschakelen van Traffic Analytics met een verwerkings interval van 10 minuten.

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

In deze zelf studie wordt ervan uitgegaan dat u een bestaande resource groep en een NSG hebt, waarmee u de stroom logboek registratie kunt inschakelen.
U kunt elk van de bovenstaande voorbeeld sjablonen lokaal opslaan als `azuredeploy.json`. Werk de eigenschaps waarden zo bij dat ze verwijzen naar geldige resources in uw abonnement.

Als u de sjabloon wilt implementeren, voert u de volgende opdracht uit in Power shell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Uw implementatie verifiëren

Er zijn een aantal manieren om te controleren of uw implementatie is geslaagd. De Power shell-console moet ' ProvisioningState ' weer geven als ' geslaagd '. Daarnaast kunt u de portal- [pagina van de NSG-stroom logboeken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) bezoeken om uw wijzigingen te bevestigen. Als er problemen zijn met de implementatie, kunt u een kijkje nemen bij het [oplossen van veelvoorkomende problemen met Azure-implementaties met Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>De resource verwijderen
Azure maakt het verwijderen van resources mogelijk via de implementatie modus ' voltooid '. Als u een stroom logboek bron wilt verwijderen, geeft u een implementatie op in de modus voltooid zonder de resource die u wilt verwijderen op te nemen. Meer informatie over de [volledige implementatie modus](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het visualiseren van uw NSG-stroom gegevens met behulp van:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Open-source hulpprogramma's](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
