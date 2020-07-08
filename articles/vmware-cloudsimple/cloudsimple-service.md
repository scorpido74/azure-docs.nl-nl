---
title: Azure VMware-oplossing op CloudSimple-service
description: Biedt een overzicht van de CloudSimple-service en-concepten.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77024947"
---
# <a name="cloudsimple-service-overview"></a>Overzicht van de CloudSimple-service

Met de CloudSimple-service kunt u Azure VMware-oplossing gebruiken door CloudSimple.  Door de service te maken, kunt u knoop punten aanschaffen, knoop punten reserveren en persoonlijke clouds maken.  U maakt de CloudSimple-service in elke Azure-regio waar de CloudSimple-service beschikbaar is. De service definieert het Edge-netwerk van de Azure VMware-oplossing door CloudSimple. Het Edge-netwerk ondersteunt services die VPN, ExpressRoute en Internet connectiviteit met uw persoonlijke Clouds bevatten.

## <a name="gateway-subnet"></a>Gatewaysubnet

Een gateway-subnet is vereist per CloudSimple-service en is uniek voor de regio waarin het is gemaakt. Het gateway-subnet wordt gebruikt bij het maken van het Edge-netwerk en vereist een/28 CIDR-blok.  De adres ruimte van het gateway-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving. De netwerken die communiceren met CloudSimple zijn onder andere on-premises netwerken en een virtueel Azure-netwerk.  Een gateway-subnet kan niet worden verwijderd nadat het is gemaakt.  Het gateway-subnet wordt verwijderd wanneer de service wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een CloudSimple-service in azure](quickstart-create-cloudsimple-service.md).
