---
title: 'Azure VMware-oplossingen (AVS): Kies een taakverdelings oplossing voor persoonlijke Clouds van AVS'
description: Hierin worden de opties voor taak verdeling beschreven voor het implementeren van een toepassing in een geavse Privécloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014875"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Een taakverdelings oplossing kiezen voor persoonlijke Clouds van AVS

Wanneer u een toepassing implementeert in een Privécloud in de Cloud, kunt u een van de verschillende opties voor taak verdeling kiezen.

U kunt een virtuele-of software-load balancer in de privécloud van uw AVS kiezen of Azure N7 gebruiken load balancer die worden uitgevoerd in uw Azure-abonnement om de front-end van uw weblaag in de Privécloud te starten. Hier worden enkele opties vermeld:

## <a name="virtual-load-balancers"></a>Virtuele load balancers

U kunt virtuele load balancer-apparaten implementeren in uw VMware-omgeving via de vCenter-interface en ze configureren om het verkeer van de front-end van uw toepassing te beëindigen.

Enkele populaire leveranciers zijn: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure N7-load balancer

Wanneer u Azure-toepassing gateway als een N7-load balancer gebruikt voor uw toepassing die wordt uitgevoerd in een AVS-Privécloud, hoeft u de load balancer software niet te beheren. De load balancer-software wordt beheerd door Azure. Alle virtuele machines in de AVS-privécloud gebruiken privé-IP-adressen en vereisen geen aanvullende NAT-regels of open bare Ip's-adressen om namen om te zetten. Vm's met een weblaag communiceren met de Azure-toepassing-gateway via een particuliere verbinding met lage latentie en een hoge band breedte.

Voor meer informatie over het configureren van deze oplossing raadpleegt u de hand leiding voor oplossingen over het gebruik van Azure-toepassing gateway als een N7-load balancer.

## <a name="azure-internal-load-balancer"></a>Interne load balancer van Azure

Als u ervoor kiest om uw toepassing uit te voeren in een hybride implementatie waarbij de web-front-end-laag wordt uitgevoerd in een Azure-vNet in uw Azure-abonnement en de DB-laag van de toepassing wordt uitgevoerd in VMware-Vm's in de Privécloud-Cloud, kunt u Azure Internal load balancer gebruiken (L 4 load balancer) vóór de Vm's van de DB-laag voor verkeer beheer.

Zie de documentatie van Azure [Internal Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) voor meer informatie.

## <a name="global-server-load-balancer"></a>load balancer globale server

Als u op zoek bent naar een op DNS gebaseerde load balancer, kunt u gebruikmaken van oplossingen van derden die beschikbaar zijn in azure Marketplace of met de systeem eigen Azure-oplossing.

Azure Traffic Manager is een op DNS gebaseerd verkeer load balancer waarmee u verkeer optimaal kunt distribueren naar Services in wereld wijde Azure-regio's en on-premises, met hoge Beschik baarheid en reactie tijd. Zie Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) -documentatie voor meer informatie.
