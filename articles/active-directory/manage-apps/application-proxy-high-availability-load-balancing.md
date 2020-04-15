---
title: Hoge beschikbaarheid en taakverdeling - Azure AD-toepassingsproxy
description: Hoe verkeersdistributie werkt met de implementatie van uw toepassingsproxy. Bevat tips voor het optimaliseren van de prestaties van de connector en het gebruik van load balancing voor back-endservers.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 992075378737552e890bd2d6fed3c519e6c62aa7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312938"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Hoge beschikbaarheid en taakverdeling van uw Application Proxy-connectors en -toepassingen

In dit artikel wordt uitgelegd hoe verkeersdistributie werkt met de implementatie van uw toepassingsproxy. We bespreken het wel:

- Hoe het verkeer wordt verdeeld onder gebruikers en connectoren, samen met tips voor het optimaliseren van de prestaties van de connector

- Hoe het verkeer stroomt tussen connectors en back-end-appservers, met aanbevelingen voor het balanceren van de belasting tussen meerdere back-endservers

## <a name="traffic-distribution-across-connectors"></a>Verkeersverdeling over connectoren

Connectoren leggen hun verbindingen op basis van principes voor hoge beschikbaarheid. Er is geen garantie dat het verkeer altijd gelijkmatig wordt verdeeld over connectoren en er geen sessieaffiniteit is. Het gebruik varieert echter en aanvragen worden willekeurig verzonden naar instanties van de Application Proxy-service. Als gevolg hiervan wordt het verkeer meestal bijna gelijkmatig verdeeld over de connectoren. Het diagram en de onderstaande stappen laten zien hoe verbindingen tot stand worden gebracht tussen gebruikers en connectoren.

