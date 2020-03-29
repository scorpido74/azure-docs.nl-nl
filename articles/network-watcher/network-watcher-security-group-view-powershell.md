---
title: Netwerkbeveiliging analyseren - Weergave beveiligingsgroep - Azure PowerShell
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
ms.openlocfilehash: 4cba2c7e25b5f76b0638da1c551514f102247ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840788"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Uw beveiliging van virtuele machines analyseren met de beveiligingsgroepweergave met PowerShell

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure-CLI](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

De weergave beveiligingsgroep retourneert geconfigureerde en effectieve netwerkbeveiligingsregels die worden toegepast op een virtuele machine. Deze mogelijkheid is handig voor het controleren en diagnosticeren van netwerkbeveiligingsgroepen en regels die zijn geconfigureerd op een vm om ervoor te zorgen dat verkeer correct wordt toegestaan of geweigerd. In dit artikel laten we u zien hoe u de geconfigureerde en effectieve beveiligingsregels ophalen naar een virtuele machine met PowerShell


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario voert `Get-AzNetworkWatcherSecurityGroupView` u de cmdlet uit om de beveiligingsregelgegevens op te halen.

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken.

## <a name="scenario"></a>Scenario

Het scenario in dit artikel haalt de geconfigureerde en effectieve beveiligingsregels voor een bepaalde virtuele machine op.

## <a name="retrieve-network-watcher"></a>Netwerkwatcher ophalen

De eerste stap is het ophalen van de instantie Network Watcher. Deze variabele wordt `Get-AzNetworkWatcherSecurityGroupView` doorgegeven aan de cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Een vm

Een virtuele machine is `Get-AzNetworkWatcherSecurityGroupView` nodig om de cmdlet tegen te draaien. In het volgende voorbeeld wordt een VM-object weergegeven.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Weergave beveiligingsgroep ophalen

De volgende stap is het ophalen van het resultaat van de beveiligingsgroepweergave.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>De resultaten bekijken

Het volgende voorbeeld is een verkorte reactie van de geretourneerde resultaten. De resultaten tonen alle effectieve en toegepaste beveiligingsregels op de virtuele machine, onderverdeeld in groepen van **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**en **EffectiveSecurityRules**.

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

Ga [naar Auditing Network Security Groups (NSG) met Network Watcher](network-watcher-nsg-auditing-powershell.md) voor meer informatie over het automatiseren van validatie van netwerkbeveiligingsgroepen.


