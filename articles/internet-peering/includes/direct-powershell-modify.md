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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680799"
---
In deze sectie wordt beschreven hoe u de volgende wijzigingsbewerkingen voor Direct peering uitvoert:

* Directe peeringverbindingen toevoegen.
* Directe peeringverbindingen verwijderen.
* Bandbreedte upgraden of downgraden op Actieve verbindingen.
* IPv4- of IPv6-sessies toevoegen op Actieve verbindingen.
* IPv4- of IPv6-sessies verwijderen op Actieve verbindingen.

### <a name="add-direct-peering-connections"></a>Directe peeringverbindingen toevoegen

In dit voorbeeld wordt beschreven hoe u verbindingen toevoegt aan bestaande direct peering.

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

Het verwijderen van een verbinding wordt momenteel niet ondersteund op PowerShell. Neem voor meer informatie contact op met [Microsoft peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Bandbreedte upgraden of downgraden op Actieve verbindingen

In dit voorbeeld wordt beschreven hoe u 10 Gbps toevoegt aan een bestaande directe verbinding.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4- of IPv6-sessies toevoegen aan Actieve verbindingen

In dit voorbeeld wordt beschreven hoe u een IPv6-sessie toevoegt aan een bestaande directe verbinding met alleen een IPv4-sessie. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4- of IPv6-sessies verwijderen op Actieve verbindingen

Het verwijderen van een IPv4- of IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund op PowerShell. Neem voor meer informatie contact op met [Microsoft peering](mailto:peeringexperience@microsoft.com).