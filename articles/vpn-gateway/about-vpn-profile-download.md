---
title: 'Azure VPN Gateway: over P2S VPN-client profielen'
description: Zo kunt u werken met het client profiel bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 326abaa123ede1fbe371c69fcbed47a310b54511
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424834"
---
# <a name="about-p2s-vpn-client-profiles"></a>Over P2S VPN-client profielen

Het gedownloade profiel bestand bevat informatie die nodig is voor het configureren van een VPN-verbinding. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-client profiel.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* De **map openvpn** bevat het *ovpn* -profiel dat moet worden gewijzigd om de sleutel en het certificaat op te laten bevatten. Zie [openvpn-clients configureren voor Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows)voor meer informatie. Als Azure AD-verificatie is geselecteerd op de VPN-gateway, is deze map niet aanwezig in het zip-bestand. Ga in plaats daarvan naar de map AzureVPN en zoek azurevpnconfig.xml.

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site.
