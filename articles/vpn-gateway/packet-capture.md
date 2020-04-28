---
title: 'Azure VPN Gateway: pakket opnames configureren'
description: Meer informatie over de functionaliteit voor het vastleggen van pakketten die u kunt gebruiken op VPN-gateways.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75353506"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Pakket opnames voor VPN-gateways configureren

Problemen met de connectiviteit en prestaties zijn vaak even complex en nemen veel tijd en moeite om de oorzaak van het probleem te beperken. De mogelijkheid om pakketten vast te leggen helpt aanzienlijk minder tijd te beperken bij het beperken van het bereik van het probleem naar bepaalde delen van het netwerk, zoals of het probleem zich aan de kant van het netwerk, de Azure-zijde van het netwerk of ergens anders bevindt. Zodra het probleem is verkort, is het veel efficiënter om fouten op te sporen en de herstel actie te ondernemen.

Er zijn een aantal algemeen beschik bare hulpprogram ma's voor het vastleggen van pakketten. Het ophalen van relevante pakket opnames met behulp van deze hulpprogram ma's is vaak dikwijls erg omslachtig bij het werken met scenario's met een hoog volume verkeer. Filter functies die worden geboden door een pakket opname van een VPN-gateway worden een belang rijke onderscheid. U kunt een VPN-gateway pakket vastleggen, naast de algemeen beschik bare hulpprogram ma's voor pakket opname.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Filter mogelijkheden voor het vastleggen van VPN-gateway pakketten

VPN-gateway pakket opnames kunnen worden uitgevoerd op de gateway of op een specifieke verbinding, afhankelijk van de behoeften van de klant. U kunt ook pakket opnames op meerdere tunnels tegelijk uitvoeren. U kunt één of twee richtings verkeer, IKE-en ESP-verkeer en interne pakketten samen met filteren op een VPN-gateway vastleggen.

Het gebruik van 5 Tuples filter (bron-subnet, doel-subnet, bron poort, doel poort, Protocol) en TCP-vlaggen (SYN, ACK, FIN, URG, PSH, RST) is handig bij het isoleren van problemen op een groot volume verkeer.

U kunt slechts één optie per eigenschap gebruiken tijdens het uitvoeren van de pakket opname.

## <a name="setup-packet-capture-using-powershell"></a>Pakket opname instellen met Power shell

Zie de onderstaande voor beelden voor Power shell-opdrachten om pakket opnames te starten en te stoppen. Zie dit Power shell- [document](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)voor meer informatie over opties voor de para meters (zoals het maken van een filter).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Pakket opname starten voor een VPN-gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Optionele para meter **-tekstmap** kunnen worden gebruikt om filter toe te passen.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Pakket opname stoppen voor een VPN-gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Pakket opname starten voor een VPN-gateway verbinding

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Optionele para meter **-tekstmap** kunnen worden gebruikt om filter toe te passen.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Pakket opname stoppen op een VPN-gateway verbinding

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Belangrijkste overwegingen

- Het uitvoeren van pakket opnames kan invloed hebben op de prestaties. Vergeet niet om het opnemen van het pakket te stoppen wanneer dit niet nodig is.
- De voorgestelde minimale pakket opname duur is 600 seconden. Een kortere duur van de pakket opname biedt mogelijk geen volledige gegevens als gevolg van het synchroniseren van problemen tussen meerdere onderdelen op het pad.
- Gegevens bestanden voor pakket opname worden gegenereerd in de PCAP-indeling. Gebruik wireshark of andere algemeen beschik bare toepassingen om PCAP-bestanden te openen.

## <a name="next-steps"></a>Volgende stappen

Zie [about VPN gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN gateway
