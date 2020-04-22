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
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678674"
---
De volgende wijzigingsbewerkingen worden ondersteund voor Exchange-peering:
* Exchange-peeringverbindingen toevoegen.
* Exchange-peeringverbindingen verwijderen.
* Voeg een IPv4- of IPv6-sessie toe op Actieve verbindingen.
* Een IPv4- of IPv6-sessie verwijderen op Actieve verbindingen.


### <a name="add-exchange-peering-connections"></a>Exchange-peeringverbindingen toevoegen

In dit voorbeeld wordt beschreven hoe u verbindingen toevoegt aan een bestaande Exchange-peering.

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

### <a name="remove-exchange-peering-connections"></a>Exchange-peeringverbindingen verwijderen

In dit voorbeeld wordt beschreven hoe u verbindingen met een bestaande Exchange-peering verwijdert.

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

Voer in de volgende opdracht in plaats van 0 het indexnummer in voor de verbinding die u wilt verwijderen.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4- of IPv6-sessie toevoegen op Actieve verbindingen

In dit voorbeeld wordt beschreven hoe u een IPv6-sessie toevoegt aan een bestaande Exchange-verbinding.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4- of IPv6-sessie verwijderen op Actieve verbindingen

Het verwijderen van een IPv4- of IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund op PowerShell. Neem voor meer informatie contact op met [Microsoft peering](mailto:peeringexperience@microsoft.com).