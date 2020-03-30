---
title: "VPN-gateway: instellingen voor IP-adres van gateway's wijzigen: Azure-portal"
description: In dit artikel u via de Azure-portal ip-adresvoorvoegsels voor uw lokale netwerkgateway bekijken.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864023"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Lokale netwerkgateway-instellingen wijzigen met de Azure-portal

Soms worden de instellingen voor uw lokale netwerkgateway AddressPrefix of GatewayIPAddress gewijzigd. In dit artikel ziet u hoe u de instellingen van de lokale netwerkgateway wijzigen. U deze instellingen ook op een andere methode wijzigen door een andere optie uit de volgende lijst te selecteren:

Voordat u de verbinding verwijdert, u de configuratie voor uw verbindingsapparaten downloaden om de gedefinieerde PSK te krijgen. Op die manier hoef je het niet aan de andere kant te herdefiniëren.

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-adresvoorvoegsels wijzigen

Wanneer u IP-adresvoorvoegsels wijzigt, zijn de stappen die u volgt afhankelijk van de vraag of uw lokale netwerkgateway een verbinding heeft.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Het IP-adres van de gateway wijzigen

Als van het VPN-apparaat waarmee u verbinding wilt maken het openbare IP-adres is gewijzigd, moet u de gateway van het lokale netwerk aanpassen met deze wijziging. Wanneer u het openbare IP-adres wijzigt, zijn de stappen die u volgt afhankelijk van de vraag of uw lokale netwerkgateway een verbinding heeft.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Volgende stappen

U uw gatewayverbinding verifiëren. Zie [Een gatewayverbinding verifiëren](vpn-gateway-verify-connection-resource-manager.md).
