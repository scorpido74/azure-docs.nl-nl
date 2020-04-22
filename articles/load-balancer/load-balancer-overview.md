---
title: Wat is Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Overzicht van Azure Load Balancer-functies, -architectuur en -implementatie. Ontdek hoe de Load Balancer werkt en hoe u deze in de cloud gebruiken.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 8596b435ffa02da7daf4ef98bfe0fe7995b9270a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768192"
---
# <a name="what-is-azure-load-balancer"></a>Wat is Azure Load Balancer?

*Load balancing* verwijst naar het gelijkmatig verdelen van load (binnenkomend netwerkverkeer) over een groep backend resources of servers. 

Azure Load Balancer werkt op laag vier van het OSI-model (Open Systems Interconnection). Het is het enige aanspreekpunt voor klanten. Load Balancer verdeelt binnenkomende stromen die aankomen bij de front-end van de load balancer naar backend pool exemplaren. Deze stromen zijn volgens geconfigureerde load balancing regels en health probes. De backendpool-exemplaren kunnen Azure Virtual Machines of instances in een virtuele machineschaalset zijn.

Een **[openbare load balancer](./concepts-limitations.md#publicloadbalancer)** kan uitgaande verbindingen bieden voor virtuele machines (VM's) binnen uw virtuele netwerk. Deze verbindingen worden bereikt door hun privé IP-adressen te vertalen naar openbare IP-adressen. Openbare load balancers worden gebruikt om het saldo internetverkeer naar uw VM's te laden.

Er wordt een **[interne (of particuliere) load balancer](./concepts-limitations.md#internalloadbalancer)** gebruikt waar alleen privé-IP's aan de frontend nodig zijn. Interne load balancers worden gebruikt om balansverkeer binnen een virtueel netwerk te laden. Een frontend van de load balancer is toegankelijk vanaf een on-premises netwerk in een hybride scenario.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Figuur: Multi-tier toepassingen in evenwicht brengen met zowel openbare als interne load balancer*

Zie [Azure Load Balancer-componenten en -beperkingen](./concepts-limitations.md) voor meer informatie over de afzonderlijke onderdelen van de load balancer

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Zie [Wat is Azure Application Gateway?](../application-gateway/overview.md) Zie [Wat is Traffic Manager als](../traffic-manager/traffic-manager-overview.md) u op zoek bent naar globale DNS-taakverdeling? Uw end-to-end scenario's kunnen baat hebben bij het combineren van deze oplossingen.
>
> Zie [Overzicht van opties voor taakverdeling in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking met azure-taakverdelingsopties.

## <a name="why-use-azure-load-balancer"></a>Waarom Azure Load Balancer gebruiken?
Met Standard Load Balancer u uw toepassingen schalen en zeer beschikbare services maken. Load balancer ondersteunt zowel inkomende als uitgaande scenario's. Load balancer biedt lage latentie en hoge doorvoer en schaalt tot miljoenen stromen voor alle TCP- en UDP-toepassingen.

Belangrijke scenario's die u uitvoeren met Standard Load Balancer zijn:

- Interne **[en](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** **[externe](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** verkeer van laadbalans naar virtuele Azure-machines.

- Verhoog de beschikbaarheid door resources binnen en **[tussen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zones **[te](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** distribueren.

- U **[configureer uitgaande connectiviteit](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** voor virtuele Azure-machines.

- Gebruik **[statussondes](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** om de belastingsbalansbronnen te controleren.

- Gebruik **[port forwarding](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** om toegang te krijgen tot virtuele machines in een virtueel netwerk via openbare IP-adres en poort.

- Ondersteuning inschakelen voor **[het balanceren](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** van **[IPv6.](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**

- Standard Load Balancer biedt multidimensionale statistieken via [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)  Deze statistieken kunnen worden gefilterd, gegroepeerd en uitgesplitst voor een bepaalde dimensie.  Ze bieden actuele en historische inzichten in prestaties en gezondheid van uw service.  Resource Health wordt ook ondersteund. Bekijk **[Standaard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md)** voor meer details.

- Load balance services op **[meerdere poorten, meerdere IP-adressen of beide.](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**

- Interne **[internal](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** en **[externe](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** resources voor lastenbalans en Azure verplaatsen.

- Load balance TCP en UDP stromen op alle poorten tegelijk met **[HA-poorten.](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)**

### <a name="secure-by-default"></a><a name="securebydefault"></a>Standaardbeveiliging

Standard Load Balancer is gebouwd op het zero trust netwerk beveiligingsmodel in de kern. Standard Load Balancer is standaard beveiligd en maakt deel uit van uw virtuele netwerk. Het virtuele netwerk is een privé en geïsoleerd netwerk.  Dit betekent dat standaardloadbalancers en standaard IP-adressen voor openbare gegevens zijn gesloten voor binnenkomende stromen, tenzij ze worden geopend door netwerkbeveiligingsgroepen. NSG's worden gebruikt om toegestaan verkeer expliciet toe te staan.  Als u geen NSG op een subnet of NIC van uw virtuele machinebron hebt, is het verkeer niet toegestaan om deze bron te bereiken. Zie [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md)voor meer informatie over NSGs en hoe u deze toepassen voor uw scenario.
Basic Load Balancer is standaard open voor internet.


## <a name="pricing-and-sla"></a>Prijzen en SLA

Zie Prijsgegevens van load [balancer](https://azure.microsoft.com/pricing/details/load-balancer/)voor prijsinformatie voor standaardlastbalansen .
Basic Load Balancer wordt gratis aangeboden.
Zie [SLA voor load balancer](https://aka.ms/lbsla). Basic Load Balancer heeft geen SLA.

## <a name="next-steps"></a>Volgende stappen
Zie [Een basisbalans upgrade om](upgrade-basic-standard.md) Basic Load Balancer te upgraden naar Standard Load Balancer.

Zie [Een openbare standaardbalanservoor maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een load balancer.

Zie Azure [Load Balancer-concepten en -beperkingen](./concepts-limitations.md) voor meer informatie over azure load balancer-beperkingen
