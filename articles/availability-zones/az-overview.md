---
title: Wat zijn Azure-beschikbaarheidszones? | Microsoft Docs
description: Als u Maxi maal beschik bare en flexibele toepassingen in azure wilt maken, Beschikbaarheidszones u fysiek afzonderlijke locaties bieden die u kunt gebruiken om uw resources uit te voeren.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cfe7374525486f7b4fc1b6cb89e40f2eaeff6737
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027165"
---
# <a name="what-are-availability-zones-in-azure"></a>Wat zijn Beschikbaarheidszones in azure?
Beschikbaarheidszones is een aanbieding met hoge Beschik baarheid die uw toepassingen en gegevens beveiligt tegen Data Center-fouten. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zone-redundante Services repliceren uw toepassingen en gegevens op Beschikbaarheidszones om te beschermen tegen enkele punten van een storing. Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Bouw hoge Beschik baarheid in uw toepassings architectuur door uw berekenings-, opslag-, netwerk-en gegevens bronnen in een zone te plaatsen en te repliceren in andere zones. Azure-Services die ondersteuning bieden aan Beschikbaarheidszones, worden onderverdeeld in twee categorieën:

- **Zonegebonden Services** : u kunt de resource vastmaken aan een specifieke zone (bijvoorbeeld virtuele machines, beheerde schijven, standaard-IP-adressen) of
- **Zone-redundante Services** : het platform repliceert automatisch over zones (bijvoorbeeld zone-redundante opslag, SQL database).

Als u een uitgebreide bedrijfs continuïteit wilt bereiken op Azure, bouwt u uw toepassings architectuur met de combi natie van Beschikbaarheidszones met Azure-regio paren. U kunt uw toepassingen en gegevens synchroon repliceren met Beschikbaarheidszones binnen een Azure-regio voor hoge Beschik baarheid en asynchroon repliceren in azure-regio's voor nood herstel beveiliging.
 
![Conceptueel overzicht van één zone die in een regio wordt weer gegeven](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> De id's van de beschikbaarheids zone (de getallen 1, 2 en 3 in de bovenstaande afbeelding) worden logisch toegewezen aan de werkelijke fysieke zones voor elk abonnement, onafhankelijk van elkaar. Dit betekent dat Beschik baarheid Zone 1 in een bepaald abonnement kan verwijzen naar een andere fysieke zone dan de beschik baarheid Zone 1 in een ander abonnement. Als gevolg hiervan is het raadzaam om geen beschikbaarheids zone-Id's over verschillende abonnementen door te geven voor plaatsing van virtuele machines.

## <a name="services-support-by-region"></a>Services ondersteunen per regio

De combi Naties van Azure-Services en-regio's die ondersteuning bieden voor Beschikbaarheidszones zijn:


|                                 |Noord- en Zuid-Amerika |              |           |           | Europa |              |          |              | Azië-Pacific |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |VS - centraal|VS - oost|VS - oost 2|VS - west 2|Frankrijk - centraal|Europa - noord|VK - zuid|Europa - west|Japan - Oost|Azië - zuidoost|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Virtuele Linux-machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Windows Virtual Machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zone-redundante opslag          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Netwerken**                     |            |              |           |           |                |              |          |             |            |                |
| Standaard-IP-adres        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standaardversie van Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute-gateway   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Databases**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;Evaluatie      | &#10003;       |
| Azure Cache voor Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analytische gegevens**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Opneming**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (alleen Premium-laag) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identity**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Tolerantie van services
Alle Azure Management-Services zijn ontworpen om te worden flexibeler dan storingen op regio niveau. In het spectrum van fouten, hebben een of meer fouten in de beschikbaarheids zone binnen een regio een kleinere fout RADIUS vergeleken met een volledige regio fout. Azure kan worden hersteld van een storing op zone niveau voor beheer services binnen de regio of vanuit een andere Azure-regio. Azure voert een kritieke onderhouds bewerking uit binnen een regio, om te voor komen dat er fouten optreden die van invloed zijn op klant resources die zijn geïmplementeerd op Beschikbaarheidszones binnen een regio.

## <a name="pricing"></a>Prijzen
Er zijn geen extra kosten verbonden aan het implementeren van virtuele machines in een beschikbaarheids zone. SLA voor de VM-uptime van 99,99% wordt aangeboden wanneer er twee of meer virtuele machines zijn geïmplementeerd in twee of meer Beschikbaarheidszones binnen een Azure-regio. Er zijn extra kosten voor de gegevens overdracht van VM-naar-VM-zone met een andere Beschik baarheid. Bekijk de pagina met [bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/) voor meer informatie.


## <a name="get-started-with-availability-zones"></a>Aan de slag met Beschikbaarheidszones
- [Maak een virtuele machine](../virtual-machines/windows/create-portal-availability-zone.md)
- [Een beheerde schijf toevoegen met Power shell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Een door een zone redundante schaalset voor virtuele machines maken](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Taak verdeling van virtuele machines in zones met behulp van een Standard Load Balancer met een zone-redundante front-end](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Taak verdeling van Vm's binnen een zone met behulp van een Standard Load Balancer met een zonegebonden-front-end](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zone-redundante opslag](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geo-noodherstel Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geo-noodherstel Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Een zone-redundante virtuele netwerkgateway maken](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zone redundante regio voor Azure Cosmos DB toevoegen](../cosmos-db/high-availability.md#availability-zone-support)
- [Aan de slag met Azure cache voor redis Beschikbaarheidszones](https://aka.ms/redis/az/getstarted)
- [Een Azure Active Directory Domain Services-exemplaar maken](../active-directory-domain-services/tutorial-create-instance.md)

## <a name="next-steps"></a>Volgende stappen
- [Snelstartsjablonen](https://aka.ms/azqs)
