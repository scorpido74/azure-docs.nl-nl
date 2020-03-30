---
title: Problemen met Azure Site-to-Site VPN worden met tussenpozen verbroken
description: Meer informatie over het oplossen van het probleem waarbij de VPN-verbinding site-naar-site regelmatig wordt verbroken.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862557"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Probleemoplossing: Azure Site-to-Site VPN verbreekt met tussenpozen de verbinding

U het probleem ondervinden dat een nieuwe of bestaande Microsoft Azure Site-to-Site VPN-verbinding niet stabiel is of regelmatig de verbinding verbreekt. In dit artikel vindt u problemen met de problemen waarmee u de oorzaak van het probleem identificeren en oplossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

### <a name="prerequisite-step"></a>Vereiste stap

Controleer het type Azure virtual network gateway:

1. Ga naar [Azure-portal](https://portal.azure.com).
2. Controleer de **overzichtspagina** van de virtuele netwerkgateway voor de tekstgegevens.
    
    ![Het overzicht van de gateway](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Stap 1 Controleer of het on-premises VPN-apparaat is gevalideerd

1. Controleer of u een [gevalideerd VPN-apparaat en een versie van het besturingssysteem](vpn-gateway-about-vpn-devices.md#devicetable)gebruikt. Als het VPN-apparaat niet is gevalideerd, moet u mogelijk contact opnemen met de fabrikant van het apparaat om te zien of er een compatibiliteitsprobleem is.
2. Zorg ervoor dat het VPN-apparaat correct is geconfigureerd. Zie [Configuratievoorbeelden voor apparaatconfiguratie bewerken](vpn-gateway-about-vpn-devices.md#editing)voor meer informatie .

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Stap 2 Controleer de instellingen van de beveiligingskoppeling (voor azure-gateways voor virtuele netwerken op basis van beleid)

1. Zorg ervoor dat het virtuele netwerk, de subnetten en de bereiken in de definitie **van lokale netwerkgateway** in Microsoft Azure hetzelfde zijn als de configuratie op het on-premises VPN-apparaat.
2. Controleer of de instellingen van de beveiligingskoppeling overeenkomen.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Stap 3 Controleren op door de gebruiker gedefinieerde routes of netwerkbeveiligingsgroepen op gatewaysubnet

Een door de gebruiker gedefinieerde route op het gatewaysubnet beperkt mogelijk het verkeer en staat ander verkeer toe. Dit maakt het lijkt erop dat de VPN-verbinding onbetrouwbaar is voor sommige verkeer en goed voor anderen. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Stap 4 Controleer de instelling 'één VPN-tunnel per subnetpaar' (voor op beleid gebaseerde virtuele netwerkgateways)

Zorg ervoor dat het on-premises VPN-apparaat is ingesteld op **één VPN-tunnel per subnetpaar** voor op beleid gebaseerde virtuele netwerkgateways.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Stap 5 Controleren op beperking van de beveiligingskoppeling (voor op beleid gebaseerde virtuele netwerkgateways)

De op beleid gebaseerde virtuele netwerkgateway heeft een limiet van 200 subnet Security Association-paren. Als het aantal Azure virtuele netwerksubnetten vermenigvuldigd keer met het aantal lokale subnetten groter is dan 200, ziet u sporadische subnetten loskoppelen.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Stap 6 On-premises vpn-apparaat extern interfaceadres controleren

- Als het IP-adres van het VPN-apparaat met internette maken is opgenomen in de definitie **van lokale netwerkgateway** in Azure, u sporadische onderbrekingen ervaren.
- De externe interface van het apparaat moet rechtstreeks op het internet zijn. Er mag geen NAT (Network Address Translation) of firewall zijn tussen het internet en het apparaat.
-  Als u Firewall Clustering configureert als een virtueel IP-adres, moet u het cluster verbreken en het VPN-toestel rechtstreeks blootstellen aan een openbare interface waarmee de gateway kan communiceren.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Stap 7 Controleer of het on-premises VPN-apparaat perfect voorwaartse geheimhouding heeft ingeschakeld

De functie **Perfect Forward Secrecy** kan de ontkoppelingsproblemen veroorzaken. Als het VPN-apparaat **Perfect forward Secrecy** heeft ingeschakeld, schakelt u de functie uit. Werk [vervolgens het iPsec-beleid van de virtuele netwerkgateway bij.](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy)

## <a name="next-steps"></a>Volgende stappen

- [Een site-naar-siteverbinding configureren met een virtueel netwerk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [IPsec/IKE-beleid configureren voor VPN-verbindingen van site naar site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

