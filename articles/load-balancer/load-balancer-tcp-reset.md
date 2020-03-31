---
title: Load Balancer TCP Reset op Idle in Azure
titleSuffix: Azure Load Balancer
description: Lees in dit artikel meer over Azure Load Balancer met bidirectionele TCP RST-pakketten op een idle time-out.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294405"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Load Balancer met TCP Reset in Idle

U [Standard Load Balancer](load-balancer-standard-overview.md) gebruiken om een voorspelbaarder toepassingsgedrag voor uw scenario's te maken door TCP Reset in Idle in te schakelen voor een bepaalde regel. Het standaardgedrag van Load Balancer is om stromen in stilte te laten vallen wanneer de niet-actieve time-out van een stroom is bereikt.  Als u deze functie inschakelt, wordt Load Balancer veroorzaakt om bidirectionele TCP Resets (TCP RST-pakket) te verzenden bij een niet-actieve time-out.  Dit zal uw toepassingseindpunten informeren dat de verbinding een time-out heeft en niet langer bruikbaar is.  Eindpunten kunnen indien nodig direct een nieuwe verbinding tot stand brengen.

![Load Balancer TCP opnieuw instellen](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
U wijzigt dit standaardgedrag en schakelt het verzenden van TCP-resets in op inactieve time-out op binnenkomende NAT-regels, regels voor taakverdeling en [uitgaande regels](https://aka.ms/lboutboundrules).  Wanneer per regel ingeschakeld, stuurt Load Balancer bidirectionele TCP Reset (TCP RST-pakketten) naar zowel client- als servereindpunten op het moment van idle time-out voor alle overeenkomende stromen.

Eindpunten die TCP RST-pakketten ontvangen, sluiten de bijbehorende socket onmiddellijk. Dit geeft een onmiddellijke melding aan de eindpunten dat de release van de verbinding heeft plaatsgevonden en dat toekomstige communicatie op dezelfde TCP-verbinding mislukt.  Toepassingen kunnen verbindingen verwijderen wanneer de socket sluit en verbindingen indien nodig herstellen zonder te wachten tot de TCP-verbinding uiteindelijk een time-out heeft.

Voor veel scenario's kan dit de noodzaak verminderen om KEEPALives (TCP -of toepassingslaag) te verzenden om de niet-actieve time-out van een stroom te vernieuwen. 

Als uw inactieve duur hoger is dan die van toegestaan door de configuratie of als uw toepassing een ongewenst gedrag vertoont waarbij TCP Resets zijn ingeschakeld, moet u mogelijk nog steeds TCP-keepalives (of keepalives van toepassingslagen) gebruiken om de levendigheid van de TCP-verbindingen te controleren.  Verder kan keepalives ook nuttig blijven voor wanneer de verbinding ergens in het pad wordt geproxieerd, met name applicatielaag keepalives.  

Onderzoek zorgvuldig het hele end-to-end scenario om te beslissen of u baat hebt bij het inschakelen van TCP Resets, het aanpassen van de niet-actieve time-out en of extra stappen nodig zijn om het gewenste toepassingsgedrag te garanderen.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>TCP-reset inschakelen bij inactieve time-out

Met API-versie 2018-07-01 u het verzenden van bidirectionele TCP-resets per regel inschakelen:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Beschikbaarheid van regio's

Beschikbaar in alle regio's.

## <a name="limitations"></a>Beperkingen

- TCP RST alleen verzonden tijdens TCP-verbinding in gevestigde status.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over [uitgaande regels](load-balancer-outbound-rules-overview.md).
- [TCP RST configureren op niet-actieve time-out](load-balancer-tcp-idle-timeout.md)
