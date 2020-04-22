---
title: Azure Front Door - methoden voor het routeren van verkeer | Microsoft Documenten
description: Dit artikel helpt u inzicht te krijgen in de verschillende verkeersrouteringsmethoden die door Front Door worden gebruikt
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
ms.openlocfilehash: b7dd00d28ecfe844094677e0ae19f4fd359d97d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687808"
---
# <a name="front-door-routing-methods"></a>Routeringsmethoden voor de voordeur

Azure Front Door ondersteunt verschillende verkeersrouteringsmethoden om te bepalen hoe u uw HTTP/HTTPS-verkeer routeren naar de verschillende serviceeindpunten. Wanneer elk van uw clientaanvragen front door bereikt, wordt de geconfigureerde routeringsmethode toegepast om ervoor te zorgen dat de aanvragen worden doorgestuurd naar de beste backend-instantie. 

Er zijn vier belangrijke concepten voor verkeersroutering beschikbaar in Front Door:

* ** [Latentie](#latency):** De op latentie gebaseerde routering zorgt ervoor dat aanvragen worden verzonden naar de laagste latentie backends die acceptabel zijn binnen een gevoeligheidsbereik. In principe worden uw gebruikersverzoeken verzonden naar de "dichtstbijzijnde" set backends met betrekking tot netwerklatentie.
* ** [Prioriteit](#priority):** U prioriteiten toewijzen aan uw verschillende backends wanneer u een back-end van de primaire service wilt gebruiken voor al het verkeer en back-ups wilt maken voor het geval de primaire of de back-upbackends niet beschikbaar zijn.
* ** [Gewogen](#weighted):** U gewichten toewijzen aan uw verschillende backends wanneer u verkeer wilt verdelen over een set backends, gelijkmatig of op basis van gewichtscoëfficiënten.
* ** [Sessieaffiniteit](#affinity):** U sessieaffiniteit configureren voor uw frontendhosts of domeinen wanneer u wilt dat volgende aanvragen van een gebruiker naar dezelfde backend worden verzonden zolang de gebruikerssessie nog actief is en de backend-instantie nog steeds gezond rapporteert op basis van statussondes. 

Alle Front Door-configuraties omvatten bewaking van de back-endstatus en geautomatiseerde, directe failover wereldwijd. Zie Voor meer informatie [Front Door Backend Monitoring](front-door-health-probes.md). Uw voordeur kan worden geconfigureerd om te werken op basis van een enkele routeringsmethode en afhankelijk van uw toepassingsbehoeften u meerdere of al deze routeringsmethoden in combinatie gebruiken om een optimale routeringstopologie te bouwen.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Laagste latencies gebaseerde verkeersroutering

Het implementeren van backends op twee of meer locaties over de hele wereld kan de responsiviteit van veel toepassingen verbeteren door verkeer te routeren naar de locatie die 'het dichtst' bij uw eindgebruikers staat. De standaardmethode voor verkeersroutering voor uw frontdoorconfiguratie stuurt aanvragen van uw eindgebruikers door naar de dichtstbijzijnde backend van de Front Door-omgeving die de aanvraag heeft ontvangen. In combinatie met de Anycast-architectuur van Azure Front Door zorgt deze aanpak ervoor dat elk van uw eindgebruikers maximale prestaties gepersonaliseerd krijgt op basis van hun locatie.

De 'dichtstbijzijnde' backend is niet noodzakelijkerwijs het dichtst zoals gemeten door geografische afstand. In plaats daarvan bepaalt Front Door de dichtstbijzijnde backends door netwerklatentie te meten. Lees meer over [de routeringsarchitectuur van Front Door.](front-door-routing-architecture.md) 

Hieronder vindt u de algehele beslissingsstroom:

| Beschikbare backends | Prioriteit | Latentiesignaal (gebaseerd op gezondheidssonde) | Gewichten |
|-------------| ----------- | ----------- | ----------- |
| Selecteer ten eerste alle backends die zijn ingeschakeld en weer gezond (200 OK) voor de statussonde. Zeg, er zijn zes backends A, B, C, D, E en F, en onder hen C is ongezond en E is uitgeschakeld. Dus, lijst van beschikbare backends is A, B, D en F.  | Vervolgens worden de backends met de hoogste prioriteit geselecteerd. Zeg, backend A, B en D hebben prioriteit 1 en backend F heeft een prioriteit van 2. Geselecteerde backends zijn dus A, B en D.| Selecteer de backends met latentiebereik (de minste latentie & latentiegevoeligheid in ms opgegeven). Bijvoorbeeld, als A 15 ms is, is B 30 ms en D is 60 ms verwijderd van de Front Door-omgeving waar het verzoek is geland, en latentiegevoeligheid is 30 ms, dan bestaat de laagste latentiepool uit backend A en B, omdat D meer dan 30 ms verwijderd is van de dichtstbijzijnde backend die A is. | Ten slotte zal Front Door rond robin het verkeer onder de uiteindelijk geselecteerde pool van backends in de verhouding van de opgegeven gewichten. Zeg, als backend A heeft een gewicht van 5 en backend B heeft een gewicht van 8, dan is het verkeer zal worden verdeeld in de verhouding van 5:8 onder backends A en B. |

>[!NOTE]
> Standaard is de eigenschap latentiegevoeligheid ingesteld op 0 ms, dat wil zeggen, altijd de aanvraag doorsturen naar de snelst beschikbare backend.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Op prioriteit gebaseerde verkeersroutering

Vaak wil een organisatie betrouwbaarheid bieden voor haar services door een of meer back-upservices te implementeren voor het geval hun primaire service uitvalt. In de hele branche wordt deze topologie ook wel Active/Standby of Active/Passive deployment topology genoemd. Met de 'Priority'-verkeersrouteringsmethode kunnen Azure-klanten dit failoverpatroon eenvoudig implementeren.

Uw standaard voordeur bevat een even grote prioriteitslijst met backends. Standaard stuurt Front Door alleen verkeer naar de back-ends met de hoogste prioriteit (laagste waarde voor prioriteit), dat wil zeggen de primaire set backends. Als de primaire backends niet beschikbaar zijn, leidt De Voordeur het verkeer naar de secundaire set backends (op één na laagste waarde voor prioriteit). Als zowel de primaire als de secundaire backends niet beschikbaar zijn, gaat het verkeer naar de derde, enzovoort. Beschikbaarheid van de backend is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de doorlopende backend status zoals bepaald door de status van de status van de status.

### <a name="configuring-priority-for-backends"></a>Prioriteit configureren voor backends

Elk van de backends in uw backend pool binnen de Front Door configuratie heeft een eigenschap genaamd 'Priority', die kan een aantal tussen 1 en 5. Met Azure Front Door configureert u de backendprioriteit die deze eigenschap expliciet gebruikt voor elke backend. Deze eigenschap is een waarde tussen 1 en 5. Lagere waarden vertegenwoordigen een hogere prioriteit. Back-ends kunnen prioriteitswaarden delen.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeersrouteringsmethode
Met de 'Weighted' traffic-routing-methode u het verkeer gelijkmatig verdelen of een vooraf gedefinieerde weging gebruiken.

In de methode Gewogen verkeersroutering wijst u een gewicht toe aan elke backend in de voordeurconfiguratie van uw backendpool. Het gewicht is een geheel getal tussen 1 en 1000. Deze parameter hanteert een standaardgewicht van '50'.

Onder de lijst van beschikbare backends binnen de geaccepteerde latentiegevoeligheid (zoals gespecificeerd), wordt het verkeer verdeeld in een round-robin mechanisme in de verhouding van de opgegeven gewichten. Als de latentiegevoeligheid is ingesteld op 0 milliseconden, wordt deze eigenschap niet van kracht, tenzij er twee backends zijn met dezelfde netwerklatentie. 

De gewogen methode maakt een aantal nuttige scenario's mogelijk:

* Geleidelijke upgrade van **toepassingen:** Wijs een percentage verkeer toe om naar een nieuwe backend te routeren en verhoog het verkeer geleidelijk in de loop van de tijd om het op gelijke voet te brengen met andere backends.
* **Toepassingsmigratie naar Azure:** maak een back-endpool met zowel Azure als externe backends. Pas het gewicht van de backends aan om de nieuwe backends te verkiezen. U dit geleidelijk instellen te beginnen met het hebben van de nieuwe backends uitgeschakeld, dan toewijzen ze de laagste gewichten, langzaam te verhogen tot niveaus waar ze het meeste verkeer. Dan eindelijk het uitschakelen van de minder voorkeur backends en het verwijderen van hen uit het zwembad.  
* **Cloud-bursting voor extra capaciteit**: Snel uitbreiden van een on-premises implementatie in de cloud door het achter Front Door. Wanneer u extra capaciteit in de cloud nodig hebt, u meer back-ends toevoegen of inschakelen en opgeven welk deel van het verkeer naar elke backend gaat.

## <a name="session-affinity"></a><a name = "affinity"></a>Sessieaffiniteit
Standaard stuurt Front Door aanvragen die afkomstig zijn van dezelfde client door naar verschillende backends op basis van load-balancing configuratie, met name omdat de latencies voor verschillende backends veranderen of als verschillende aanvragen van dezelfde gebruiker op een andere Front Door-omgeving worden weergegeven. Bij sommige stateful toepassingen en in bepaalde andere situaties wordt er echter de voorkeur aan gegeven om opeenvolgende aanvragen van één gebruiker naar de back-end te sturen waarmee ook de eerste aanvraag is verwerkt. De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde back-end wilt behouden. Door door door front door beheerde cookies te gebruiken, kan Azure Front Door het volgende verkeer van een gebruikerssessie naar dezelfde backend leiden voor verwerking zolang de backend in orde is en de gebruikerssessie niet is verlopen. 

U kunt sessieaffiniteit inschakelen op het hostniveau van de front-end, voor elk van de geconfigureerde domeinen (of subdomeinen). Na het inschakelen voegt Front Door een cookie toe aan de gebruikerssessie. Met op cookies gebaseerde sessieaffiniteit kan Front Door verschillende gebruikers herkennen, zelfs als deze hetzelfde IP-adres hebben. Hierdoor kan er nog meer verkeer worden verdeeld tussen uw verschillende back-ends.

De cookie blijft even lang actief als de gebruikerssessie, omdat Front Door momenteel alleen ondersteuning biedt voor sessiecookies. 

> [!NOTE]
> Publieke volmachten kunnen de affiniteit met sessies verstoren. Dit komt omdat het instellen van een sessie vereist Front Door om een sessie affiniteit cookie toe te voegen aan het antwoord, die niet kan worden gedaan als het antwoord is cacheable als het zou verstoren de cookies van andere clients die dezelfde bron. Om dit te voorkomen, wordt sessieaffiniteit **niet** vastgesteld als de backend een cacheable antwoord stuurt wanneer dit wordt geprobeerd. Als de sessie al is vastgesteld, maakt het niet uit of het antwoord van de backend cacheable is.
> Sessieaffiniteit wordt in de volgende omstandigheden vastgesteld, **tenzij** het antwoord een HTTP 304-statuscode heeft:
> - Het antwoord heeft specifieke ```Cache-Control``` waarden ingesteld voor de koptekst die caching voorkomt, zoals 'privé' of no-store.The response has specific values set for the header that prevents caching, such as "private" or no-store".
> - Het antwoord ```Authorization``` bevat een koptekst die niet is verlopen.
> - Het antwoord heeft een HTTP 302-statuscode.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
