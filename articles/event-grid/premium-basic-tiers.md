---
title: Azure Event Grid Premium- en Basic-lagen
description: In dit artikel wordt het verschil beschreven tussen Azure Event Grid Premium- en Basic-lagen en wanneer u elk
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300716"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Premium premium- en basislagen azure-gebeurtenisraster
Azure Event Grid heeft twee lagen: **Premium** en **Basic**. De basislaag maakt gebruik van verbruik of betalen per gebruik. Het geeft je alle basistools voor pub/sub die je nodig hebt om Event Grid te gebruiken voor event-driven programmeermodellen. De premium laag gaat nog een stap verder met beveiligingsfuncties gericht op de onderneming. De premium tier is in de vroege **preview** met veel van de functies nog in ontwikkeling.

## <a name="overview"></a>Overzicht
Alle aangepaste onderwerpen en domeinen in gebeurtenisraster behoren tot de basislaag of de premiumlaag. Vanaf `2020-04-01-preview` de API-versie u de gewenste laag kiezen als onderdeel van het maken van een onderwerp of domein. De standaardwaarde is de basislaag. De onderwerpen en domeinen die zijn gemaakt met oudere API-versies, worden standaard naar de basislaag geleid. Zie Hoe u de laag voor [onderwerpen en domeinen](update-tier.md)wilt bijwerken als u de prijscategorie voor uw onderwerpen en domeinen wilt wijzigen.

## <a name="capabilities-and-features"></a>Mogelijkheden en functies

In de volgende tabel worden verschillen tussen de lagen beschreven:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| IP-firewallregels voor binnendringen                          | Preview  | Preview |
| Servicetags voor uitgaande                                | Preview  | Preview |
| Private endpoint VNet-integratie bij binnendringen          | Niet beschikbaar   | Preview |

## <a name="availability"></a>Beschikbaarheid
Tijdens de eerste preview zijn premium tier-onderwerpen en domeinen met private endpoint-integratie beschikbaar in de volgende regio's:

- VS - oost
- VS - west 2
- VS - zuid-centraal

## <a name="pricing-and-quotas"></a>Prijzen en quota
Zie [Rekeninghoudende prijzen](https://azure.microsoft.com/pricing/details/event-grid/) voor de prijsdetails van het gebruik van de basislaag. De Premium-laagprijzen zijn nog niet aangekondigd en zijn gratis totdat de prijzen beschikbaar zijn.

De bestaande quota voor onderwerp- en domeintelling en doorvoer zijn van toepassing op zowel premium- als basisniveauresources totdat premiumtier-prijzen zijn aangekondigd.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- Zie het artikel [Prijsprijsniveau bijwerken](update-tier.md) als u wilt upgraden van basislaag naar premiumlaag. 
- U ip-firewall configureren voor uw Event Grid-bron om de toegang via het openbare internet te beperken vanuit slechts een selecte set IP-adressen of IP-adresbereiken. Zie [Firewall configureren](configure-firewall.md)voor stapsgewijze instructies.
- U privéeindpunten configureren om de toegang alleen vanaf geselecteerde virtuele netwerken te beperken. Zie [Privéeindpunten configureren](configure-private-endpoints.md)voor stapsgewijze instructies.