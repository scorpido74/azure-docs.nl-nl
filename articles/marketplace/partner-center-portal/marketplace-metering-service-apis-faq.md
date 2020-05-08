---
title: Veelgestelde vragen over meter service-Api's-micro soft Commercial Marketplace
description: Veelgestelde vragen over de Api's van de meter service voor SaaS-aanbiedingen in Microsoft AppSource en Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857894"
---
# <a name="marketplace-metering-service-apis---faq"></a>Service-API's voor Marketplace-meting - Veelgestelde vragen

Wanneer een Azure-gebruiker zich abonneert op een SaaS-service die gebruikmaakt van factuur met data limiet, wordt het verbruik bijgehouden voor elke facturerings dimensie die wordt gebruikt door de klant. Als het verbruik de inbegrepen hoeveel heden overschrijdt die zijn ingesteld voor de periode die is geselecteerd door de klant, stuurt uw service gebruiks gebeurtenissen naar micro soft.

## <a name="emit-usage-events"></a>Gebruiks gebeurtenissen verzenden

>[!Note]
>Deze sectie is alleen van toepassing op SaaS-aanbiedingen, waarbij ten minste een van de abonnementen meet service dimensies heeft die zijn gedefinieerd op het moment van publicatie van de aanbieding.

![Gebruiks gebeurtenissen verzenden](media/isv-emits-usage-event.png)

Raadpleeg de [API voor SaaS batch Usage](./marketplace-metering-service-apis.md#batch-usage-event) voor informatie over het API-contract voor het verzenden van gebruiks gebeurtenissen.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hoe vaak verwacht het gebruik?

In het ideale geval moet u het gebruik elk uur voor het afgelopen uur verwachten, alleen als er in het vorige uur gebruik wordt gebruikt.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Wat is de maximale vertraging tussen het tijdstip waarop een gebeurtenis plaatsvindt en hoe lang een gebruiks gebeurtenis wordt verzonden naar micro soft?

In het ideale geval wordt de gebruiks gebeurtenis elke uur verzonden voor gebeurtenissen die in het afgelopen uur zijn opgetreden. Er worden echter vertragingen verwacht. De toegestane maximum vertraging is 24 uur, waarna er geen gebruiks gebeurtenissen worden geaccepteerd.

Als bijvoorbeeld een gebruiks gebeurtenis plaatsvindt om 1 uur op een dag, hebt u tot 1 uur op de volgende dag een gebruiks gebeurtenis die aan deze gebeurtenis is gekoppeld. Wanneer het gebruik van het systeem een bedrukte tijd heeft, wordt de gebruiks gebeurtenis hersteld en verzonden voor het uren interval waarin het gebruik is opgetreden, zonder verlies van betrouw baarheid.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Wat gebeurt er wanneer u meer dan één gebruiks gebeurtenis in hetzelfde uur verzendt?

Er wordt slechts één gebruiks gebeurtenis geaccepteerd voor het uur interval. Het uur interval begint bij minuut 0 en eindigt op minuut 59.  Als er meer dan één gebruiks gebeurtenis voor hetzelfde uur interval wordt verzonden, worden latere gebruiks gebeurtenissen verwijderd als dubbele waarden.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Wat gebeurt er wanneer u het gebruik van een SaaS-abonnement dat al is afgemeld, uitschrijft?

Een gebruiks gebeurtenis die wordt verzonden naar het Marketplace-platform wordt niet geaccepteerd nadat een SaaS-abonnement is verwijderd.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kunt u een lijst met alle SaaS-abonnementen, waaronder actieve en niet-geabonneerde abonnementen, ophalen?

Ja, wanneer u de `GET /saas/subscriptions` API aanroept, bevat een lijst met alle SaaS-abonnementen. In het veld Status in het antwoord voor elk SaaS-abonnement wordt vastgelegd of het abonnement actief of afgemeld is. De aanroep naar List-abonnementen retourneert Maxi maal 100 abonnementen op het moment.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Wat gebeurt er als de Marketplace-meet service een storing heeft?

Als de ISV een aangepaste meter verzendt en een fout ontvangt, moet de ISV wachten en het opnieuw proberen.

Als de fout zich blijft voordoen, verzendt u die aangepaste meter het volgende uur opnieuw (accumulatie van het aantal). Ga door met dit proces totdat er een niet-fout bericht wordt ontvangen.

## <a name="next-steps"></a>Volgende stappen

- Zie Api's voor Marketplace- [meet service](./marketplace-metering-service-apis.md)voor meer informatie.
