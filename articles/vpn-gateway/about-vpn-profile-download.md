---
title: 'Azure VPN-gateway: over P2S VPN-clientprofielen'
description: Zo u werken met het clientprofielbestand
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/17/2020
ms.author: cherylmc
ms.openlocfilehash: b78d32dbb7b4e0d9a3b13ff741e6e38c12be0e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528503"
---
# <a name="about-p2s-vpn-client-profiles"></a>Over P2S VPN-clientprofielen

Het gedownloade profielbestand bevat informatie die nodig is om een VPN-verbinding te configureren. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-clientprofiel.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* De **OpenVPN-map** bevat het *ovpn-profiel* dat moet worden aangepast om de sleutel en het certificaat op te nemen. Zie [OpenVPN-clients configureren voor Azure VPN Gateway voor](vpn-gateway-howto-openvpn-clients.md#windows)meer informatie. Deze map is niet aanwezig in het zip-bestand als Azure AD-verificatie is geselecteerd op de VPN-gateway. In plaats daarvan bevindt de azurevpnconfig.xml zich in de AzureVPN-map.

## <a name="next-steps"></a>Volgende stappen

Zie Van punt tot locatie voor meer informatie over [point-to-site.](point-to-site-about.md)
