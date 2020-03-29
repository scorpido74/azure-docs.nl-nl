---
title: 'Azure VPN-gateway: pakketopnames configureren'
description: Meer informatie over functies voor het vastleggen van pakketten die u gebruiken op VPN-gateways.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353506"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Pakketopnames configureren voor VPN-gateways

Connectiviteit en prestatiegerelateerde problemen zijn vaak vaak complex en vergen aanzienlijke tijd en moeite om de oorzaak van het probleem te beperken. De mogelijkheid om pakketten vast te leggen helpt de tijd te verkorten bij het beperken van het bereik van het probleem tot bepaalde delen van het netwerk, zoals of het probleem zich aan de klantzijde van het netwerk bevindt, de Azure-kant van het netwerk of ergens daartussenin. Zodra het probleem is verkleind, is het veel efficiënter om te debuggen en corrigerende maatregelen te nemen.

Er zijn een aantal algemeen beschikbare tools voor het vastleggen van pakketten. Het verkrijgen van relevante pakketopnames met behulp van deze hulpprogramma's is echter vaak soms omslachtig, vooral wanneer u met verkeersscenario's met een hoog volume werkt. Filtermogelijkheden die worden geboden door een VPN-gateway packet capture wordt een belangrijke differentiator. U een VPN-gateway-pakketopname gebruiken naast algemeen beschikbare tools voor het vastleggen van pakketten.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Mogelijkheden voor het vastleggen van VPN-gateway-pakketten

VPN-gatewaypakketopnames kunnen worden uitgevoerd op de gateway of op een specifieke verbinding, afhankelijk van de behoeften van de klant. U ook pakketopnames uitvoeren op meerdere tunnels tegelijk. U enkel- of bidirectionerend verkeer, IKE- en ESP-verkeer en binnenpakketten vastleggen, samen met filteren op een VPN-gateway.

Het gebruik van 5 tuplesfilter (bronsubnet, doelsubnet, bronpoort, doelpoort, protocol) en TCP-vlaggen (SYN, ACK, FIN, URG, PSH, RST) is handig bij het isoleren van problemen op een hoog volumeverkeer.

U slechts één optie per eigenschap gebruiken tijdens het uitvoeren van de pakketopname.

## <a name="setup-packet-capture-using-powershell"></a>Pakketopname instellen met PowerShell

Zie de voorbeelden hieronder voor PowerShell-opdrachten om pakketopnames te starten en te stoppen. Zie dit [PowerShell-document](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)voor meer informatie over parameteropties (zoals het maken van filter).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Packet capture starten voor een VPN-gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Optionele parameter **-FilterData** kan worden gebruikt om filter toe te passen.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Stoppen met packet capture voor een VPN-gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Packet capture starten voor een VPN-gatewayverbinding

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Optionele parameter **-FilterData** kan worden gebruikt om filter toe te passen.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Stoppen met het vastleggen van pakketten op een VPN-gatewayverbinding

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Belangrijkste overwegingen

- Het uitvoeren van pakketopnames kan de prestaties beïnvloeden. Vergeet niet om de pakketopname te stoppen wanneer het niet nodig is.
- De voorgestelde minimale pakketopnameduur is 600 seconden. Het hebben van een kortere duur van het vastleggen van pakketten biedt mogelijk geen volledige gegevens vanwege synchronisatieproblemen tussen meerdere onderdelen op het pad.
- Packet capture-gegevensbestanden worden gegenereerd in PCAP-indeling. Gebruik Wireshark of andere algemeen beschikbare toepassingen om PCAP-bestanden te openen.

## <a name="next-steps"></a>Volgende stappen

Zie [Over VPN Gateway](vpn-gateway-about-vpngateways.md) voor meer informatie over VPN Gateway
