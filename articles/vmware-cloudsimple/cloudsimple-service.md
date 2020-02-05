---
title: Azure VMware-oplossingen (AVS)-service
description: Hierin wordt een overzicht gegeven van de AVS-service en-concepten.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024947"
---
# <a name="avs-service-overview"></a>Overzicht van AVS-service

Met de AVS-service kunt u de Azure VMware-oplossing door AVS gebruiken. Door de service te maken, kunt u knoop punten aanschaffen, knoop punten reserveren en Privécloud-persoonlijke clouds maken. U maakt de AVS-service in elke Azure-regio waar de AVS-service beschikbaar is. De service definieert het Edge-netwerk van de Azure VMware-oplossing door AVS. Het Edge-netwerk ondersteunt services die VPN, ExpressRoute en Internet connectiviteit bevatten voor uw AVS-persoonlijke Clouds.

## <a name="gateway-subnet"></a>Gatewaysubnet

Een gateway-subnet is vereist per AVS-service en is uniek voor de regio waarin het is gemaakt. Het gateway-subnet wordt gebruikt bij het maken van het Edge-netwerk en vereist een/28 CIDR-blok. De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de AVS-omgeving. De netwerken die communiceren met AVS, zijn onder andere on-premises netwerken en een virtueel Azure-netwerk. Een gateway-subnet kan niet worden verwijderd nadat het is gemaakt. Het gateway-subnet wordt verwijderd wanneer de service wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een AVS-service in azure](quickstart-create-cloudsimple-service.md).
