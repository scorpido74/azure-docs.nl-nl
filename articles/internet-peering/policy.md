---
title: Microsoft-peeringbeleid
titleSuffix: Azure
description: Microsoft-peeringbeleid
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775223"
---
# <a name="peering-policy"></a>Peeringbeleid
De algemene vereisten van Microsoft vanuit uw netwerk worden uitgelegd in de onderstaande secties. Deze zijn van toepassing op zowel direct peering- als Exchange-peering-aanvragen.

## <a name="technical-requirements"></a>Technische vereisten

* Een volledig redundant netwerk met voldoende capaciteit om verkeer zonder congestie uit te wisselen.
* Peer zal een openbaar routable Autonomous System Number (ASN) hebben.
* Zowel IPv4 als IPv6 worden ondersteund en Microsoft verwacht sessies van beide typen in elke peeringlocatie vast te stellen.
* MD5 wordt niet ondersteund.
* **ASN-details:**
    * Microsoft beheert AS8075 samen met de volgende ASN's: AS8068, AS8069, AS12076. Voor een volledige lijst van ASN's met AS8075-peering raadpleegt u MICROSOFT.
    * Alle partijen die met Microsoft samenkijken, komen overeen om onder geen enkele omstandigheid routes van AS12076 (Express Route) te accepteren en moeten AS12076 op alle peers filteren.
* **Routeringsbeleid:**
    * Peer zal ten minste één openbaar routeerbaar /24 hebben.
    * Microsoft zal ontvangen Multi-Exit Discriminators (MED) overschrijven.
    * Microsoft ontvangt het liefst BGP-community-tags van collega's om de oorsprong van de route aan te geven.
    * Peer wordt verwacht dat hun routes te registreren in een openbare Internet Routing Registry (IRR) database, met het oog op het filteren, en zal te goeder trouw inspanningen om deze informatie up-to-date te houden.
    * We raden peers aan een max-voorvoegsel van 1000 (IPv4) en 100 (IPv6) routes in te stellen op peering sessies met Microsoft.
    * Tenzij vooraf specifiek overeengekomen, worden collega's geacht consistente routes aan te kondigen op alle locaties waar ze met Microsoft gekeken.
    * In het algemeen zullen peeringsessies met AS8075 alle AS-MICROSOFT-routes adverteren. AS8075 interconnects in Afrika en Azië kunnen worden beperkt tot routes die relevant zijn voor de desbetreffende regio.
    * Geen van beide partijen zal een statische route, een route in laatste instantie, of anderszins verkeer te sturen naar de andere partij voor een route niet aangekondigd via BGP.
    * Van collega's wordt verwacht dat ze zich houden aan [de manrs-industrienormen](https://www.manrs.org/) voor routebeveiliging.

## <a name="operational-requirements"></a>Operationele vereisten
* Een volledig bemand 24x7 Network Operations Center (NOC), dat kan helpen bij het oplossen van alle technische en prestatieproblemen, beveiligingsschendingen, denial of service-aanvallen of enig ander misbruik dat afkomstig is van de peer of hun klanten.
* Van peers wordt verwacht dat ze een volledig en up-to-date profiel hebben op [PeeringDB,](https://www.peeringdb.com) inclusief een 24x7 NOC-e-mail van bedrijfsdomein en telefoonnummer. We gebruiken deze informatie om de gegevens van de peer te valideren, zoals NOC-informatie, technische contactgegevens en hun aanwezigheid in de peeringfaciliteiten, enz. Persoonlijke Yahoo-, Gmail- en hotmail-accounts worden niet geaccepteerd.

## <a name="physical-connection-requirements"></a>Fysieke verbindingsvereisten
* De locaties waar u verbinding maken met Microsoft voor Direct peering of Exchange-peering worden vermeld in [PeeringDB](https://www.peeringdb.com/net/694)
* **Exchange-peering:**
    * Interconnectie moet via single-mode fiber met behulp van de juiste 10Gbps optica.
    * Van peers wordt verwacht dat ze hun poorten upgraden wanneer het piekgebruik meer dan 50% bedraagt.
* **Direct peering:**
    * Interconnectie moet via single-mode fiber zijn met behulp van de juiste 10Gbps of 100Gbps optica.
    * Microsoft zal alleen direct peering opzetten met ISP- of Network Service-providers.
    * Van peers wordt verwacht dat ze hun poorten upgraden wanneer het piekgebruik meer dan 50% bedraagt en een diverse capaciteit in elke metro behouden, hetzij binnen één locatie, hetzij op verschillende locaties in een metro.
    * Elke Direct-peering bestaat uit twee verbindingen met twee Microsoft edge-routers van de routers van de Peer die zich in de rand van Peer bevinden. Microsoft vereist dubbele BGP-sessies voor deze verbindingen. De peer kan ervoor kiezen om geen redundante apparaten aan het einde te implementeren.

## <a name="traffic-requirements"></a>Verkeerseisen
* Peers over Exchange peering moeten minimaal 200 Mb verkeer en minder dan 2 Gb hebben.  Voor verkeer van meer dan 2 Gb Direct peering moet worden herzien.
* Voor Direct peering moet verkeer van uw netwerk naar Microsoft voldoen aan de minimumvereiste.

    | Geo                      | Minimaal verkeer naar Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mbps                       |
    | APAC (behalve India)      |   2 Gbps                       |
    | APAC (alleen India)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | LATAM LATAM                    |   2 Gbps                       |
    | Midden-Oosten              | 500 Mbps                       |
    | N.v.t.                       |   2 Gbps                       |

* **Diversiteit:**
    * In NA, Europa, APAC en LATAM, interconnect in ten minste drie geografisch diverse locaties indien haalbaar en onderhouden van diverse capaciteit om het verkeer te failover binnen elke metro.
    * In Afrika, het Midden-Oosten en India, met elkaar verbinden op zoveel mogelijk verschillende locaties. Moet voldoende divers vermogen behouden om ervoor te zorgen dat het verkeer in de regio blijft.

## <a name="next-steps"></a>Volgende stappen

* Volg [Direct peering walkthrough](walkthrough-direct-all.md)voor meer informatie over stappen voor het instellen van Direct-peering met Microsoft.
* Volg [Exchange-peering walkthrough](walkthrough-exchange-all.md)voor meer informatie over stappen voor het instellen van Exchange-peering met Microsoft.