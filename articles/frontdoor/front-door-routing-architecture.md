---
title: Azure front-deur-routerings architectuur | Microsoft Docs
description: Dit artikel helpt u bij het begrijpen van het algemene weergave aspect van de architectuur van de voor deur.
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
ms.openlocfilehash: a088e52f742f96a13ba61969c2d7a6697c96b145
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80879289"
---
# <a name="routing-architecture-overview"></a>Overzicht van routerings architectuur

De Azure front-deur wanneer deze uw client aanvragen ontvangt, beantwoordt deze (als de cache is ingeschakeld) of stuurt deze door naar de juiste back-end van de toepassing (als een omgekeerde proxy).

</br>Er zijn mogelijkheden voor het optimaliseren van het verkeer bij route ring naar Azure front deur en bij het routeren naar back-ends.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>De voor deur omgeving voor verkeers routering (anycast) selecteren

Route ring naar de Azure front-deur omgevingen maakt gebruik van [anycast](https://en.wikipedia.org/wiki/Anycast) voor DNS-(Domain Name System) en http-verkeer (Hypertext Transfer Protocol), zodat gebruikers verkeer naar de dichtstbijzijnde omgeving gaat in termen van de netwerk topologie (minste hops). Deze architectuur biedt doorgaans betere retour tijden voor eind gebruikers (waardoor de voor delen van de gesplitste TCP worden gemaximaliseerd). Met de voor deur worden de omgevingen in primaire en terugval ' belsignalen ' ingedeeld.  De buitenste ring heeft omgevingen die dichter bij gebruikers zijn en die lagere latenties bieden.  De interne ring heeft omgevingen die de failover voor de buitenste ring omgeving kunnen afhandelen als er een probleem optreedt. De buitenste ring is het voorkeurs doel voor al het verkeer, maar de interne ring is nood zakelijk om de verkeers overloop van de buitenste ring af te handelen. Op basis van Vip's (virtuele Internet Protocol-adressen) wordt elke frontend-host of een domein dat wordt bediend door de voor deur, een primair VIP toegewezen, dat wordt aangekondigd door omgevingen in zowel de interne als buitenste ring, evenals een terugval-VIP, die alleen wordt aangekondigd door omgevingen in de binnenste ring. 

</br>Deze algehele strategie zorgt ervoor dat aanvragen van uw eind gebruikers altijd de dichtstbijzijnde front-deur omgeving bereiken en dat zelfs als de voorkeurs omgeving van de voor deur niet in orde is, het verkeer automatisch naar de volgende dichtstbijzijnde omgeving gaat.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Verbinding maken met de front-deur omgeving (gesplitste TCP)

[Gesplitste TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) is een techniek om LATENTIE en TCP-problemen te reduceren door een verbinding te verbreken waardoor een hoge retour tijd in kleinere delen zou ontstaan.  Door de voor deur omgevingen dichter bij eind gebruikers te plaatsen en TCP-verbindingen in de front-deur omgeving te beëindigen, wordt één TCP-verbinding met een grote RTT (round-trip time) naar de back-end van de toepassing gesplitst in twee TCP-verbindingen. De korte verbinding tussen de eind gebruiker en de voor deur houdt in dat de verbinding tot stand wordt gebracht via drie korte retour aanslagen in plaats van drie lange retour reizen, waarbij latentie wordt bespaard.  De lange verbinding tussen de front-deur omgeving en de back-end kan vooraf worden ingesteld en opnieuw worden gebruikt voor meerdere aanroepen van eind gebruikers, waarna de TCP-verbindings tijd opnieuw wordt bespaard.  Het effect wordt vermenigvuldigd bij het tot stand brengen van een SSL/TLS-verbinding (Transport Layer Security) als er meer ronde trips zijn om de verbinding te beveiligen.

## <a name="processing-request-to-match-a-routing-rule"></a>De aanvraag wordt verwerkt om te voldoen aan een routerings regel
Na het tot stand brengen van een verbinding en het uitvoeren van een TLS-Handshake, is het afstemmen van een routerings regel de eerste stap wanneer een aanvraag land op een front-deur omgeving wordt uitgevoerd. Dit komt overeen met het bepalen van alle configuraties in de voor deur, waaronder een specifieke routerings regel die overeenkomt met de aanvraag voor. Lees meer informatie over de wijze waarop de voor deur de [route afstemming](front-door-route-matching.md) biedt.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Beschik bare back-end in de back-end-pool identificeren voor de routerings regel
Wanneer de voor deur een overeenkomst heeft voor een routerings regel die is gebaseerd op de inkomende aanvraag en als er geen cache is, wordt de status van de status test opgehaald voor de back-end-groep die aan de overeenkomende route is gekoppeld. Meer informatie over de status van de back-end van de voor deur wordt gecontroleerd met behulp van [Health probe](front-door-health-probes.md) .

## <a name="forwarding-the-request-to-your-application-backend"></a>De aanvraag door sturen naar de back-end van uw toepassing
Ten slotte, ervan uitgaande dat er geen caching is geconfigureerd, wordt de gebruikers aanvraag doorgestuurd naar de ' beste ' back-end op basis van de configuratie van uw [voor deur routerings methode](front-door-routing-methods.md) .

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
