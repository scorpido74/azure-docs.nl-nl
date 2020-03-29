---
title: Azure Event Grid-berichtbezorging bewaken
description: In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om de status van de levering van Azure Event Grid-berichten te bekijken.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722132"
---
# <a name="monitor-event-grid-message-delivery"></a>De bezorging van gebeurtenisrasterberichten bewaken 

In dit artikel wordt beschreven hoe u de portal gebruiken om de status van gebeurtenisleveringen te bekijken.

Event Grid biedt duurzame levering. Het levert elk bericht ten minste eenmaal voor elk abonnement. Evenementen worden onmiddellijk naar de geregistreerde webhook van elk abonnement gestuurd. Als een webhook de ontvangst van een gebeurtenis niet bevestigt binnen 60 seconden na de eerste leveringspoging, probeert Event Grid de levering van de gebeurtenis opnieuw in.

Voor informatie over het bezorgen en opnieuw proberen van [gebeurtenissen, het verzenden van het gebeurtenisrasterbericht en het opnieuw proberen](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Leveringsstatistieken

De portal toont statistieken voor de status van het leveren van gebeurtenisberichten.

Voor onderwerpen zijn de statistieken:

* **Geslaagd publiceren:** gebeurtenis is met succes naar het onderwerp verzonden en verwerkt met een 2xx-antwoord.
* **Publiceren mislukt:** gebeurtenis die naar het onderwerp wordt verzonden, maar met een foutcode is afgewezen.
* **Ongeëvenaard**: Gebeurtenis is gepubliceerd in het onderwerp, maar niet gekoppeld aan een evenementabonnement. Het evenement werd geschrapt.

Voor abonnementen zijn de statistieken:

* **Levering geslaagd**: Gebeurtenis is met succes geleverd aan het eindpunt van het abonnement en heeft een 2xx-antwoord ontvangen.
* **Levering mislukt**: Gebeurtenis verzonden naar het eindpunt van het abonnement, maar heeft een 4xx- of 5xx-antwoord ontvangen.
* **Verlopen gebeurtenissen**: Gebeurtenis is niet geleverd en alle pogingen tot opnieuw proberen zijn verzonden. Het evenement werd geschrapt.
* **Overeenkomende gebeurtenissen**: Gebeurtenis in het onderwerp is gekoppeld aan het evenementabonnement.

## <a name="event-subscription-status"></a>Status van gebeurtenisabonnement

Als u statistieken voor een gebeurtenisabonnement wilt zien, u zoeken op abonnementstype of op abonnementen voor een specifieke bron.

Als u wilt zoeken op het type gebeurtenisabonnement, selecteert u **Alle services**.

![Alle services selecteren](./media/monitor-event-delivery/all-services.png)

Zoek naar **gebeurtenisraster** en selecteer **Gebeurtenisrasterabonnementen** in de beschikbare opties.

![Zoeken naar abonnementen op gebeurtenissen](./media/monitor-event-delivery/search-and-select.png)

Filter op het type gebeurtenis, het abonnement en de locatie. Selecteer **Statistieken** voor het abonnement dat u wilt weergeven.

![Gebeurtenisabonnementen filteren](./media/monitor-event-delivery/filter-events.png)

Bekijk de statistieken voor het evenementonderwerp en -abonnement.

![Gebeurtenisstatistieken weergeven](./media/monitor-event-delivery/subscription-metrics.png)

Als u de statistieken voor een specifieke resource wilt vinden, selecteert u die resource. Selecteer vervolgens **Gebeurtenissen**.

![Gebeurtenissen voor een resource selecteren](./media/monitor-event-delivery/select-events.png)

U ziet de statistieken voor abonnementen voor die resource.

## <a name="custom-event-status"></a>Aangepaste gebeurtenisstatus

Als u een aangepast onderwerp hebt gepubliceerd, u de statistieken hiervoor bekijken. Selecteer de resourcegroep voor het onderwerp en selecteer het onderwerp.

![Aangepaste onderwerp selecteren](./media/monitor-event-delivery/select-custom-topic.png)

Bekijk de statistieken voor het aangepaste gebeurtenisonderwerp.

![Gebeurtenisstatistieken weergeven](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Waarschuwingen instellen

U waarschuwingen instellen voor de statistieken van het onderwerp en het domeinniveau voor aangepaste onderwerpen en gebeurtenisdomeinen. Selecteer in het **overzichtsblad** voor de optie Waarschuwingen van links het resourcemenu om waarschuwingsregels weer te geven, te beheren en te maken. [Meer informatie over Azure Monitor Alerts](../azure-monitor/platform/alerts-overview.md)

![Gebeurtenisstatistieken weergeven](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het bezorgen en opnieuw proberen van [gebeurtenissen, het verzenden van het gebeurtenisrasterbericht en het opnieuw proberen](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
