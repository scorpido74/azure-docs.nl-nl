---
title: Configuratie problemen met de Azure-front-deur service oplossen
description: In deze zelf studie leert u hoe u zelf een aantal veelvoorkomende problemen met uw voor deur kunt oplossen.
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
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184564"
---
# <a name="troubleshooting-common-routing-issues"></a>Veelvoorkomende problemen met route ring oplossen
In dit artikel wordt beschreven hoe u een aantal veelvoorkomende problemen met route ring kunt oplossen voor de configuratie van de Azure front-deur-service. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>De hostnaam is niet doorgestuurd naar de back-end en retourneert de 400-status code


### <a name="symptom"></a>Symptoom
- U hebt een front-deur gemaakt, maar een aanvraag naar de frontend-host retourneert een HTTP 400-status code.

  - U hebt een DNS-toewijzing van een aangepast domein gemaakt aan de frontend-host die u hebt geconfigureerd. Het verzenden van een aanvraag naar de aangepaste domein-hostnaam retourneert echter een HTTP 400-status code en lijkt niet te worden gerouteerd naar de back-end (s) die u hebt geconfigureerd.

### <a name="cause"></a>Oorzaak
- Dit probleem kan zich voordoen als u geen routerings regel hebt geconfigureerd voor het aangepaste domein dat u hebt toegevoegd als een frontend-host. Een routerings regel moet expliciet worden toegevoegd voor die frontend-host, zelfs als er al een is geconfigureerd voor de frontend-host onder het subdomein van de voor deur (*. azurefd.net) waaraan uw aangepaste domein een DNS-toewijzing heeft.

### <a name="troubleshooting-steps"></a>Probleemoplossings stappen
- Voeg een routerings regel van het aangepaste domein toe aan de gewenste back-end-groep.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Aanvraag voor frontend-hostnaam retourneert 404-status code

### <a name="symptom"></a>Symptoom
- U hebt een voor deur gemaakt en een front-end-host geconfigureerd, een back-end-pool met ten minste één back-end en een routerings regel die de frontend-host verbindt met de back-end-groep. Uw inhoud lijkt niet beschikbaar te zijn bij het verzenden van een aanvraag naar de geconfigureerde frontend-host, omdat er een HTTP 404-status code wordt geretourneerd.

### <a name="cause"></a>Oorzaak
Er zijn verschillende mogelijke oorzaken voor dit symptoom:
 - De back-end is geen open bare back-end en is niet zichtbaar voor de front-deur service.

- De back-end is onjuist geconfigureerd. Dit zorgt ervoor dat de front-deur service de verkeerde aanvraag verzendt (dat wil zeggen, uw back-end accepteert alleen HTTP, maar u hebt niet de optie HTTPS toegestaan zodat de voor deur HTTPS-aanvragen doorstuurt).
- De back-end weigert de host-header die is doorgestuurd met de aanvraag naar de back-end.
- De configuratie voor de back-end is nog niet volledig geïmplementeerd.

### <a name="troubleshooting-steps"></a>Probleemoplossings stappen
1. Implementatie tijd
    - Zorg ervoor dat u ongeveer 10 minuten hebt gewacht totdat de configuratie is geïmplementeerd.

2. Controleer de back-end-instellingen
   - Navigeer naar de back-end-pool waarnaar de aanvraag moet worden doorgestuurd (afhankelijk van hoe u de routerings regel hebt geconfigureerd) en controleer of het _type back-end_ en de back-end van de backend juist zijn. Als de back-end een aangepaste host is, controleert u of u deze correct hebt gespeld. 

   - Controleer uw HTTP-en HTTPS-poorten. In de meeste gevallen zijn 80 en 443 (respectievelijk) juist. u hoeft geen wijzigingen aan te brengen. Er is echter een kans dat uw back-end niet op deze manier is geconfigureerd en luistert op een andere poort.

     - Controleer of de back-end- _host-header_ is geconfigureerd voor de back-end waarnaar de frontend-host moet worden gerouteerd. In de meeste gevallen moet deze header hetzelfde zijn als de naam van de _back-end_. Een onjuiste waarde kan echter verschillende HTTP 4xx-status codes veroorzaken als de back-end iets anders verwacht. Als u het IP-adres van uw back-end invoert, moet u mogelijk de host-header van de _back-end_ instellen op de hostnaam van de back-end.


3. Controleer de instellingen voor de routerings regel
     - Ga naar de routerings regel die moet worden gerouteerd van de frontend-hostnaam die u wilt verzenden naar een back-end-groep. Zorg ervoor dat de geaccepteerde protocollen correct zijn geconfigureerd of dat, als dat niet het geval is, ervoor moet worden gezorgd dat de protocol front-deur wordt gebruikt bij het door sturen van de aanvraag correct is geconfigureerd. De _geaccepteerde protocollen_ bepalen welke aanvragen aan de voor deur moeten accepteren en het _doorstuur protocol_ bepaalt welk protocol front deur moet worden gebruikt om de aanvraag door te sturen naar de back-end.
          - Als de back-end alleen HTTP-aanvragen accepteert, zijn de volgende configuraties geldig:
               - _Geaccepteerde protocollen_ zijn http en HTTPS. _Protocol voor door sturen_ is http. De overeenkomst werkt niet, omdat HTTPS een toegestaan protocol is en als er een aanvraag is ontvangen als HTTPS, probeert de voor deur de aanvraag door te sturen via HTTPS.

               - _Geaccepteerde protocollen_ zijn http. Het _doorstuur protocol_ komt overeen met de aanvraag of https.

   - Het opnieuw schrijven van de _URL_ is standaard uitgeschakeld en u moet dit veld alleen gebruiken als u het bereik van op back-gehoste bronnen wilt beperken dat u beschikbaar wilt maken. Als deze functie is uitgeschakeld, stuurt de voor deur hetzelfde aanvraag traject dat het ontvangt. Het is mogelijk dat dit veld onjuist is geconfigureerd en dat de voor deur een bron aanvraagt vanaf de back-end die niet beschikbaar is, waardoor een HTTP 404-status code wordt geretourneerd.

