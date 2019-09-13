---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919665"
---
|**SKU**   | **S2S-/VNet-naar-VNet-<br>tunnels** | **P2S<br> SSTP-verbindingen** | **P2S<br> IKEv2/openvpn-verbindingen** | **Benchmark cumulatieve<br>doorvoer** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Met maximaal 10    | Met maximaal 128  | Niet ondersteund  | 100 Mbps  | Niet ondersteund| Nee |
|**VpnGw1**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Nee |
|**VpnGw2**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Nee |
|**VpnGw3**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Nee |
|**VpnGw1AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 250       | 650 Mbps  | Ondersteund | Ja |
|**VpnGw2AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 500       | 1 Gbps    | Ondersteund | Ja |
|**VpnGw3AZ**| Met maximaal 30*   | Met maximaal 128  | Met maximaal 1000      | 1,25 Gbps | Ondersteund | Ja |

(\*) Gebruik [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md) als u meer dan 30 S2S VPN-tunnels nodig hebt.

* Er gelden afzonderlijke verbindingslimieten. U kunt bijvoorbeeld 128 SSTP-verbindingen en ook 250 IKEv2-verbindingen hebben in een SKU van het type VpnGw1.

* Prijsinformatie vindt u op de pagina [Prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Gegevens over de SLA (Service Level Agreement) vindt u op de pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ en VpnGw3AZ worden alleen ondersteund voor VPN-gateways met behulp van het Resource Manager-implementatie model.

* De Basic-SKU wordt beschouwd als verouderd. Deze SKU kent bepaalde functiebeperkingen. Zo kunt u een gateway die gebruikmaakt van een Basic-SKU, niet overzetten naar een van de nieuwe gateway-SKU's. In plaats daarvan moet u een nieuwe SKU gebruiken, wat betekent dat u uw VPN-gateway eerst moet verwijderen en vervolgens opnieuw moet maken. Controleer of de functie die u nodig hebt, wordt ondersteund voordat u kiest voor de Basic-SKU.

* De Benchmark cumulatieve doorvoer is gebaseerd op de metingen van meerdere tunnels die via één gateway worden gecombineerd. De Benchmark cumulatieve doorvoer voor een VPN-gateway is gebaseerd op de combinatie S2S + P2S. **Als u veel P2S-verbindingen gebruikt, kan dit in verband met doorvoerbeperkingen een negatieve invloed hebben op S2S-verbindingen.** In verband met omstandigheden in het internetverkeer en het gedrag van uw toepassing geeft de Benchmark cumulatieve doorvoer geen gegarandeerde doorvoer aan.

* Om onze klanten te helpen de relatieve prestaties van VpnGw Sku's te begrijpen, hebben we de open bare iPerf-en CTSTraffic-hulpprogram ma's gebruikt om de prestaties te meten. De onderstaande tabel bevat de resultaten van prestatie testen met behulp van verschillende algoritmen. Zoals u kunt zien, worden de beste prestaties verkregen wanneer we het GCMAES256-algoritme voor zowel IPsec-versleuteling als-integriteit hebben gebruikt. Er zijn gemiddelde prestaties bij het gebruik van AES256 voor IPsec-versleuteling en SHA256 voor integriteit. Wanneer we DES3 gebruiken voor IPsec-versleuteling en SHA256 voor integriteit, hebben we de laagste prestaties ontvangen.

|**SKU**   | **Gebruikte<br>algoritmen** | **Waargenomen<br>door Voer** | **Gemeten pakketten per<br>seconde** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58.000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60,000|
