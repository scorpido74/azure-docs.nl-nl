---
title: Api's voor marketplace-meetservice - veelgestelde vragen | Azure Marketplace
description: Het gebruik van een SaaS-aanbieding in de Azure Marketplace uitzenden.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275778"
---
# <a name="marketplace-metering-service-apis---faq"></a>Service-API's voor Marketplace-meting - Veelgestelde vragen

Zodra een Azure-gebruiker zich abonneert op een SaaS-service met facturering met datawaarde, houdt u het verbruik bij voor elke factureringsdimensie die door de klant wordt gebruikt. Als het verbruik hoger is dan de opgenomen hoeveelheden die zijn ingesteld voor de door de klant geselecteerde term, zendt uw service gebruiksgebeurtenissen uit naar Microsoft.

## <a name="emit-usage-events"></a>Gebruiksgebeurtenissen uitzenden

>[!Note]
>Deze sectie is alleen van toepassing op SaaS-aanbiedingen, waarbij ten minste één van de plannen meetserviceafmetingen heeft die zijn gedefinieerd op het moment van publicatie van de aanbieding.

![Gebruiksgebeurtenissen uitzenden](media/isv-emits-usage-event.png)

Zie de [API voor batchgebruik saas](./marketplace-metering-service-apis.md#batch-usage-event) voor informatie over het API-contract voor het uitzenden van gebruiksgebeurtenissen.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hoe vaak wordt verwacht dat het gebruik uitstraalt?

Idealiter wordt van u verwacht dat u het afgelopen uur elk uur gebruik uitstraalt, alleen als er gebruik is in het vorige uur.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Wat is de maximale vertraging tussen het tijdstip van een gebeurtenis en de tijd dat een gebruiksgebeurtenis wordt uitgezonden naar Microsoft?

Idealiter wordt de gebruiksgebeurtenis elk uur uitgezonden voor gebeurtenissen die zich in het afgelopen uur hebben voorgedaan. Er worden echter vertragingen verwacht. De maximaal toegestane vertraging is 24 uur, waarna gebruiksgebeurtenissen niet worden geaccepteerd.

Als een gebruiksgebeurtenis bijvoorbeeld op een dag om 13.00 uur plaatsvindt, hebt u de volgende dag tot 13.00 uur de tijd om een gebruiksgebeurtenis uit te zenden die aan deze gebeurtenis is gekoppeld. Dit betekent dat in het geval van het systeem emitterende gebruik een down time heeft, het kan herstellen en vervolgens de gebruiksgebeurtenis verzenden voor het uurinterval waarin het gebruik is gebeurd, zonder verlies van trouw.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Wat gebeurt er als u meer dan één gebruiksgebeurtenis op hetzelfde uur verzendt?

Er wordt slechts één gebruiksgebeurtenis geaccepteerd voor het uurinterval. Het uurinterval begint bij minuut 0 en eindigt op minuut 59.  Als er meer dan één gebruiksgebeurtenis wordt uitgezonden voor hetzelfde uurinterval, worden eventuele volgende gebruiksgebeurtenissen als duplicaten verwijderd.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Wat gebeurt er als u gebruik uitzendt voor een SaaS-abonnement dat al is afgemeld?

Elke gebruiksgebeurtenis die wordt uitgezonden naar het Marketplace-platform, wordt niet geaccepteerd nadat een SaaS-abonnement is verwijderd.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>u een lijst krijgen van alle SaaS-abonnementen, inclusief actieve en afgemelde abonnementen?

Ja, wanneer u `GET /saas/subscriptions` de API aanroept, bevat deze een lijst met alle SaaS-abonnementen. Het statusveld in het antwoord voor elk SaaS-abonnement geeft aan of het abonnement actief of uitgeschreven is. De call-to-list Abonnementen retourneert op dat moment maximaal 100 abonnementen.

## <a name="next-steps"></a>Volgende stappen

- Zie [API's voor marketplace-meetservice voor](./marketplace-metering-service-apis.md) meer informatie.
