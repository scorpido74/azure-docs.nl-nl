---
title: Concepten-netwerk-interconnectiviteit
description: Meer informatie over belang rijke aspecten en gebruiks voorbeelden van netwerken en interconnectiviteit in azure VMware-oplossing.
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3420f6aa61ced7632175f3e12edda9de72639517
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750562"
---
# <a name="azure-vmware-solution-preview-networking-and-interconnectivity-concepts"></a>Azure VMware-oplossing preview netwerken en interconnectiviteit-concepten

Met de Azure VMware-oplossing (AVS) beschikt u over een persoonlijke VMware-cloud omgeving die toegankelijk is voor gebruikers en toepassingen van on-premises en op Azure gebaseerde omgevingen of resources. Services, zoals Azure ExpressRoute en VPN-verbindingen, leveren de connectiviteit. Voor deze services zijn specifieke netwerk adresbereiken en Firewall poorten vereist voor het inschakelen van de services.  

Wanneer u een privécloud implementeert, worden particuliere netwerken voor beheer, inrichting en vMotion Get gemaakt. Ze worden gebruikt voor toegang tot de vCenter-en NSX-T-beheer-en virtual machine vMotion of-implementatie. Alle particuliere netwerken zijn toegankelijk vanuit een VNet in azure of vanuit on-premises omgevingen. ExpressRoute Global Reach wordt gebruikt om privéclouds te verbinden met on-premises omgevingen en voor deze verbinding is een VNet met een ExpressRoute-circuit in uw abonnement vereist.

Wanneer u een privécloud implementeert, worden de toegang tot internet en Azure-Services ook ingericht en verstrekt, zodat Vm's op productie netwerken deze kunnen gebruiken.  Internet toegang is standaard uitgeschakeld voor nieuwe persoonlijke Clouds en kan op elk gewenst moment worden ingeschakeld of uitgeschakeld.

Een handig perspectief op interconnectiviteit is het overwegen van de twee typen AVS-implementaties voor privécloud:

1. Met [**eenvoudige Azure-interconnectiviteit**](#azure-virtual-network-interconnectivity) kunt u uw privécloud beheren en gebruiken met slechts één virtueel netwerk in Azure. Deze implementatie is het meest geschikt voor AVS-evaluaties of-implementaties die geen toegang nodig hebben van on-premises omgevingen.

1. [**Volledig on-premises naar privécloud interconnectiviteit**](#on-premises-interconnectivity) breidt de basis implementatie van alleen Azure uit om interconnectiviteit toe te voegen tussen on-premises en geavse persoonlijke Clouds.
 
In dit artikel worden enkele belang rijke concepten besproken waarmee netwerken en interconnectiviteit worden gemaakt, met inbegrip van vereisten en beperkingen. We gaan ook meer informatie over de twee typen AVS interconnectiviteit-implementaties in de privécloud. In dit artikel vindt u de informatie die u nodig hebt om uw netwerk te configureren om met AVS op de juiste manier te werken.

## <a name="avs-private-cloud-use-cases"></a>Gebruiks voorbeelden van privécloud in de Cloud

De use cases voor de persoonlijke Clouds van AVS zijn:
- Nieuwe workloads voor virtuele VMware-machines in de Cloud
- VM-werk belasting die is opbursteerd naar de Cloud (alleen voor automatische AVS)
- Migratie van VM-workloads naar de Cloud (alleen on-premises naar AVS)
- Herstel na nood geval (AVS naar AVS of on-premises naar AVS)
- Verbruik van Azure-Services

> [!TIP]
> Alle use cases voor de AVS-service zijn ingeschakeld met on-premises verbindingen met de privécloud.

## <a name="azure-virtual-network-interconnectivity"></a>Azure Virtual Network interconnectiviteit

In het virtuele netwerk naar de privécloud-implementatie kunt u de privécloud van Azure VMware-oplossingen beheren, werk belastingen gebruiken in uw privécloud en toegang krijgen tot Azure-Services via de ExpressRoute-verbinding. 

In het onderstaande diagram ziet u de basis netwerk interconnectiviteit die zijn ingesteld op het moment van een implementatie van een privécloud. Hierin wordt het logische, op ExpressRoute gebaseerde netwerk weer gegeven tussen een virtueel netwerk in Azure en een privécloud. De interconnectiviteit voldoet aan drie van de primaire use cases:
* Inkomende toegang tot de vCenter-Server en NSX-T-beheer die toegankelijk is vanaf virtuele machines in uw Azure-abonnement en niet vanaf uw on-premises systemen. 
* Uitgaande toegang van Vm's naar Azure-Services. 
* Inkomende toegang en verbruik van werk belastingen met een privécloud.

:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Basis-virtueel netwerk naar connectiviteit in de privécloud" border="false":::

## <a name="on-premises-interconnectivity"></a>On-premises interconnectiviteit

In het virtuele netwerk en on-premises naar volledige privécloud-implementatie hebt u toegang tot uw persoonlijke Clouds van Azure VMware-oplossingen vanuit on-premises omgevingen. Deze implementatie is een uitbrei ding van de basis implementatie die wordt beschreven in de vorige sectie. Net als bij de basis implementatie is een ExpressRoute-circuit vereist, maar bij deze implementatie wordt het gebruikt om vanuit on-premises omgevingen verbinding te maken met uw privécloud in Azure. 

In het onderstaande diagram ziet u de interconnectiviteit on-premises naar de privécloud, waarmee de volgende gebruiks voorbeelden worden ingeschakeld:
* Warme/koude cross-vCenter vMotion
* On-premises naar Azure VMware-oplossing persoonlijke Cloud beheer toegang

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Virtuele netwerken en on-premises volledige particuliere cloud connectiviteit" border="false":::

Voor een volledige interconnectiviteit in uw privécloud schakelt u ExpressRoute Global Reach in en vraagt u een autorisatie sleutel en een privé-peering-ID voor Global Reach op in de Azure Portal. De autorisatie sleutel en de peering-ID worden gebruikt om Global Reach te maken tussen een ExpressRoute-circuit in uw abonnement en het ExpressRoute-circuit voor uw nieuwe privécloud. Zodra de twee ExpressRoute-circuits zijn gekoppeld, worden netwerk verkeer tussen uw on-premises omgevingen gerouteerd naar uw privécloud.  Raadpleeg de [zelf studie voor het maken van een ExpressRoute Global Reach peering naar een privécloud](tutorial-expressroute-global-reach-private-cloud.md) voor de procedures om de autorisatie sleutel en de PEERING-id aan te vragen en te gebruiken.

## <a name="next-steps"></a>Volgende stappen 

- Meer informatie over de [overwegingen en vereisten voor de netwerk verbinding](tutorial-network-checklist.md). 
- Meer informatie over [concepten voor persoonlijke Cloud opslag](concepts-storage.md).


<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->

