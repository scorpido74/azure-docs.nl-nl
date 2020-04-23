---
title: bestand opnemen
description: bestand opnemen
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 511354633b9f88f3d6cd2e2170ce3b7ca1f4ecdb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096103"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Netwerk limieten-Azure Resource Manager
De volgende limieten gelden alleen voor netwerk resources die worden beheerd via **Azure Resource Manager** per regio per abonnement. Meer informatie over het [weer geven van uw huidige resource gebruik op basis van uw abonnements limieten](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> We hebben onlangs alle standaard limieten uitgebreid tot hun maximum limieten. Als er geen kolom maximum limiet is, heeft de resource geen aanpas bare limieten. Als u deze limieten hebt verhoogd met ondersteuning in het verleden en geen bijgewerkte limieten in de volgende tabellen ziet, kunt u gratis [een online klant ondersteunings aanvraag openen](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Resource | Limiet | 
| --- | --- |
| Virtuele netwerken |1000 |
| Subnetten per virtueel netwerk |3000 |
| Peering van virtuele netwerken per virtueel netwerk |500 |
| [Virtuele netwerk gateways (VPN-gateways) per virtueel netwerk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuele netwerk gateways (ExpressRoute-gateways) per virtueel netwerk](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-servers per virtueel netwerk |20 |
| Privé-IP-adressen per virtueel netwerk |65.536 |
| Privé-IP-adressen per netwerk interface |256 |
| Privé-IP-adressen per virtuele machine |256 |
| Open bare IP-adressen per netwerk interface |256 |
| Open bare IP-adressen per virtuele machine |256 |
| [Gelijktijdige TCP-of UDP-stromen per NIC van een virtuele machine of rolinstantie](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Netwerk interface kaarten |65.536 |
| Netwerkbeveiligingsgroepen |5.000 |
| NSG-regels per NSG |1000 |
| IP-adressen en-bereiken die zijn opgegeven voor bron of doel in een beveiligings groep |4000 |
| Toepassingsbeveiligingsgroepen |3000 |
| Toepassings beveiligings groepen per IP-configuratie, per NIC |20 |
| IP-configuraties per toepassings beveiligings groep |4000 |
| Toepassings beveiligings groepen die kunnen worden opgegeven in alle beveiligings regels van een netwerk beveiligings groep |100 |
| Door de gebruiker gedefinieerde route tabellen |200 |
| Door de gebruiker gedefinieerde routes per route tabel |400 |
| Punt-naar-site-basis certificaten per Azure-VPN Gateway |20 |
| Kranen van virtueel netwerk |100 |
| Netwerk interface Tik configuraties per virtueel netwerk Tik |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limieten voor openbaar IP-adres
| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Open bare IP-adressen<sup>1</sup> | 10 voor Basic. | Neem contact op met ondersteuning. |
| Statische open bare IP-adressen<sup>1</sup> | 10 voor Basic. | Neem contact op met ondersteuning. |
| Standaard open bare IP-adressen<sup>1</sup> | 10 | Neem contact op met ondersteuning. |
| Voorvoegsels voor openbare IP | beperkt door het aantal standaard open bare Ip's in een abonnement | Neem contact op met ondersteuning. |
| Lengte van openbaar IP-voor voegsel | /28 | Neem contact op met ondersteuning. |

<sup>1</sup> De standaard limieten voor open bare IP-adressen variëren per aanbiedings categorie type, zoals gratis proef versie, betalen naar gebruik, CSP. De standaard instelling voor Enterprise Agreement abonnementen is 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limieten van Load Balancer
De volgende beperkingen gelden alleen voor netwerkresources die worden beheerd via Azure Resource Manager. De beperkingen gelden per regio en per abonnement. Meer informatie over het [weer geven van uw huidige resource gebruik op basis van uw abonnements limieten](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Resource                                | Limiet         |
|-----------------------------------------|-------------------------------|
| Load balancers                          | 1000                         |
| Regels per resource                      | 1500                         |
| Regels per NIC (voor alle IP-adressen op een NIC) | 300                           |
| Front-end-IP-configuraties              | 600                           |
| Grootte van back-end-pool                       | 1.000 IP-configuraties, één virtueel netwerk |
| Backend-bronnen per <sup>Load Balancer 1<sup> | 150                   |
| Poorten met hoge Beschik baarheid                 | 1 per interne frontend       |
| Uitgaande regels per Load Balancer        | 600                           |
| [Time-out voor TCP-inactiviteit](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minuten/30 minuten          |

<sup>1</sup> De limiet is Maxi maal 150 resources, in een combi natie van zelfstandige bronnen voor virtuele machines, beschikbaarheids sets en schaal sets voor virtuele machines.

**Basis Load Balancer**

| Resource                                | Limiet        |
|-----------------------------------------|------------------------------|
| Load balancers                          | 1000                        |
| Regels per resource                      | 250                          |
| Regels per NIC (voor alle IP-adressen op een NIC) | 300                          |
| Front-end-IP-configuraties              | 200                          |
| Grootte van back-end-pool                       | 300 IP-configuraties, één beschikbaarheidsset |
| Beschikbaarheids sets per Load Balancer     | 150                          |

<a name="virtual-networking-limits-classic"></a>De volgende limieten gelden alleen voor netwerk resources die worden beheerd via het **klassieke** implementatie model per abonnement. Meer informatie over het [weer geven van uw huidige resource gebruik op basis van uw abonnements limieten](../articles/networking/check-usage-against-limits.md).

| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Virtuele netwerken |100 |100 |
| Lokale netwerksites |20 |50 |
| DNS-servers per virtueel netwerk |20 |20 |
| Privé-IP-adressen per virtueel netwerk |4.096 |4.096 |
| Gelijktijdige TCP-of UDP-stromen per NIC van een virtuele machine of rolinstantie |500.000, tot 1.000.000 voor twee of meer Nic's. |500.000, tot 1.000.000 voor twee of meer Nic's. |
| Netwerk beveiligings groepen (Nsg's) |200 |200 |
| NSG-regels per NSG |1000 |1000 |
| Door de gebruiker gedefinieerde route tabellen |200 |200 |
| Door de gebruiker gedefinieerde routes per route tabel |400 |400 |
| Openbare IP-adressen (dynamisch) |500 |500 |
| Gereserveerde openbare IP-adressen |500 |500 |
| Openbare VIP per implementatie |5 |Contact opnemen met ondersteuning |
| Privé-VIP (interne taak verdeling) per implementatie |1 |1 |
| Toegangs beheer lijsten (Acl's) voor eind punten |50 |50 |
