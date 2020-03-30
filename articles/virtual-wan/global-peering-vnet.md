---
title: Globale VNet-peering configureren voor Azure Virtual WAN | Microsoft Documenten
description: Sluit een VNet in een andere regio aan op uw Virtual WAN-hub.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588224"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Globale VNet-peering (cross-region VNet) configureren voor Virtual WAN

U een VNet in een andere regio aansluiten op uw Virtuele WAN-hub.

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u aan de volgende criteria hebt voldaan:

* De cross-region VNet (spaak) is niet verbonden met een andere Virtual WAN hub. Een spaak kan alleen worden aangesloten op één virtuele hub.
* De VNet (spaak) bevat geen virtuele netwerkgateway (bijvoorbeeld een Azure VPN Gateway of ExpressRoute virtual network gateway). Als de VNet een virtuele netwerkgateway bevat, moet u de gateway verwijderen voordat u het gesproken VNet met de hub verbindt.

## <a name="register-this-feature"></a><a name="register"></a>Deze functie registreren

U zich voor deze functie registreren met PowerShell. Als u 'Probeer het proberen' selecteert in het onderstaande voorbeeld, wordt Azure Cloud-Shell geopend en hoeft u de PowerShell-cmdlets niet lokaal op uw computer te installeren. Indien nodig u abonnementen wijzigen met de cmdlet 'Select-AzSubscription -SubscriptionId'. <subid>

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Registratie verifiëren

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Een VNet aansluiten op de hub

In deze stap maakt u de peeringverbinding tussen uw hub en de cross-region VNet. Herhaal deze stappen voor elk VNet dat u wilt verbinden.

1. Klik op de pagina voor uw virtuele WAN op **Virtuele netwerkverbinding**.
2. Klik op de pagina Virtuele netwerkverbinding op **+Verbinding toevoegen**.
3. Vul de volgende velden in op de pagina **Verbinding toevoegen**:

    * **Verbindingsnaam** - voer een naam in voor uw verbinding.
    * **Hubs** - selecteer de hub die u wilt koppelen aan deze verbinding.
    * **Abonnement** - controleer of het abonnement klopt.
    * **Virtueel netwerk** - selecteer het virtuele netwerk dat met deze hub wilt verbinden. Het virtuele netwerk mag geen bestaande virtuele netwerkgateway hebben.
4. Klik op **OK** om de peerverbinding te maken.

## <a name="next-steps"></a>Volgende stappen

Zie [Virtueel WAN-overzicht](virtual-wan-about.md)voor meer informatie over Virtual WAN.