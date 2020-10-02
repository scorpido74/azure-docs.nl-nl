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
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630607"
---
# <a name="troubleshooting-common-routing-issues"></a>Algemene routeringsproblemen oplossen

In dit artikel wordt beschreven hoe u een aantal veelvoorkomende problemen met route ring kunt oplossen voor de configuratie van uw Azure-front-deur.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 reactie van de voor deur na een paar seconden

### <a name="symptom"></a>Symptoom

* Reguliere aanvragen die worden verzonden naar uw back-end zonder voorafgaande deur, worden uitgevoerd, maar via de voor deur krijgt u een antwoord van 503-fout berichten.
* De fout van de voor deur wordt na een paar seconden weer gegeven (meestal ongeveer 30 seconden)

### <a name="cause"></a>Oorzaak

De oorzaak van dit probleem kan een van de volgende twee dingen zijn:
 
* Het duurt langer voordat de back-end is geconfigureerd (de standaard waarde is 30 seconden) om de aanvraag van de voor deur te ontvangen.
* De tijd die nodig is om een antwoord te verzenden naar het verzoek van de voor deur neemt langer duren dan de time-outwaarde. 

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

* Verzend de aanvraag rechtstreeks naar uw back-end (zonder de voor deur te passeren) en te zien wat de gebruikelijke tijd is om te reageren op uw back-end.
* Verzend de aanvraag via de voor deur en kijk of u 503-antwoorden krijgt. Als dat niet het geval is, is het probleem mogelijk geen time-outprobleem. Neem contact op met ondersteuning.
* Als de voor deur de oorzaak is van de reactie code van 503 fouten, configureert u het `sendReceiveTimeout` veld voor de voor deur. U kunt de standaardtime-out tot 4 minuten verlengen (240 seconden). De instelling bevindt zich onder de `backendPoolSettings` en wordt aangeroepen `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Aanvragen die worden verzonden naar het aangepaste domein, retour neren 400-status code

### <a name="symptom"></a>Symptoom

* U hebt een front deur gemaakt, maar een aanvraag aan de domein-of frontend-host retourneert een HTTP 400-status code.
* U hebt een DNS-toewijzing voor een aangepast domein gemaakt aan de frontend-host die u hebt geconfigureerd. Het verzenden van een aanvraag naar de aangepaste domein-hostnaam retourneert echter een HTTP 400-status code. Dit lijkt niet te worden gerouteerd naar de back-end die u hebt geconfigureerd.

### <a name="cause"></a>Oorzaak

Het probleem treedt op als u geen routerings regel hebt geconfigureerd voor het aangepaste domein dat is toegevoegd als de frontend-host. Een routerings regel moet expliciet worden toegevoegd voor die frontend-host. Zelfs als er al een is geconfigureerd voor de frontend-host onder het subdomein van de voor deur (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Voeg een routerings regel voor het aangepaste domein toe om verkeer naar de geselecteerde back-end-groep te sturen.

## <a name="front-door-doesnt-redirect-http-to-https"></a>HTTP wordt niet omgeleid naar HTTPS via de voor deur

### <a name="symptom"></a>Symptoom

De voor deur heeft een regel voor het door sturen van HTTP naar HTTPS, maar de toegang tot het domein blijft behouden als het protocol.

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u de routerings regels niet goed hebt geconfigureerd voor uw voor deur. In principe is uw huidige configuratie niet specifiek en kunnen er conflicterende regels zijn.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Aanvraag voor frontend-hostnaam retourneert 404-status code

### <a name="symptom"></a>Symptoom

 U hebt een voor deur gemaakt door een frontend-host te configureren, een back-end-pool met ten minste één back-end in deze en een routerings regel die de frontend-host verbindt met de back-end-groep. Uw inhoud is niet beschikbaar wanneer u een aanvraag voor de geconfigureerde frontend-host als resultaat maakt, wordt er een HTTP 404-status code geretourneerd.

### <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken voor dit symptoom:

* De back-end is geen open bare back-end en is niet zichtbaar voor de voor deur.
* De back-end is onjuist geconfigureerd, waardoor de front deur de verkeerde aanvraag verzendt. Met andere woorden, uw back-end accepteert alleen HTTP en u hebt het toestaan van HTTPS niet uitgeschakeld. De voor deur probeert HTTPS-aanvragen door te sturen.
* De back-end weigert de host-header die is doorgestuurd met de aanvraag naar de back-end.
* De configuratie voor de back-end is nog niet volledig geïmplementeerd.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

1. Implementatie tijd
   * Zorg ervoor dat u ongeveer 10 minuten hebt gewacht voordat de configuratie wordt geïmplementeerd.

2. Controleer de back-end-instellingen
    * Navigeer naar de back-end-pool waarnaar de aanvraag moet worden doorgestuurd (afhankelijk van hoe u de routerings regel hebt geconfigureerd). Controleer of het *type back-end* en de hostnaam van de back-end juist zijn. Als de back-end een aangepaste host is, controleert u of u deze correct hebt gespeld. 

    * Controleer uw HTTP-en HTTPS-poorten. In de meeste gevallen zijn 80 en 443 (respectievelijk) juist en er zijn geen wijzigingen vereist. Er is echter een kans dat uw back-end niet op deze manier is geconfigureerd en luistert op een andere poort.

        * Controleer of de back-end- _host-header_ is geconfigureerd voor de back-end waarnaar de frontend-host moet worden gerouteerd. In de meeste gevallen moet deze header hetzelfde zijn als de naam van de *back-end*. Een onjuiste waarde kan echter verschillende HTTP 4xx-status codes veroorzaken als de back-end iets anders verwacht. Als u het IP-adres van uw back-end invoert, moet u mogelijk de host-header van de *back-end* instellen op de hostnaam van de back-end.

3. Controleer de instellingen voor de routerings regel:
    * Ga naar de routerings regel die moet worden gerouteerd van de frontend-hostnaam die u wilt verzenden naar een back-end-groep. Zorg ervoor dat de geaccepteerde protocollen op de juiste wijze zijn geconfigureerd bij het door sturen van de aanvraag. Het veld *geaccepteerde protocollen* bepaalt welke aanvragen aan de voor deur moeten worden geaccepteerd. Het *doorstuur protocol* bepaalt welk protocol front deur moet worden gebruikt om de aanvraag door te sturen naar de back-end.
         * Als de back-end alleen HTTP-aanvragen accepteert, zijn de volgende configuraties geldig:
            * *Geaccepteerde protocollen* zijn http en HTTPS. *Protocol voor door sturen* is http. De overeenkomst werkt niet, omdat HTTPS een toegestaan protocol is en als een aanvraag is ontvangen als HTTPS, probeert de voor deur de aanvraag door te sturen via HTTPS.

            * *Geaccepteerde protocollen* zijn http. Het *doorstuur protocol* komt overeen met de aanvraag of http.

    - Het opnieuw schrijven van de *URL* is standaard uitgeschakeld. Dit veld wordt alleen gebruikt als u het bereik van op back-gehoste bronnen wilt beperken dat u beschikbaar wilt maken. Als deze functie is uitgeschakeld, stuurt de voor deur hetzelfde aanvraag traject dat het ontvangt. Dit veld kan niet worden geconfigureerd. Als de voor deur echter een bron aanvraagt vanaf de back-end die niet beschikbaar is, wordt een HTTP 404-status code geretourneerd.
