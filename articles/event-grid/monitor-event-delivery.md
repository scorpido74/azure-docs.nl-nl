---
title: Azure Event Grid metrische gegevens weer geven en waarschuwingen instellen
description: In dit artikel wordt beschreven hoe u de Azure Portal gebruikt om metrische gegevens weer te geven voor Azure Event Grid onderwerpen en abonnementen, en hierop waarschuwingen te maken.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: spelluru
ms.openlocfilehash: e74d2d8982cac961aa65d6576c80a92cb53ce387
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100633"
---
# <a name="monitor-event-grid-message-delivery"></a>Bezorging van Event Grid bericht bewaken 
In dit artikel wordt beschreven hoe u de portal kunt gebruiken om de metrische gegevens voor Event Grid-onderwerpen en-abonnementen te bekijken en om waarschuwingen te maken. 

## <a name="metrics"></a>Metrische gegevens

In de portal worden metrische gegevens weer gegeven voor de status van het leveren van gebeurtenis berichten.

Hier volgen enkele metrische gegevens voor onderwerpen:

* **Publiceren is voltooid**: de gebeurtenis is verzonden naar het onderwerp en verwerkt met een 2xx-antwoord.
* **Publiceren is mislukt**: de gebeurtenis is verzonden naar het onderwerp, maar er is een fout code afgewezen.
* Niet- **overeenkomende**: de gebeurtenis is gepubliceerd naar het onderwerp, maar is niet gekoppeld aan een gebeurtenis abonnement. De gebeurtenis is verwijderd.

Hier volgen enkele metrische gegevens voor abonnementen:

* De **levering is voltooid**: de gebeurtenis is bezorgd bij het eind punt van het abonnement en er is een 2xx-antwoord ontvangen.
* De **Bezorging is mislukt**: elke keer dat de service probeert te leveren en de gebeurtenis-handler retourneert geen succes 2xx-code, wordt het prestatie meter item dat **kan worden bezorgd** verhoogd. Als we proberen dezelfde gebeurtenis meerdere keren te leveren en mislukken, wordt het **prestatie meter** item voor elke fout verhoogd.
* **Verlopen gebeurtenissen**: de gebeurtenis is niet bezorgd en alle nieuwe pogingen zijn verzonden. De gebeurtenis is verwijderd.
* **Overeenkomende gebeurtenissen**: de gebeurtenis in het onderwerp is afgestemd op het gebeurtenis abonnement.

    > [!NOTE]
    > Zie [metrische gegevens die worden ondersteund door Azure Event grid](metrics.md)voor een volledige lijst met metrische gegevens.

## <a name="view-custom-topic-metrics"></a>Metrische gegevens over aangepaste onderwerpen weer geven

Als u een aangepast onderwerp hebt gepubliceerd, kunt u de metrische gegevens weer geven. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Typ in de zoek balk van het onderwerp **Event grid onderwerpen**en selecteer vervolgens **Event grid onderwerpen** in de vervolg keuzelijst. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Event Grid onderwerpen zoeken en selecteren":::
3. Selecteer uw aangepaste onderwerp in de lijst met onderwerpen. 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="Uw aangepaste onderwerp selecteren":::
4. Bekijk de metrische gegevens voor het onderwerp van de aangepaste gebeurtenis op de pagina met het **Event grid onderwerp** . In de volgende afbeelding is de sectie **Essentials** waarin de resource groep, het abonnement etc. wordt weer gegeven, geminimaliseerd. 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="Metrische gebeurtenis gegevens weer geven":::

U kunt grafieken met ondersteunde metrische gegevens maken met behulp van het tabblad **metrische gegevens** van de pagina met het **Event grid onderwerp** .

:::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="Pagina met metrische gegevens over het onderwerp":::

Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md) voor meer informatie over metrische gegevens.

Zie bijvoorbeeld het grafiek met metrische gegevens voor de metrische gegevens van **gepubliceerde gebeurtenissen** .

:::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="Metrische gegevens van gepubliceerde gebeurtenissen":::


## <a name="view-subscription-metrics"></a>Metrische gegevens van abonnement weer geven
1. Ga naar de pagina met het **Event grid-onderwerp** door de stappen uit de vorige sectie te volgen. 
2. Selecteer het abonnement in het onderste deel venster, zoals in het volgende voor beeld wordt weer gegeven. 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="Gebeurtenis abonnement selecteren":::    

    U kunt ook zoeken naar **Event grid-abonnementen** in de zoek balk van de Azure Portal, **het type onderwerp**, het **abonnement**en de **locatie** selecteren om een gebeurtenis abonnement te zien. 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="Selecteer een gebeurtenis abonnement op de pagina Event Grid abonnementen":::        

    Voor aangepaste onderwerpen selecteert u **Event grid onderwerpen** als **onderwerps type**. Selecteer voor systeem onderwerpen het type van de Azure-resource, bijvoorbeeld **opslag accounts (BLOB, GPv2)**. 
