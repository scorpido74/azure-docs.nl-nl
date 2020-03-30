---
title: Problemen met azure front door-configuratie oplossen
description: In deze zelfstudie leert u hoe u een aantal van de veelvoorkomende problemen die u voor uw voordeur tegenkomen, zelf oplossen.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471503"
---
# <a name="troubleshooting-common-routing-issues"></a>Veelvoorkomende routeringsproblemen oplossen

In dit artikel wordt beschreven hoe u een aantal van de veelvoorkomende routeringsproblemen oplost waarmee u mogelijk wordt geconfronteerd met uw Azure-voordeurconfiguratie.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 reactie van Voordeur na een paar seconden

### <a name="symptom"></a>Symptoom

- Regelmatige verzoeken verzonden naar uw backend zonder te gaan door de voordeur zijn succesvol, maar gaan via Front Door resulteert in 503 foutreacties.

- De storing van Front Door blijkt na een paar seconden (meestal rond na 30 seconden)

### <a name="cause"></a>Oorzaak

Dit symptoom treedt op wanneer uw back-end langer duurt dan de time-outconfiguratie (standaard is 30 seconden) om het verzoek van Front Door te ontvangen of als het langer duurt dan deze time-outwaarde om een antwoord te sturen op het verzoek van Voordeur. 

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

- Stuur het verzoek direct naar uw backend (zonder door de voordeur te gaan) en zie wat de gebruikelijke tijd is die nodig is om uw backend te beantwoorden.
- Stuur het verzoek via De Voordeur en kijk of u 503 reacties ziet. Zo niet, dan is dit misschien niet een time-out probleem. Neem contact op met de ondersteuning.
- Als het doorlopen via Front Door resulteert in 503 foutresponscode, configureer dan het veld sendReceiveTimeout voor uw voordeur om de standaard time-out uit te breiden tot 4 minuten (240 seconden). De instelling is `backendPoolSettings` onder `sendRecvTimeoutSeconds`de en wordt genoemd . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Aanvragen die naar het aangepaste domein worden verzonden, retourneert de statuscode van 400

### <a name="symptom"></a>Symptoom

- U hebt een voordeur gemaakt, maar een verzoek aan het domein of frontendhost retourt een HTTP 400-statuscode.

- U hebt een DNS-toewijzing gemaakt van een aangepast domein naar de frontendhost die u hebt geconfigureerd. Als u een aanvraag naar de aangepaste hostnaam van het domein verzendt, wordt echter een HTTP 400-statuscode geretourneerd en lijkt u geen route te sturen naar de backend(s) die u hebt geconfigureerd.

### <a name="cause"></a>Oorzaak

Dit symptoom kan optreden als u geen routeringsregel hebt geconfigureerd voor het aangepaste domein dat u als frontendhost hebt toegevoegd. Er moet expliciet een routeringsregel worden toegevoegd voor die frontendhost, zelfs als deze al is geconfigureerd voor de frontendhost onder het frontdoor-subdomein (*.azurefd.net) waarvoor uw aangepaste domein een DNS-toewijzing heeft.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Voeg een routeringsregel van het aangepaste domein toe aan de gewenste backendpool.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Voordeur leidt HTTP niet om naar HTTPS

### <a name="symptom"></a>Symptoom

Uw voordeur heeft een routeringsregel die zegt HTTP omleiden naar HTTPS, maar toegang tot het domein blijft HTTP als protocol behouden.

### <a name="cause"></a>Oorzaak

Dit gedrag kan optreden als u de routeringsregels voor uw voordeur niet correct hebt geconfigureerd. In principe is uw huidige configuratie niet specifiek en kan er conflicterende regels zijn.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Verzoek om hostname frontend retourneert 404-statuscode

### <a name="symptom"></a>Symptoom