![Diagram met verbindingen tussen gebruikers en connectoren](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Een gebruiker op een clientapparaat probeert toegang te krijgen tot een on-premises toepassing die is gepubliceerd via Application Proxy.
2. De aanvraag gaat via een Azure Load Balancer om te bepalen welke application proxy-serviceinstantie de aanvraag moet aannemen. Per regio zijn er tientallen exemplaren beschikbaar om de aanvraag te accepteren. Deze methode helpt om het verkeer gelijkmatig te verdelen over de service-exemplaren.
3. Het verzoek wordt verzonden naar [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/).
4. Service Bus signalen naar een beschikbare connector. De connector neemt vervolgens het verzoek op bij Service Bus.
   - In stap 2 gaan aanvragen naar verschillende toepassingsproxyservice-exemplaren, zodat verbindingen waarschijnlijk worden gemaakt met verschillende connectors. Hierdoor worden connectoren bijna gelijkmatig gebruikt binnen de groep.
5. De connector geeft het verzoek door aan de back-endserver van de toepassing. Vervolgens stuurt de toepassing het antwoord terug naar de connector.
6. De connector voltooit het antwoord door een uitgaande verbinding met de service-instantie te openen van waaruit de aanvraag is gekomen. Dan wordt deze verbinding onmiddellijk gesloten. Standaard is elke connector beperkt tot 200 gelijktijdige uitgaande verbindingen.
7. Het antwoord wordt vervolgens teruggegeven aan de client van de service-instantie.
8. Latere aanvragen van dezelfde verbinding herhalen de bovenstaande stappen.

Een toepassing heeft vaak veel resources en opent meerdere verbindingen wanneer deze wordt geladen. Elke verbinding gaat door de bovenstaande stappen om te worden toegewezen aan een service-instantie, selecteert u een nieuwe beschikbare connector als de verbinding nog niet eerder is gekoppeld aan een connector.


## <a name="best-practices-for-high-availability-of-connectors"></a>Aanbevolen procedures voor hoge beschikbaarheid van connectoren

- Vanwege de manier waarop verkeer wordt verdeeld over connectoren voor hoge beschikbaarheid, is het essentieel om altijd ten minste twee connectoren in een connectorgroep te hebben. Drie connectoren hebben de voorkeur om extra buffer te bieden tussen connectoren. Volg de benodigde documentatie over capaciteitsplanning om het juiste aantal connectoren te bepalen dat u nodig had.

- Plaats connectoren op verschillende uitgaande verbindingen om een enkel storingspunt te voorkomen. Als connectors dezelfde uitgaande verbinding gebruiken, kan een netwerkprobleem met de verbinding gevolgen hebben voor alle connectors die deze verbinding gebruiken.

- Vermijd het forceren van connectors om opnieuw op te starten wanneer ze zijn aangesloten op productietoepassingen. Dit kan een negatieve invloed hebben op de verdeling van het verkeer over connectoren. Door connectors opnieuw op te starten, zijn er meer connectors niet beschikbaar en worden de aansluitingen naar de resterende beschikbare connector gestouwd. Het resultaat is een ongelijk gebruik van de connectoren in eerste instantie.

- Vermijd alle vormen van inline-inspectie op uitgaande TLS-communicatie tussen connectors en Azure. Dit type inline inspectie veroorzaakt degradatie aan de communicatiestroom.

- Zorg ervoor dat automatische updates voor uw connectors blijven draaien. Als de application proxy connector updater-service wordt uitgevoerd, worden uw connectors automatisch bijgewerkt en ontvangen ze de nieuwste update. Als u de Connector Updater-service niet op uw server ziet, moet u uw connector opnieuw installeren om updates te ontvangen.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Verkeersstroom tussen connectors en back-endtoepassingsservers

Een ander belangrijk gebied waar hoge beschikbaarheid een factor is, is de verbinding tussen connectors en de back-endservers. Wanneer een toepassing wordt gepubliceerd via Azure AD Application Proxy, stroomt het verkeer van de gebruikers naar de toepassingen door drie hop:

1. De gebruiker maakt verbinding met het openbare eindpunt van de Azure AD Application Proxy service op Azure. De verbinding wordt gelegd tussen het ip-adres (openbaar) van de client en het IP-adres van het eindpunt van application proxy.
2. De application proxy-connector haalt de HTTP-aanvraag van de client uit de Application Proxy Service.
3. De application proxy-connector maakt verbinding met de doeltoepassing. De connector gebruikt een eigen IP-adres voor het tot stand brengen van de verbinding.

![Diagram van de gebruiker die verbinding maakt met een toepassing via application proxy](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Forwarded-For header veld overwegingen
In sommige situaties (zoals auditing, load balancing etc.) is het delen van het oorspronkelijke IP-adres van de externe client met de on-premises omgeving een vereiste. Om aan de vereiste te voldoen, voegt azure AD Application Proxy-connector het veld X-Forwarded-For met het ip-adres van de client (openbaar) toe aan de HTTP-aanvraag. Het juiste netwerkapparaat (load balancer, firewall) of de webserver of back-endtoepassing kan de informatie vervolgens lezen en gebruiken.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Aanbevolen procedures voor taakverdeling tussen meerdere app-servers
Wanneer de verbindingsgroep die is toegewezen aan de application proxy-toepassing twee of meer connectoren heeft en u de back-endwebtoepassing op meerdere servers (serverfarm) uitvoert, is een goede strategie voor het balanceren van de belasting vereist. Een goede strategie zorgt ervoor dat servers klantaanvragen gelijkmatig ophalen en over- of onderbenutting van servers in de serverfarm voorkomen.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Scenario 1: Back-end toepassing vereist geen sessiepersistentie
Het eenvoudigste scenario is waar de back-end webtoepassing geen sessie-stickiness (sessiepersistentie) vereist. Elk verzoek van de gebruiker kan worden afgehandeld door elke back-end toepassingsinstantie in de serverfarm. U een layer 4 load balancer gebruiken en deze zonder affiniteit configureren. Enkele opties zijn Microsoft Network Load Balancing en Azure Load Balancer of een load balancer van een andere leverancier. U ook round-robin DNS configureren.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Scenario 2: Back-end toepassing vereist sessie persistentie
In dit scenario vereist de back-endwebtoepassing sessiestickiness (sessiepersistentie) tijdens de geverifieerde sessie. Alle aanvragen van de gebruiker moeten worden afgehandeld door de back-end toepassingsinstantie die op dezelfde server in de serverfarm wordt uitgevoerd.
Dit scenario kan ingewikkelder zijn omdat de client meestal meerdere verbindingen met de application proxy-service tot leggen. Aanvragen via verschillende verbindingen kunnen bij verschillende connectors en servers in de farm binnenkomen. Omdat elke connector zijn eigen IP-adres gebruikt voor deze communicatie, kan de load balancer niet zorgen voor sessiestickiness op basis van het IP-adres van de connectors. Bron IP Affinity kan ook niet worden gebruikt.
Hier volgen enkele opties voor scenario 2:

- Optie 1: Baseer de sessie persistentie op een sessiecookie die is ingesteld door de load balancer. Deze optie wordt aanbevolen omdat de belasting hierdoor gelijkmatiger over de back-endservers kan worden verdeeld. Het vereist een layer 7 load balancer met deze mogelijkheid en die het HTTP-verkeer kan verwerken en de TLS-verbinding kan beëindigen. U Azure Application Gateway (Sessieaffiniteit) of een load balancer van een andere leverancier gebruiken.

- Optie 2: Baseer de sessiepersistentie op het veld X-Forwarded-For header. Deze optie vereist een layer 7 load balancer met deze mogelijkheid en die het HTTP-verkeer kan verwerken en de TLS-verbinding kan beëindigen.  

- Optie 3: Configureer de back-endtoepassing om geen sessiepersistentie te vereisen.

Raadpleeg de documentatie van uw softwareleverancier om inzicht te krijgen in de vereisten voor het balanceren van de back-endtoepassing.

## <a name="next-steps"></a>Volgende stappen

- [Toepassingsproxy inschakelen](application-proxy-add-on-premises-application.md)
- [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
- [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)
- [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)
- [Meer informatie over hoe Azure AD-architectuur hoge beschikbaarheid ondersteunt](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
