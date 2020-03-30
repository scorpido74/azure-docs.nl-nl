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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774261"
---
In deze sectie wordt beschreven hoe u de volgende wijzigingsbewerkingen voor Direct peering uitvoert:

* Directe peeringverbindingen toevoegen
* Directe peeringverbindingen verwijderen
* Bandbreedte upgraden of downgraden op Actieve verbindingen.
* IPv4/IPv6-sessie toevoegen op Actieve verbindingen.
* IPv4/IPv6-sessie verwijderen op Actieve verbindingen.

### <a name="add-direct-peering-connections"></a>Directe peeringverbindingen toevoegen

Hieronder voorbeeld beschrijft hoe u verbindingen toevoegt aan bestaande Direct-peering

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

### <a name="remove-direct-peering-connections"></a>Directe peeringverbindingen verwijderen

Het verwijderen van een verbinding wordt momenteel niet ondersteund op PowerShell. Neem contact op met [Microsoft-peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Bandbreedte upgraden of downgraden op Actieve verbindingen

Hieronder voorbeeld beschrijft hoe je 10Gbps toevoegt aan bestaande directe verbinding.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie toevoegen op Actieve verbindingen.

Hieronder voorbeeld beschrijft hoe u IPv6-sessie toevoegt op een bestaande directe verbinding met alleen IPv4-sessie. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-sessie verwijderen op Actieve verbindingen.

Het verwijderen van een IPv4/IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund op PowerShell. Neem contact op met [Microsoft-peering](mailto:peeringexperience@microsoft.com).