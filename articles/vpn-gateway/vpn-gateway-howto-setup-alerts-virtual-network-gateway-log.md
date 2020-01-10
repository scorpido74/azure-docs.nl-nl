---
title: 'Azure VPN Gateway: waarschuwingen voor diagnostische logboek gebeurtenissen configureren'
description: Stappen voor het configureren van waarschuwingen voor gebeurtenissen van VPN Gateway Diagnostische logboeken
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: e2d0227f7481f75c1b0a4e3fe9d0fa3134a7261c
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778597"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Waarschuwingen instellen voor diagnostische logboek gebeurtenissen van VPN Gateway

Dit artikel helpt u bij het instellen van waarschuwingen op basis van diagnostische logboek gebeurtenissen van Azure VPN Gateway met behulp van Azure Log Analytics. 

De volgende logboeken zijn beschikbaar in Azure:

|***Naam*** | ***Beschrijving*** |
|---        | ---               |
|GatewayDiagnosticLog | Bevat Diagnostische logboeken voor configuratie gebeurtenissen van de gateway, primaire wijzigingen en onderhouds gebeurtenissen |
|TunnelDiagnosticLog | Bevat status wijzigings gebeurtenissen voor de tunnel. Tunnel Connect/Disconnect-gebeurtenissen hebben een samenvattings reden voor de status wijziging, indien van toepassing |
|RouteDiagnosticLog | Registreert wijzigingen aan statische routes en BGP-gebeurtenissen die optreden op de gateway |
|IKEDiagnosticLog | Registreert IKE-besturings berichten en gebeurtenissen op de gateway |
|P2SDiagnosticLog | Registreert Point-to-site Control-berichten en gebeurtenissen op de gateway |

## <a name="setup"></a>Waarschuwingen instellen

Met de volgende voorbeeld stappen wordt een waarschuwing gemaakt voor een verbindings gebeurtenis waarbij een site-naar-site-VPN-tunnel is betrokken:


1. Zoek in het Azure Portal naar **log Analytics** onder **alle services** en selecteer **log Analytics werk ruimten**.

   ![Selecties voor het naar Log Analytics werk ruimten gaan](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Selecteer **maken** op de pagina **log Analytics** .

   ![Pagina Log Analytics met de knop maken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecteer")

3. Selecteer **nieuwe maken** en vul de details in.

   ![Details voor het maken van een Log Analytics-werk ruimte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecteer")

4. Zoek uw VPN-gateway op de Blade **bewaking** > **Diagnostische instellingen** .

   ![Selecties voor het zoeken naar de VPN-gateway in Diagnostische instellingen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecteer")

5. Als u Diagnostische gegevens wilt inschakelen, dubbelklikt u op de gateway en selecteert u vervolgens **Diagnostische gegevens inschakelen**.

   ![Selecties voor het inschakelen van diagnostische gegevens](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecteer")

6. Vul de details in en zorg ervoor dat **verzenden naar log Analytics** en **TunnelDiagnosticLog** zijn geselecteerd. Kies de Log Analytics-werk ruimte die u in stap 3 hebt gemaakt.

   ![Geselecteerde selectie vakjes](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecteer")

7. Ga naar het overzicht van de resource van de virtuele netwerk gateway en selecteer **waarschuwingen** op het tabblad **bewaking** . Maak vervolgens een nieuwe waarschuwings regel of bewerk een bestaande waarschuwings regel.

   ![Selecties voor het maken van een nieuwe waarschuwings regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecteer")

   ![punt-naar-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecteer")
8. Selecteer de Log Analytics-werk ruimte en de resource.

   ![Selecties voor werk ruimte en resource](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecteer")

9. Selecteer **aangepaste logboek zoekactie** als de signaal logica onder **voor waarde toevoegen**.

   ![Selecties voor een aangepaste zoek opdracht in Logboeken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecteer")

10. Voer in het tekstvak **Zoekquery** te volgende query in. Vervang de waarden in < > en TimeGenerated naar wens.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Stel de drempel waarde in op 0 en selecteer **gereed**.

    ![Een query invoeren en een drempel waarde selecteren](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecteer")

11. Selecteer op de pagina **regel maken** de optie **Nieuw maken** onder de sectie **actie groepen** . Vul de details in en selecteer **OK**.

    ![Details voor een nieuwe actie groep](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecteer")

12. Vul op de pagina **regel maken** de details in voor het **aanpassen van acties** en zorg ervoor dat de juiste naam wordt weer gegeven in de sectie naam van de **actie groep** . Selecteer **waarschuwings regel maken** om de regel te maken.

    ![Selecties voor het maken van een regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecteer")

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen instellen voor VPN gateway metrische gegevens](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)voor het configureren van waarschuwingen voor metrische gegevens van de tunnel.
