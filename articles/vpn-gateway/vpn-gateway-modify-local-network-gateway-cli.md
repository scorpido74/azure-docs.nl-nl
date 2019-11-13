---
title: 'VPN Gateway: Wijzig de IP-adres instellingen van de gateway: Azure CLI'
description: In dit artikel wordt stapsgewijs beschreven hoe u IP-adres voorvoegsels voor uw lokale netwerk gateway kunt wijzigen met behulp van de Azure CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 26462f73680d35371c6a03c3224742626e8f2090
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014968"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Instellingen voor lokale netwerk gateway wijzigen met behulp van Azure CLI

Soms worden de instellingen voor het adres voorvoegsel of het IP-adres van de gateway van het lokale netwerk gewijzigd. In dit artikel wordt beschreven hoe u de instellingen van uw lokale netwerk gateway wijzigt. U kunt deze instellingen ook met behulp van een andere methode wijzigen door een andere optie te selecteren in de volgende lijst:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>Voordat u begint

Installeer de meest recente versie van de CLI-opdrachten (2,0 of hoger). Zie [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI-opdrachten.

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>IP-adres voorvoegsels wijzigen

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>Het IP-adres van de gateway wijzigen

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Volgende stappen

U kunt uw gateway verbinding controleren. Zie [een gateway verbinding controleren](vpn-gateway-verify-connection-resource-manager.md).

