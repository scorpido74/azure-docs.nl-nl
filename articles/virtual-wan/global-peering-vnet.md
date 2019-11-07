---
title: Globale VNet-peering configureren voor Azure Virtual WAN | Microsoft Docs
description: Verbind een VNet in een andere regio met uw virtuele WAN-hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588224"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Wereld wijde VNet-peering (multi-regio VNet) configureren voor virtuele WAN

U kunt een VNet in een andere regio verbinden met uw virtuele WAN-hub.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u aan de volgende criteria hebt voldaan:

* De kruislingse regio VNet (spoke) is niet verbonden met een andere virtuele WAN-hub. Een spoke kan alleen worden verbonden met één virtuele hub.
* Het VNet (spoke) bevat geen virtuele netwerk gateway (bijvoorbeeld een virtuele netwerk gateway van Azure VPN Gateway of ExpressRoute). Als het VNet een virtuele netwerk gateway bevat, moet u de gateway verwijderen voordat u de spoke VNet verbindt met de hub.

## <a name="register"></a>Deze functie registreren

U kunt zich registreren voor deze functie met behulp van Power shell. Als u ' Probeer het ' uit het onderstaande voor beeld selecteert, wordt Azure Cloud-shell geopend en hoeft u de Power shell-cmdlets niet lokaal op uw computer te installeren. Indien nodig kunt u abonnementen wijzigen met behulp van de cmdlet Select-AzSubscription-SubscriptionId <subid>.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify"></a>Registratie verifiëren

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="hub"></a>Een VNet verbinden met de hub

In deze stap maakt u de peering-verbinding tussen uw hub en het andere VNet. Herhaal deze stappen voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de peerverbinding te maken.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van virtuele WANs](virtual-wan-about.md)voor meer informatie over Virtual WAN.