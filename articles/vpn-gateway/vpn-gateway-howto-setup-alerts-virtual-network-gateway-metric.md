---
title: Waarschuwingen instellen voor metrische gegevens van Azure VPN Gateway
description: Stappen voor het configureren van waarschuwingen voor VPN Gateway metrische gegevens
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: ce9ab972d961a9193c3aa568878f9c93ccdcf6b1
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988022"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Waarschuwingen instellen voor VPN Gateway metrische gegevens

Dit artikel helpt u bij het instellen van waarschuwingen op Azure VPN Gateway metrische gegevens. Azure Monitor biedt de mogelijkheid om waarschuwingen in te stellen voor Azure-resources. U kunt waarschuwingen instellen voor virtuele netwerk gateways van het type VPN.


|**Gegevens**   | **Teleenheid** | **Granulariteit** | **Beschrijving** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minuten| Gemiddelde gecombineerde bandbreedte gebruik van alle site-naar-site-verbindingen op de gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuut  | Gemiddelde gecombineerde bandbreedte gebruik van alle punt-naar-site-verbindingen op de gateway.    |
|**P2SConnectionCount**| Count  | 1 minuut  | Aantal Point-to-site-verbindingen op de gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minuten  | Gemiddeld bandbreedte gebruik van tunnels die zijn gemaakt op de gateway. |
|**TunnelEgressBytes** | Bytes | 5 minuten | Uitgaand verkeer op tunnels die zijn gemaakt op de gateway.   |
|**TunnelEgressPackets** | Count | 5 minuten | Aantal uitgaande pakketten op tunnels die zijn gemaakt op de gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minuten | Aantal uitgaande pakketten dat is verwijderd op tunnels die zijn veroorzaakt door een niet-overeenkomend verkeers selectie. |
|**TunnelIngressBytes** | Bytes | 5 minuten | Binnenkomend verkeer op tunnels die zijn gemaakt op de gateway.   |
|**TunnelIngressPackets** | Count | 5 minuten | Aantal inkomende pakketten op tunnels die zijn gemaakt op de gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minuten | Aantal binnenkomende pakketten dat is verwijderd op tunnels die zijn veroorzaakt door een niet-overeenkomend Traffic-selector. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure Monitor waarschuwingen instellen op basis van metrische gegevens met behulp van de Azure Portal

In de volgende voorbeeld stappen wordt een waarschuwing voor een gateway gemaakt voor:

- **Metriek:** TunnelAverageBandwidth
- **Voor waarde:** Band breedte > tien bytes/seconde
- **Venster:** 5 minuten
- **Waarschuwings actie:** E-mail



1. Ga naar de gateway resource van het virtuele netwerk en selecteer **waarschuwingen** op het tabblad **bewaking** . Maak vervolgens een nieuwe waarschuwings regel of bewerk een bestaande waarschuwings regel.

   ![Selecties voor het maken van een waarschuwings regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Maken")

2. Selecteer uw VPN-gateway als de resource.

   ![De knop selecteren en de VPN-gateway in de lijst met resources](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Selecteer")

3. Selecteer een metrische waarde die voor de waarschuwing moet worden geconfigureerd.

   ![Geselecteerde metrische gegevens in de lijst met metrische gegevens](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Selecteer")
4. De signaal logica configureren. Er zijn er drie onderdelen:

    a. **Dimensies**: als de metriek dimensies heeft, kunt u specifieke dimensie waarden selecteren zodat de waarschuwing alleen gegevens van die dimensie evalueert. Dit zijn optioneel.

    b. **Voor waarde**: dit is de bewerking om de metrische waarde te evalueren.

    c. **Tijd**: Geef de granulatie op van metrische gegevens en de tijds periode om de waarschuwing te evalueren.

   ![Details voor het configureren van de signaal logica](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Selecteer")

5. Als u de geconfigureerde regels wilt weer geven, selecteert u **waarschuwings regels beheren**.

   ![Knop voor het beheren van waarschuwings regels](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Selecteer")

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen instellen op VPN gateway bron logboeken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)voor informatie over het configureren van waarschuwingen voor tunnel bron Logboeken.
