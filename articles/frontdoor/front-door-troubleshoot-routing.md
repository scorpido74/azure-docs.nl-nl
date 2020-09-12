---
title: Problemen met configuratie van Azure front-deur oplossen
description: In deze zelf studie leert u hoe u zelf een aantal veelvoorkomende problemen met uw voor deur kunt oplossen.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: duau
ms.openlocfilehash: babe24d0c934cffac00a5100d1da7ee252d147da
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399052"
---
# <a name="troubleshooting-common-routing-issues"></a>Veelvoorkomende problemen met route ring oplossen

In dit artikel wordt beschreven hoe u een aantal veelvoorkomende problemen met route ring kunt oplossen voor de configuratie van uw Azure-front-deur.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 reactie van de voor deur na een paar seconden

### <a name="symptom"></a>Symptoom

- Reguliere aanvragen die worden verzonden naar uw back-end zonder voorafgaande deur, worden uitgevoerd, maar via de voor deur krijgt u een antwoord van 503-fout berichten.

- De fout van de voor deur wordt na een paar seconden weer gegeven (meestal ongeveer 30 seconden)

### <a name="cause"></a>Oorzaak

Dit symptoom treedt op wanneer de back-end de time-outconfiguratie overschrijdt (de standaard waarde is 30 seconden) om de aanvraag van de voor deur te ontvangen of overschrijdt de time-outwaarde om een reactie van de voor deur te verzenden. 

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

