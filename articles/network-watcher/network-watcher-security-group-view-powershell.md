---
title: Netwerk beveiliging analyseren met Azure Network Watcher-beveiligings groep weer geven-Power shell | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Power shell gebruikt voor het analyseren van de beveiliging van virtuele machines met een beveiligings groep.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4c7b79460169612a046b19a4d66f222936710a8e
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163904"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>De beveiliging van uw virtuele machine analyseren met behulp van Power shell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure-CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

De weer gave beveiligings groep retourneert geconfigureerde en efficiënte netwerk beveiligings regels die worden toegepast op een virtuele machine. Deze mogelijkheid is nuttig bij het controleren en diagnosticeren van netwerk beveiligings groepen en-regels die zijn geconfigureerd op een virtuele machine om ervoor te zorgen dat verkeer correct wordt toegestaan of geweigerd. In dit artikel laten we zien hoe u de geconfigureerde en efficiënte beveiligings regels kunt ophalen voor een virtuele machine met behulp van Power shell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario voert u de `Get-AzNetworkWatcherSecurityGroupView` cmdlet uit om de gegevens van de beveiligings regel op te halen.

In dit scenario wordt ervan uitgegaan dat u de stappen in [Create a Network Watcher](network-watcher-create.md) voor het maken van een Network Watcher, al hebt gevolgd.

## <a name="scenario"></a>Scenario

In het scenario dat in dit artikel wordt behandeld, worden de geconfigureerde en efficiënte beveiligings regels voor een bepaalde virtuele machine opgehaald.

## <a name="retrieve-network-watcher"></a>Network Watcher ophalen

De eerste stap bestaat uit het ophalen van het Network Watcher-exemplaar. Deze variabele wordt door gegeven aan `Get-AzNetworkWatcherSecurityGroupView` de cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Een virtuele machine ophalen

Een virtuele machine is vereist om de `Get-AzNetworkWatcherSecurityGroupView` cmdlet uit te voeren. In het volgende voor beeld wordt een VM-object opgehaald.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Weer gave van beveiligings groep ophalen

De volgende stap is het ophalen van het resultaat van de beveiligings groep weer geven.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>De resultaten weer geven

Het volgende voor beeld is een kortere reactie op de geretourneerde resultaten. De resultaten tonen alle effectief en toegepaste beveiligings regels op de virtuele machine, onderverdeeld in groepen **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**en **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Volgende stappen

Ga naar [controle netwerk beveiligings groepen (NSG) met Network Watcher](network-watcher-nsg-auditing-powershell.md) voor meer informatie over het automatiseren van de validatie van netwerk beveiligings groepen.


