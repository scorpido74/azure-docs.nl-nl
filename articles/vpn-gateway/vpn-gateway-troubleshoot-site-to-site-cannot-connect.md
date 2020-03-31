---
title: 'Problemen oplossen met een Azure-vpn-verbinding van site-naar-site die geen titelSuffix: Azure VPN-gateway kan verbinden'
description: Meer informatie over het oplossen van problemen met een site-to-site VPN-verbinding die plotseling niet meer werkt en niet opnieuw kan worden verbonden.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862574"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Probleemoplossing: een Azure-vpn-verbinding van site-naar-site kan geen verbinding maken en werkt niet meer

Nadat u een site-to-site VPN-verbinding hebt geconfigureerd tussen een on-premises netwerk en een virtueel Azure-netwerk, werkt de VPN-verbinding plotseling niet meer en kan deze niet opnieuw worden verbonden. In dit artikel vindt u stappen voor het oplossen van problemen om u te helpen dit probleem op te lossen. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Als u het probleem wilt oplossen, probeert u [eerst de Azure VPN-gateway](vpn-gateway-resetgw-classic.md) te resetten en de tunnel opnieuw in te stellen vanaf het on-premises VPN-apparaat. Als het probleem zich blijft voordoen, volgt u deze stappen om de oorzaak van het probleem te identificeren.

### <a name="prerequisite-step"></a>Vereiste stap

Controleer het type Azure VPN-gateway.

1. Ga naar de [Azure-portal.](https://portal.azure.com)

2. Controleer de **overzichtspagina** van de VPN-gateway voor de tekstgegevens.
    
    ![Overzicht van de gateway](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Step 1. Controleer of het on-premises VPN-apparaat is gevalideerd

1. Controleer of u een [gevalideerd VPN-apparaat en een versie van het besturingssysteem](vpn-gateway-about-vpn-devices.md#devicetable)gebruikt. Als het apparaat geen gevalideerd VPN-apparaat is, moet u mogelijk contact opnemen met de fabrikant van het apparaat om te zien of er een compatibiliteitsprobleem is.

2. Zorg ervoor dat het VPN-apparaat correct is geconfigureerd. Zie [Voorbeelden van apparaatconfiguratie bewerken](vpn-gateway-about-vpn-devices.md#editing)voor meer informatie .

### <a name="step-2-verify-the-shared-key"></a>Stap 2. De gedeelde sleutel verifiëren

Vergelijk de gedeelde sleutel voor het on-premises VPN-apparaat met de Azure Virtual Network VPN om ervoor te zorgen dat de toetsen overeenkomen. 

Als u de gedeelde sleutel voor de Azure VPN-verbinding wilt bekijken, gebruikt u een van de volgende methoden:

**Azure-portal**

1. Ga naar de VPN-gateway-site-to-site-verbinding die u hebt gemaakt.

2. Klik **in** de sectie Instellingen op **Gedeelde toets**.
    
    ![Gedeelde sleutel](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het implementatiemodel azure resource manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Voor het klassieke implementatiemodel:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Stap 3. De VPN-peer-IP's verifiëren

-   De IP-definitie in het object **Local Network Gateway** in Azure moet overeenkomen met het ip-adres van het on-premises apparaat.
-   De IP-definitie van azure-gateway die is ingesteld op het on-premises apparaat, moet overeenkomen met het IP-adres van de Azure-gateway.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Stap 4. Controleer UDR en NSGs op het gatewaysubnet

Controleer en verwijder de door de gebruiker gedefinieerde routeringsroutes (UDR) of Network Security Groups (NSG's) op het gatewaysubnet en test het resultaat. Als het probleem is opgelost, valideert u de instellingen die UDR of NSG heeft toegepast.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Stap 5. Controleer het externe interfaceadres van het on-premises VPN-apparaat

- Als het IP-adres op internet van het VPN-apparaat is opgenomen in de **definitie van lokaal netwerk** in Azure, u sporadische onderbrekingen ervaren.
- De externe interface van het apparaat moet rechtstreeks op het internet zijn. Er mag geen netwerkadresvertaling of firewall zijn tussen het internet en het apparaat.
- Als u firewallclustering wilt configureren om een virtueel IP-adres te hebben, moet u het cluster verbreken en het VPN-toestel rechtstreeks blootstellen aan een openbare interface waarmee de gateway kan communiceren.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Stap 6. Controleren of de subnetten exact overeenkomen (op Azure-beleid gebaseerde gateways)

-   Controleer of de virtuele netwerkadresruimte(en) precies overeenkomt tussen het virtuele Azure-netwerk en de on-premises definities.
-   Controleer of de subnetten exact overeenkomen tussen de **local network gateway** en on-premises definities voor het on-premises netwerk.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Stap 7. Controleer de azure-gatewaystatussonde

1. Open statussondes door naar de volgende URL te bladeren:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Klik door de certificaatwaarschuwing.
3. Als u een antwoord ontvangt, wordt de VPN-gateway als gezond beschouwd. Als u geen antwoord ontvangt, is de gateway mogelijk niet in orde of veroorzaakt een NSG op het gateway-subnet het probleem. De volgende tekst is een voorbeeldreactie:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Stap 8. Controleer of het on-premises VPN-apparaat de perfecte forward secrecy-functie heeft ingeschakeld

De perfecte voorwaartse geheimhoudingsfunctie kan problemen veroorzaken met de ontkoppeling. Als het VPN-apparaat een perfecte voorwaartse geheimhouding heeft ingeschakeld, schakelt u de functie uit. Werk vervolgens het VPN-gateway-IPsec-beleid bij.

## <a name="next-steps"></a>Volgende stappen

-   [Een site-to-site-verbinding configureren met een virtueel netwerk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Een IPsec/IKE-beleid configureren voor vpn-verbindingen van site-naar-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
