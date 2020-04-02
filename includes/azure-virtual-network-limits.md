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
ms.openlocfilehash: 95cb29e871cce2ba600ab654d48c685b90ed027e
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80573256"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Netwerklimieten - Azure Resource Manager
De volgende limieten gelden alleen voor netwerkbronnen die worden beheerd via **Azure Resource Manager** per regio per abonnement. Meer informatie over het [weergeven van uw huidige resourcegebruik aan de hand van uw abonnementslimieten.](../articles/networking/check-usage-against-limits.md)

> [!NOTE]
> We hebben onlangs alle standaardlimieten verhoogd tot hun maximale limieten. Als er geen kolom met maximale limiet is, heeft de resource geen aanpasbare limieten. Als u deze limieten in het verleden hebt verhoogd door ondersteuning en geen bijgewerkte limieten in de volgende tabellen [ziet, opent u kosteloos een online verzoek om klantenondersteuning](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Resource | Limiet | 
| --- | --- |
| Virtuele netwerken |1000 |
| Subnetten per virtueel netwerk |3000 |
| Peering van virtuele netwerken per virtueel netwerk |500 |
| [Virtual network gateways (VPN gateways) per virtueel netwerk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Virtuele netwerkgateways (ExpressRoute-gateways) per virtueel netwerk](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS-servers per virtueel netwerk |20 |
| Privé-IP-adressen per virtueel netwerk |65.536 |
| Privé-IP-adressen per netwerkinterface |256 |
| Privé-IP-adressen per virtuele machine |256 |
| Openbare IP-adressen per netwerkinterface |256 |
| Openbare IP-adressen per virtuele machine |256 |
| [Gelijktijdige TCP- of UDP-stromen per NIC van een virtuele machine of rolinstantie](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500.000 |
| Netwerkinterfacekaarten |65.536 |
| Netwerkbeveiligingsgroepen |5.000 |
| NSG-regels per NSG |1000 |
| IP-adressen en bereiken opgegeven voor bron of bestemming in een beveiligingsgroep |4000 |
| Toepassingsbeveiligingsgroepen |3000 |
| Toepassingsbeveiligingsgroepen per IP-configuratie, per NIC |20 |
| IP-configuraties per toepassingsbeveiligingsgroep |4000 |
| Toepassingsbeveiligingsgroepen die kunnen worden opgegeven binnen alle beveiligingsregels van een netwerkbeveiligingsgroep |100 |
| Door de gebruiker gedefinieerde routetabellen |200 |
| Door de gebruiker gedefinieerde routes per routetabel |400 |
| Point-to-site rootcertificaten per Azure VPN-gateway |20 |
| Virtual network TAP's |100 |
| Tap-configuraties voor netwerkinterface per tap voor virtueel netwerk |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Limieten voor openbare IP-adres
| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Openbare IP-adressen<sup>1</sup> | 10 voor Basic. | Neem contact op met ondersteuning. |
| Statische openbare IP-adressen<sup>1</sup> | 10 voor Basic. | Neem contact op met ondersteuning. |
| Standaard IP-adressen van openbare<sup>producten 1</sup> | 10 | Neem contact op met ondersteuning. |
| Voorvoegsels voor openbare IP | beperkt door het aantal standaard openbare IP's in een abonnement | Neem contact op met ondersteuning. |
| Lengte van openbare IP-voorvoegsel | /28 | Neem contact op met ondersteuning. |

<sup>1.</sup> Standaardlimieten voor openbare IP-adressen variëren per type aanbiedingscategorie, zoals Gratis proefversie, Betalen per gebruik, CSP. De standaardinstelling voor Enterprise Agreement-abonnementen is bijvoorbeeld 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limieten voor load balancer
De volgende beperkingen gelden alleen voor netwerkresources die worden beheerd via Azure Resource Manager. De beperkingen gelden per regio en per abonnement. Meer informatie over het [weergeven van uw huidige resourcegebruik aan de hand van uw abonnementslimieten.](../articles/networking/check-usage-against-limits.md)

**Standaard load balancer**

| Resource                                | Limiet         |
|-----------------------------------------|-------------------------------|
| Load balancers                          | 1000                         |
| Regels per resource                      | 1500                         |
| Regels per NIC (in alle IP's op een NIC) | 300                           |
| Frontend IP-configuraties              | 600                           |
| Grootte van back-end-pool                       | 1.000 IP-configuraties, één virtueel netwerk |
| Back-endresources per <sup>load balancer 1<sup> | 150                   |
| Poorten met hoge beschikbaarheid                 | 1 per interne frontend       |
| Uitgaande regels per load balancer        | 20                            |
| [TCP-inactieve time-out](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minuten/30 minuten          |

<sup>1.</sup> De limiet is maximaal 150 resources, in elke combinatie van zelfstandige virtuele machineresources, beschikbaarheidssetresources en bronnen met virtuele machineschaalset.

**Basisbalansbalans**

| Resource                                | Limiet        |
|-----------------------------------------|------------------------------|
| Load balancers                          | 1000                        |
| Regels per resource                      | 250                          |
| Regels per NIC (in alle IP's op een NIC) | 300                          |
| Frontend IP-configuraties              | 200                          |
| Grootte van back-end-pool                       | 300 IP-configuraties, set met één beschikbaarheid |
| Beschikbaarheidssets per load balancer     | 150                          |

<a name="virtual-networking-limits-classic"></a>De volgende limieten zijn alleen van toepassing op netwerkbronnen die worden beheerd via het **klassieke** implementatiemodel per abonnement. Meer informatie over het [weergeven van uw huidige resourcegebruik aan de hand van uw abonnementslimieten.](../articles/networking/check-usage-against-limits.md)

| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Virtuele netwerken |100 |100 |
| Lokale netwerksites |20 |50 |
| DNS-servers per virtueel netwerk |20 |20 |
| Privé-IP-adressen per virtueel netwerk |4,096 |4,096 |
| Gelijktijdige TCP- of UDP-stromen per NIC van een virtuele machine of rolinstantie |500.000, tot 1.000.000 voor twee of meer NIC's. |500.000, tot 1.000.000 voor twee of meer NIC's. |
| Netwerkbeveiligingsgroepen (NSGs) |200 |200 |
| NSG-regels per NSG |1000 |1000 |
| Door de gebruiker gedefinieerde routetabellen |200 |200 |
| Door de gebruiker gedefinieerde routes per routetabel |400 |400 |
| Openbare IP-adressen (dynamisch) |500 |500 |
| Gereserveerde openbare IP-adressen |500 |500 |
| Openbare VIP per implementatie |5 |Contact opnemen met ondersteuning |
| Private VIP (internal load balancing) per implementatie |1 |1 |
| Lijst met beheerlijsten voor eindpunten (ACL's) |50 |50 |
