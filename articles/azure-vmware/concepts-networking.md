---
title: Concepten-netwerk-interconnectiviteit
description: Meer informatie over belang rijke aspecten en gebruiks voorbeelden van netwerken en interconnectiviteit in azure VMware-oplossing (AVS)
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 35d886fe0f6a68e522d7f2cf20b450b5d9afc199
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629213"
---
# <a name="azure-vmware-solution-avs-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-oplossing (AVS) Preview-netwerken en interconnectiviteit-concepten

Met netwerk interconnectiviteit tussen uw persoonlijke Clouds van Azure VMware (AVS) en on-premises omgevingen of virtuele netwerken in azure kunt u uw privécloud openen en gebruiken. In dit artikel worden enkele belang rijke netwerk-en interconnectiviteit-concepten beschreven die de basis vormen van interconnectiviteit.

Een handig perspectief op interconnectiviteit is het overwegen van de twee typen AVS-implementaties voor privécloud: implementaties met eenvoudige Azure-interconnectiviteit en-implementaties, met volledige on-premises naar privécloud interconnectiviteit.

De use cases voor de persoonlijke Clouds van AVS zijn:
- nieuwe workloads voor virtuele VMware-machines in de Cloud
- VM-werk belasting bursting naar de Cloud
- Migratie VM-werk belasting naar de Cloud
- herstel na nood geval
- verbruik van Azure-Services

 Alle use cases voor de AVS-service zijn ingeschakeld met on-premises verbindingen met de privécloud. Het basis interconnectiviteit model is het meest geschikt voor AVS-evaluaties of-implementaties die geen toegang nodig hebben tot on-premises omgevingen.

De twee typen AVS-interconnectiviteit worden in de onderstaande secties beschreven.  De meest eenvoudige interconnectiviteit is ' Azure Virtual Network-connectiviteit '; u kunt hiermee uw privécloud beheren en gebruiken met slechts één virtueel netwerk in Azure. De interconnectiviteit die wordt beschreven in ' on-premises connectiviteit ', breidt de connectiviteit van het virtuele netwerk uit om ook interconnectiviteit te omvatten tussen on-premises omgevingen en persoonlijke Clouds van AVS.

## <a name="azure-virtual-network-interconnectivity"></a>Azure Virtual Network interconnectiviteit

Het basis netwerk interconnectiviteit dat is ingesteld op het moment van een implementatie van een privécloud, wordt weer gegeven in het onderstaande diagram. Hierin wordt het logische, op ExpressRoute gebaseerde netwerk weer gegeven tussen een virtueel netwerk in Azure en een privécloud. De interconnectiviteit voldoet aan drie van de primaire use cases:
- Inkomende toegang tot beheer netwerken waarin de vCenter-Server en NSX-T-beheer zich bevinden.
    - Toegankelijk vanaf Vm's binnen uw Azure-abonnement, niet vanaf uw on-premises systemen.
- Uitgaande toegang van Vm's naar Azure-Services.
- Inkomende toegang en verbruik van werk belastingen met een privécloud.

![Eenvoudige virtuele netwerk-naar-particuliere cloud connectiviteit](./media/concepts/adjacency-overview-drawing-single.png)

Het ExpressRoute-circuit in dit virtuele netwerk naar een privécloud wordt gemaakt wanneer u een verbinding maakt van een virtueel netwerk in uw abonnement op het ExpressRoute-circuit van uw privécloud. De peering gebruikt een autorisatie sleutel en een circuit-ID die u aanvraagt in de Azure Portal. De ExpressRoute-verbinding die via de peering tot stand is gebracht, is een particuliere, een-op-een-verbinding tussen uw privécloud en het virtuele netwerk. U kunt uw privécloud beheren, werk belastingen gebruiken in uw privécloud en toegang krijgen tot Azure-Services via die ExpressRoute-verbinding.

Wanneer u een automatische AVS-Cloud implementeert, is één/22 adres ruimte voor privé-netwerken vereist. Deze adres ruimte mag niet overlappen met adres ruimten die worden gebruikt in andere virtuele netwerken in uw abonnement. Binnen deze adres ruimte worden beheer-, inrichting-en vMotion-netwerken automatisch ingericht. De route ring is gebaseerd op BGP en wordt automatisch ingericht en ingeschakeld voor elke implementatie van een privécloud.

Wanneer er een privécloud wordt geïmplementeerd, krijgt u de IP-adressen voor vCenter en NSX-T-beheer. Voor toegang tot deze beheer interfaces maakt u extra resources in een virtueel netwerk in uw abonnement. De procedures voor het maken van deze resources en het tot stand brengen van ExpressRoute persoonlijke peering zijn opgenomen in de zelf studies.

