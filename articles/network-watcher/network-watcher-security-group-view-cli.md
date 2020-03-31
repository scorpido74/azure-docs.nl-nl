---
title: Netwerkbeveiliging analyseren met de weergave Beveiligingsgroep - Azure CLI
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u Azure CLI gebruiken om een beveiliging van virtuele machines te analyseren met de weergave beveiligingsgroep.
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
ms.openlocfilehash: 73f1efc512bf031021791da8cc55bc4e7d98a812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840772"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Uw beveiliging van virtuele machines analyseren met de beveiligingsgroepweergave met Azure CLI

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure-CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

De weergave beveiligingsgroep retourneert geconfigureerde en effectieve netwerkbeveiligingsregels die worden toegepast op een virtuele machine. Deze mogelijkheid is handig voor het controleren en diagnosticeren van netwerkbeveiligingsgroepen en regels die zijn geconfigureerd op een vm om ervoor te zorgen dat verkeer correct wordt toegestaan of geweigerd. In dit artikel laten we u zien hoe u de geconfigureerde en effectieve beveiligingsregels ophaalt naar een virtuele machine met Azure CLI

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure-command line-interface voor Mac, Linux en Windows (CLI) installeren.](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken.

## <a name="scenario"></a>Scenario

Het scenario in dit artikel haalt de geconfigureerde en effectieve beveiligingsregels voor een bepaalde virtuele machine op.

## <a name="get-a-vm"></a>Een vm

Een virtuele machine is `vm list` vereist om de cmdlet te laten draaien. In de volgende opdracht worden de virtuele machines in een resourcegroep weergegeven:

```azurecli
az vm list -resource-group resourceGroupName
```

Zodra u de virtuele machine kent, u de `vm show` cmdlet gebruiken om de bron-id te krijgen:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Weergave beveiligingsgroep ophalen

De volgende stap is het ophalen van het resultaat van de beveiligingsgroepweergave.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>De resultaten bekijken

Het volgende voorbeeld is een verkorte reactie van de geretourneerde resultaten. De resultaten tonen alle effectieve en toegepaste beveiligingsregels op de virtuele machine, onderverdeeld in groepen van **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**en **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Ga [naar Auditing Network Security Groups (NSG) met Network Watcher](network-watcher-nsg-auditing-powershell.md) voor meer informatie over het automatiseren van validatie van netwerkbeveiligingsgroepen.

Meer informatie over de beveiligingsregels die worden toegepast op uw netwerkbronnen door het [overzicht van de beveiligingsgroepweergave](network-watcher-security-group-view-overview.md) te bezoeken
