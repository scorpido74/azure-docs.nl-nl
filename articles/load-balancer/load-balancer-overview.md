---
title: Wat is Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Overzicht van Azure Load Balancer-functies, -architectuur en -implementatie. Meer informatie over hoe de Load Balancer werkt en hoe u deze kunt gebruiken in de Cloud.
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
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045392"
---
# <a name="what-is-azure-load-balancer"></a>Wat is Azure Load Balancer?

*Taak verdeling* verwijst naar een gelijkmatige verdeling van belasting (binnenkomend netwerk verkeer) in een groep back-endservers of servers. Azure biedt [verschillende opties voor taak verdeling](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) die u kunt kiezen op basis van uw behoeften. In dit document worden de Azure Load Balancer beschreven.

Azure Load Balancer werkt op laag 4 van het OSI-model (Open Systems Interconnection). Het is het enige contact punt voor clients. Met Load Balancer worden nieuwe inkomende stromen gedistribueerd die aan de front-end van de load balancer voor de exemplaren van een back-end-groep arriveren. Deze stromen zijn gebaseerd op geconfigureerde taakverdelings regels en status controles. De exemplaren van de back-end-pool kunnen Azure Virtual Machines of exemplaren in een schaalset voor virtuele machines zijn.


Een **[open bare Load Balancer](./concepts-limitations.md#publicloadbalancer)** kan uitgaande verbindingen bieden voor virtuele machines (vm's) in uw virtuele netwerk. Deze verbindingen worden gerealiseerd door hun privé-IP-adressen te vertalen naar open bare IP-adressen. Open bare load balancers worden gebruikt voor het verdelen van het Internet verkeer naar uw Vm's.

Een **[intern (of privé) Load Balancer](./concepts-limitations.md#internalloadbalancer)** wordt alleen gebruikt wanneer privé ip's alleen op het front-end zijn vereist. Interne load balancers worden gebruikt voor het verdelen van verkeer binnen een virtueel netwerk. Een load balancer frontend kan worden geopend vanuit een on-premises netwerk in een hybride scenario.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Afbeelding: toepassingen met meerdere lagen verdelen met behulp van zowel open bare als interne Load Balancer*

Zie [Azure Load Balancer onderdelen en beperkingen](./concepts-limitations.md) voor meer informatie over de afzonderlijke Load Balancer onderdelen

>[!NOTE]
> Micro soft raadt aan [Standard Load Balancer](./load-balancer-standard-overview.md).
Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. Load Balancer en de SKU van het open bare IP-adres moeten overeenkomen wanneer u ze gebruikt met open bare IP-adressen. Load Balancer en open bare IP-Sku's zijn niet onveranderbaar.

## <a name="why-use-azure-load-balancer"></a>Waarom Azure Load Balancer gebruiken?
Met Azure Load Balancer kunt u uw toepassingen schalen en Maxi maal beschik bare Services maken. De Load Balancer ondersteunt zowel binnenkomende als uitgaande scenario's. De Load Balancer biedt lage latentie en hoge door Voer en schaalt Maxi maal miljoenen stromen voor alle TCP-en UDP-toepassingen.

De belangrijkste scenario's die u kunt bereiken met Azure Load Balancer zijn onder andere:

- Taak verdeling van **[intern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** en **[extern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** verkeer naar virtuele machines van Azure.

- Verg root de beschik baarheid door resources te verdelen **[binnen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** en **[tussen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zones.

- Configureer de **[uitgaande connectiviteit](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** voor virtuele Azure-machines.

- Gebruik **[status tests](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** om resources met taak verdeling te bewaken.

- Gebruik **[poort door sturen](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** om toegang te krijgen tot virtuele machines in een virtueel netwerk via een openbaar IP-adres en poort.

- Ondersteuning inschakelen voor **[taak verdeling](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** van **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Profiteer van **[metrische gegevens en diagnostische gegevens](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** voor Azure Load Balancer met **[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Taak verdeling van services op **[meerdere poorten, meerdere IP-adressen of beide](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- Verplaats **[interne](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** en **[externe](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** Load Balancer resources over Azure-regio's.

- Taak verdeling van de TCP-en UDP-stroom op alle poorten tegelijk met behulp van **[ha-poorten](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Prijzen

Gebruik van Standard Load Balancer wordt in rekening gebracht.

* Aantal geconfigureerde taakverdelings-en uitgaande regels. Inkomende NAT-regels tellen niet in het totale aantal regels.
* De hoeveelheid gegevens die binnenkomend en uitgaand is verwerkt, onafhankelijk van regels.

Zie [Load Balancer prijzen](https://azure.microsoft.com/pricing/details/load-balancer/)voor Standard Load Balancer prijs informatie.

Basic Load Balancer wordt gratis aangeboden.

## <a name="sla"></a>SLA

Zie [Sla voor Load Balancer](https://aka.ms/lbsla)voor meer informatie over de Standard Load Balancer sla.

## <a name="next-steps"></a>Volgende stappen

Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer.

Voor meer informatie over Azure Load Balancer beperkingen en onderdelen raadpleegt u [Azure Load Balancer concepten en beperkingen](./concepts-limitations.md)
