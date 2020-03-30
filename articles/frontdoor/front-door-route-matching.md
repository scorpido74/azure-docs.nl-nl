---
title: Azure Front Door - Monitoring voor het afstemmen van routeregelregels | Microsoft Documenten
description: In dit artikel u begrijpen hoe Azure Front Door overeenkomt met welke routeringsregel u moet gebruiken voor een binnenkomende aanvraag
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 605974e76c3ca878784129f7c9827a78d0642da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471588"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Hoe Voordeur aanvragen koppelt aan een routeringsregel

Na het tot stand brengen van een verbinding en het doen van een SSL-handdruk, wanneer een aanvraag op een Front Door-omgeving landt, is een van de eerste dingen die Front Door doet, bepalen d.m.m.) de gedefinieerde actie. In het volgende document wordt uitgelegd hoe Voordeur bepaalt welke routeconfiguratie moet worden gebruikt bij het verwerken van een HTTP-aanvraag.

## <a name="structure-of-a-front-door-route-configuration"></a>Structuur van een voordeurrouteconfiguratie
Een voordeurregelconfiguratie bestaat uit twee belangrijke delen: een "linkerkant" en een "rechterkant". We koppelen het binnenkomende verzoek aan de linkerkant van de route, terwijl de rechterkant bepaalt hoe we de aanvraag verwerken.

### <a name="incoming-match-left-hand-side"></a>Inkomende wedstrijd (linkerkant)
De volgende eigenschappen bepalen of de binnenkomende aanvraag overeenkomt met de routeringsregel (of aan de linkerkant):

* **HTTP-protocollen** (HTTP/HTTPS)
* **Hosts** (bijvoorbeeld www\.foo.com, \*.bar.com)
* **Paden** (bijvoorbeeld /\*/ /gebruikers/\*, /file.gif)

Deze eigenschappen worden intern uitgebreid, zodat elke combinatie van Protocol/Host/Path een potentiële overeenkomstset is.

### <a name="route-data-right-hand-side"></a>Routegegevens (rechterkant)
De beslissing over het verwerken van de aanvraag, hangt af van de vraag of caching is ingeschakeld of niet voor de specifieke route. Als we dus geen antwoord in de cache hebben voor de aanvraag, sturen we het verzoek door naar de juiste backend in de geconfigureerde backendpool.

## <a name="route-matching"></a>Routematching
Deze sectie zal zich richten op hoe we overeenkomen met een bepaalde Front Door routing regel. Het basisconcept is dat we altijd overeenkomen met de **meest specifieke match eerst** kijken alleen naar de "linkerkant".  We matchen eerst op basis van http-protocol, dan frontend host, dan het pad.

### <a name="frontend-host-matching"></a>Frontend host matching
Bij het matchen van Frontend-hosts gebruiken we de logica zoals hieronder:

1. Zoek naar een routering met een exacte overeenkomst op de host.
2. Als er geen exacte frontendhosts overeenkomen, weigert u het verzoek en verzendt u een fout van 400 bad request.

Om dit proces verder uit te leggen, laten we eens kijken naar een voorbeeldconfiguratie van front door routes (alleen aan de linkerkant):

| Routeringsregel | Frontend hosts | Pad |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /gebruikers/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*, /afbeeldingen/\* |

Als de volgende binnenkomende aanvragen naar De Voordeur zijn verzonden, komen ze overeen met de volgende routeringsregels van bovenaf:

| Inkomende frontend host | Overeenkomende routeringsregel(en) |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Fout 400: Slecht verzoek |
| foo.adventure-works.com | C |
| contoso.com | Fout 400: Slecht verzoek |
| www\.adventure-works.com | Fout 400: Slecht verzoek |
| www\.northwindtraders.com | Fout 400: Slecht verzoek |

### <a name="path-matching"></a>Padmatching
Nadat u de specifieke frontendhost hebt bepaald en mogelijke routeringsregels hebt gefilterd op alleen de routes met die frontendhost, filtert Front Door vervolgens de routeringsregels op basis van het aanvraagpad. We gebruiken een vergelijkbare logica als frontend hosts:

1. Zoek naar een routeringsregel met een exacte overeenkomst op het pad
2. Als er geen exacte overeenkomstpaden zijn, zoekt u naar routeringsregels met een pad met jokertekens dat overeenkomt met
3. Als er geen routeringsregels worden gevonden met een overeenkomend pad, weigert u de aanvraag en retourneert u een HTTP-antwoord van de fout 400: Slecht verzoek.

>[!NOTE]
> Paden zonder wildcard worden beschouwd als exacte paden. Zelfs als het pad eindigt in een slash, wordt het nog steeds als exacte overeenkomst beschouwd.

Om verder uit te leggen, laten we eens kijken naar een andere reeks voorbeelden:

| Routeringsregel | Frontend-host    | Pad     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /pad/   |

Gezien deze configuratie zou de volgende voorbeeldtabel resulteren in:

| Binnenkomend verzoek    | Overeenkomende route |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Als er geen routeringsregels zijn voor een exact-match frontendhost met een catch-all route Path (),`/*`dan is er geen overeenkomst met een routeringsregel.
>
> Voorbeeldconfiguratie:
>
> | Route | Host             | Pad    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Overeenkomende tabel:
>
> | Binnenkomend verzoek       | Overeenkomende route |
> |------------------------|---------------|
> | profile.domain.com/other | Geen. Fout 400: Slecht verzoek |

### <a name="routing-decision"></a>Routeringsbesluit
Zodra we hebben gekoppeld aan een enkele front door routing regel, moeten we vervolgens kiezen hoe de aanvraag te verwerken. Als voor de overeenkomende routeringsregel een antwoord in de cache beschikbaar is, wordt hetzelfde teruggegeven aan de client. Anders is het volgende dat wordt geëvalueerd of u [URL Opnieuw schrijven (aangepast doorstuurpad)](front-door-url-rewrite.md) hebt geconfigureerd voor de overeenkomende routeringsregel of niet. Als er geen aangepast doorstuurpad is gedefinieerd, wordt de aanvraag doorgestuurd naar de juiste backend in de geconfigureerde backendpool zoals die is. Anders wordt het aanvraagpad bijgewerkt volgens het [aangepaste doorstuurpad](front-door-url-rewrite.md) dat is gedefinieerd en vervolgens doorsturen naar de backend.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
