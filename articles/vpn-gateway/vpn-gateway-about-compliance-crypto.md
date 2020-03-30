---
title: 'Azure VPN-gateway: cryptografische vereisten'
description: In dit artikel worden cryptografische vereisten en Azure VPN-gateways besproken
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: yushwang
ms.openlocfilehash: 93e13592d9c434b159ad4f4c10ef30328941c64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902824"
---
# <a name="about-cryptographic-requirements-and-azure-vpn-gateways"></a>Over cryptografische vereisten en Azure VPN-gateways

In dit artikel wordt besproken hoe u Azure VPN-gateways configureren om te voldoen aan uw cryptografische vereisten voor zowel cross-premises S2S VPN-tunnels als VNet-naar-VNet-verbindingen binnen Azure.

## <a name="about-ikev1-and-ikev2-for-azure-vpn-connections"></a>Over IKEv1 en IKEv2 voor Azure VPN-verbindingen

Traditioneel stonden we IKEv1-verbindingen alleen toe voor Basic SKU's en stonden IKEv2-verbindingen toe voor alle VPN-gateway SKU's, met andere dan Basic SKU's. De Basic SKU's staan slechts 1 verbinding toe en samen met andere beperkingen, zoals prestaties, gebruikten klanten oudere apparaten die alleen IKEv1-protocollen ondersteunen, beperkte ervaring. Om de ervaring van klanten die IKEv1-protocollen gebruiken te verbeteren, staan we nu IKEv1-verbindingen toe voor alle VPN-gateway SKU's, met uitzondering van Basic SKU. Zie [VPN Gateway SKU's voor](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku)meer informatie.

![Azure VPN Gateway IKEv1- en IKEv2-verbindingen](./media/vpn-gateway-about-compliance-crypto/ikev1-ikev2-connections.png)

Wanneer IKEv1- en IKEv2-verbindingen worden toegepast op dezelfde VPN-gateway, wordt de doorvoer tussen deze twee verbindingen automatisch ingeschakeld.

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>Over iPsec- en IKE-beleidsparameters voor Azure VPN-gateways

IPsec en IKE protocol standaard ondersteunt een breed scala van cryptografische algoritmen in verschillende combinaties. Als u geen specifieke combinatie van cryptografische algoritmen en parameters aanvraagt, gebruiken Azure VPN-gateways een reeks standaardvoorstellen. De standaardbeleidssets zijn gekozen om de interoperabiliteit te maximaliseren met een breed scala aan VPN-apparaten van derden in standaardconfiguraties. Als gevolg hiervan kunnen het beleid en het aantal voorstellen niet alle mogelijke combinaties van beschikbare cryptografische algoritmen en belangrijke sterke punten omvatten.

De standaardbeleidsinstelling voor Azure VPN-gateway wordt vermeld in het artikel: [Over VPN-apparaten en IPsec/IKE-parameters voor site-to-site VPN-gatewayverbindingen](vpn-gateway-about-vpn-devices.md).

## <a name="cryptographic-requirements"></a>Cryptografische vereisten

Voor communicatie waarvoor specifieke cryptografische algoritmen of parameters nodig zijn, meestal vanwege nalevings- of beveiligingsvereisten, u nu hun Azure VPN-gateways configureren om een aangepast IPsec/IKE-beleid te gebruiken met specifieke cryptografische algoritmen en belangrijke sterke punten, in plaats van de Azure-standaardbeleidssets.

Het IKEv2-beleid voor de hoofdmodus voor Azure VPN-gateways gebruikt bijvoorbeeld alleen Diffie-Hellman Group 2 (1024 bits), terwijl u mogelijk sterkere groepen moet opgeven die moeten worden gebruikt in IKE, zoals Groep 14 (2048-bits), Groep 24 (2048-bits MODP-groep) of ECP (elliptische curve groepen) 256 of 384-bits (respectievelijk groep 19 en groep 20). Vergelijkbare vereisten gelden ook voor het beleid voor de snelle modus van IPsec.

## <a name="custom-ipsecike-policy-with-azure-vpn-gateways"></a>Aangepast IPsec/IKE-beleid met Azure VPN-gateways

Azure VPN-gateways ondersteunen nu per verbinding, aangepast IPsec/IKE-beleid. Voor een Site-to-Site- of VNet-verbinding of VNet-verbinding u een specifieke combinatie van cryptografische algoritmen voor IPsec en IKE kiezen met de gewenste toetssterkte, zoals in het volgende voorbeeld wordt weergegeven:

![ipsec-ike-beleid](./media/vpn-gateway-about-compliance-crypto/ipsecikepolicy.png)

U een IPsec/IKE-beleid maken en een nieuwe of bestaande verbinding toepassen.

### <a name="workflow"></a>Werkstroom

1. Maak de virtuele netwerken, VPN-gateways of lokale netwerkgateways voor uw connectiviteitstopologie zoals beschreven in andere how-to-documenten
2. Een IPsec/IKE-beleid maken
3. U het beleid toepassen wanneer u een S2S- of VNet-verbinding maakt
4. Als de verbinding al is gemaakt, u het beleid toepassen of bijwerken op een bestaande verbinding

## <a name="ipsecike-policy-faq"></a>Veelgestelde vragen over iPsec/IKE-beleid

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [IPsec/IKE-beleid configureren](vpn-gateway-ipsecikepolicy-rm-powershell.md) voor stapsgewijze instructies voor het configureren van het aangepaste IPsec/IKE-beleid voor een verbinding.

Zie ook [Meerdere vpn-apparaten op basis van beleid](vpn-gateway-connect-multiple-policybased-rm-ps.md) verbinden voor meer informatie over de optie UsePolicyBasedTrafficSelectors.
