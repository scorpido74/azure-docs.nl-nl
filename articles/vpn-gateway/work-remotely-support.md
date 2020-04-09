---
title: 'Werken op afstand met P2S: Azure VPN-gateway'
description: Op deze pagina wordt beschreven hoe u VPN Gateway gebruiken om werken op afstand mogelijk te maken vanwege de COVID-19-pandemie.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 2d07a13c654f30e48c37d2e8d3e801166e26f4f4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886582"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Extern werk met Azure VPN Gateway Point-to-site

>[!NOTE]
>In dit artikel wordt beschreven hoe u Azure VPN Gateway, Azure, Microsoft-netwerk en het Azure-partnerecosysteem gebruiken om op afstand te werken en netwerkproblemen te beperken waarmee u wordt geconfronteerd vanwege de CRISIS COVID-19.
>

In dit artikel worden de opties beschreven die beschikbaar zijn voor organisaties om externe toegang voor hun gebruikers in te stellen of om hun bestaande oplossingen aan te vullen met extra capaciteit tijdens de COVID-19-epidemie.

De Azure point-to-site-oplossing is cloudgebaseerd en kan snel worden ingericht om tegemoet te komen aan de toegenomen vraag van gebruikers om thuis te werken. Het kan gemakkelijk opschalen en net zo gemakkelijk en snel worden uitgeschakeld wanneer de verhoogde capaciteit niet meer nodig is.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Over Point-to-Site VPN

Met een point-to-site-VPN-gatewayverbinding (P2S) kunt u vanaf een afzonderlijke clientcomputer een beveiligde verbinding maken met uw virtuele netwerk. Een P2S-verbinding wordt tot stand gebracht door deze te starten vanaf de clientcomputer. Deze oplossing is handig voor teleforenzen die vanaf een externe locatie verbinding willen maken met Azure VNets of on-premises datacenters, zoals thuis of een conferentie. In dit artikel wordt beschreven hoe u gebruikers in staat stelt op afstand te werken op basis van verschillende scenario's.

De onderstaande tabel toont de besturingssystemen van de client en de verificatieopties die voor hen beschikbaar zijn. Het zou nuttig zijn om de verificatiemethode te selecteren op basis van het clientbesturingssysteem dat al in gebruik is. Selecteer bijvoorbeeld OpenVPN met certificaatgebaseerde verificatie als u een mix hebt van clientbesturingssystemen die verbinding moeten maken. Houd er ook rekening mee dat point-to-site VPN alleen wordt ondersteund op routegebaseerde VPN-gateways.

![point-to-site](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenario 1 - Gebruikers hebben alleen toegang nodig tot bronnen in Azure

In dit scenario hoeven de externe gebruikers alleen toegang te hebben tot bronnen die zich in Azure bevinden.

![point-to-site](./media/working-remotely-support/scenario1.png "Scenario 1")

Op hoog niveau zijn de volgende stappen nodig om gebruikers in staat te stellen veilig verbinding te maken met Azure-bronnen:

1. Maak een virtuele netwerkgateway (als deze niet bestaat).
2. Configureer point-to-site VPN op de gateway.

   * Volg [deze koppeling](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)voor certificaatverificatie.
   * Voor OpenVPN, volg [deze link](vpn-gateway-howto-openvpn.md).
   * Volg [deze koppeling](openvpn-azure-ad-tenant.md)voor Azure AD-verificatie .
   * Volg [deze koppeling](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)voor het oplossen van point-to-site-verbindingen.
3. Download en distribueer de VPN-clientconfiguratie.
4. Distribueer de certificaten (als certificaatverificatie is geselecteerd) naar de clients.
5. Maak verbinding met Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenario 2 - Gebruikers hebben toegang nodig tot bronnen in Azure en/of on-prem-bronnen

In dit scenario moeten de externe gebruikers toegang hebben tot bronnen die zich in Azure en in het on-premises datacenter(s) bevinden.

![point-to-site](./media/working-remotely-support/scenario2.png "Scenario 2")

Op hoog niveau zijn de volgende stappen nodig om gebruikers in staat te stellen veilig verbinding te maken met Azure-bronnen:

1. Maak een virtuele netwerkgateway (als deze niet bestaat).
2. Point-to-site VPN configureren op de gateway (zie [Scenario 1](#scenario1)).
3. Configureer een site-to-site tunnel op de Azure virtual network gateway met BGP ingeschakeld.
4. Configureer het on-premises apparaat om verbinding te maken met de virtuele netwerkgateway van Azure.
5. Download het point-to-site-profiel van de Azure-portal en distribueer naar clients

Zie [deze link](vpn-gateway-howto-site-to-site-resource-manager-portal.md)voor meer informatie over het instellen van een vpn-tunnel van site-to-site.

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Veelgestelde vragen voor native Azure-certificaatverificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Veelgestelde vragen over RADIUS-verificatie

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Volgende stappen

* [Een P2S-verbinding configureren - Azure AD-verificatie](openvpn-azure-ad-tenant.md)

* [Een P2S-verbinding configureren - RADIUS-verificatie](point-to-site-how-to-radius-ps.md)

* [Een P2S-verbinding configureren - Azure native certificaatverificatie](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" is een handelsmerk van OpenVPN Inc.**