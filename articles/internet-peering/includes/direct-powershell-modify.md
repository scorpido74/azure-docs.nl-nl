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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774261"
---
In deze sectie wordt beschreven hoe u de volgende wijzigings bewerkingen uitvoert voor directe peering:

* Directe peering-verbindingen toevoegen
* Directe peering-verbindingen verwijderen
* De band breedte van actieve verbindingen upgraden of verlagen.
* Voeg een IPv4/IPv6-sessie toe aan actieve verbindingen.
* IPv4/IPv6-sessie op actieve verbindingen verwijderen.

### <a name="add-direct-peering-connections"></a>Directe peering-verbindingen toevoegen

Hieronder wordt beschreven hoe u verbindingen toevoegt aan bestaande directe peering

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Directe peering-verbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund in Power shell. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>De band breedte van actieve verbindingen upgraden of verlagen

Hieronder wordt beschreven hoe u 10 Gbps toevoegt aan een bestaande directe verbinding.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Voeg een IPv4/IPv6-sessie toe aan actieve verbindingen.

Hieronder wordt beschreven hoe u een IPv6-sessie toevoegt aan een bestaande directe verbinding met alleen een IPv4-sessie. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie op actieve verbindingen verwijderen.

Het verwijderen van een IPv4/IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund in Power shell. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com).