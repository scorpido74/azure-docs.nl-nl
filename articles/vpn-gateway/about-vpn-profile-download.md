---
title: 'Azure VPN Gateway: over P2S VPN-client profielen'
description: Zo kunt u werken met het client profiel bestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/13/2020
ms.author: cherylmc
ms.openlocfilehash: 4526611fa8b80a7b97ee7317f5e285c50f05b0de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650655"
---
# <a name="about-p2s-vpn-client-profiles"></a>Over P2S VPN-client profielen

Het gedownloade profiel bestand bevat informatie die nodig is voor het configureren van een VPN-verbinding. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-client profiel.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* De **map openvpn** bevat het *ovpn* -profiel dat moet worden gewijzigd om de sleutel en het certificaat op te laten bevatten. Zie [openvpn-clients configureren voor Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows)voor meer informatie. Als Azure AD-verificatie is geselecteerd op de VPN-gateway, is deze map niet aanwezig in het zip-bestand. Ga in plaats daarvan naar de map AzureVPN en zoek azurevpnconfig. XML.

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site.