- Verzend de aanvraag rechtstreeks naar uw back-end (zonder de voor deur te passeren) en te zien wat de gebruikelijke tijd is om te reageren op uw back-end.
- Verzend de aanvraag via de voor deur en controleer of er 503 reacties worden weer gegeven. Als dat niet het geval is, is dit mogelijk geen time-outprobleem. Neem contact op met de ondersteuning.
- Als de voor deur de fout code van 503-fouten overschrijdt, configureert u het veld sendReceiveTimeout voor de voor deur om de standaardtime-out uit te breiden tot 4 minuten (240 seconden). De instelling bevindt zich onder de `backendPoolSettings` en wordt aangeroepen `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Aanvragen die worden verzonden naar het aangepaste domein, retour neren 400-status code

### <a name="symptom"></a>Symptoom

- U hebt een front-deur gemaakt, maar een aanvraag aan de domein-of frontend-host retourneert een HTTP 400-status code.

- U hebt een DNS-toewijzing van een aangepast domein gemaakt aan de frontend-host die u hebt geconfigureerd. Het verzenden van een aanvraag naar de aangepaste domein-hostnaam retourneert echter een HTTP 400-status code en lijkt niet te worden gerouteerd naar de back-end (s) die u hebt geconfigureerd.

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als u geen routerings regel hebt geconfigureerd voor het aangepaste domein dat u hebt toegevoegd als een frontend-host. Een routerings regel moet expliciet worden toegevoegd voor die frontend-host, zelfs als er al een is geconfigureerd voor de frontend-host onder het subdomein van de voor deur (*. azurefd.net) waaraan uw aangepaste domein een DNS-toewijzing heeft.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Voeg een routerings regel van het aangepaste domein toe aan de gewenste back-end-groep.

## <a name="front-door-is-not-redirecting-http-to-https"></a>HTTP omleiden naar HTTPS wordt niet omgeleid naar de voor deur

### <a name="symptom"></a>Symptoom

De voor deur heeft een regel voor het door sturen van HTTP naar HTTPS, maar de toegang tot het domein blijft behouden als het protocol.

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als u de routerings regels voor de voor deur niet juist hebt geconfigureerd. In principe is uw huidige configuratie niet specifiek en kunnen er conflicterende regels zijn.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Aanvraag voor frontend-hostnaam retourneert 404-status code

### <a name="symptom"></a>Symptoom

- U hebt een voor deur gemaakt en een front-end-host geconfigureerd, een back-end-pool met ten minste één back-end en een routerings regel die de frontend-host verbindt met de back-end-groep. Uw inhoud lijkt niet beschikbaar te zijn bij het verzenden van een aanvraag naar de geconfigureerde frontend-host, omdat er een HTTP 404-status code wordt geretourneerd.

### <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken voor dit symptoom:

- De back-end is geen open bare back-end en is niet zichtbaar voor de voor deur.
- De back-end is onjuist geconfigureerd. Dit zorgt ervoor dat de front-deur de verkeerde aanvraag verzendt (dat wil zeggen, uw back-end accepteert alleen HTTP, maar u hebt niet de optie HTTPS toegestaan zodat de voor deur HTTPS-aanvragen doorstuurt).
- De back-end weigert de host-header die is doorgestuurd met de aanvraag naar de back-end.
- De configuratie voor de back-end is nog niet volledig geïmplementeerd.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

1. Implementatie tijd
   - Zorg ervoor dat u ongeveer 10 minuten hebt gewacht totdat de configuratie is geïmplementeerd.

2. Controleer de back-end-instellingen
    - Navigeer naar de back-end-pool waarnaar de aanvraag moet worden doorgestuurd (afhankelijk van hoe u de routerings regel hebt geconfigureerd) en controleer of het _type back-end_ en de back-end van de backend juist zijn. Als de back-end een aangepaste host is, controleert u of u deze correct hebt gespeld. 

    - Controleer uw HTTP-en HTTPS-poorten. In de meeste gevallen zijn 80 en 443 (respectievelijk) juist. u hoeft geen wijzigingen aan te brengen. Er is echter een kans dat uw back-end niet op deze manier is geconfigureerd en luistert op een andere poort.

        - Controleer of de back-end- _host-header_ is geconfigureerd voor de back-end waarnaar de frontend-host moet worden gerouteerd. In de meeste gevallen moet deze header hetzelfde zijn als de naam van de _back-end_. Een onjuiste waarde kan echter verschillende HTTP 4xx-status codes veroorzaken als de back-end iets anders verwacht. Als u het IP-adres van uw back-end invoert, moet u mogelijk de host-header van de _back-end_ instellen op de hostnaam van de back-end.


3. Controleer de instellingen voor de routerings regel
    - Ga naar de routerings regel die moet worden gerouteerd van de frontend-hostnaam die u wilt verzenden naar een back-end-groep. Zorg ervoor dat de geaccepteerde protocollen correct zijn geconfigureerd of dat, als dat niet het geval is, ervoor moet worden gezorgd dat de protocol front-deur wordt gebruikt bij het door sturen van de aanvraag correct is geconfigureerd. In het veld _geaccepteerde protocollen_ wordt bepaald welke aanvragen aan de voor deur moeten worden geaccepteerd en het _doorstuur protocol_ bepaalt welk protocol front-deur moet worden gebruikt om de aanvraag door te sturen naar de back-end.
         - Als de back-end alleen HTTP-aanvragen accepteert, zijn de volgende configuraties geldig:
            - _Geaccepteerde protocollen_ zijn http en HTTPS. _Protocol voor door sturen_ is http. De overeenkomst werkt niet, omdat HTTPS een toegestaan protocol is en als er een aanvraag is ontvangen als HTTPS, probeert de voor deur de aanvraag door te sturen via HTTPS.

            - _Geaccepteerde protocollen_ zijn http. Het _doorstuur protocol_ komt overeen met de aanvraag of http.

    - Het opnieuw schrijven van de _URL_ is standaard uitgeschakeld en u moet dit veld alleen gebruiken als u het bereik van op back-gehoste bronnen wilt beperken dat u beschikbaar wilt maken. Als deze functie is uitgeschakeld, stuurt de voor deur hetzelfde aanvraag traject dat het ontvangt. Het is mogelijk dat dit veld onjuist is geconfigureerd en dat de voor deur een bron aanvraagt vanaf de back-end die niet beschikbaar is, waardoor een HTTP 404-status code wordt geretourneerd.

