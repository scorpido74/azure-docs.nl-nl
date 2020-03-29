---
title: Netwerkbeveiliging analyseren - Weergave beveiligingsgroep - Azure REST API
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u PowerShell gebruiken om de beveiliging van virtuele machines te analyseren met de beveiligingsgroepweergave.
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
ms.openlocfilehash: c9c76e9c06d4c45a096cff79dac82bb80ebe25d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840737"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Uw beveiliging van virtuele machines analyseren met de beveiligingsgroepweergave met behulp van REST API

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure-CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

De weergave beveiligingsgroep retourneert geconfigureerde en effectieve netwerkbeveiligingsregels die worden toegepast op een virtuele machine. Deze mogelijkheid is handig voor het controleren en diagnosticeren van netwerkbeveiligingsgroepen en regels die zijn geconfigureerd op een vm om ervoor te zorgen dat verkeer correct wordt toegestaan of geweigerd. In dit artikel laten we u zien hoe u de effectieve en toegepaste beveiligingsregels op haalt voor een virtuele machine met restAPI


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario belt u de Network Watcher Rest API om de weergave van de beveiligingsgroep voor een virtuele machine te krijgen. ARMclient wordt gebruikt om de REST API aan te roepen met PowerShell. ARMClient is te vinden op chocolatey bij [ARMClient op Chocolatey](https://chocolatey.org/packages/ARMClient)

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken. In het scenario wordt ook ervan uitgegaan dat er een resourcegroep met een geldige virtuele machine bestaat om te worden gebruikt.

## <a name="scenario"></a>Scenario

Het scenario in dit artikel haalt de effectieve en toegepaste beveiligingsregels voor een bepaalde virtuele machine.

## <a name="log-in-with-armclient"></a>Inloggen met ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Een virtuele machine ophalen

Voer het volgende script uit om een virtuele machine terug te sturenDe volgende code heeft variabelen nodig:

- **abonnementId** - De abonnements-id kan ook worden opgehaald met de **Get-AzSubscription** cmdlet.
- **resourceGroupName** - De naam van een resourcegroep die virtuele machines bevat.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

De informatie die nodig **id** is, `Microsoft.Compute/virtualMachines` is de id onder het type in reactie, zoals te zien in het volgende voorbeeld:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Beveiligingsgroepweergave voor virtuele machine bekijken

In het volgende voorbeeld wordt de weergave van de beveiligingsgroep van een gerichte virtuele machine gevraagd. De resultaten van dit voorbeeld kunnen worden gebruikt om te vergelijken met de regels en beveiliging gedefinieerd door de oorsprong om te zoeken naar configuratie drift.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Bekijk het antwoord

Het volgende voorbeeld is het antwoord dat is geretourneerd van de vorige opdracht. De resultaten tonen alle effectieve en toegepaste beveiligingsregels op de virtuele machine, onderverdeeld in groepen van **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**en **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Ga [naar Auditing Network Security Groups (NSG) met Network Watcher](network-watcher-security-group-view-powershell.md) voor meer informatie over het automatiseren van validatie van netwerkbeveiligingsgroepen.


