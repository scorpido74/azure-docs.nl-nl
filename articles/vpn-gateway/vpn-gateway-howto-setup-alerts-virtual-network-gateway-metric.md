---
title: Waarschuwingen instellen voor Azure VPN Gateway-statistieken
description: Stappen om waarschuwingen te configureren op VPN Gateway-statistieken
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605235"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Waarschuwingen instellen voor VPN Gateway-statistieken

Met dit artikel u waarschuwingen instellen voor Azure VPN Gateway-statistieken. Azure Monitor biedt de mogelijkheid om waarschuwingen voor Azure-resources in te stellen. U waarschuwingen instellen voor virtuele netwerkgateways van het type VPN.


|**Gegevens**   | **Eenheid** | **Granulariteit** | **Beschrijving** | 
|---       | ---        | ---       | ---            | ---       |
|**Gemiddelde bandbreedte**| Bytes/s  | 5 minuten| Gemiddeld gecombineerd bandbreedtegebruik van alle site-to-site verbindingen op de gateway.     |
|**P2SBandbreedte**| Bytes/s  | 1 minuut  | Gemiddelde gecombineerde bandbreedtegebruik van alle point-to-site verbindingen op de gateway.    |
|**Aantal P2SConnection-verbindingen**| Count  | 1 minuut  | Aantal point-to-site verbindingen op de gateway.   |
|**TunnelGemiddeldebandbreedte** | Bytes/s    | 5 minuten  | Gemiddeld bandbreedtegebruik van tunnels die op de gateway zijn gemaakt. |
|**TunnelEgressBytes** | Bytes | 5 minuten | Uitgaand verkeer op tunnels gemaakt op de gateway.   |
|**TunnelEgressPackets** | Count | 5 minuten | Aantal uitgaande pakketten op tunnels die op de gateway zijn gemaakt.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minuten | Aantal uitgaande pakketten die zijn gedropt op tunnels die worden veroorzaakt door een mismatch tussen verkeerskiezer. |
|**TunnelIngressBytes** | Bytes | 5 minuten | Binnenkomend verkeer op tunnels gemaakt op de gateway.   |
|**TunnelIngressPackets** | Count | 5 minuten | Aantal binnenkomende pakketten op tunnels die op de gateway zijn gemaakt.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minuten | Aantal binnenkomende pakketten die zijn gedropt op tunnels die worden veroorzaakt door een mismatch tussen verkeerskiezer. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure Monitor-waarschuwingen instellen op basis van metrische gegevens met behulp van de Azure-portal

In de volgende voorbeeldstappen wordt een waarschuwing op een gateway gemaakt voor:

- **Metrische:** TunnelGemiddeldebandbreedte
- **Voorwaarde:** Bandbreedte > 10 bytes per seconde
- **Venster:** 5 minuten
- **Waarschuwingsactie:** E-mail



1. Ga naar de bron van de virtuele netwerkgateway en selecteer **Waarschuwingen** op het tabblad **Controle.** Maak vervolgens een nieuwe waarschuwingsregel of bewerk een bestaande waarschuwingsregel.

   ![Selecties voor het maken van een waarschuwingsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Maken")

2. Selecteer uw VPN-gateway als bron.

   ![De knop Selecteren en de VPN-gateway in de lijst met bronnen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Selecteer")

3. Selecteer een statistiek die u wilt configureren voor de waarschuwing.

   ![Geselecteerde statistiek in de lijst met statistieken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Selecteer")
4. Configureer de signaallogica. Er zijn drie componenten aan:

    a. **Afmetingen:** Als de statistiek afmetingen heeft, u specifieke dimensiewaarden selecteren, zodat de waarschuwing alleen gegevens van die dimensie evalueert. Deze zijn optioneel.

    b. **Voorwaarde**: Dit is de bewerking om de metrische waarde te evalueren.

    c. **Tijd:** Geef de granulariteit van metrische gegevens op en de periode om de waarschuwing te evalueren.

   ![Details voor het configureren van signaallogica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Selecteer")

5. Als u de geconfigureerde regels wilt weergeven, selecteert u **Waarschuwingsregels beheren**.

   ![Knop voor het beheren van waarschuwingsregels](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Selecteer")

## <a name="next-steps"></a>Volgende stappen

Zie [Waarschuwingen instellen voor diagnostische logboeken van VPN-gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)als u waarschuwingen voor diagnostische logboeken voor tunneldiagnoses wilt configureren.
