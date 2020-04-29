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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74829172"
---
|**VPN<br>-<br>gateway genereren** |**SKU**   | **S2S-/VNet-naar-VNet-<br>tunnels** | **P2S<br> SSTP-verbindingen** | **P2S<br> IKEv2/openvpn-verbindingen** | **Benchmark cumulatieve<br>doorvoer** | **BGP** | **Zone-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Hoofd**   | Met maximaal 10    | Met maximaal 128  | Niet ondersteund  | 100 Mbps  | Niet ondersteund| Nee |
|**Generation1**|**VpnGw1**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Nee |
|**Generation1**|**VpnGw2**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Nee |
|**Generation1**|**VpnGw3**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Nee |
|**Generation1**|**VpnGw1AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Ja |
|**Generation1**|**VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Ja |
|**Generation1**|**VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Ja |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1,25 Gbps | Ondersteund | Nee |
|**Generation2**|**VpnGw3**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 2,5 Gbps  | Ondersteund | Nee |
|**Generation2**|**VpnGw4**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 5000      | 5 Gbps    | Ondersteund | Nee |
|**Generation2**|**VpnGw5**  | Met maximaal 30*   | Met maximaal 128  | Met maximaal 10.000      | 10 Gbps   | Ondersteund | Nee |
|**Generation2**|**VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1,25 Gbps | Ondersteund | Ja |
|**Generation2**|**VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 2,5 Gbps  | Ondersteund | Ja |
|**Generation2**|**VpnGw4AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 5000      | 5 Gbps    | Ondersteund | Ja |
|**Generation2**|**VpnGw5AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 10.000      | 10 Gbps   | Ondersteund | Ja |

(*) Gebruik [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) als u meer dan 30 S2S VPN-tunnels nodig hebt.

* Het wijzigen van de grootte van VpnGw Sku's is toegestaan binnen dezelfde generatie, behalve het wijzigen van de grootte van de basis-SKU. De basis-SKU is een verouderde SKU waarvoor beperkingen gelden voor de functie. Als u van de Basic-naar een andere VpnGw-SKU wilt gaan, moet u de basis-VPN-gateway van de Basic-SKU verwijderen en een nieuwe gateway maken met de gewenste combi natie van generatie en SKU.

* Er gelden afzonderlijke verbindingslimieten. U kunt bijvoorbeeld 128 SSTP-verbindingen en ook 250 IKEv2-verbindingen hebben in een SKU van het type VpnGw1.

* Prijsinformatie vindt u op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Gegevens over de SLA (Service Level Agreement) vindt u op de pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Bij één tunnel kan een maximum van 1 Gbps door voer worden bereikt. De benchmark-door Voer in de bovenstaande tabel is gebaseerd op de metingen van meerdere tunnels die via één gateway worden geaggregeerd. De Benchmark cumulatieve doorvoer voor een VPN-gateway is gebaseerd op de combinatie S2S + P2S. **Als u veel P2S-verbindingen gebruikt, kan dit in verband met doorvoerbeperkingen een negatieve invloed hebben op S2S-verbindingen.** In verband met omstandigheden in het internetverkeer en het gedrag van uw toepassing geeft de Benchmark cumulatieve doorvoer geen gegarandeerde doorvoer aan.

Om onze klanten te helpen bij het begrijpen van de relatieve prestaties van Sku's die gebruikmaken van verschillende algoritmen, hebben we de open bare iPerf-en CTSTraffic-hulpprogram ma's gebruikt om prestaties te meten. De volgende tabel bevat de resultaten van prestatie tests voor generatie 1, VpnGw Sku's. Zoals u kunt zien, worden de beste prestaties verkregen wanneer we het GCMAES256-algoritme voor zowel IPsec-versleuteling als-integriteit hebben gebruikt. Er zijn gemiddelde prestaties bij het gebruik van AES256 voor IPsec-versleuteling en SHA256 voor integriteit. Wanneer we DES3 gebruiken voor IPsec-versleuteling en SHA256 voor integriteit, hebben we de laagste prestaties ontvangen.

|**Generatie**|**SKU**   | **Gebruikte<br>algoritmen** | **Waargenomen<br>door Voer** | **Gemeten pakketten per<br>seconde** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60.000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60.000|
