---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774417"
---
De volgende wijzigings bewerking wordt ondersteund voor Exchange-peering
1. Exchange-peering verbindingen toevoegen
1. Exchange-peering verbindingen verwijderen
1. Voeg een IPv4/IPv6-sessie toe aan actieve verbindingen.
1. IPv4/IPv6-sessie op actieve verbindingen verwijderen.


### <a name="add-exchange-peering-connections"></a>Exchange-peering verbindingen toevoegen

Hieronder wordt beschreven hoe u verbindingen toevoegt aan bestaande Exchange-peering

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Exchange-peering verbindingen verwijderen

Hieronder wordt beschreven hoe u verbindingen met bestaande Exchange-peering verwijdert

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Bekijk alle verbindingen en selecteer de verbinding die u wilt verwijderen. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

In de onderstaande opdracht, in plaats van 0, voert u het index nummer in voor de verbinding die u wilt verwijderen.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie toevoegen aan actieve verbindingen

Hieronder wordt beschreven hoe u een IPv6-sessie toevoegt aan een bestaande Exchange-verbinding.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie op actieve verbindingen verwijderen

Het verwijderen van een IPv4/IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund in Power shell. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com).