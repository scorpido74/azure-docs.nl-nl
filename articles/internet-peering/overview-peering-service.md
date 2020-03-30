---
title: Internetpeering vs. Peering Service
titleSuffix: Azure
description: Internetpeering vs. Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75775703"
---
# <a name="internet-peering-vs-peering-service"></a>Internetpeering vs. Peering Service

Internetpeering verwijst naar een interconnectie tussen het wereldwijde netwerk van Microsoft (AS8075) en het netwerk providers van providers van providers. Een serviceprovider kan een Peering Service-partner worden door de onderstaande peeringservicepartnerschapsvereisten te implementeren om betrouwbare en goed presterende openbare connectiviteit te bieden met een optimale routering van de klant naar het Microsoft-netwerk.

## <a name="about-peering-service"></a>Info over Peering Service
Peering Service is een partnerschapsprogramma met belangrijke serviceproviders om hun zakelijke gebruikers de beste openbare internetverbinding te bieden. Partners die deel uitmaken van het programma hebben directe, zeer beschikbare, georedundante verbindingen en geoptimaliseerde routering naar Microsoft. Peering Service is een aanvulling op het Microsoft-connectiviteitsportfolio:
*   ExpressRoute voor privéconnectiviteit met IaaS- of PaaS-bronnen (ondersteuning voor privé-IP-ruimte)
    *   Partnergebaseerde connectiviteit
    *   Directe 100G-connectiviteit met Microsoft
*   IPSEC via het internet voor VPN-connectiviteit met de cloud
*   SD-WAN-connectiviteit met Azure via Virtual WAN

Het doelsegment voor Peering Service is SaaS-connectiviteit, SD-WAN-klanten die bereid zijn om internetbreakout te doen bij de vestiging en klanten met een dubbele strategie MPLS en enterprise-grade internet.

Het primaire doel bij het maken van verbinding met Microsoft Cloud moet zijn om de latentie te minimaliseren door de retourtijd (RTT) te verkorten van een gebruikerssite naar het Microsoft Global Network, de openbare netwerkbackbone van Microsoft die alle datacenters van Microsoft met elkaar verbindt en meerdere toegangspunten voor cloudtoepassingen. Zie [De beste connectiviteit en prestaties krijgen in Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Afbeelding van gedistribueerde toegang](./media/distributed-access.png)

In de figuur boven elke bijkantoor van een wereldwijde onderneming maakt verbinding met de dichtstbijzijnde mogelijke Microsoft edge locatie mogelijk via het netwerk van de partner.

**Voordelen van de klant van Peering Service:**
* Beste openbare routering via internet naar Microsoft Cloud Services voor optimale prestaties en betrouwbaarheid.
* Mogelijkheid om de gewenste SP te selecteren om verbinding te maken met Microsoft Cloud.
* Verkeersinzichten zoals latentierapportage en voorfixmonitoring.
* Optimale Netwerkhop (AS Hops) van Microsoft Cloud.
* Routeanalyses en -statistieken - Gebeurtenissen voor Border Gateway Protocol[(BGP)](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)routeanomalieën (lek/hijack detectie) en suboptimale routering.

## <a name="peering-service-partnership-requirements"></a>Vereisten voor peering service-partnerschap
* Connectiviteit met Microsoft Cloud op een locatie die het dichtst bij de klant staat. Een partnerserviceprovider stuurt gebruikersverkeer door naar Microsoft edge die het dichtst bij de gebruiker staat. Op dezelfde manier zal Microsoft op het verkeer naar de gebruiker het verkeer (met behulp van BGP-tag) doorsturen naar de randlocatie die het dichtst bij de gebruiker staat en zal SP het verkeer aan de gebruiker leveren.
* Partner behoudt een hoge beschikbare, hoge doorvoer- en georedundante connectiviteit met Microsoft Global Network.
* Partner kan hun bestaande peering gebruiken om Peering Service te ondersteunen als deze voldoet aan de vereiste

## <a name="faq"></a>Veelgestelde vragen
Zie [Peering Service - VEELGESTELDE VRAGEN](service-faqs.md)voor veelgestelde vragen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over klantvoordelen met [Peering Service](https://docs.microsoft.com/azure/peering-service/).
* Meer informatie over stappen om een direct peering-service voor peeringservice in [peeringservice-partnerin te](walkthrough-peering-service-all.md)schakelen.
