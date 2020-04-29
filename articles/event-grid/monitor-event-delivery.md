---
title: Bezorging van Azure Event Grid bericht bewaken
description: In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om de status van de levering van Azure Event Grid berichten te bekijken.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722132"
---
# <a name="monitor-event-grid-message-delivery"></a>Bezorging van Event Grid bericht bewaken 

In dit artikel wordt beschreven hoe u de portal kunt gebruiken om de status van de gebeurtenis leveringen te bekijken.

Event Grid biedt een duurzame levering. Het levert elk bericht ten minste één keer per abonnement. Gebeurtenissen worden direct naar de geregistreerde webhook van elk abonnement verzonden. Als een webhook de ontvangst van een gebeurtenis binnen 60 seconden van de eerste bezorgings poging niet bevestigt, Event Grid nieuwe pogingen van de gebeurtenis.

[Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Metrische leverings gegevens

In de portal worden metrische gegevens weer gegeven voor de status van het leveren van gebeurtenis berichten.

Voor onderwerpen zijn de metrische gegevens:

* **Publiceren is voltooid**: de gebeurtenis is verzonden naar het onderwerp en verwerkt met een 2xx-antwoord.
* **Publiceren is mislukt**: de gebeurtenis is verzonden naar het onderwerp, maar er is een fout code afgewezen.
* Niet- **overeenkomende**: de gebeurtenis is gepubliceerd naar het onderwerp, maar is niet gekoppeld aan een gebeurtenis abonnement. De gebeurtenis is verwijderd.

Voor abonnementen zijn de metrische gegevens:

* De **levering is voltooid**: de gebeurtenis is bezorgd bij het eind punt van het abonnement en er is een 2xx-antwoord ontvangen.
* De **Bezorging is mislukt**: de gebeurtenis is verzonden naar het eind punt van het abonnement, maar er is een 4xx-of 5xx-antwoord ontvangen.
* **Verlopen gebeurtenissen**: de gebeurtenis is niet bezorgd en alle nieuwe pogingen zijn verzonden. De gebeurtenis is verwijderd.
* **Overeenkomende gebeurtenissen**: de gebeurtenis in het onderwerp is afgestemd op het gebeurtenis abonnement.

## <a name="event-subscription-status"></a>Status van gebeurtenis abonnement

Als u de metrische gegevens voor een gebeurtenis abonnement wilt bekijken, kunt u zoeken op abonnements type of op abonnementen voor een specifieke resource.

Als u wilt zoeken op type gebeurtenis abonnement, selecteert u **alle services**.

![Alle services selecteren](./media/monitor-event-delivery/all-services.png)

Zoek naar **Event grid** en selecteer **Event grid-abonnementen** uit de beschik bare opties.

![Zoeken naar gebeurtenis abonnementen](./media/monitor-event-delivery/search-and-select.png)

Filteren op het type gebeurtenis, het abonnement en de locatie. Selecteer **metrische gegevens** voor het abonnement dat u wilt weer geven.

![Gebeurtenis abonnementen filteren](./media/monitor-event-delivery/filter-events.png)

De metrische gegevens weer geven voor het onderwerp en het abonnement van de gebeurtenis.

![Metrische gebeurtenis gegevens weer geven](./media/monitor-event-delivery/subscription-metrics.png)

Als u de metrische gegevens voor een specifieke resource wilt vinden, selecteert u die resource. Selecteer vervolgens **gebeurtenissen**.

![Gebeurtenissen voor een resource selecteren](./media/monitor-event-delivery/select-events.png)

U ziet de metrische gegevens voor abonnementen voor die bron.

## <a name="custom-event-status"></a>Aangepaste gebeurtenis status

Als u een aangepast onderwerp hebt gepubliceerd, kunt u de metrische gegevens weer geven. Selecteer de resource groep voor het onderwerp en selecteer het onderwerp.

![Aangepaste onderwerp selecteren](./media/monitor-event-delivery/select-custom-topic.png)

Bekijk de metrische gegevens voor het onderwerp van de aangepaste gebeurtenis.

![Metrische gebeurtenis gegevens weer geven](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Waarschuwingen instellen

U kunt waarschuwingen instellen voor het onderwerp en metrische gegevens op domein niveau voor aangepaste onderwerpen en gebeurtenis domeinen. Selecteer in de Blade overzicht voor de optie **waarschuwingen** aan de linkerkant had resource menu om waarschuwings regels weer te geven, te beheren en te maken. [Meer informatie over Azure Monitor-waarschuwingen](../azure-monitor/platform/alerts-overview.md)

![Metrische gebeurtenis gegevens weer geven](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Volgende stappen

* [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