- U hebt een voordeur gemaakt en een frontendhost geconfigureerd, een backendpool met ten minste één backend erin en een routeringsregel die de frontendhost verbindt met de backendpool. Uw inhoud lijkt niet beschikbaar te zijn bij het verzenden van een aanvraag naar de geconfigureerde frontendhost omdat een HTTP 404-statuscode wordt geretourneerd.

### <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken voor dit symptoom:

- De backend is geen publiek gerichte backend en is niet zichtbaar voor de voordeur.
- De backend is verkeerd geconfigureerd, waardoor de voordeur het verkeerde verzoek verzendt (dat wil zeggen dat je backend alleen HTTP accepteert, maar je hebt niet ongecontroleerd waardoor HTTPS is toegestaan, dus Voordeur probeert HTTPS-verzoeken door te sturen).
- De backend is het afwijzen van de host header die is doorgestuurd met het verzoek naar de backend.
- De configuratie voor de backend is nog niet volledig geïmplementeerd.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

1. Implementatietijd
   - Zorg ervoor dat u ~ 10 minuten hebt gewacht tot de configuratie is geïmplementeerd.

2. Controleer de backend-instellingen
    - Navigeer naar de backendpool waarnaar de aanvraag moet worden geleid (afhankelijk van hoe u de routeringsregel hebt geconfigureerd) en controleer of het _type backendhost_ en de naam van de backendhost correct zijn. Als de backend een aangepaste host is, moet u ervoor zorgen dat u deze correct hebt gespeld. 

    - Controleer uw HTTP- en HTTPS-poorten. In de meeste gevallen zijn respectievelijk 80 en 443 correct en zijn er geen wijzigingen nodig. Er is echter een kans dat uw backend niet op deze manier is geconfigureerd en op een andere poort luistert.

        - Controleer de _backend-hostkoptekst_ die is geconfigureerd voor de backends naar de Frontend-host. In de meeste gevallen moet deze koptekst hetzelfde zijn als de naam van de _backendhost._ Een onjuiste waarde kan echter leiden tot verschillende HTTP 4xx-statuscodes als de backend iets anders verwacht. Als u het IP-adres van uw backend invoert, moet u mogelijk de _backend-hostkop_ instellen op de hostnaam van de backend.


3. De instellingen voor routeregel controleren
    - Navigeer naar de routeringsregel die moet worden geleid van de frontend-hostnaam in kwestie naar een backendpool. Zorg ervoor dat de geaccepteerde protocollen correct zijn geconfigureerd of zo niet, zorg ervoor dat het protocol Voordeur wordt gebruikt bij het doorsturen van de aanvraag correct is geconfigureerd. Het _veld geaccepteerde protocollen_ bepaalt welke aanvragen Front Door moet accepteren en het _forwarding-protocol_ bepaalt welk protocol Front Door moet gebruiken om het verzoek naar de backend door te sturen.
         - Als de backend bijvoorbeeld alleen HTTP-aanvragen accepteert, zijn de volgende configuraties geldig:
            - _Geaccepteerde protocollen_ zijn HTTP en HTTPS. _Doorstuurprotocol_ is HTTP. Match request werkt niet, omdat HTTPS een toegestaan protocol is en als er een verzoek als HTTPS binnenkwam, probeert Front Door het door te sturen via HTTPS.

            - _Geaccepteerde protocollen_ zijn HTTP. _Doorstuurprotocol_ is een matchrequest of HTTPS.

    - _Url Rewrite_ is standaard uitgeschakeld en u moet dit veld alleen gebruiken als u het bereik wilt verkleinen van backend-gehoste resources die u beschikbaar wilt stellen. Wanneer uitgeschakeld, zal De Voordeur het zelfde verzoekpad doorsturen dat het ontvangt. Het is mogelijk dat dit veld verkeerd is geconfigureerd en Voordeur een resource aanvraagt vanaf de backend die niet beschikbaar is, waardoor een HTTP 404-statuscode wordt teruggestuurd.

