---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74829172"
---
|**VPN-gatewaygeneratie<br><br>** |**Sku**   | **S2S-/VNet-naar-VNet-<br>tunnels** | **P2S<br> SSTP-verbindingen** | **P2S<br> IKEv2/OpenVPN-verbindingen** | **Benchmark cumulatieve<br>doorvoer** | **BGP** | **Zone-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generatie1**|**Basic**   | Met maximaal 10    | Met maximaal 128  | Niet ondersteund  | 100 Mbps  | Niet ondersteund| Nee |
|**Generatie1**|**VpnGw1**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Nee |
|**Generatie1**|**VpnGw2**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Nee |
|**Generatie1**|**VpnGw3**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Nee |
|**Generatie1**|**VpnGw1AZ VpnGw1AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Ja |
|**Generatie1**|**VpnGw2AZ VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Ja |
|**Generatie1**|**VpnGw3AZ VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Ja |
|        |            |            |           |                |           |           |     |
|**Generatie2**|**VpnGw2**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1,25 Gbps | Ondersteund | Nee |
|**Generatie2**|**VpnGw3**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 2,5 Gbps  | Ondersteund | Nee |
|**Generatie2**|**VpnGw4 Vpn4**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 5000      | 5 Gbps    | Ondersteund | Nee |
|**Generatie2**|**VpnGw5**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 10.000      | 10 Gbps   | Ondersteund | Nee |
|**Generatie2**|**VpnGw2AZ VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1,25 Gbps | Ondersteund | Ja |
|**Generatie2**|**VpnGw3AZ VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 2,5 Gbps  | Ondersteund | Ja |
|**Generatie2**|**VpnGw4AZ Vpn4AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 5000      | 5 Gbps    | Ondersteund | Ja |
|**Generatie2**|**VpnGw5AZ VpnGw5AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 10.000      | 10 Gbps   | Ondersteund | Ja |

(*) Gebruik [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) als u meer dan 30 S2S VPN-tunnels nodig hebt.

* Het wijzigen van vpngw SKU's is toegestaan binnen dezelfde generatie, behalve het wijzigen van het formaat van de Basic SKU. De Basic SKU is een legacy SKU en heeft functiebeperkingen. Om van Basic naar een andere VpnGw SKU te gaan, moet u de Basic SKU VPN-gateway verwijderen en een nieuwe gateway maken met de gewenste generatie- en SKU-groottecombinatie.

* Er gelden afzonderlijke verbindingslimieten. U kunt bijvoorbeeld 128 SSTP-verbindingen en ook 250 IKEv2-verbindingen hebben in een SKU van het type VpnGw1.

* Prijsinformatie vindt u op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Gegevens over de SLA (Service Level Agreement) vindt u op de pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Op een enkele tunnel kan een maximale doorvoer van 1 Gbps worden bereikt. Aggregate Throughput Benchmark in de bovenstaande tabel is gebaseerd op metingen van meerdere tunnels geaggregeerd via een enkele gateway. De Benchmark cumulatieve doorvoer voor een VPN-gateway is gebaseerd op de combinatie S2S + P2S. **Als u veel P2S-verbindingen gebruikt, kan dit in verband met doorvoerbeperkingen een negatieve invloed hebben op S2S-verbindingen.** In verband met omstandigheden in het internetverkeer en het gedrag van uw toepassing geeft de Benchmark cumulatieve doorvoer geen gegarandeerde doorvoer aan.

Om onze klanten te helpen de relatieve prestaties van SKU's te begrijpen met behulp van verschillende algoritmen, gebruikten we openbaar beschikbare iPerf- en CTSTraffic-tools om prestaties te meten. De onderstaande tabel bevat de resultaten van prestatietests voor Generatie 1, VpnGw SKU's. Zoals u zien, worden de beste prestaties verkregen wanneer we GCMAES256-algoritme gebruikten voor zowel IPsec-versleuteling als integriteit. We hebben gemiddelde prestaties bij het gebruik van AES256 voor IPsec-versleuteling en SHA256 voor integriteit. Toen we DES3 gebruikten voor IPsec Encryption en SHA256 voor Integriteit kregen we de laagste prestaties.

|**Generatie**|**Sku**   | **Gebruikte<br>algoritmen** | **Doorvoer<br>waargenomen** | **Pakketten per<br>seconde waargenomen** |
|---           |---       | ---                 | ---            | ---                    |
|**Generatie1**|**VpnGw1**| GCMAES256 GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**Generatie1**|**VpnGw2**| GCMAES256 GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**Generatie1**|**VpnGw3**| GCMAES256 GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60.000|
|**Generatie1**|**VpnGw1AZ VpnGw1AZ**| GCMAES256 GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**Generatie1**|**VpnGw2AZ VpnGw2AZ**| GCMAES256 GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**Generatie1**|**VpnGw3AZ VpnGw3AZ**| GCMAES256 GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60.000|
