---
title: Azure VMware-oplossing door CloudSimple - VPN-gateways
description: Meer informatie over CloudSimple Site-to-Site VPN- en Point-to-Site VPN-concepten
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024854"
---
# <a name="vpn-gateways-overview"></a>Overzicht VPN-gateways

Een VPN-gateway wordt gebruikt om versleuteld verkeer te verzenden tussen een CloudSimple-regionetwerk op een on-premises locatie of een computer via het openbare internet.  Elke regio kan één VPN-gateway hebben, die meerdere verbindingen kan ondersteunen. Wanneer u meerdere verbindingen naar dezelfde VPN-gateway hebt gemaakt, delen alle VPN-tunnels de bandbreedte die voor de gateway beschikbaar is.

CloudSimple biedt twee soorten VPN-gateways:

* Site-to-Site VPN-gateway
* Point-to-Site VPN-gateway

## <a name="site-to-site-vpn-gateway"></a>Site-to-Site VPN-gateway

Een Site-to-Site VPN-gateway wordt gebruikt om versleuteld verkeer te verzenden tussen een CloudSimple-regionetwerk en een on-premises datacenter. Gebruik deze verbinding om het subnet/CIDR-bereik te definiëren voor netwerkverkeer tussen uw on-premises netwerk en het Regionetwerk CloudSimple.

Met de VPN-gateway u services van on-premises op uw Private Cloud en services op uw Private Cloud gebruiken vanuit het on-premises netwerk.  CloudSimple biedt een op beleid gebaseerde VPN-server voor het tot stand brengen van de verbinding vanuit uw on-premises netwerk.

Use cases voor Site-to-Site VPN:

* Toegankelijkheid van uw Private Cloud vCenter vanaf elk werkstation in uw on-premises netwerk.
* Gebruik uw on-premises Active Directory als vCenter-identiteitsbron.
* Handige overdracht van VM-sjablonen, ISO's en andere bestanden van uw on-premises bronnen naar uw Private Cloud vCenter.
* Toegankelijkheid van workloads die op uw private cloud worden uitgevoerd vanuit uw on-premises netwerk.

![Topologie van site-naar-site VPN-verbinding](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Cryptografische parameters

Een Site-to-Site VPN-verbinding gebruikt de volgende standaard cryptografische parameters om een beveiligde verbinding tot stand te brengen.  Wanneer u een verbinding maakt vanaf uw on-premises VPN-apparaat, gebruikt u een van de volgende parameters die worden ondersteund door uw on-premises VPN-gateway.

#### <a name="phase-1-proposals"></a>Voorstellen voor fase 1

| Parameter | Voorstel 1 | Voorstel 2 | Voorstel 3 |
|-----------|------------|------------|------------|
| IKE-versie | IKEv1 | IKEv1 | IKEv1 |
| Versleuteling | AES 128 | AES 256 | AES 256 |
| Hash-algoritme| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman Group (DH Group) | 2 | 2 | 2 |
| Levenstijd | 28.800 seconden | 28.800 seconden | 28.800 seconden |
| Gegevensgrootte | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Voorstellen voor fase 2

| Parameter | Voorstel 1 | Voorstel 2 | Voorstel 3 |
|-----------|------------|------------|------------|
| Versleuteling | AES 128 | AES 256 | AES 256 |
| Hash-algoritme| SHA 256 | SHA 256 | SHA 1 |
| Perfect Forward Secrecy Group (PFS Group) | Geen | Geen | Geen |
| Levenstijd | 1800 seconden | 1800 seconden | 1800 seconden |
| Gegevensgrootte | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Stel TCP MSS Clamping in op 1200 op uw VPN-apparaat. Of als uw VPN-apparaten mss-klemmen niet ondersteunen, u de MTU op de tunnelinterface ook instellen op 1240 bytes.

## <a name="point-to-site-vpn-gateway"></a>Point-to-Site VPN-gateway

Een Point-to-Site VPN wordt gebruikt om versleuteld verkeer te verzenden tussen een CloudSimple regionetwerk en een clientcomputer.  Point-to-Site VPN is de eenvoudigste manier om toegang te krijgen tot uw Private Cloud-netwerk, inclusief uw Private Cloud vCenter en workload VM's.  Gebruik Point-to-Site VPN-connectiviteit als u op afstand verbinding maakt met de Private Cloud.

## <a name="next-steps"></a>Volgende stappen

* [VPN-gateway instellen](vpn-gateway.md)
