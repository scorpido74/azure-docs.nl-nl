---
title: Concepten-netwerk-interconnectiviteit
description: Meer informatie over belang rijke aspecten en gebruiks voorbeelden van netwerken en interconnectiviteit in azure VMware-oplossing (AVS)
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c0416da9c745ccf92970ff39f623a782d5784983
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062834"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-oplossing (AVS) Preview-netwerken en interconnectiviteit-concepten

Met netwerk interconnectiviteit tussen uw persoonlijke Clouds van Azure VMware (AVS) en on-premises omgevingen of virtuele netwerken in azure kunt u uw privécloud openen en gebruiken. In dit artikel worden enkele belang rijke concepten besproken die de basis vormen van netwerken en interconnectiviteit.

Een handig perspectief op interconnectiviteit is het overwegen van de twee typen AVS-implementaties voor privécloud:

1. Met [**eenvoudige Azure-interconnectiviteit**](#azure-virtual-network-interconnectivity) kunt u uw privécloud beheren en gebruiken met slechts één virtueel netwerk in Azure. Deze implementatie is het meest geschikt voor AVS-evaluaties of-implementaties die geen toegang nodig hebben van on-premises omgevingen.

1. [**Volledig on-premises naar privécloud interconnectiviteit**](#on-premises-interconnectivity) breidt de basis implementatie van alleen Azure uit om interconnectiviteit toe te voegen tussen on-premises en geavse persoonlijke Clouds.
 
In de onderstaande secties vindt u meer informatie over de vereisten en de twee typen automatische AVS-Cloud interconnectiviteit-implementaties die worden beschreven.

## <a name="avs-private-cloud-use-cases"></a>Gebruiks voorbeelden van privécloud in de Cloud

De use cases voor de persoonlijke Clouds van AVS zijn:
- nieuwe workloads voor virtuele VMware-machines in de Cloud
- VM-werk belasting die is opbursteerd naar de Cloud (alleen voor automatische AVS)
- Migratie van VM-workloads naar de Cloud (alleen on-premises naar AVS)
- herstel na nood geval (AVS naar AVS of on-premises naar AVS)
- verbruik van Azure-Services

 Alle use cases voor de AVS-service zijn ingeschakeld met on-premises verbindingen met de privécloud. 

## <a name="virtual-network-and-expressroute-circuit-requirements"></a>Vereisten voor het virtuele netwerk en het ExpressRoute-circuit
 
Wanneer u een verbinding maakt vanuit een virtueel netwerk in uw abonnement, wordt het ExpressRoute-circuit tot stand gebracht via peering, een autorisatie sleutel en een peering-ID die u in de Azure Portal aanvraagt. De peering is een particuliere, een-op-een-verbinding tussen uw privécloud en het virtuele netwerk.

> [!NOTE] 
> Het ExpressRoute-circuit maakt geen deel uit van een implementatie van een privécloud. Het on-premises ExpressRoute-circuit valt buiten het bereik van dit document. Als u een on-premises verbinding met uw privécloud nodig hebt, kunt u een van uw bestaande ExpressRoute-circuits gebruiken of deze in de Azure Portal aanschaffen.

Wanneer u een privécloud implementeert, ontvangt u IP-adressen voor vCenter en NSX-T-beheer. Voor toegang tot deze beheer interfaces moet u extra resources maken in een virtueel netwerk in uw abonnement. U vindt de procedures voor het maken van deze resources en het tot stand brengen van ExpressRoute-persoonlijke peering in de zelf studies.

Het logische netwerk van de privécloud wordt geleverd met vooraf ingerichte NSX-T. Een laag-0-gateway en een laag-1-gateway is vooraf ingericht voor u. U kunt een segment maken en dit koppelen aan de bestaande gateway van de Tier-1 of koppelen aan de nieuwe laag-1-gateway die u definieert. NSX-T-onderdelen voor logische netwerken bieden Oost-West-connectiviteit tussen workloads en bieden ook een Noord-Zuid-verbinding met het internet en Azure-Services. 

## <a name="routing-and-subnet-requirements"></a>Routerings-en netwerk vereisten

De route ring is op basis van Border Gateway Protocol (BGP), die automatisch wordt ingericht en ingeschakeld voor elke implementatie van een privécloud. Voor de persoonlijke Clouds van AVS bent u verplicht om particuliere cloud netwerk adres ruimten te plannen met een mini maal/22 prefix lengte CIDR-netwerk adres blokken voor subnetten, zoals in de onderstaande tabel wordt weer gegeven. Het adres blok mag niet overlappen met de adres blokken die worden gebruikt in andere virtuele netwerken die zich in uw abonnement en on-premises netwerken bevinden. Binnen dit adres blok worden beheer, inrichting en vMotion-netwerken automatisch ingericht.

Voor beeld van een `/22` CIDR-netwerk adres blok:`10.10.0.0/22`

De subnetten:

| Netwerkgebruik             | Subnet | Voorbeeld        |
| ------------------------- | ------ | -------------- |
| Privécloudbeheer  | `/24`  | `10.10.0.0/24` |
| vMotion-netwerk           | `/24`  | `10.10.1.0/24` |
| VM-workloads              | `/24`  | `10.10.2.0/24` |
| ExpressRoute-peering      | `/24`  | `10.10.3.8/30` |


## <a name="azure-virtual-network-interconnectivity"></a>Azure Virtual Network interconnectiviteit

In het virtuele netwerk naar de privécloud-implementatie kunt u de privécloud van uw AVS beheren, workloads gebruiken in uw privécloud en toegang krijgen tot Azure-Services via de ExpressRoute-verbinding. 

In het onderstaande diagram ziet u de basis netwerk interconnectiviteit die zijn ingesteld op het moment van een implementatie van een privécloud. Hierin wordt het logische, op ExpressRoute gebaseerde netwerk weer gegeven tussen een virtueel netwerk in Azure en een privécloud. De interconnectiviteit voldoet aan drie van de primaire use cases:
* Inkomende toegang tot de vCenter-Server en NSX-T-beheer die toegankelijk is vanaf virtuele machines in uw Azure-abonnement en niet vanaf uw on-premises systemen. 
* Uitgaande toegang van Vm's naar Azure-Services. 
* Inkomende toegang en verbruik van werk belastingen met een privécloud.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Basis-virtueel netwerk naar connectiviteit in de privécloud" border="false":::

## <a name="on-premises-interconnectivity"></a>On-premises interconnectiviteit

In het virtuele netwerk en on-premises naar de volledige implementatie van een privécloud, hebt u toegang tot uw persoonlijke Clouds van uw AVS vanuit on-premises omgevingen. Deze implementatie is een uitbrei ding van de basis implementatie die wordt beschreven in de vorige sectie. Net als bij de basis implementatie is een ExpressRoute-circuit vereist, maar bij deze implementatie wordt het gebruikt om vanuit on-premises omgevingen verbinding te maken met uw privécloud in Azure. 

In het onderstaande diagram ziet u de interconnectiviteit on-premises naar de privécloud, waarmee de volgende gebruiks voorbeelden worden ingeschakeld:
* Warme/koude cross-vCenter vMotion
* On-premises toegang tot beheer van privé-Clouds

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuele netwerken en on-premises volledige particuliere cloud connectiviteit" border="false":::

Voor een volledige interconnectiviteit in uw privécloud schakelt u ExpressRoute Global Reach in en vraagt u een autorisatie sleutel en een privé-peering-ID voor Global Reach op in de Azure Portal. De autorisatie sleutel en de peering-ID worden gebruikt om Global Reach te maken tussen een ExpressRoute-circuit in uw abonnement en het ExpressRoute-circuit voor uw nieuwe privécloud. Zodra de twee ExpressRoute-circuits zijn gekoppeld, worden netwerk verkeer tussen uw on-premises omgevingen gerouteerd naar uw privécloud.  Raadpleeg de [zelf studie voor het maken van een ExpressRoute Global Reach peering naar een privécloud](tutorial-expressroute-global-reach-private-cloud.md) voor de procedures om de autorisatie sleutel en de PEERING-id aan te vragen en te gebruiken.


## <a name="next-steps"></a>Volgende stappen 

De volgende stap is om meer te weten te komen over [persoonlijke Cloud opslag concepten](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
