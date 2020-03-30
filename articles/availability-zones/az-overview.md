---
title: Wat zijn Azure Availability Zones?
description: Als u zeer beschikbare en veerkrachtige toepassingen in Azure wilt maken, bieden beschikbaarheidszones fysiek gescheiden locaties die u gebruiken om uw resources uit te voeren.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 9f1b0f1885019c75252a4c5b8333f342660fac0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057272"
---
# <a name="what-are-availability-zones-in-azure"></a>Wat zijn beschikbaarheidszones in Azure?
Availability Zones is een aanbod met hoge beschikbaarheid dat uw toepassingen en gegevens beschermt tegen datacenterstoringen. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zoneredundante services repliceren uw toepassingen en gegevens in beschikbaarheidszones om te beschermen tegen single-points-of-failure. Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

Een beschikbaarheidszone in een Azure-regio is een combinatie van een foutdomein en een updatedomein. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Bouw hoge beschikbaarheid in uw toepassingsarchitectuur door uw gegevensbronnen, opslag, netwerking en gegevensbronnen binnen een zone te plaatsen en te repliceren in andere zones. Azure-services die ondersteuning bieden voor beschikbaarheidszones worden onderverdeeld in twee categorieën:

- **Zonale services** – u maakt de resource vast aan een specifieke zone (bijvoorbeeld virtuele machines, beheerde schijven, standaard IP-adressen), of
- **Zone-redundante services**: het platform wordt automatisch gerepliceerd in verschillende zones (bijvoorbeeld: zone-redundante opslag, SQL-database).

Als u uitgebreide bedrijfscontinuïteit op Azure wilt bereiken, bouwt u uw toepassingsarchitectuur met de combinatie van beschikbaarheidszones met Azure-regioparen. U uw toepassingen en gegevens synchroon repliceren met beschikbaarheidszones binnen een Azure-gebied voor hoge beschikbaarheid en asynchroon repliceren in Azure-regio's voor bescherming tegen noodherstel.
 
![conceptuele weergave van één zone die in een regio naar beneden gaat](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> De beschikbaarheidszone-id's (de nummers 1, 2 en 3 in de afbeelding hierboven) worden logischerwijs onafhankelijk van elkaar toegewezen aan de werkelijke fysieke zones voor elk abonnement. Dat betekent dat beschikbaarheidszone 1 in een bepaald abonnement kan verwijzen naar een andere fysieke zone dan Beschikbaarheidszone 1 in een ander abonnement. Als gevolg hiervan wordt aanbevolen om niet te vertrouwen op availability zone-id's voor verschillende abonnementen voor het plaatsen van virtuele machines.

## <a name="services-support-by-region"></a>Ondersteuning voor diensten per regio

De combinaties van Azure-services en -regio's die beschikbaarheidszones ondersteunen, zijn:


|                                 |Noord- en Zuid-Amerika |              |           |           | Europa |              |          |              | Azië en Stille Oceaan |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |VS - centraal|VS - oost|VS - oost 2|VS - west 2|Frankrijk - centraal|Europa - noord|Verenigd Koninkrijk Zuid|Europa -west|Japan - oost|Azië - zuidoost|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Linux Virtual Machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtuele Windows-machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Schaalsets voor virtuele machines      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure App-serviceomgevingen ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Opslag**   |            |              |           |           |                |              |          |             |            |                |
| Beheerde schijven                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zoneredundante opslag          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Networking**                     |            |              |           |           |                |              |          |             |            |                |
| Standaard IP-adres        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standaard load balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute-gateway   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Application Gateway(V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Databases**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003; (preview)      | &#10003;       |
| Azure Cache voor Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analytics**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integratie**                     |            |              |           |           |                |              |          |             |            |                |
| Servicebus (alleen premiumlaag) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identiteit**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Tolerantie voor services
Alle Azure-beheerservices zijn ontworpen om bestand te zijn tegen fouten op regioniveau. In het spectrum van fouten hebben een of meer storingen in de beschikbaarheidszone binnen een regio een kleinere storingsstraal in vergelijking met een hele regiofout. Azure kan herstellen van een fout op zoneniveau van beheerservices binnen de regio of vanuit een andere Azure-regio. Azure voert kritisch onderhoud één zone tegelijk uit binnen een regio, om fouten te voorkomen die van invloed zijn op de klantbronnen die zijn geïmplementeerd in beschikbaarheidszones binnen een regio.

## <a name="pricing"></a>Prijzen
Er zijn geen extra kosten verbonden aan virtuele machines die zijn geïmplementeerd in een beschikbaarheidszone. 99,99% VM-uptime SLA wordt aangeboden wanneer twee of meer VM's worden geïmplementeerd in twee of meer beschikbaarheidszones binnen een Azure-gebied. Er zullen extra kosten voor de vm-to-VM-gegevensoverdracht tussen de beschikbaarheidszone en de VM zijn. Bekijk de [prijspagina Bandbreedte voor](https://azure.microsoft.com/pricing/details/bandwidth/) meer informatie.


## <a name="get-started-with-availability-zones"></a>Aan de slag met beschikbaarheidszones
- [Een virtuele machine maken](../virtual-machines/windows/create-portal-availability-zone.md)
- [Een beheerde schijf toevoegen met PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Een zoneredundante virtuele machineschaalset maken](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Load balance VM's over zones met behulp van een Standaard Load Balancer met een zone-redundante frontend](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Load balance VM's binnen een zone met behulp van een Standaard Load Balancer met een zonale frontend](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zone-redundante opslag](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geo-noodherstel Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geo-noodherstel Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Een zone-redundante virtuele netwerkgateway maken](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zoneredundante regio toevoegen voor Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure-cache voor beschikbaarheidszones van Redis aan de slag](https://aka.ms/redis/az/getstarted)
- [Een Azure Active Directory Domain Services-instantie maken](../active-directory-domain-services/tutorial-create-instance.md)
- [Een AKS-cluster (Azure Kubernetes Service) maken dat gebruikmaakt van beschikbaarheidszones](../aks/availability-zones.md)

## <a name="next-steps"></a>Volgende stappen
- [Snelstartsjablonen](https://aka.ms/azqs)
