---
title: 'Scenario: any-to-any'
titleSuffix: Azure Virtual WAN
description: "Scenario's voor route ring: any-to-any"
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6115ca375c3e5bf2be3335fe2231628ec7bf309f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267734"
---
# <a name="scenario-any-to-any"></a>Scenario: any-to-any

Wanneer u werkt met virtuele WAN-hub routering, zijn er heel veel beschik bare scenario's. In een wille keurig scenario kan elke spoke een andere spoke bereiken. Als er meerdere hubs bestaan, is hub-naar-hub-route ring (ook wel inter-hub genoemd) standaard ingeschakeld in standaard virtueel WAN. Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="design"></a><a name="design"></a>Ontwerp

Om erachter te komen hoeveel route tabellen nodig zijn in een virtueel WAN-scenario, kunt u een verbindings matrix maken waarbij elke cel aangeeft of een bron (rij) kan communiceren met een doel (kolom). De connectiviteits matrix in dit scenario is trivial, maar we hebben deze opgenomen zodat deze consistent zijn met andere scenario's.

| Van |   Tot |  *VNets* | *Vertakkingen* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|      X     |     X    |
| Vertakkingen   | &#8594;|    X     |     X    |

In elk van de cellen in de vorige tabel wordt beschreven of een virtuele WAN-verbinding (de ' aan '-zijde van de stroom, de rijkoppen in de tabel) een doel voorvoegsel (de ' aan '-zijde van de stroom, de kolom koppen in de tabel cursief) voor een specifieke verkeers stroom, waarbij een ' X ' betekent dat de connectiviteit wordt verschaft door Virtual WAN.

Aangezien alle verbindingen van zowel VNets als filialen (VPN, ExpressRoute en gebruikers VPN) dezelfde connectiviteits vereisten hebben, is er één route tabel vereist. Als gevolg hiervan worden alle verbindingen gekoppeld en door gegeven aan dezelfde route tabel, de standaard route tabel:

* Virtuele netwerken:
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**
* Sleutel
  * Gekoppelde route tabel: **standaard**
  * Door geven aan route tabellen: **standaard**

Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.

## <a name="architecture"></a><a name="architecture"></a>Architectuur

In **afbeelding 1**kunnen alle VNets en branches (VPN, EXPRESSROUTE, P2S) elkaar bereiken. In een virtuele hub werken verbindingen als volgt:

* Een VPN-verbinding verbindt een VPN-site met een VPN-gateway.
* Een virtuele netwerk verbinding verbindt een virtueel netwerk met een virtuele hub. De router van de virtuele hub biedt de doorvoer functionaliteit tussen VNets.
* Een ExpressRoute-verbinding verbindt een ExpressRoute-circuit met een ExpressRoute-gateway.

Deze verbindingen (standaard bij het maken) zijn gekoppeld aan de standaard route tabel, tenzij u de routerings configuratie van de verbinding instelt op **geen**of een aangepaste route tabel. Deze verbindingen geven routes ook standaard door aan de standaard route tabel. Dit biedt een wille keurig scenario waarbij elke spoke (VNet, VPN, er, P2S) elkaar kan bereiken.

**Afbeelding 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="afbeelding 1":::

## <a name="workflow"></a><a name="workflow"></a>Werkstroom

Dit scenario is standaard ingeschakeld voor virtuele WAN. Als de instelling voor vertakking-naar-vertakking is uitgeschakeld in de WAN-configuratie, wordt de verbinding tussen vertakkings Spaak niet toegestaan. VPN/ExpressRoute/gebruiker VPN worden beschouwd als vertakkings spaak in virtueel WAN

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
* Zie [about Virtual hub Routing](about-virtual-hub-routing.md)(Engelstalig) voor meer informatie over virtuele-hub-route ring.
