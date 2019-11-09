---
title: bestand opnemen
description: bestand opnemen
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 8b08e0ced0c7094890a80c37452c7f1b001fe511
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888696"
---
| | Standaard SKU | Basis-SKU |
| --- | --- | --- |
| Grootte van de back-end-groep | Ondersteunt maximaal 1000 instanties. | Ondersteunt maximaal 100 instanties. |
| Eind punten van de back-end-pool | Een virtuele machine in één virtueel netwerk, met inbegrip van de combi natie van virtuele machines, beschikbaarheids sets en virtuele-machine schaal sets. | Virtuele machines in een beschikbaarheidsset of virtuele-machineschaalset. |
| [Statuscontroles](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Gedrag statustest inactief](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-verbindingen blijven actief op een instantie die omlaag __en__ op alle tests wordt gecontroleerd. | TCP-verbindingen blijven actief op een instantie die wordt getest. Alle TCP-verbindingen worden beëindigd wanneer alle tests uitvallen. |
| Beschikbaarheidszones | Zone-redundante en zonegebonden front-ends voor binnenkomend en uitgaand verkeer. Toewijzingen van uitgaande stromen blijven zone storingen. Taak verdeling in meerdere zones. | Niet beschikbaar |
| Diagnostiek | Azure Monitor. Multi-dimensionale metrische gegevens, waaronder byte-en pakket tellers. Status van de status test. Verbindings pogingen (TCP SYN). Uitgaande verbindings status (geslaagde en mislukte stroom SNAT). Afmetingen van actief gegevens vlak. | Alleen Azure-Log Analytics voor open bare Load Balancer. Waarschuwing voor SNAT-uitputting. Aantal statussen van back-end. |
| HA-poorten | Interne load balancer | Niet beschikbaar |
| Standaardbeveiliging | Open bare IP-, open bare Load Balancer-eind punten en interne Load Balancer-eind punten worden gesloten voor binnenkomende stromen, tenzij toegestaan door een netwerk beveiligings groep. | Standaard geopend. De netwerk beveiligings groep is optioneel. |
| [Uitgaande verbindingen](../articles/load-balancer/load-balancer-outbound-connections.md) | U kunt uitgaande NAT op basis van pools expliciet definiëren met [uitgaande regels](../articles/load-balancer/load-balancer-outbound-rules-overview.md). U kunt meerdere front-ends met de opt-out voor de taakverdelings regel gebruiken. Een uitgaand scenario _moet_ expliciet worden gemaakt voor de virtuele machine, de beschikbaarheidsset of de schaalset voor virtuele machines om uitgaande verbindingen te gebruiken. Service-eind punten van het virtuele netwerk kunnen worden bereikt zonder dat er een uitgaande verbinding wordt gedefinieerd en dat er geen gegevens worden verwerkt. Alle open bare IP-adressen, waaronder Azure PaaS-services die niet beschikbaar zijn als service-eind punten voor virtuele netwerken, moeten worden bereikt door gebruik te maken van uitgaande verbindingen en aantallen naar gegevens die worden verwerkt. Wanneer alleen een interne Load Balancer fungeert als virtuele machine, beschikbaarheidsset of virtuele-machine schaalset, zijn uitgaande verbindingen via de standaard-SNAT niet beschikbaar. Gebruik in plaats daarvan [Uitgaande regels](../articles/load-balancer/load-balancer-outbound-rules-overview.md) . De functie voor uitgaande SNAT-programmering is afhankelijk van het transport protocol van de regel voor inkomende taak verdeling. | Eén front-end, geselecteerd op wille keurige wijze als er meerdere front-ends aanwezig zijn. Wanneer alleen interne Load Balancer een virtuele machine, een beschikbaarheidsset of een virtuele-machine schaalset, wordt standaard SNAT gebruikt. |
| [Regels voor uitgaand verkeer](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Declaratieve uitgaande NAT-configuratie met behulp van open bare IP-adressen of open bare IP-voor voegsels of beide. Configureer bare uitgaande time-out voor inactiviteit (4-120 minuten). Aangepaste toewijzing van de SNAT-poort | Niet beschikbaar |
| [TCP opnieuw instellen bij inactiviteit](../articles/load-balancer/load-balancer-tcp-reset.md) | Schakel in dat TCP opnieuw wordt ingesteld (TCP RST) voor welke regel dan ook als er sprake is van inactiviteit | Niet beschikbaar |
| [Meerdere front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkomend en [uitgaand](../articles/load-balancer/load-balancer-outbound-connections.md) | Alleen inkomend |
| Beheerbewerkingen | De meeste bewerkingen < 30 seconden | 60 tot 90 seconden typisch |
| SLA | 99,99% voor gegevenspad met twee gezonde virtuele machines. | Niet van toepassing | 
| Prijzen | Kosten worden in rekening gebracht op basis van het aantal regels en verwerkte inkomende en uitgaande gegevens die zijn gekoppeld aan de resource. | Geen kosten |
|  |  |  |
