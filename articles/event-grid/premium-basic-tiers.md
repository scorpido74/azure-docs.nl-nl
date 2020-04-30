---
title: Azure Event Grid Premium-en Basic-lagen
description: In dit artikel wordt het verschil beschreven tussen de Azure Event Grid Premium-en Basic-lagen en wanneer u elk gebruikt
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79300716"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid Premium-en Basic-lagen
Azure Event Grid heeft twee lagen: **Premium** en **Basic**. De laag basis maakt gebruik van verbruik of betalen per gebruik-prijzen. Hiermee beschikt u over alle eenvoudige pub/sub-hulpprogram ma's die u nodig hebt om Event Grid te gebruiken voor op gebeurtenissen gebaseerde programmeer modellen. De Premium-laag neemt deze stap verder met beveiligings functies die voor de onderneming zijn gericht. De Premium-laag bevindt zich in vroege **Preview** en veel functies zijn nog in ontwikkeling.

## <a name="overview"></a>Overzicht
Alle aangepaste onderwerpen en domeinen in Event Grid behoren tot de Basic-laag of de Premium-laag. Vanaf de `2020-04-01-preview` API-versie kunt u de gewenste laag kiezen als onderdeel van het maken van een onderwerp of domein. De standaard waarde is de laag Basic. De onderwerpen en domeinen die zijn gemaakt met oudere API-versies, worden standaard ingesteld op de Basic-laag. Zie [tier bijwerken voor onderwerpen en domeinen voor informatie over](update-tier.md)het wijzigen van de prijs categorie voor uw onderwerpen en domeinen.

## <a name="capabilities-and-features"></a>Mogelijkheden en functies

In de volgende tabel worden de verschillen tussen de lagen beschreven:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| IP-firewall regels voor binnenkomend verkeer                          | Preview  | Preview |
| Service tags voor uitgaand verkeer                                | Preview  | Preview |
| Integratie van het persoonlijke endpoint VNet op inkomend verkeer          | Niet beschikbaar   | Preview |

## <a name="availability"></a>Beschikbaarheid
Tijdens de eerste preview-versie van de Premium-laag zijn de onderwerpen en domeinen met persoonlijke endpoint Integration beschikbaar in de volgende regio's:

- VS - oost
- VS - west 2
- VS - zuid-centraal

## <a name="pricing-and-quotas"></a>Prijzen en quota
Zie [Event grid prijzen](https://azure.microsoft.com/pricing/details/event-grid/) voor de prijs informatie over het gebruik van de Basic-laag. De prijs voor de Premium-laag is nog niet aangekondigd en is gratis, totdat de prijzen beschikbaar zijn.

De bestaande quota's op het niveau van het onderwerp en het domein en de door Voer zijn van toepassing op de resources Premium en Basic, totdat de prijzen voor de Premium-laag worden aangekondigd.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- Zie het artikel [prijs categorie bijwerken](update-tier.md) als u een upgrade wilt uitvoeren van de Basic-laag naar de Premium-laag. 
- U kunt IP-Firewall voor uw Event Grid-bron configureren om de toegang via het open bare Internet te beperken tot alleen een select set met IP-adressen of IP-adresbereiken. Zie [Configure firewall](configure-firewall.md)(Engelstalig) voor stapsgewijze instructies.
- U kunt privé-eind punten zo configureren dat alleen toegang wordt beperkt tot geselecteerde virtuele netwerken. Zie voor stapsgewijze instructies [persoonlijke eind punten configureren](configure-private-endpoints.md).