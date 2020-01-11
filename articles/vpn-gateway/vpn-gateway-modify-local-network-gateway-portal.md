---
title: 'VPN Gateway: Wijzig de IP-adres instellingen van de gateway: Azure Portal'
description: In dit artikel vindt u instructies voor het wijzigen van IP-adres voorvoegsels voor uw lokale netwerk gateway met behulp van de Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: fdb98242cede36f818604a7a5d713f5f7c75daad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864023"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Instellingen voor lokale netwerk gateway wijzigen met behulp van de Azure Portal

Soms worden de instellingen voor de AddressPrefix of GatewayIPAddress van uw lokale netwerk gateway gewijzigd. In dit artikel wordt beschreven hoe u de instellingen van uw lokale netwerk gateway wijzigt. U kunt deze instellingen ook met behulp van een andere methode wijzigen door een andere optie te selecteren in de volgende lijst:

Voordat u de verbinding verwijdert, kunt u de configuratie voor uw apparaten waarmee u verbinding maakt, downloaden om de gedefinieerde PSK te verkrijgen. Op die manier hoeft u deze niet aan de andere kant opnieuw te definiëren.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="ipaddprefix"></a>IP-adres voorvoegsels wijzigen

Wanneer u IP-adres voorvoegsels wijzigt, zijn de stappen die u volgt, afhankelijk van het feit of de lokale netwerk gateway een verbinding heeft.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="gwip"></a>Het IP-adres van de gateway wijzigen

Als van het VPN-apparaat waarmee u verbinding wilt maken het openbare IP-adres is gewijzigd, moet u de gateway van het lokale netwerk aanpassen met deze wijziging. Wanneer u het open bare IP-adres wijzigt, zijn de stappen die u volgt, afhankelijk van het feit of de lokale netwerk gateway een verbinding heeft.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt uw gateway verbinding controleren. Zie [een gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).
