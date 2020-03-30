---
title: Azure VMware-oplossing door CloudSimple - Service
description: Biedt een overzicht van cloudsimple service en concepten.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024947"
---
# <a name="cloudsimple-service-overview"></a>Overzicht van cloudSimple-service

Met de CloudSimple-service u Azure VMware-oplossing van CloudSimple gebruiken.  Als u de service maakt, u knooppunten kopen, knooppunten reserveren en Privéclouds maken.  U maakt de CloudSimple-service in elke Azure-regio waar de CloudSimple-service beschikbaar is. De service definieert het edge-netwerk van Azure VMware-oplossing door CloudSimple. Het edge-netwerk ondersteunt services zoals VPN, ExpressRoute en internetverbinding met uw Private Clouds.

## <a name="gateway-subnet"></a>Gatewaysubnet

Een gatewaysubnet is vereist per CloudSimple-service en is uniek voor de regio waarin het is gemaakt. Het gatewaysubnet wordt gebruikt bij het maken van het edge-netwerk en vereist een /28 CIDR-blok.  De subnetadresruimte van de gateway moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving. De netwerken die communiceren met CloudSimple omvatten on-premises netwerken en het virtuele Azure-netwerk.  Een gatewaysubnet kan niet worden verwijderd nadat het is gemaakt.  Het gatewaysubnet wordt verwijderd wanneer de service wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een CloudSimple-service op Azure](quickstart-create-cloudsimple-service.md).
