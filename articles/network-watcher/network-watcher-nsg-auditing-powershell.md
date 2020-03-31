---
title: NSG-controle automatiseren - Weergave beveiligingsgroep
titleSuffix: Azure Network Watcher
description: Op deze pagina vindt u instructies voor het configureren van controle van een netwerkbeveiligingsgroep
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
ms.openlocfilehash: 59c1b6e6c281a736a79d110bd7d943344bcd5130
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840975"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>NSG-controle automatiseren met azure Network Watcher Security-groepsweergave

Klanten staan vaak voor de uitdaging om de beveiligingshouding van hun infrastructuur te verifiëren. Deze uitdaging is niet anders voor hun VM's in Azure. Het is belangrijk om een vergelijkbaar beveiligingsprofiel te hebben op basis van de NSG-regels (Network Security Group). Met de weergave Beveiligingsgroep u nu de lijst met regels op een VM binnen een NSG krijgen. U een gouden NSG-beveiligingsprofiel definiëren en Security Group View op een wekelijkse cadans initiëren en de uitvoer vergelijken met het gouden profiel en een rapport maken. Op deze manier u met gemak alle VM's identificeren die niet voldoen aan het voorgeschreven beveiligingsprofiel.

Zie [Overzicht netwerkbeveiliging](../virtual-network/security-overview.md)als u niet bekend bent met netwerkbeveiligingsgroepen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario vergelijkt u een bekende goede basislijn met de resultaten van de beveiligingsgroepweergave die zijn geretourneerd voor een virtuele machine.

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken. In het scenario wordt ook ervan uitgegaan dat er een resourcegroep met een geldige virtuele machine bestaat om te worden gebruikt.

## <a name="scenario"></a>Scenario

Het scenario dat in dit artikel wordt behandeld, krijgt de weergave van de beveiligingsgroep voor een virtuele machine.

In dit scenario zult u:

- Een bekende goede regelset ophalen
- Een virtuele machine ophalen met Rest API
- Beveiligingsgroepweergave voor virtuele machine bekijken
- Reactie evalueren

## <a name="retrieve-rule-set"></a>Regelset ophalen

De eerste stap in dit voorbeeld is om te werken met een bestaande basislijn. Het volgende voorbeeld is een json die is `Get-AzNetworkSecurityGroup` geëxtraheerd uit een bestaande netwerkbeveiligingsgroep met behulp van de cmdlet die als basislijn voor dit voorbeeld wordt gebruikt.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Regelingesteld converteren naar PowerShell-objecten

In deze stap lezen we een json-bestand dat eerder is gemaakt met de regels die naar verwachting in de netwerkbeveiligingsgroep voor dit voorbeeld staan.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Netwerkwatcher ophalen

De volgende stap is het ophalen van de instantie Network Watcher. De `$networkWatcher` variabele wordt `AzNetworkWatcherSecurityGroupView` doorgegeven aan de cmdlet.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Een vm

Een virtuele machine is `Get-AzNetworkWatcherSecurityGroupView` nodig om de cmdlet tegen te draaien. In het volgende voorbeeld wordt een VM-object weergegeven.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Weergave beveiligingsgroep ophalen

De volgende stap is het ophalen van het resultaat van de beveiligingsgroepweergave. Dit resultaat wordt vergeleken met de "baseline" json die eerder werd getoond.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>De resultaten analyseren

Het antwoord wordt gegroepeerd op netwerkinterfaces. De verschillende typen regels die worden geretourneerd, zijn effectieve en standaardbeveiligingsregels. Het resultaat wordt verder uitgesplitst naar de manier waarop het wordt toegepast, hetzij op een subnet of een virtuele NIC.

In het volgende PowerShell-script worden de resultaten van de weergave beveiligingsgroep vergeleken met een bestaande uitvoer van een NSG. Het volgende voorbeeld is een eenvoudig voorbeeld van `Compare-Object` hoe de resultaten kunnen worden vergeleken met cmdlet.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Het volgende voorbeeld is het resultaat. U zien dat twee van de regels die in de eerste regelset stonden, niet aanwezig waren in de vergelijking.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Volgende stappen

Zie [Netwerkbeveiligingsgroepen beheren](../virtual-network/manage-network-security-group.md) om de netwerkbeveiligingsgroep en beveiligingsregels in kwestie op te sporen als de instellingen zijn gewijzigd.













