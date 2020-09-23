---
title: 'Azure VPN Gateway: over P2S VPN-client profielen'
description: Gebruik dit artikel om de informatie te vinden die u nodig hebt voor een VPN-client profiel.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6a09767a7992a5f902adea6f99e937f3fc6fa7fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985925"
---
# <a name="about-p2s-vpn-client-profiles"></a>Over P2S VPN-client profielen

Het gedownloade profiel bestand bevat informatie die nodig is voor het configureren van een VPN-verbinding. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-client profiel.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* De **map openvpn** bevat het *ovpn* -profiel dat moet worden gewijzigd om de sleutel en het certificaat op te laten bevatten. Zie [openvpn-clients configureren voor Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows)voor meer informatie. Als Azure AD-verificatie is geselecteerd op de VPN-gateway, is deze map niet aanwezig in het zip-bestand. Ga in plaats daarvan naar de map AzureVPN en zoek azurevpnconfig.xml.

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site.
