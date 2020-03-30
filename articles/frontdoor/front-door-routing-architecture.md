---
title: Azure Front Door - routeringsarchitectuur | Microsoft Documenten
description: Dit artikel helpt u inzicht te krijgen in het globale weergaveaspect van de architectuur van Front Door.
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
ms.openlocfilehash: fd1f06bcb92ea97e0e9e9a6eefeac957031575a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471554"
---
# <a name="routing-architecture-overview"></a>Overzicht van routeringsarchitectuur

De Azure-voordeur wanneer deze uw clientaanvragen ontvangt, beantwoordt deze vervolgens (als caching is ingeschakeld) of stuurt deze door naar de juiste backend van de toepassing (als omgekeerde proxy).

</br>Er zijn mogelijkheden om het verkeer te optimaliseren bij het routeren naar Azure Front Door en bij het routeren naar backends.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>De frontdoor-omgeving selecteren voor verkeersroutering (Anycast)

Routering naar de Azure Front Door-omgevingen maakt gebruik van [Anycast](https://en.wikipedia.org/wiki/Anycast) voor zowel DNS-verkeer (Domain Name System) als HTTP -verkeer (Hypertext Transfer Protocol), zodat gebruikersverkeer naar de dichtstbijzijnde omgeving gaat in termen van netwerktopologie (minste hop). Deze architectuur biedt doorgaans betere retourtijden voor eindgebruikers (het maximaliseren van de voordelen van Split TCP). Front Door organiseert zijn omgevingen in primaire en fallback "ringen".  De buitenste ring heeft omgevingen die dichter bij gebruikers staan en bieden lagere latencies.  De binnenste ring heeft omgevingen die de failover voor de buitenste ring omgeving kan verwerken in het geval er een probleem optreedt. De buitenste ring is het voorkeursdoel voor al het verkeer, maar de binnenste ring is nodig om de verkeersoverloop vanaf de buitenste ring te verwerken. In termen van VIP's (Virtual Internet Protocol adressen), elke frontend host, of domein geserveerd door Front Door is toegewezen aan een primaire VIP, die wordt aangekondigd door omgevingen in zowel de binnenste en buitenste ring, evenals een fallback VIP, die alleen wordt aangekondigd door omgevingen in de binnenste ring. 

</br>Deze algemene strategie zorgt ervoor dat verzoeken van uw eindgebruikers altijd de dichtstbijzijnde Front Door-omgeving bereiken en dat zelfs als de gewenste Front Door-omgeving ongezond is, het verkeer automatisch naar de dichtstbijzijnde omgeving wordt verplaatst.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Verbinding maken met de frontdoor-omgeving (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) is een techniek om latencies en TCP-problemen te verminderen door het breken van een verbinding die een hoge retourtijd in kleinere stukken zou oplopen.  Door de Front Door-omgevingen dichter bij eindgebruikers te plaatsen en TCP-verbindingen in de Front Door-omgeving te beëindigen, wordt één TCP-verbinding met een grote retourtijd (RTT) naar de backend van de toepassing opgesplitst in twee TCP-verbindingen. De korte verbinding tussen de eindgebruiker en de Front Door-omgeving betekent dat de verbinding wordt gelegd over drie korte retourvluchten in plaats van drie lange rondreizen, waardoor latentie wordt bespaard.  De lange verbinding tussen de Front Door-omgeving en de backend kan vooraf worden vastgesteld en hergebruikt voor meerdere gesprekken met eindgebruikers, waardoor de TCP-verbindingstijd opnieuw wordt bespaard.  Het effect wordt vermenigvuldigd bij het opzetten van een SSL/TLS -verbinding (Transport Layer Security) omdat er meer retouren zijn om de verbinding te beveiligen.

## <a name="processing-request-to-match-a-routing-rule"></a>Verwerkingsaanvraag om een routeringsregel te koppelen
Na het tot stand brengen van een verbinding en het doen van een SSL-handshake, is het overeenkomen van een routeringsregel de eerste stap wanneer een aanvraag op een Front Door-omgeving terechtkomt. Deze overeenkomst bepaalt in principe uit alle configuraties in Front Door, welke specifieke routeregel moet overeenkomen met het verzoek. Lees hoe Front Door [routematching](front-door-route-matching.md) doet voor meer informatie.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Beschikbare back-ends in de backendpool identificeren voor de routeringsregel
Zodra Front Door een overeenkomst heeft voor een routeringsregel op basis van de binnenkomende aanvraag en als er geen caching is, is de volgende stap het trekken van de status van de status van de status van de status van de status van de status van de status van de backend die is gekoppeld aan de overeenkomende route. Lees hoe Front Door de backend-status controleert met behulp van [Health Probes](front-door-health-probes.md) voor meer informatie.

## <a name="forwarding-the-request-to-your-application-backend"></a>Het verzoek doorsturen naar de backend van uw toepassing
Tot slot, ervan uitgaande dat er geen caching geconfigureerd, de gebruiker verzoek wordt doorgestuurd naar de "beste" backend op basis van uw [Front Door routing methode](front-door-routing-methods.md) configuratie.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
