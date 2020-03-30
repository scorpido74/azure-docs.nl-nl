---
title: 'Werken op afstand met P2S: Azure VPN-gateway'
description: Op deze pagina wordt beschreven hoe u Azure Bastion gebruiken om werken op afstand mogelijk te maken vanwege de COVID-19-pandemie.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337107"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Extern werk met Azure VPN Gateway Point-to-site

>[!NOTE]
>In dit artikel wordt beschreven hoe u Azure VPN Gateway, Azure, Microsoft-netwerk en het Azure-partnerecosysteem gebruiken om op afstand te werken en netwerkproblemen te beperken waarmee u wordt geconfronteerd vanwege de CRISIS COVID-19.
>

In dit artikel worden de opties beschreven die beschikbaar zijn voor organisaties om externe toegang voor hun gebruikers in te stellen of om hun bestaande oplossingen aan te vullen met extra capaciteit tijdens de COVID-19-epidemie.

De Azure point-to-site-oplossing is gebaseerd op de cloud en kan snel worden ingericht om tegemoet te komen aan de toegenomen vraag van gebruikers om thuis te werken. Het kan gemakkelijk opschalen en net zo gemakkelijk en snel worden uitgeschakeld wanneer de verhoogde capaciteit niet meer nodig is.

## <a name="about-point-to-site-vpn"></a>Over Point-to-Site VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor teleforenzen die vanaf een externe locatie verbinding willen maken met Azure VNets of on-premises datacenters, zoals thuis of een conferentie. In dit artikel wordt beschreven hoe u gebruikers in staat stelt op afstand te werken op basis van verschillende scenario's.

De onderstaande tabel toont de besturingssystemen van de client en de verificatieopties die voor hen beschikbaar zijn. Het zou nuttig zijn om de verificatiemethode te selecteren op basis van het clientbesturingssysteem dat al in gebruik is. Selecteer bijvoorbeeld OpenVPN met certificaatgebaseerde verificatie als u een mix hebt van clientbesturingssystemen die verbinding moeten maken. Houd er ook rekening mee dat point-to-site VPN alleen wordt ondersteund op routegebaseerde VPN-gateways.

![point-to-site](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>Scenario 1 - Gebruikers hebben alleen toegang nodig tot bronnen in Azure

In dit scenario hoeven de externe gebruikers alleen toegang te hebben tot bronnen die zich in Azure bevinden.

![point-to-site](./media/working-remotely-support/scenario1.png "Scanario 1")

Op hoog niveau zijn de volgende stappen nodig om gebruikers in staat te stellen veilig verbinding te maken met Azure-bronnen:

1. Een virtuele netwerkgateway maken (als deze niet bestaat)
2. Point-to-site VPN configureren op de gateway
    3. [Volg deze koppeling voor certificaatverificatie.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)
    2.  [Voor OpenVPN, volg deze link](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn).
    3.  [Volg deze koppeling voor Azure AD-verificatie](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant).
    4.  [Volg deze koppeling voor het oplossen van point-to-site-verbindingen.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)
3. De VPN-clientconfiguratie downloaden en distribueren
4. De certificaten distribueren (als certificaatverificatie is geselecteerd) onder de clients
5. Verbinding maken met Azure VPN

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>Scenario 2 - Gebruikers hebben toegang nodig tot bronnen in Azure en/of on-prem-bronnen

In dit scenario moeten de externe gebruikers toegang hebben tot bronnen die zich in Azure en in het on-premises datacenter(s) bevinden.

![point-to-site](./media/working-remotely-support/scenario2.png "Scenario 2")

Op hoog niveau zijn de volgende stappen nodig om gebruikers in staat te stellen veilig verbinding te maken met Azure-bronnen:

1. Een virtuele netwerkgateway maken (als deze niet bestaat)
2. Point-to-site VPN configureren op de gateway (zie scenario 1 hierboven)
3. Site-to-site tunnel configureren op Azure Virtual Network Gateway met BGP ingeschakeld
4. On-premises apparaat configureren om verbinding te maken met Azure Virtual Network Gateway
5. Download het point-to-site-profiel van de Azure-portal en distribueer naar clients

[Volg deze link om te leren hoe u een site-to-site VPN-tunnel in-](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Veelgestelde vragen voor native Azure-certificaatverificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Veelgestelde vragen over RADIUS-verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren - Azure AD-verificatie](openvpn-azure-ad-tenant.md)

* [Een P2S-verbinding configureren - RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren - Azure native certificaatverificatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" is een handelsmerk van OpenVPN Inc.**