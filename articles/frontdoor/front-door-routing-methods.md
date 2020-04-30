---
title: Methoden voor het routeren van verkeer van Azure voor de voor deur | Microsoft Docs
description: Dit artikel helpt u bij het begrijpen van de verschillende routerings methoden voor verkeer die worden gebruikt door de voor deur
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687808"
---
# <a name="front-door-routing-methods"></a>Routerings methoden voor voor deur

Azure front-deur ondersteunt diverse methoden voor het routeren van verkeer om te bepalen hoe u uw HTTP/HTTPS-verkeer naar de verschillende service-eind punten kunt routeren. Met elk van de aanvragen van uw client die de voor deur bereiken, wordt de geconfigureerde routerings methode toegepast om ervoor te zorgen dat de aanvragen worden doorgestuurd naar de beste back-end-instantie. 

Er zijn vier belang rijke concepten voor verkeers routering die beschikbaar zijn in de voor deur:

* ** [Latentie](#latency):** Met de latentie gebaseerde route ring zorgt u ervoor dat aanvragen worden verzonden naar de laagste latentie back-end die acceptabel is binnen een gevoeligheids bereik. In principe worden uw gebruikers aanvragen verzonden naar de ' dichtstbijgelegen ' set back-ends met betrekking tot netwerk latentie.
* ** [Prioriteit](#priority):** U kunt prioriteiten toewijzen aan uw verschillende back-endservers wanneer u een back-end van een primaire service voor alle verkeer wilt gebruiken en back-ups wilt maken voor het geval de primaire of de back-end niet beschikbaar zijn.
* ** [Gewogen](#weighted):** U kunt gewichten toewijzen aan uw verschillende back-ends wanneer u verkeer wilt distribueren over een reeks back-ends, hetzij gelijkmatig als op basis van gewichts coëfficiënten.
* ** [Sessie affiniteit](#affinity):** U kunt sessie affiniteit voor uw frontend-hosts of-domeinen configureren als u wilt dat de volgende aanvragen van een gebruiker naar dezelfde back-end worden verzonden, mits de gebruikers sessie nog steeds actief is en het back-end-exemplaar in orde is op basis van status controles. 

Alle Front Door-configuraties omvatten bewaking van de back-endstatus en geautomatiseerde, directe failover wereldwijd. Zie voor meer informatie [back-end van front deur controleren](front-door-health-probes.md). U kunt de voor deur zo configureren dat deze op basis van één routerings methode werkt en afhankelijk van de behoeften van uw toepassing, kunt u meerdere of al deze routerings methoden in combi natie gebruiken om een optimale routerings topologie te bouwen.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Laagste verkeer op basis van latentie-route ring

Het implementeren van back-ends op twee of meer locaties over de hele wereld kan de reactie snelheid van veel toepassingen verbeteren door verkeer naar de locatie die zich het dichtst bij uw eind gebruikers bevindt. De standaard methode voor het routeren van verkeer voor de configuratie van de voor deur stuurt aanvragen van uw eind gebruikers door naar de dichtstbijzijnde back-end vanuit de front-deur omgeving die de aanvraag heeft ontvangen. In combi natie met de anycast-architectuur van de voor deur van Azure, zorgt deze benadering ervoor dat elk van uw eind gebruikers de maximale prestaties op basis van hun locatie kan verkrijgen.

De ' dichtstbijgelegen ' back-end is niet noodzakelijkerwijs het dichtst bij de geografische afstand gemeten. In plaats daarvan bepaalt de voor deur de dichtstbijzijnde back-end door netwerk latentie te meten. Lees meer over [de routerings architectuur van de front deur](front-door-routing-architecture.md). 

Hieronder ziet u de algehele beslissings stroom:

| Beschik bare back-ends | Prioriteit | Latentie signaal (op basis van de status test) | Gewicht |
|-------------| ----------- | ----------- | ----------- |
| Selecteer eerst alle back-ends die zijn ingeschakeld en in orde zijn geretourneerd (200 OK) voor de status test. Stel dat er zes back-ends A, B, C, D, E en F zijn, en dat er onder C sprake is van een slechte status en E is uitgeschakeld. De lijst met beschik bare back-ends is dus A, B, D en F.  | Vervolgens worden de back-end met de hoogste prioriteit geselecteerd voor de beschik bare items. Zeg, back-end A, B en D hebben prioriteit 1 en backend F de prioriteit 2. Daarom zijn de geselecteerde back-ends A, B en D.| Selecteer de back-ends met het latentie bereik (minste latentie & latentie gevoeligheid in MS opgegeven). Stel, als een 15 MS is, B 30 MS en D 60 MS weg van de front-deur omgeving waar de aangevraagde aanvraag en latentie gevoeligheid 30 MS zijn, en de laagste latentie groep bestaat uit de back-end A en B, omdat D groter is dan 30 MS van de dichtstbijzijnde back-end. | Ten slotte wordt de voor deur round robin het verkeer tussen de uiteindelijke geselecteerde pool met back-ends in de verhouding van de opgegeven gewichten. Als back-end A een gewicht van 5 heeft en backend B een gewicht van 8 heeft, wordt het verkeer gedistribueerd in de verhouding 5:8 tussen back-ends A en B. |

>[!NOTE]
> De eigenschap voor de latentie gevoeligheid wordt standaard ingesteld op 0 MS, dat wil zeggen, de aanvraag altijd door sturen naar de snelste beschik bare back-end.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Op prioriteit gebaseerd verkeer: route ring

Een organisatie wil vaak betrouw baarheid bieden voor de services door een of meer back-upservices te implementeren voor het geval de primaire service uitvalt. In de hele branche wordt deze topologie ook wel actief/stand-by of actieve/passieve implementatie topologie genoemd. Met de routerings methode ' Priority ' kunnen Azure-klanten het failover-patroon eenvoudig implementeren.

De standaard-deur bevat een lijst met gelijke prioriteiten voor de back-end. Standaard verzendt de voor deur alleen verkeer naar de back-ends met de hoogste prioriteit (laagste waarde voor de prioriteit), de primaire set back-ends. Als de primaire back-ends niet beschikbaar zijn, stuurt front deur het verkeer naar de secundaire set back-ends (tweede laagste waarde voor prioriteit). Als zowel de primaire als de secundaire back-end niet beschikbaar zijn, gaat het verkeer naar de derde, enzovoort. De beschik baarheid van de back-end is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de voortdurende status van de back-end, zoals bepaald door de status controles.

### <a name="configuring-priority-for-backends"></a>De prioriteit voor back-ends configureren

Elk van de back-ends in uw back-end-groep binnen de configuratie van de voor deur heeft een eigenschap met de naam priority. Dit kan een getal tussen 1 en 5 zijn. Met de voor deur van Azure kunt u de back-end-prioriteit expliciet configureren met behulp van deze eigenschap voor elke back-end. Deze eigenschap is een waarde tussen 1 en 5. Lagere waarden vertegenwoordigen een hogere prioriteit. Back-ends kunnen prioriteits waarden delen.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeer-routerings methode
Met de methode gewogen verkeers routering kunt u verkeer gelijkmatig distribueren of een vooraf gedefinieerde wegings factor gebruiken.

In de routerings methode gewogen verkeer wijst u een gewicht toe aan elke back-end in de front-deur configuratie van de back-endserver. Het gewicht is een geheel getal tussen 1 en 1000. Voor deze para meter wordt het standaard gewicht van ' 50 ' gebruikt.

In de lijst met beschik bare back-ends binnen de toegestane latentie gevoeligheid (zoals opgegeven), wordt het verkeer gedistribueerd in een Round Robin-mechanisme in de verhouding van de opgegeven gewichten. Als de latentie gevoeligheid is ingesteld op 0 milliseconden, wordt deze eigenschap pas van kracht als er twee back-ends zijn met dezelfde netwerk latentie. 

De methode weighted maakt enkele handige scenario's mogelijk:

* **Geleidelijke upgrade**van de toepassing: wijs een percentage van het verkeer toe om naar een nieuwe back-end te routeren, en verhoog het verkeer geleidelijk gedurende een periode om het gemiddeld met andere back-ends te brengen.
* **Toepassings migratie naar Azure**: Maak een back-end-groep met zowel Azure als externe back-endservers. Pas het gewicht van de back-ends aan om de voor keur te geven aan de nieuwe back-end. U kunt dit geleidelijk instellen, te beginnen met het uitschakelen van de nieuwe back-ups en vervolgens de laagste gewichten toewijzen, waardoor deze langzaam toeneemt op het niveau van het verkeer. Vervolgens worden de minder voorkeurs back-ends uitgeschakeld en verwijderd uit de groep.  
* **Cloud-bursting voor extra capaciteit**: Vouw snel een on-premises implementatie uit in de Cloud door deze achter de deur te plaatsen. Wanneer u extra capaciteit nodig hebt in de Cloud, kunt u meer back-endservers toevoegen of inschakelen en opgeven welk deel van het verkeer naar elke back-end gaat.

## <a name="session-affinity"></a><a name = "affinity"></a>Sessie affiniteit
Standaard zonder sessie affiniteit stuurt front deur aanvragen die afkomstig zijn van dezelfde client naar verschillende back-ends op basis van de taakverdelings configuratie, met name als de latenties voor verschillende back-ends worden gewijzigd of als andere aanvragen van hetzelfde gebruikers recht op een andere front-deur omgeving worden ingesteld. Bij sommige stateful toepassingen en in bepaalde andere situaties wordt er echter de voorkeur aan gegeven om opeenvolgende aanvragen van één gebruiker naar de back-end te sturen waarmee ook de eerste aanvraag is verwerkt. De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde back-end wilt behouden. Door gebruik te maken van door de voor deur beheerde cookies kan Azure front-deur volgend verkeer van een gebruikers sessie naar dezelfde back-end omleiden voor verwerking, zolang de back-end in orde is en de gebruikers sessie niet is verlopen. 

U kunt sessieaffiniteit inschakelen op het hostniveau van de front-end, voor elk van de geconfigureerde domeinen (of subdomeinen). Na het inschakelen voegt Front Door een cookie toe aan de gebruikerssessie. Met op cookies gebaseerde sessieaffiniteit kan Front Door verschillende gebruikers herkennen, zelfs als deze hetzelfde IP-adres hebben. Hierdoor kan er nog meer verkeer worden verdeeld tussen uw verschillende back-ends.

De cookie blijft even lang actief als de gebruikerssessie, omdat Front Door momenteel alleen ondersteuning biedt voor sessiecookies. 

> [!NOTE]
> Open bare proxy's kunnen problemen veroorzaken met sessie affiniteit. Dit komt doordat voor het maken van een sessie aan de voor deur een sessie affiniteits cookie moet worden toegevoegd aan het antwoord. Dit kan niet worden gedaan als het antwoord in de cache kan worden opgeslagen, omdat de cookies van andere clients die dezelfde bron aanvragen, worden verstoord. Als u dit wilt beveiligen, wordt de sessie affiniteit **niet** tot stand gebracht als de back-end een cachebaar antwoord stuurt wanneer dit wordt geprobeerd. Als de sessie al tot stand is gebracht, maakt het niet uit of het antwoord van de back-end in de cache kan worden opgeslagen.
> Sessie affiniteit wordt in de volgende omstandigheden tot stand gebracht, **tenzij** het antwoord een HTTP 304-status code heeft:
> - Het antwoord heeft specifieke waarden die zijn ingesteld ```Cache-Control``` voor de header, waardoor caching niet mogelijk is, zoals ' persoonlijk ' of no-Store '.
> - Het antwoord bevat een ```Authorization``` header die niet is verlopen.
> - Het antwoord heeft een HTTP 302-status code.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
