---
title: Api's voor Marketplace-meet Service-Veelgestelde vragen | Azure Marketplace
description: Verzend het gebruik van een SaaS-aanbieding in azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: c4f51adbcaa5e5b750169f53a1333544365fd4f3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825502"
---
# <a name="marketplace-metering-service-apis---faq"></a>Service-API's voor Marketplace-meting - Veelgestelde vragen

Zodra een Azure-gebruiker zich abonneert op een SaaS-service die gebruikmaakt van factuur met data limiet, houdt u het verbruik bij voor elke facturerings dimensie die wordt gebruikt door de klant. Als het verbruik de inbegrepen hoeveel heden overschrijdt die zijn ingesteld voor de periode die is geselecteerd door de klant, stuurt uw service gebruiks gebeurtenissen naar micro soft.

## <a name="emit-usage-events"></a>Gebruiks gebeurtenissen verzenden

>[!Note]
>Deze sectie is alleen van toepassing op SaaS-aanbiedingen, waarbij ten minste een van de abonnementen meet service dimensies heeft die zijn gedefinieerd op het moment van publicatie van de aanbieding.

![Gebruiks gebeurtenissen verzenden](media/isv-emits-usage-event.png)

Raadpleeg de [API voor SaaS batch Usage](./marketplace-metering-service-apis.md#batch-usage-event) voor informatie over het API-contract voor het verzenden van gebruiks gebeurtenissen.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hoe vaak verwacht het gebruik?

In het ideale geval moet u het gebruik elk uur voor het afgelopen uur verwachten, alleen als er in het vorige uur gebruik wordt gebruikt.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Wat is de maximale vertraging tussen het tijdstip waarop een gebeurtenis plaatsvindt en hoe lang een gebruiks gebeurtenis wordt verzonden naar micro soft?

In het ideale geval wordt de gebruiks gebeurtenis elke uur verzonden voor gebeurtenissen die in het afgelopen uur zijn opgetreden. Er worden echter vertragingen verwacht. De toegestane maximum vertraging is 24 uur, waarna er geen gebruiks gebeurtenissen worden geaccepteerd.

Als bijvoorbeeld een gebruiks gebeurtenis plaatsvindt om 1 uur op een dag, hebt u tot 1 uur op de volgende dag een gebruiks gebeurtenis die aan deze gebeurtenis is gekoppeld. Dit betekent dat in het geval van het door het systeem verzonden gebruik een bewerkings tijd is, de gebeurtenis kan worden hersteld en verzonden voor het uur interval waarin het gebruik heeft plaatsgevonden, zonder verlies van betrouw baarheid.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Wat gebeurt er wanneer u meer dan één gebruiks gebeurtenis in hetzelfde uur verzendt?

Er wordt slechts één gebruiks gebeurtenis geaccepteerd voor het uur interval. Het uur interval begint bij minuut 0 en eindigt op minuut 59.  Als er meer dan één gebruiks gebeurtenis voor hetzelfde uur interval wordt verzonden, worden latere gebruiks gebeurtenissen verwijderd als dubbele waarden.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Wat gebeurt er wanneer u het gebruik van een SaaS-abonnement dat al is afgemeld, uitschrijft?

Een gebruiks gebeurtenis die wordt verzonden naar het Marketplace-platform wordt niet geaccepteerd nadat een SaaS-abonnement is verwijderd.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kunt u een lijst met alle SaaS-abonnementen, waaronder actieve en niet-geabonneerde abonnementen, ophalen?

Ja, wanneer u de API van `GET /saas/subscriptions` aanroept, bevat een lijst met alle SaaS-abonnementen. In het veld Status in het antwoord voor elk SaaS-abonnement wordt vastgelegd of het abonnement actief of afgemeld is. De aanroep naar List-abonnementen retourneert Maxi maal 100 abonnementen op het moment.

## <a name="next-steps"></a>Volgende stappen

- Zie [api's voor Marketplace metering service](./marketplace-metering-service-apis.md) voor meer informatie.
