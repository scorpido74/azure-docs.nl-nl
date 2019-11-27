---
title: bestand opnemen
description: bestand opnemen
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: ac1687d371630089436640af15cf46491a38ab51
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485421"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Netwerk limieten-Azure Resource Manager de volgende limieten gelden alleen voor netwerk resources die worden beheerd via **Azure Resource Manager** per regio per abonnement. Meer informatie over het [weer geven van uw huidige resource gebruik op basis van uw abonnements limieten](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> We hebben onlangs alle standaard limieten uitgebreid tot hun maximum limieten. Als er geen kolom maximum limiet is, heeft de resource geen aanpas bare limieten. Als u deze limieten hebt verhoogd met ondersteuning in het verleden en geen bijgewerkte limieten in de volgende tabellen ziet, kunt u gratis [een online klant ondersteunings aanvraag openen](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Bron | Standaard/maximum limiet | 
| --- | --- |
| Virtuele netwerken |1000 |
| Subnetten per virtueel netwerk |3\.000 |
| Peering van virtuele netwerken per virtueel netwerk |500 |
| [Virtuele netwerk gateways (VPN-gateways) per virtueel netwerk](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| DNS-servers per virtueel netwerk |20 |
| Privé-IP-adressen per virtueel netwerk |65.536 |
| Privé-IP-adressen per netwerk interface |256 |
| Privé-IP-adressen per virtuele machine |256 |
| Open bare IP-adressen per netwerk interface |256 |
| Open bare IP-adressen per virtuele machine |256 |
| Gelijktijdige TCP-of UDP-stromen per NIC van een virtuele machine of rolinstantie |500,000 |
| Netwerk interface kaarten |65.536 |
| Netwerkbeveiligingsgroepen |5,000 |
| NSG-regels per NSG |1000 |
| IP-adressen en-bereiken die zijn opgegeven voor bron of doel in een beveiligings groep |4,000 |
| Toepassingsbeveiligingsgroepen |3\.000 |
| Toepassings beveiligings groepen per IP-configuratie, per NIC |20 |
| IP-configuraties per toepassings beveiligings groep |4,000 |
| Toepassings beveiligings groepen die kunnen worden opgegeven in alle beveiligings regels van een netwerk beveiligings groep |100 |
| Door de gebruiker gedefinieerde route tabellen |200 |
| Door de gebruiker gedefinieerde routes per route tabel |400 |
| Punt-naar-site-basis certificaten per Azure-VPN Gateway |20 |
| Kranen van virtueel netwerk |100 |
| Netwerk interface Tik configuraties per virtueel netwerk Tik |100 |

#### <a name="publicip-address"></a>Limieten voor openbaar IP-adres
| Bron | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Open bare IP-adressen-dynamisch | 1\.000 voor Basic. |Neem contact op met ondersteuning. |
| Open bare IP-adressen-statisch | 1\.000 voor Basic. |Neem contact op met ondersteuning. |
| Open bare IP-adressen-statisch | 1\.000 voor Standard.|Neem contact op met ondersteuning. |
| Lengte van openbaar IP-voor voegsel | /28 | Neem contact op met ondersteuning. |

#### <a name="load-balancer"></a>Limieten van Load Balancer
De volgende beperkingen gelden alleen voor netwerkresources die worden beheerd via Azure Resource Manager. De beperkingen gelden per regio en per abonnement. Meer informatie over het [weer geven van uw huidige resource gebruik op basis van uw abonnements limieten](../articles/networking/check-usage-against-limits.md).

| Bron | Standaard/maximum limiet |
| --- | --- |
| Load balancers | 1000 | 
| Regels per resource, basis | 250 |
| Regels per resource, standaard | 1,500 | 
| Regels per IP-configuratie | 299 |
| Regels per NIC | 300 |
| Front-end-IP-configuraties, basis | 200 |
| Front-end-IP-configuraties, standaard | 600 |
| Back-end-pool, Basic | 100, één beschikbaarheidsset |
| Back-end-pool, standaard | 1\.000, één virtueel netwerk |
| Back-end-resources per load balancer, standaard<sup>1</sup> | 150 |
| Poorten met hoge Beschik baarheid, standaard | 1 per interne front-end |

<sup>1</sup> De limiet is Maxi maal 150 resources, in een combi natie van zelfstandige virtuele-machine resources, bronnen voor beschikbaarheids sets en het instellen van resources met virtuele-machine schaal sets.

#### <a name="virtual-networking-limits-classic"></a>De volgende limieten gelden alleen voor netwerk resources die worden beheerd via het **klassieke** implementatie model per abonnement. Meer informatie over het [weer geven van uw huidige resource gebruik op basis van uw abonnements limieten](../articles/networking/check-usage-against-limits.md).

| Bron | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Virtuele netwerken |100 |100 |
| Lokale netwerksites |20 |50 |
| DNS-servers per virtueel netwerk |20 |20 |
| Privé-IP-adressen per virtueel netwerk |4\.096 |4\.096 |
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
