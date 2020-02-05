---
title: VMware-oplossingen (AVS)-Azure-netwerk verbindingen
description: Meer informatie over het verbinden van uw virtuele Azure-netwerk met uw AVS-regio netwerk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025075"
---
# <a name="azure-network-connections-overview"></a>Overzicht van Azure-netwerk verbindingen

Wanneer u een AVS-service in een regio maakt en knoop punten maakt, kunt u het volgende doen:

* Vraag een Azure ExpressRoute-circuit aan en koppel deze aan het AVS-netwerk in die regio.
* Verbind uw AVS-regio netwerk met uw virtuele Azure-netwerk of uw on-premises netwerk met behulp van Azure ExpressRoute.
* Toegang bieden tot services die worden uitgevoerd in uw Azure-abonnement of uw on-premises netwerk vanuit uw Privécloud.

De ExpressRoute-verbinding is een hoge band breedte met een lage latentie.

## <a name="benefits"></a>Voordelen

Met Azure-netwerk verbinding kunt u:

* Gebruik Azure als back-updoel voor virtuele machines in uw Privécloud.
* Implementeer KMS-servers in uw Azure-abonnement om uw vSAN-gegevens opslag in de Privécloud te versleutelen.
* Gebruik hybride toepassingen waarbij de weblaag van de toepassing wordt uitgevoerd in de AVS-open bare Cloud terwijl de lagen van de toepassing en de data base in uw Privécloud worden uitgevoerd.

## <a name="azure-virtual-network-connection"></a>Virtuele Azure-netwerk verbinding

Persoonlijke Clouds van AVS kunnen worden verbonden met uw Azure-resources met behulp van ExpressRoute. Met de ExpressRoute-verbinding kunt u toegang krijgen tot resources die worden uitgevoerd in uw Azure-abonnement vanuit de Privécloud van uw AVS. Met deze verbinding kunt u uw persoonlijke AVS-Cloud netwerk uitbreiden naar uw virtuele Azure-netwerk. Routes van een AVS-netwerk worden via BGP uitgewisseld met uw virtuele Azure-netwerk. Als u virtuele netwerk peering hebt geconfigureerd, zijn alle gekoppelde virtuele netwerken toegankelijk vanuit uw AVS-netwerk.

![Azure ExpressRoute-verbinding met virtueel netwerk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-verbinding met een on-premises netwerk

U kunt uw bestaande Azure ExpressRoute-circuit aansluiten op uw AVS-regio. De functie ExpressRoute Global Reach wordt gebruikt om de twee circuits met elkaar te verbinden. Er wordt een verbinding tot stand gebracht tussen de on-premises en AVS ExpressRoute-circuits. Deze verbinding stelt u in staat om uw on-premises netwerken uit te breiden naar een een particulier Cloud netwerk van de AVS. Routes van uw AVS-netwerk worden uitgewisseld via BGP met uw on-premises netwerk.

![On-premises ExpressRoute-verbinding-Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Verbinding met een on-premises netwerk en een virtueel Azure-netwerk

Verbindingen met een on-premises netwerk en een virtueel Azure-netwerk kunnen naast uw AVS-netwerk worden gebruikt. De verbinding maakt gebruik van BGP om routes uit te wisselen tussen een on-premises netwerk, een virtueel netwerk van Azure en een AVS-netwerk. Wanneer u uw AVS-netwerk aansluit op uw virtuele Azure-netwerk in de aanwezigheid van een Global Reach verbinding, worden de routes van het virtuele Azure-netwerk weer gegeven in uw on-premises netwerk. Route uitwisseling vindt plaats tussen de Edge-routers in Azure.

![On-premises ExpressRoute-verbinding met Azure Virtual Network-verbinding](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Belang rijke overwegingen

Als u verbinding maakt met een AVS-netwerk vanuit een on-premises netwerk en/of vanuit een virtueel Azure-netwerk, kan route uitwisseling tussen alle netwerken worden ingeschakeld.

* Een virtueel Azure-netwerk is zichtbaar vanuit een on-premises netwerk en een AVS-netwerk.
* Als u verbinding hebt gemaakt met uw virtuele Azure-netwerk vanuit een on-premises netwerk, is verbinding met een AVS-netwerk via Global Reach toegang tot virtuele netwerken vanuit het AVS-netwerk mogelijk.
* Subnetten **mogen niet** overlappen tussen de netwerken die zijn verbonden.
* AVS adverteert **geen** standaard route naar de ExpressRoute-verbindingen
* Als uw on-premises router de standaard route adverteert, zal verkeer van het AVS-netwerk en het virtuele netwerk van Azure de geadverteerde standaard route gebruiken. Als gevolg hiervan is het niet mogelijk om met open bare IP-adressen toegang te krijgen tot virtuele machines in Azure.

## <a name="next-steps"></a>Volgende stappen

* [Virtueel Azure-netwerk verbinden met AVS met behulp van ExpressRoute](virtual-network-connection.md)
* [Verbinding maken tussen on-premises en AVS met ExpressRoute](on-premises-connection.md)