3. Bekijk de metrische gegevens voor het abonnement op de start pagina van het abonnement in een grafiek. U kunt **algemene**, **fout**-, **latentie**-en **onbestelbare** metrische gegevens weer geven voor afgelopen 1 uur, 6 uur, 12 uur, 1 dag, 7 dagen of 30 dagen. 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="Metrische gegevens op de start pagina van het abonnement":::    

## <a name="view-system-topic-metrics"></a>Metrische gegevens over het systeem onderwerp weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Typ in de zoek balk van het onderwerp **Event grid systeem onderwerpen**en selecteer vervolgens **Event grid systeem onderwerpen** in de vervolg keuzelijst. 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="Event Grid systeem onderwerpen zoeken en selecteren":::
3. Selecteer uw systeem onderwerp in de lijst met onderwerpen. 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="Selecteer uw systeem onderwerp":::
4. Bekijk de metrische gegevens voor het onderwerp systeem op de pagina **Event grid systeem onderwerp** . In de volgende afbeelding is de sectie **Essentials** waarin de resource groep, het abonnement etc. wordt weer gegeven, geminimaliseerd. 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="Metrische gegevens over het systeem onderwerp weer geven op de pagina overzicht":::

U kunt grafieken met ondersteunde metrische gegevens maken met behulp van het tabblad **metrische gegevens** van de pagina met het **Event grid onderwerp** .

:::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="Systeem onderwerp-metrische gegevens pagina":::

Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md) voor meer informatie over metrische gegevens.


## <a name="set-alerts"></a>Waarschuwingen instellen
U kunt waarschuwingen instellen voor onderwerpen en domeinen in de Azure Portal. 

De volgende procedure laat zien hoe u een waarschuwing kunt maken voor de metriek van **onbestelbare gebeurtenissen** voor een aangepast onderwerp. In dit voor beeld wordt een e-mail bericht verzonden naar de eigenaar van de Azure-resource groep wanneer het aantal onbestelbare gebeurtenissen voor een onderwerp boven 10 komt. 

1. Selecteer op de pagina **Event grid onderwerp** voor uw onderwerp **waarschuwingen** in het menu links en selecteer vervolgens **+ nieuwe waarschuwings regel**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Pagina waarschuwingen-knop nieuwe waarschuwings regel":::
    
    
    U kunt ook waarschuwingen maken met behulp van de pagina **metrische gegevens** . De stappen zijn vergelijkbaar. 

    :::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Pagina metrische gegevens-knop waarschuwing maken":::   
    
2. Controleer op de pagina **waarschuwings regel maken** of het onderwerp is geselecteerd voor de resource. Klik vervolgens op **voor waarde selecteren**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Pagina waarschuwingen-voor waarde selecteren":::    
3. Voer op de pagina **signaal logica configureren** de volgende stappen uit:
    1. Selecteer een metrische waarde of een vermelding in het activiteiten logboek. In dit voor beeld wordt **onbestelbare gebeurtenissen** geselecteerd. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Onbestelbare gebeurtenissen selecteren":::        
    2. Selecteer dimensies (optioneel). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Signaallogica configureren":::        
    3. Schuif omlaag. Selecteer in de sectie **waarschuwings logica** een **operator**, **aggregatie type**, voer een **drempel waarde**in en selecteer **gereed**. In dit voor beeld wordt een waarschuwing geactiveerd wanneer het totale aantal onbestelbare gebeurtenissen groter is dan 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Waarschuwings logica":::                
4. Klik op de pagina **waarschuwings regel maken** op **actie groep selecteren**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Knop actie groep selecteren":::
5. Selecteer **actie groep maken** op de werk balk om een nieuwe actie groep te maken. U kunt ook een bestaande actie groep selecteren.        
6. Voer op de pagina **actie groep toevoegen** de volgende stappen uit:
    1. Voer een **naam in voor de actie groep**.
    1. Voer een **korte naam** in voor de actie groep.
    1. Selecteer een **Azure-abonnement** waarin u de actie groep wilt maken.
    1. Selecteer de **Azure-resource groep** waarin u de actie groep wilt maken.
    1. Voer een **naam in voor de actie**. 
    1. Selecteer het **actie type**. In dit voor beeld is de **rol e-mail Azure Resource Manager** geselecteerd, met name de rol van **eigenaar** . 
    1. Selecteer **OK** om de pagina te sluiten. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Pagina actie groep toevoegen":::                   
7. Ga terug naar de pagina **waarschuwings regel maken** en voer een naam in voor de waarschuwings regel en selecteer vervolgens **waarschuwings regel maken**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Naam van waarschuwingsregel":::  
8. Nu ziet u op de pagina **waarschuwingen** van het onderwerp een koppeling voor het beheren van waarschuwings regels als er nog geen waarschuwingen zijn. Als er waarschuwingen zijn, selecteert u **waarschuwings regels voor beheer** op de werk balk.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Waarschuwingen beheren":::

    > [!NOTE]
    > Dit artikel heeft geen betrekking op alle verschillende stappen en combi naties die u kunt gebruiken om een waarschuwing te maken. Zie [overzicht van waarschuwingen](../azure-monitor/platform/alerts-overview.md)voor een overzicht van waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

* [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
