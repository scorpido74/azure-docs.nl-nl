---
title: "VPN-gateway: instellingen voor IP-adres van gateway's wijzigen: Azure CLI"
description: In dit artikel u ip-adresvoorvoegsels voor uw lokale netwerkgateway wijzigen met behulp van de Azure CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: bc051a7e0a19dc54431266cfa5f37131868bdc07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864040"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Lokale netwerkgateway-instellingen wijzigen met de Azure CLI

Soms worden de instellingen voor het voorvoegsel van uw lokale netwerkgatewayadres of het IP-adres gateway gewijzigd. In dit artikel ziet u hoe u de instellingen van de lokale netwerkgateway wijzigen. U deze instellingen ook op een andere methode wijzigen door een andere optie uit de volgende lijst te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure-CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

Installeer de nieuwste versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI-opdrachten.

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-adresvoorvoegsels wijzigen

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Het IP-adres van de gateway wijzigen

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Volgende stappen

U uw gatewayverbinding verifiëren. Zie [Een gatewayverbinding verifiëren](vpn-gateway-verify-connection-resource-manager.md).