U ontwerpt de logische netwerken voor de privécloud en implementeert deze met NSX-T. De privécloud wordt geleverd met vooraf ingerichte NSX-T. Een laag-0-gateway & laag-1-gateway is vooraf ingericht voor de u. U kunt een segment maken en dit koppelen aan de bestaande gateway van de laag 1 of koppelen aan een nieuwe gateway voor laag 1 die u kunt definiëren. NSX-T-onderdelen voor logische netwerken bieden Oost-West-connectiviteit tussen workloads en bieden ook een Noord-Zuid-verbinding met het internet en Azure-Services. 

## <a name="on-premises-interconnectivity"></a>On-premises interconnectiviteit

U kunt ook on-premises omgevingen verbinden met uw persoonlijke AVS-Clouds. Dit type interconnectiviteit is een uitbrei ding van de Basic-interconnectiviteit die wordt beschreven in de vorige sectie.

![virtuele netwerken en on-premises volledige particuliere cloud connectiviteit](./media/concepts/adjacency-overview-drawing-double.png)

Als u een volledige interconnectiviteit wilt instellen voor een privécloud, gebruikt u de Azure Portal om ExpressRoute Global Reach in te scha kelen tussen een ExpressRoute circuit van de privécloud en een on-premises ExpressRoute-circuit. Deze configuratie breidt de basis connectiviteit uit om toegang te bieden tot persoonlijke Clouds vanuit on-premises omgevingen.

Er is een on-premises naar Azure Virtual Network ExpressRoute-circuit vereist om verbinding te maken tussen on-premises omgevingen en uw privécloud in Azure. Dit ExpressRoute-circuit bevindt zich in uw abonnement en maakt geen deel uit van een implementatie van een privécloud. Het on-premises ExpressRoute-circuit valt buiten het bereik van dit document. Als u een on-premises verbinding met uw privécloud nodig hebt, kunt u een van uw bestaande ExpressRoute-circuits gebruiken of deze in de Azure Portal aanschaffen.

Als de twee ExpressRoute-circuits zijn gekoppeld aan Global Reach, routert het netwerk verkeer tussen uw on-premises omgevingen en uw privécloud. De on-premises naar de privécloud interconnectiviteit wordt weer gegeven in het voor gaande diagram. De interconnectiviteit die in het diagram wordt weer gegeven, maakt gebruik van de volgende gebruiks voorbeelden:

- Warme/koude cross-vCenter vMotion
- On-premises to AVS Private Cloud Management Access

Als u de volledige connectiviteit wilt inschakelen, kunt u in de Azure Portal een autorisatie sleutel en een privé-peering-ID voor Global Reach aanvragen. U gebruikt de sleutel en ID om Global Reach te maken tussen een ExpressRoute-circuit in uw abonnement en het ExpressRoute-circuit voor uw nieuwe privécloud. De [zelf studie voor het maken van een privécloud](tutorial-create-private-cloud.md) bevat de procedures voor het aanvragen en gebruiken van de sleutel en id.

Voor de routerings vereisten van de oplossing moet u particuliere-cloud netwerk adres ruimten plannen, zodat u geen overlapt met andere virtuele netwerken en on-premises netwerken. Voor persoonlijke Clouds van AVS is mini maal een `/22` CIDR-netwerk adres blok vereist voor subnetten, zoals hieronder wordt weer gegeven. Dit netwerk vormt een aanvulling op uw on-premises netwerken. Als u verbinding wilt maken met on-premises omgevingen en virtuele netwerken, moet dit een niet-overlappend netwerk adres blok zijn.

Voor beeld van een `/22` CIDR-netwerk adres blok:`10.10.0.0/22`

De subnetten:

| Netwerkgebruik             | Subnet | Voorbeeld        |
| ------------------------- | ------ | -------------- |
| Privécloudbeheer            | `/24`    | `10.10.0.0/24`   |
| vMotion-netwerk       | `/24`    | `10.10.1.0/24`   |
| VM-workloads | `/24`   | `10.10.2.0/24`   |
| ExpressRoute-peering | `/24`    | `10.10.3.8/30`   |

## <a name="next-steps"></a>Volgende stappen 

De volgende stap is om meer te weten te komen over [persoonlijke Cloud opslag concepten](concepts-storage.md).

<!-- LINKS - external -->
[enable Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach

<!-- LINKS - internal -->

