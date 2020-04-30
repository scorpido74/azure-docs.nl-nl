---
title: Internet peering versus peering service
titleSuffix: Azure
description: Internet peering versus peering service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75775703"
---
# <a name="internet-peering-vs-peering-service"></a>Internet peering versus peering service

Internet peering verwijst naar een wille keurige verbinding tussen het wereld wijde netwerk (AS8075) van micro soft en de vervoerders of het netwerk van de service providers. Een service provider kan een peering service-partner worden door de vereisten voor de peering service-samen werking te implementeren die hieronder worden uitgelegd om betrouw bare en hoogwaardige open bare connectiviteit te bieden met optimale route ring van de klant naar het micro soft-netwerk.

## <a name="about-peering-service"></a>Info over Peering Service
Peering-service is een partnerschaps programma met belang rijke service providers om de beste, open bare Internet connectiviteit te bieden aan hun zakelijke gebruikers. Partners die deel uitmaken van het programma, hebben direct, geografisch redundante verbindingen en geoptimaliseerde route ring naar micro soft. Peering-service is een aanvulling op de micro soft Connectivity-Port Folio:
*   ExpressRoute voor particuliere connectiviteit met IaaS-of PaaS-bronnen (ondersteuning voor privé IP-adres ruimte)
    *   Connectiviteit op basis van de partner
    *   Direct 100G connectiviteit met micro soft
*   IPSEC via internet voor VPN-verbinding met de Cloud
*   SD-WAN-verbinding met Azure via Virtual WAN

Het doel segment voor peering-service is SaaS-connectiviteit, SD-WAN-klanten die bereid zijn om Internet groepen te doen bij het filiaal en klanten die een MPLS met twee strategieën hebben en Internet.

Het primaire doel voor het maken van verbinding met Microsoft Cloud moet de latentie beperken door de RTT (round-trip) van een gebruikers site te beperken tot het globale micro soft-netwerk. Dit is de open bare netwerk-backbone van micro soft die alle data centers van micro soft en meerdere ingangs punten in de Cloud toepassing verbindt. Zie [de beste connectiviteit en prestaties in Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Afbeelding van gedistribueerde toegang](./media/distributed-access.png)

In de afbeelding boven elk filiaal van een wereld wijde onderneming wordt verbinding gemaakt met de dichtstbijzijnde micro soft Edge-locatie mogelijk via het netwerk van de partner.

**Voor delen van peering service-klant:**
* Beste open bare route ring via internet om services te Microsoft Cloud voor optimale prestaties en betrouw baarheid.
* De mogelijkheid om het gewenste SP te selecteren om verbinding te maken met Microsoft Cloud.
* Verkeers inzichten zoals latentie rapportage en voorvoegsel controle.
* De optimale netwerk-hops (als hops) van Microsoft Cloud.
* Route analyses en statistieken voor de route afwijkingen van Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (detectie van lekken/overname) en suboptimale route ring.

## <a name="peering-service-partnership-requirements"></a>Vereisten voor peering service-samen werking
* Connectiviteit met Microsoft Cloud op een locatie die het dichtst bij de klant ligt. Een partner serviceprovider stuurt gebruikers verkeer naar micro soft Edge die het dichtst bij de gebruiker ligt. Op soort gelijke wijze stuurt micro soft verkeer (met behulp van BGP-tag) naar de locatie van de Edge die het dichtst bij de gebruiker ligt en levert het verkeer naar de gebruiker.
* De partner houdt hoge beschik bare, hoge door Voer en geo-redundante connectiviteit met het wereld wijde netwerk van micro soft bij.
* De partner kan gebruikmaken van hun bestaande peering om peering service te ondersteunen als deze aan de vereisten voldoet

## <a name="faq"></a>Veelgestelde vragen
Zie voor veelgestelde vragen [peering Service-Veelgestelde vragen](service-faqs.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de voor delen van klanten met de [peering-service](https://docs.microsoft.com/azure/peering-service/).
* Meer informatie over de stappen voor het inschakelen van een directe peering voor peering-service in het [scenario voor peering service-partners](walkthrough-peering-service-all.md).
