---
title: Azure Service Bus metrische gegevens in Azure Monitor | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Azure Monitor kunt gebruiken om Service Bus entiteiten (wacht rijen, onderwerpen en abonnementen) te bewaken.
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 569eb31c6cbe8b95773d52f6e1325801fbabf86f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773536"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus metrische gegevens in Azure Monitor

Service Bus metrische gegevens geven u de status van resources in uw Azure-abonnement. Met een uitgebreide set met metrische gegevens kunt u de algemene status van uw Service Bus-resources controleren, niet alleen op het niveau van de naam ruimte, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van Service Bus kunt bewaken. Metrische gegevens kunnen ook helpen problemen hoofdoorzaak zonder contact opnemen met ondersteuning van Azure.

Azure Monitor biedt een uniforme gebruikersinterfaces voor bewaking over de verschillende Azure-services. Zie voor meer informatie, [bewaken in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en de [ophalen van Azure Monitor metrics met .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op GitHub.

> [!IMPORTANT]
> Als er gedurende twee uur geen interactie met een entiteit is geweest, worden de metrische gegevens als een waarde weer gegeven totdat de entiteit niet langer inactief is.

## <a name="access-metrics"></a>Toegang tot metrische gegevens

Azure Monitor biedt meerdere manieren voor toegang tot metrische gegevens. U hebt toegang tot metrische gegevens via de [Azure Portal](https://portal.azure.com), of u kunt gebruikmaken van de Azure monitor API'S (rest en .net) en analyse oplossingen, zoals Azure monitor logboeken en Event hubs. Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md)voor meer informatie.

Metrische gegevens zijn standaard ingeschakeld en u hebt toegang tot gegevens van de meest recente 30 dagen. Als u behouden van gegevens voor een langere periode wilt, kunt u metrische gegevens om een Azure Storage-account te archiveren. Deze waarde wordt geconfigureerd in [Diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in azure monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens controleren na verloop van tijd in de [Azure-portal](https://portal.azure.com). Het volgende voorbeeld laat zien hoe om binnenkomende aanvragen op accountniveau en geslaagde aanvragen weer te geven:

![][1]

U kunt ook toegang tot metrische gegevens rechtstreeks via de naamruimte. Hiertoe selecteert u uw naam ruimte en klikt u op **metrische gegevens**. Als u metrische gegevens wilt weer geven die zijn gefilterd op het bereik van de entiteit, selecteert u de entiteit en klikt u op **metrische gegevens**.

![][2]

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde.

## <a name="billing"></a>Billing

Metrische gegevens en waarschuwingen op Azure Monitor worden per waarschuwing in rekening gebracht. Deze kosten moeten beschikbaar zijn op de Portal wanneer de waarschuwing wordt ingesteld en voordat deze wordt opgeslagen. 

Aanvullende oplossingen waarmee metrische gegevens worden opgenomen, worden rechtstreeks door deze oplossingen in rekening gebracht. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens om een Azure Storage-account te archiveren. U wordt ook gefactureerd door Log Analytics als u metrische gegevens streamt naar Log Analytics voor geavanceerde analyse.

De volgende metrische gegevens geven u een overzicht van de status van uw service. 

> [!NOTE]
> Er zijn verschillende metrische gegevens niet meer ondersteund als ze worden verplaatst onder een andere naam. Dit moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutelwoord 'afgeschaft' wordt niet ondersteund voortaan.

Alle metrische waarden worden verzonden naar Azure Monitor elke minuut. De tijdgranulatie definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijds interval voor alle Service Bus meet waarden is 1 minuut.

## <a name="request-metrics"></a>Aanvraag voor metrische gegevens

Telt het aantal aanvragen voor beheer van gegevens en bewerkingen.

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| Binnenkomende aanvragen| Het aantal aanvragen voor de Service Bus-service gedurende een opgegeven periode. <br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|Geslaagde aanvragen|Het aantal geslaagde aanvragen voor de Service Bus-service gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|Server fouten|Het aantal niet-verwerkte aanvragen vanwege een fout in de Service Bus-service gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|Gebruikers fouten (Zie de volgende Subsectie)|Het aantal aanvragen die niet worden verwerkt wegens gebruikersfouten gedurende een bepaalde periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|Vertraagde aanvragen|Het aantal aanvragen dat is beperkt omdat het gebruik is overschreden.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|

### <a name="user-errors"></a>Gebruikers fouten

De volgende twee typen fouten worden geclassificeerd als gebruikers fouten:

1. Fouten aan de client zijde (in HTTP die 400 fouten zouden zijn).
2. Fouten die optreden tijdens het verwerken van berichten, zoals [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Bericht metrische gegevens

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
|Inkomende berichten|Het aantal gebeurtenissen of berichten dat is verzonden naar Service Bus gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
|Uitgaande berichten|Het aantal gebeurtenissen of berichten dat is ontvangen van Service Bus gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|
| Berichten| Aantal berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Dimensie: EntityName |
| ActiveMessages| Aantal actieve berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Dimensie: EntityName |
| Onbestelbare berichten| Aantal onbestelbare berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/>Dimensie: EntityName |
| Geplande berichten| Aantal geplande berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld  <br/> Dimensie: EntityName |

## <a name="connection-metrics"></a>Metrische verbindingsgegevens

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
|ActiveConnections|Het aantal actieve verbindingen voor een naamruimte, maar ook op een entiteit.<br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Dimensie: EntityName|

## <a name="resource-usage-metrics"></a>Metrische gegevens over resource gebruik

> [!NOTE] 
> De volgende metrische gegevens zijn alleen beschikbaar voor de **Premium** -laag. 

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
|CPU-gebruik per naam ruimte|Het percentage CPU-gebruik van de naam ruimte.<br/><br/> Eenheid: percentage <br/> Aggregatie type: maximum <br/> Dimensie: EntityName|
|Gebruik van geheugen grootte per naam ruimte|Het percentage geheugen gebruik van de naam ruimte.<br/><br/> Eenheid: percentage <br/> Aggregatie type: maximum <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Metrische gegevens over dimensies

Azure Service Bus ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Dimensies toevoegen aan uw metrische gegevens is optioneel. Als u dimensies niet toevoegt, worden de metrische gegevens opgegeven op het niveau van de naamruimte. 

|Dimensie naam|Beschrijving|
| ------------------- | ----------------- |
|EntityName| Service Bus ondersteunt Messa ging-entiteiten in de naam ruimte.|

## <a name="set-up-alerts-on-metrics"></a>Waarschuwingen instellen voor metrische gegevens

1. Selecteer op het tabblad **metrieken** van de pagina **Service Bus naam ruimte** de optie **waarschuwingen configureren**. 

    ![Pagina metrische gegevens: menu waarschuwingen configureren](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecteer de optie **doel selecteren** en voer de volgende acties uit op de pagina **een resource selecteren** : 
    1. Selecteer **Service Bus naam ruimten** voor het veld **filteren op resource type** . 
    2. Selecteer uw abonnement voor het veld **filteren op abonnement** .
    3. Selecteer de **Service Bus-naam ruimte** in de lijst. 
    4. Selecteer **Done**. 
    
        ![Naamruimte selecteren](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecteer **criteria toevoegen**en voer de volgende acties uit op de pagina **signaal logica configureren** :
    1. Selecteer **metrische gegevens** voor het **signaal type**. 
    2. Selecteer een signaal. Bijvoorbeeld: **service fouten**. 

        ![Server fouten selecteren](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecteer **groter dan** voor **waarde**.
    2. Selecteer **totaal** voor **tijd aggregatie**. 
    3. Voer **5** in als **drempel waarde**. 
    4. Selecteer **Done**.    

        ![Voor waarde opgeven](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Vouw op de pagina **regel maken** de optie **waarschuwings Details definiëren**uit en voer de volgende acties uit:
    1. Voer een **naam** in voor de waarschuwing. 
    2. Voer een **Beschrijving** in voor de waarschuwing.
    3. Selecteer **Ernst** voor de waarschuwing. 

        ![Meldingsdetails](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Vouw op de pagina **regel maken** de optie **actie groep definiëren**uit, selecteer **nieuwe actie groep**en voer de volgende acties uit op de **pagina actie groep toevoegen**. 
    1. Voer een naam in voor de actie groep.
    2. Voer een korte naam in voor de actie groep. 
    3. Selecteer uw abonnement. 
    4. Selecteer een resourcegroep. 
    5. Voor dit scenario voert u **E-mail verzenden** in voor de **actie naam**.
    6. Selecteer **e-mail/SMS/push/Voice** voor **actie type**. 
    7. Selecteer **Details bewerken**. 
    8. Voer op de pagina **e-mail/SMS/push/Voice** de volgende acties uit:
        1. **E-mail**selecteren. 
        2. Typ het **e-mail adres**. 
        3. Selecteer **OK**.

            ![Meldingsdetails](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Selecteer **OK**op de pagina **actie groep toevoegen** . 
1. Selecteer op de pagina **regel maken** de optie **waarschuwings regel maken**. 

    ![Knop waarschuwings regel maken](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


