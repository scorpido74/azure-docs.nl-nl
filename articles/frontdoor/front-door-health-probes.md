---
title: Azure front-deur-back-end-status controle | Microsoft Docs
description: Dit artikel helpt u te begrijpen hoe de status van uw back-ends wordt gecontroleerd door Azure front-deur
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471571"
---
# <a name="health-probes"></a>Statuscontroles

Om de status en nabijheid van elke back-end vanuit een bepaalde front-deur omgeving te bepalen, stuurt elke front-deur omgeving periodiek een synthetische HTTP/HTTPS-aanvraag naar elk van uw geconfigureerde back-ends. Front Door gebruikt de reacties op deze tests dan om te bepalen welke back-ends het beste zijn voor het routeren van echte aanvragen van klanten. 

> [!WARNING]
> Omdat de voor deur veel Edge-omgevingen wereld wijd heeft, vraagt de status test een volume aan uw back-ends een hoge bereik van 25 aanvragen elke minuut tot Maxi maal 1200 aanvragen per minuut, afhankelijk van de frequentie van de Health probe die is geconfigureerd. Met de standaard test frequentie van 30 seconden moet het test volume op uw back-end ongeveer 200 aanvragen per minuut bedragen.

## <a name="supported-protocols"></a>Ondersteunde protocollen

De voor deur ondersteunt het verzenden van tests over HTTP-of HTTPS-protocollen. De tests worden via dezelfde TCP-poorten verzonden als de poorten die zijn geconfigureerd voor het routeren van aanvragen van klanten. Deze instelling kan niet worden overschreven.

## <a name="supported-http-methods-for-health-probes"></a>Ondersteunde HTTP-methoden voor status tests

De voor deur ondersteunt de volgende HTTP-methoden voor het verzenden van de status tests:

1. **Ophalen:** De GET-methode geeft aan welke gegevens (in de vorm van een entiteit) worden geïdentificeerd door de aanvraag-URI.
2. **Kop:** De HEAD-methode is identiek om aan te geven, behalve dat de server geen bericht tekst in het antwoord mag retour neren. Voor nieuwe profielen voor de voor deur wordt standaard de test methode ingesteld als HEAD.

> [!NOTE]
> Voor een lagere belasting en kosten op uw back-end wordt aanbevolen om hoofd aanvragen voor status tests te gebruiken.

## <a name="health-probe-responses"></a>Antwoorden op status testen

| Antwoorden  | Description | 
| ------------- | ------------- |
| Status bepalen  |  Een 200 OK-status code geeft aan dat de back-end in orde is. Alle andere zaken worden beschouwd als een fout. Als er om een of andere reden (inclusief netwerk fout) geen geldig HTTP-antwoord wordt ontvangen voor een test, wordt de test als een fout beschouwd.|
| Meet latentie  | Latentie is de klok tijd gemeten vanaf het moment onmiddellijk voordat de test aanvraag wordt verzonden naar het moment dat de laatste byte van het antwoord wordt ontvangen. We gebruiken een nieuwe TCP-verbinding voor elke aanvraag, zodat deze meting niet kan worden afgestemd op back-ends met bestaande warme verbindingen.  |

## <a name="how-front-door-determines-backend-health"></a>De status van de back-end voor de deur bepalen

Azure front deur maakt gebruik van hetzelfde proces van drie stappen hieronder voor alle algoritmen om de status te bepalen.

1. Uitgeschakelde back-ends uitsluiten.

2. Back-end met status controles uitsluiten:
    * Deze selectie wordt uitgevoerd door de laatste _n_ antwoorden op de status test te bekijken. Als ten minste _x_ in orde is, wordt de back-end als gezond beschouwd.

    * _n_ wordt geconfigureerd door de eigenschap SampleSize in instellingen voor taak verdeling te wijzigen.

    * _x_ wordt geconfigureerd door de eigenschap SuccessfulSamplesRequired in instellingen voor taak verdeling te wijzigen.

3. Van de set met gezonde back-ends in de back-end-pool, worden ook de latentie (round-trip tijd) voor elke back-end gemeten.


## <a name="complete-health-probe-failure"></a>Het volt ooien van de status test is mislukt

Als de status controles voor elke back-end in een back-end-groep mislukken, worden alle back-ends in orde beschouwd en wordt het verkeer gerouteerd in een round robin distributie over alle.

Zodra een back-end een goede status krijgt, wordt het normale algoritme voor taak verdeling door de voor deur hervat.

## <a name="disabling-health-probes"></a>Status controles uitschakelen

Als u één back-end in uw back-end-groep hebt, kunt u ervoor kiezen om de status controles uit te scha kelen die de belasting van de back-end van uw toepassing verminderen. Zelfs als u meerdere back-ends in de back-end-groep hebt, maar slechts een van deze is ingeschakeld, kunt u status controles uitschakelen.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
