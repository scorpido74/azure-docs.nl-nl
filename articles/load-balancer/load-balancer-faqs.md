---
title: Veelgestelde vragen-Azure Load Balancer
description: Antwoorden op veelgestelde vragen over de Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 4ae15d0898cedb0ed17dc308584769395aa819c2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85079473"
---
# <a name="frequently-asked-questions"></a>Veelgestelde vragen

## <a name="what-types-of-load-balancer-exist"></a>Welke soorten Load Balancer bestaan?
Interne load balancers die verkeer verdelen binnen een VNET en externe load balancers die verkeer van en naar een Internet verbonden eind punt balanceren. Zie [Load Balancer typen](components.md#frontend-ip-configurations)voor meer informatie. 

Voor beide typen biedt Azure een basis-SKU en een standaard-SKU met verschillende functies voor functionaliteit, prestaties, beveiliging en status bijhouden. Deze verschillen worden beschreven in het artikel over het vergelijken van de [SKU](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hoe kan ik een upgrade uitvoeren van een basis versie naar een Standard Load Balancer?
Zie de [upgrade van Basic naar Standard](upgrade-basic-standard.md) -artikel voor een geautomatiseerd script en richt lijnen voor het bijwerken van een load BALANCER-SKU.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Wat zijn de verschillende opties voor taak verdeling in azure?
Zie de [Load Balancer technologie handleiding](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) voor de beschik bare taakverdelings Services en aanbevolen gebruik voor elk.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Waar vind ik Load Balancer ARM-sjablonen?
Bekijk de [lijst met Azure Load Balancer Quick](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) start-sjablonen voor arm-sjablonen van algemene implementaties.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Wat is het verschil tussen binnenkomende NAT-regels en de regels voor taak verdeling?
NAT-regels worden gebruikt om een back-end-bron op te geven voor het routeren van verkeer naar. U kunt bijvoorbeeld een specifieke load balancer poort configureren voor het verzenden van RDP-verkeer naar een specifieke virtuele machine. Taakverdelings regels worden gebruikt om een pool van back-endservers op te geven voor het routeren van verkeer naar, waarbij de belasting voor elk exemplaar wordt verdeeld. Een load balancer regel kan bijvoorbeeld TCP-pakketten routeren op poort 80 van de load balancer in een groep webservers.

## <a name="what-is-ip-1686312916"></a>Wat is IP-168.63.129.16?
Het virtuele IP-adres voor de host die als Azure-infra structuur is gelabeld Load Balancer waar de Azure Health-tests afkomstig zijn. Bij het configureren van back-end-exemplaren, moeten ze verkeer van dit IP-adres toestaan om te reageren op status controles. Deze regel communiceert niet met toegang tot uw Load Balancer frontend. Als u geen gebruik maakt van de Azure Load Balancer, kunt u deze regel onderdrukken. Meer informatie over service Tags vindt u [hier](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-blancer"></a>Kan ik globale VNET-peering gebruiken met Basic Load Blancer?
Nee. Basis Load Balancer biedt geen ondersteuning voor globale VNET-peering. U kunt in plaats daarvan een Standard Load Balancer gebruiken. Zie de [upgrade van Basic naar Standard](upgrade-basic-standard.md) -artikel voor naadloze upgrade.

## <a name="next-steps"></a>Volgende stappen
Als uw vraag hier niet wordt vermeld, kunt u met uw vraag feedback over deze pagina sturen. Hiermee maakt u een GitHub-probleem voor het product team om ervoor te zorgen dat al onze vragen van de gewaardeerde klant worden beantwoord.
