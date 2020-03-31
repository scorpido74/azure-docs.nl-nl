---
title: Azure VMware-oplossing van CloudSimple - Kies een oplossing voor het balanceren van de lastenafweging voor CloudSimple Private Clouds
description: Beschrijft de opties voor het implementeren van een toepassing in een private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014875"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Kies een oplossing voor het balanceren van de lastenvoor CloudSimple Private Clouds

Wanneer u een toepassing implementeert in een CloudSimple Private Cloud, u een van de verschillende opties kiezen voor taakverdeling.

U een virtuele of op software gebaseerde load balancer kiezen in uw CloudSimple-privécloud of zelfs Azure L7-load balancer gebruiken die in uw Azure-abonnement wordt uitgevoerd om uw webtier VM's te front-enden die worden uitgevoerd in de CloudSimple Private Cloud. Hier zetten we een aantal opties op een rij:

## <a name="virtual-load-balancers"></a>Virtuele load balancers

U virtuele load balancer-apparaten implementeren in uw VMware-omgeving via de vCenter-interface en deze configureren om uw toepassingsverkeer vooraf te beheren.

Enkele populaire leveranciers zijn: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7-loadbalancer

Wanneer u Azure Application Gateway gebruikt als L7-loadbalancer voor uw toepassing die wordt uitgevoerd in een Private Cloud, hoeft u de load balancer-software niet te beheren. De load balancer-software wordt beheerd door Azure. Alle webtierVM's in de Private Cloud gebruiken privé-IP-adressen en vereisen geen extra NAT-regels of openbare IP-adressen om namen op te lossen. Vm's met weblagen communiceren met de Azure Application Gateway via een privéverbinding met lage latentie met hoge bandbreedte.

Raadpleeg de oplossingshandleiding voor het gebruik van Azure Application Gateway als L7-loadbalancer voor meer informatie over het configureren van deze oplossing.

## <a name="azure-internal-load-balancer"></a>Azure interne load balancer

Als u ervoor kiest om uw toepassing uit te voeren in een hybride implementatie waarbij de front-endlaag van het web binnen een Azure vNet wordt uitgevoerd in uw Azure-abonnement en de DB-laag van de toepassing wordt uitgevoerd in VMware VM's in CloudSimple Private Cloud, u interne belasting van Azure gebruiken balancer (L4 load balancer) voor uw DB-tier VM's voor verkeersbeheer.

Zie Azure Internal [Load Balancer-documentatie](../load-balancer/concepts-limitations.md#internalloadbalancer) voor meer informatie.

## <a name="global-server-load-balancer"></a>Global server load balancer

Als u op zoek bent naar een op DNS gebaseerde load balancer, u oplossingen van derden gebruiken die beschikbaar zijn in Azure Marketplace of voor de native Azure-oplossing gaan.

Azure Traffic Manager is een op DNS gebaseerde verkeerslastenbalancer waarmee u verkeer optimaal distribueren naar services over wereldwijde Azure-regio's en on-premises, terwijl u tegelijkertijd een hoge beschikbaarheid en responsiviteit biedt. Zie Azure Traffic [Manager-documentatie](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) voor meer informatie.
