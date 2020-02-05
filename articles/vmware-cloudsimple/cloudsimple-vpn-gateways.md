---
title: Azure VMware-oplossingen (AVS)-VPN-gateways
description: Meer informatie over AVS site-naar-site VPN en punt-naar-site-VPN-concepten
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024854"
---
# <a name="vpn-gateways-overview"></a>Overzicht van VPN-gateways

Een VPN-gateway wordt gebruikt voor het verzenden van versleuteld verkeer tussen een AVS-regio netwerk op een on-premises locatie of een computer via het open bare Internet. Elke regio kan één VPN-gateway hebben, die meerdere verbindingen kan ondersteunen. Wanneer u meerdere verbindingen naar dezelfde VPN-gateway hebt gemaakt, delen alle VPN-tunnels de bandbreedte die voor de gateway beschikbaar is.

AVS biedt twee soorten VPN-gateways:

* Site-naar-site VPN-gateway
* Punt-naar-site-VPN-gateway

## <a name="site-to-site-vpn-gateway"></a>Site-naar-site VPN-gateway

Een site-naar-site-VPN-gateway wordt gebruikt voor het verzenden van versleuteld verkeer tussen een AVS-regio netwerk en een on-premises Data Center. Gebruik deze verbinding om de subnetten/CIDR-bereik te definiëren voor netwerk verkeer tussen uw on-premises netwerk en het AVS-regio netwerk.

De VPN-gateway stelt u in staat om services van on-premises te gebruiken op de privécloud van uw AVS en op de services van uw automatische AVS-Cloud vanuit het on-premises netwerk. AVS biedt een op beleid gebaseerde VPN-server voor het tot stand brengen van de verbinding vanaf uw on-premises netwerk.

Gebruiks voorbeelden voor site-naar-site-VPN:

* Toegankelijkheid van uw AVS-persoonlijke Cloud-vCenter vanaf elk werk station in uw on-premises netwerk.
* Gebruik van uw on-premises Active Directory als een vCenter-identiteits bron.
* Handige overdracht van VM-sjablonen, Iso's en andere bestanden van uw on-premises resources naar uw AVS-persoonlijke Cloud-vCenter.
* Toegankelijkheid van werk belastingen die worden uitgevoerd op uw AVS-Privécloud vanuit uw on-premises netwerk.

![Topologie van site-naar-site-VPN-verbinding](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Cryptografische para meters

Een site-naar-site-VPN-verbinding maakt gebruik van de volgende standaard cryptografie parameters om een beveiligde verbinding tot stand te brengen. Wanneer u een verbinding maakt vanaf uw on-premises VPN-apparaat, gebruikt u een van de volgende para meters die worden ondersteund door uw on-premises VPN-gateway.

#### <a name="phase-1-proposals"></a>Voorst Ellen fase 1

| Parameter | Voor stel 1 | Voor stel 2 | Voor stel 3 |
|-----------|------------|------------|------------|
| IKE-versie | IKEv1 | IKEv1 | IKEv1 |
| Versleuteling | AES 128 | AES 256 | AES 256 |
| Hash-algoritme| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman-groep (DH-groep) | 2 | 2 | 2 |
| Levens duur | 28.800 seconden | 28.800 seconden | 28.800 seconden |
| Gegevens grootte | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Voorst Ellen voor fase 2

| Parameter | Voor stel 1 | Voor stel 2 | Voor stel 3 |
|-----------|------------|------------|------------|
| Versleuteling | AES 128 | AES 256 | AES 256 |
| Hash-algoritme| SHA 256 | SHA 256 | SHA 1 |
| Perfecte Forward Secrecy-groep (PFS-groep) | Geen | Geen | Geen |
| Levens duur | 1\.800 seconden | 1\.800 seconden | 1\.800 seconden |
| Gegevens grootte | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Stel TCP MSS in op 1200 op uw VPN-apparaat. Als uw VPN-apparaten geen ondersteuning bieden voor MSS-bekleming, kunt u in plaats daarvan ook de MTU op de tunnel interface instellen op 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Punt-naar-site-VPN-gateway

Een punt-naar-site-VPN wordt gebruikt voor het verzenden van versleuteld verkeer tussen een AVS-regio netwerk en een client computer. Punt-naar-site-VPN is de eenvoudigste manier om toegang te krijgen tot uw persoonlijke Cloud netwerk van uw AVS, met inbegrip van uw AVS-Vm's voor Privécloud en werk belastingen. Gebruik punt-naar-site-VPN-verbinding als u extern verbinding maakt met de automatische AVS-Cloud.

## <a name="next-steps"></a>Volgende stappen

* [VPN-gateway instellen](vpn-gateway.md)
