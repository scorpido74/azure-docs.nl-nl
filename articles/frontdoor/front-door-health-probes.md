---
title: Azure Front Door - backend health monitoring | Microsoft Documenten
description: Dit artikel helpt u te begrijpen hoe Azure Front Door de status van uw back-ends bewaakt
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471571"
---
# <a name="health-probes"></a>Statuscontroles

Om de gezondheid en nabijheid van elke backend van een bepaalde Front Door-omgeving te bepalen, stuurt elke Front Door-omgeving periodiek een synthetisch HTTP/HTTPS-verzoek naar elk van uw geconfigureerde backends. Front Door gebruikt de reacties op deze tests dan om te bepalen welke back-ends het beste zijn voor het routeren van echte aanvragen van klanten. 

> [!WARNING]
> Aangezien Front Door wereldwijd veel randomgevingen heeft, kan het volume van de statussonde vrij hoog zijn - variërend van 25 aanvragen per minuut tot wel 1200 aanvragen per minuut, afhankelijk van de statussondefrequentie die is geconfigureerd. Met de standaard sondefrequentie van 30 seconden moet het volume van de sonde op uw backend ongeveer 200 aanvragen per minuut zijn.

## <a name="supported-protocols"></a>Ondersteunde protocollen

Front Door ondersteunt het verzenden van sondes via HTTP- of HTTPS-protocollen. De tests worden via dezelfde TCP-poorten verzonden als de poorten die zijn geconfigureerd voor het routeren van aanvragen van klanten. Deze instelling kan niet worden overschreven.

## <a name="supported-http-methods-for-health-probes"></a>Ondersteunde HTTP-methoden voor gezondheidssondes

Front Door ondersteunt de volgende HTTP-methoden voor het verzenden van de statussondes:

1. **GET:** De GET-methode betekent ophalen welke informatie (in de vorm van een entiteit) wordt geïdentificeerd door de Request-URI.
2. **HOOFD:** De HEAD-methode is identiek aan GET, behalve dat de server geen bericht-body in de reactie mag retourneren. Voor nieuwe frontdoorprofielen is de sondemethode standaard ingesteld als HEAD.

> [!NOTE]
> Voor lagere belasting en kosten op uw backends, Front Door beveelt het gebruik van HEAD verzoeken voor de gezondheid sondes.

## <a name="health-probe-responses"></a>Reacties op gezondheidssonde

| Antwoorden  | Beschrijving | 
| ------------- | ------------- |
| Gezondheid bepalen  |  Een statuscode van 200 OK geeft aan dat de backend in orde is. Al het andere wordt beschouwd als een mislukking. Als om welke reden (inclusief netwerkstoring) een geldig HTTP-antwoord niet wordt ontvangen voor een sonde, wordt de sonde als een fout geteld.|
| Latentie meten  | Latentie is de wandkloktijd gemeten vanaf het moment vlak voordat we het sondeverzoek sturen naar het moment waarop we de laatste byte van het antwoord ontvangen. We gebruiken voor elk verzoek een nieuwe TCP-verbinding, zodat deze meting niet is bevooroordeeld naar backends met bestaande warme verbindingen.  |

## <a name="how-front-door-determines-backend-health"></a>Hoe Front Door de gezondheid van de backend bepaalt

Azure Front Door gebruikt hetzelfde proces in drie stappen hieronder voor alle algoritmen om de status te bepalen.

1. Uitgeschakelde backends uitsluiten.

2. Sluit backends met fouten in de statussondes uit:
    * Deze selectie wordt gedaan door te kijken naar de laatste _n_ gezondheid sonde reacties. Als ten minste _x_ gezond zijn, wordt de backend als gezond beschouwd.

    * _n_ wordt geconfigureerd door de eigenschap SampleSize te wijzigen in taakverdelingsinstellingen.

    * _x_ is geconfigureerd door de eigenschap Met succesvollemonstersvereist te wijzigen in taakverdelingsinstellingen.

3. Uit de set van gezonde backends in de backend pool, Front Door meet bovendien en onderhoudt de latentie (heen-en-terug tijd) voor elke backend.


## <a name="complete-health-probe-failure"></a>Volledige statussonde mislukt

Als de gezondheid sondes niet voor elke backend in een backend zwembad, dan Front Door overweegt alle backends gezond en routes verkeer in een ronde robin verdeling over alle van hen.

Zodra elke backend terugkeert naar een gezonde staat, dan zal Front Door het normale load-balancing algoritme hervatten.

## <a name="disabling-health-probes"></a>Gezondheidssondes uitschakelen

Als u één backend in uw backendpool hebt, u ervoor kiezen om de statussondes uit te schakelen die de belasting op de backend van uw toepassing verminderen. Zelfs als u meerdere backends in de backend pool, maar slechts een van hen is ingeschakeld staat, u uitschakelen gezondheid sondes.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
