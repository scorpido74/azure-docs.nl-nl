---
title: Beleid voor micro soft-peering
titleSuffix: Azure
description: Beleid voor micro soft-peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775223"
---
# <a name="peering-policy"></a>Peering-beleid
De algemene vereisten van micro soft van uw netwerk worden uitgelegd in de volgende secties. Deze zijn van toepassing op aanvragen voor directe peering en uitwisseling van peering.

## <a name="technical-requirements"></a>Technische vereisten

* Een volledig redundant netwerk met voldoende capaciteit voor het uitwisselen van verkeer zonder congestie.
* Peer heeft een openbaar routeerbaar autonoom systeem nummer (ASN).
* Zowel IPv4 als IPv6 worden ondersteund en micro soft verwacht sessies van beide typen op elke peering-locatie te maken.
* MD5 wordt niet ondersteund.
* **ASN-Details:**
    * Micro soft beheert AS8075 samen met de volgende Asn's: AS8068, AS8069, AS12076. Voor een volledige lijst met Asn's met AS8075-peering, verwijzen naar micro soft.
    * Alle partijen die met micro soft overeenkomen, accepteren geen routes van AS12076 (Express route) onder geen enkele omstandigheden en moeten AS12076 op alle peers uitfilteren.
* **Routerings beleid:**
    * De peer heeft ten minste één openbaar routeerbaar/24.
    * Micro soft overschrijft ontvangen multi-exit Discriminators (MED).
    * Micro soft geeft de voor keur aan het ontvangen van BGP Community-Tags van peers om de route oorsprong aan te geven.
    * Peers wordt verwacht hun routes te registreren in een IR-data base (open bare Internet Routing REGI ster) voor het filteren en maakt het goeder om deze informatie up-to-date te houden.
    * We suggereren dat peers een max. voor voegsel van 1000 (IPv4) en 100 (IPv6)-routes instellen voor peering-sessies met micro soft.
    * Tenzij specifiek vooraf overeengekomen, moeten peers naar verwachting consistente routes aankondigen op alle locaties waar ze met micro soft worden gepeerd.
    * Over het algemeen worden peering-sessies met AS8075 alle routes van micro soft aangekondigd. AS8075-verbindingen in Afrika en Azië kunnen worden beperkt tot routes die relevant zijn voor de betreffende regio.
    * Geen van beide partijen stelt een statische route, een route van de laatste redmiddel of het verzenden van verkeer naar de andere partij in voor een route die niet via BGP is aangekondigd.
    * Peers wordt verwacht te voldoen aan de [MANRS](https://www.manrs.org/) industrie normen voor route beveiliging.

## <a name="operational-requirements"></a>Operationele vereisten
* Een volledig personeel van 24x7 netwerk Operations Center (NOC), dat kan helpen bij de oplossing van alle technische en prestatie problemen, beveiligings schendingen, denial of service-aanvallen of andere misbruik die afkomstig is van de peer of hun klanten.
* Peers wordt verwacht een volledig en bijgewerkt profiel op [PeeringDB](https://www.peeringdb.com) te hebben, inclusief een 24x7 NOCse-mail van het bedrijfs domein en telefoon nummer. We gebruiken deze informatie om de details van de peer te valideren, zoals NOC-informatie, technische contact gegevens en hun aanwezigheid bij de peering-faciliteiten, enzovoort. Persoonlijke Yahoo-, Gmail-en Hotmail-accounts worden niet geaccepteerd.

## <a name="physical-connection-requirements"></a>Vereisten voor fysieke verbinding
* De locaties waar u verbinding kunt maken met micro soft voor directe peering of Exchange-peering, worden weer gegeven in [PeeringDB](https://www.peeringdb.com/net/694)
* **Exchange-peering:**
    * InterConnect moet groter zijn dan de enkelvoudige modus, met behulp van de juiste 10 Gbps-glasvezels.
    * Bij peers wordt verwacht dat hun poorten worden bijgewerkt wanneer het piek gebruik 50% overschrijdt.
* **Directe peering:**
    * InterConnect moet groter zijn dan de enkelvoudige modus, met behulp van de juiste 10 Gbps of 100Gbps-glasvezel.
    * Micro soft brengt alleen directe peering tot stand met ISP-of netwerk serviceproviders.
    * Bij peers wordt verwacht dat hun poorten worden bijgewerkt wanneer het piek gebruik 50% overschrijdt en de diverse capaciteit in elke metro lijn op één locatie of op verschillende locaties in een metro punt wordt gehandhaafd.
    * Elke directe peering bestaat uit twee verbindingen met twee micro soft Edge-routers van de routers van de peer die zich in de rand van de peer bevinden. Micro soft vereist twee BGP-sessies over deze verbindingen. De peer kan ervoor kiezen om geen redundante apparaten te implementeren.

## <a name="traffic-requirements"></a>Verkeers vereisten
* Peers via Exchange-peering moeten mini maal 200 MB verkeer hebben en minder dan 2 GB.  Voor verkeer van meer dan 2 GB direct peering moet worden gecontroleerd.
* Voor directe peering moet het verkeer van uw netwerk naar micro soft voldoen aan de onderstaande minimum vereiste.

    | Geografisch gebied                      | Mini maal verkeer naar micro soft   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mbps                       |
    | APAC (met uitzonde ring van India)      |   2 Gbps                       |
    | APAC (alleen India)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | Midden-Oosten              | 500 Mbps                       |
    | N.V.T.                       |   2 Gbps                       |

* **Situaties**
    * In NA, Europa, APAC en LATAM, Interconnect in ten minste drie geografische locaties, indien mogelijk, en het onderhoud van diverse capaciteit zodat verkeer binnen elke metro lijn kan worden gefailoverd.
    * In Afrika, Midden-Oosten en India, Interconnect zo veel mogelijk verschillende locaties. Moeten voldoende uiteenlopende capaciteit hebben om ervoor te zorgen dat verkeer in de regio blijft.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over de stappen om direct peering in te stellen met micro soft, volgt u het [scenario voor directe peering](walkthrough-direct-all.md).
* Voor meer informatie over de stappen voor het instellen van Exchange-peering met micro soft, volgt u het [scenario voor Exchange-peering](walkthrough-exchange-all.md).