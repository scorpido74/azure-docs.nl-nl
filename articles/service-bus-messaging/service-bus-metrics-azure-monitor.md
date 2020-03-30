---
title: Azure Service Bus-statistieken in Azure Monitor| Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u Azure Monitor gebruiken om servicebusentiteiten (wachtrijen, onderwerpen en abonnementen) te controleren.
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240791"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus-statistieken in Azure Monitor

ServiceBus-statistieken geven u de status van resources in uw Azure-abonnement. Met een uitgebreide set metrische gegevens u de algehele status van uw Service Bus-resources beoordelen, niet alleen op het niveau van de naamruimte, maar ook op entiteitsniveau. Deze statistieken kunnen belangrijk zijn omdat ze u helpen om de status van Service Bus te controleren. Statistieken kunnen ook helpen bij het oplossen van problemen met de oorzaak van de oorzaak zonder dat u contact hoeft op te nemen met Azure-ondersteuning.

Azure Monitor biedt uniforme gebruikersinterfaces voor bewaking in verschillende Azure-services. Zie [Controle in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en de Azure [Monitor-statistieken ophalen met het voorbeeld .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op GitHub voor meer informatie.

> [!IMPORTANT]
> Wanneer er gedurende 2 uur geen interactie met een entiteit is geweest, worden de statistieken "0" als waarde weergegeven totdat de entiteit niet langer actief is.

## <a name="access-metrics"></a>Toegangsstatistieken

Azure Monitor biedt meerdere manieren om toegang te krijgen tot statistieken. U via de [Azure-portal](https://portal.azure.com)toegang krijgen tot statistieken of de Azure Monitor API's (REST en .NET) en analyseoplossingen zoals Azure Monitor-logboeken en gebeurtenishubs gebruiken. Zie [Statistieken in Azure Monitor](../azure-monitor/platform/data-platform-metrics.md)voor meer informatie.

Statistieken zijn standaard ingeschakeld en u hebt toegang tot de meest recente 30 dagen aan gegevens. Als u gegevens voor een langere periode moet bewaren, u metrische gegevens archiveren naar een Azure Storage-account. Deze waarde is geconfigureerd in [diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot statistieken in de portal

U statistieken in de loop van de tijd controleren in de [Azure-portal.](https://portal.azure.com) In het volgende voorbeeld ziet u hoe u succesvolle aanvragen en binnenkomende aanvragen op accountniveau weergeven:

![][1]

U ook rechtstreeks via de naamruimte statistieken openen. Selecteer hiervoor uw naamruimte en klik op **Statistieken**. Als u statistieken wilt weergeven die zijn gefilterd op het bereik van de entiteit, selecteert u de entiteit en klikt u vervolgens op **Metrische gegevens**.

![][2]

Voor metrische gegevens die dimensies ondersteunen, moet u filteren met de gewenste dimensiewaarde.

## <a name="billing"></a>Billing

Statistieken en waarschuwingen op Azure Monitor worden per waarschuwing in rekening gebracht. Deze kosten moeten beschikbaar zijn op de portal wanneer de waarschuwing is ingesteld en voordat deze wordt opgeslagen. 

Aanvullende oplossingen die metrische gegevens opnemen, worden rechtstreeks door deze oplossingen gefactureerd. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. U wordt ook gefactureerd door Log Analytics als u metrische gegevens streamt naar Log Analytics voor geavanceerde analyse.

De volgende statistieken geven u een overzicht van de status van uw service. 

> [!NOTE]
> We zijn verschillende statistieken aan het deprecating omdat ze onder een andere naam worden verplaatst. Hiervoor u uw referenties bijwerken. Statistieken die zijn gemarkeerd met het trefwoord 'afgeschaft' worden in de toekomst niet ondersteund.

Alle metrische waarden worden elke minuut naar Azure Monitor verzonden. De tijdgranulariteit definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijdsinterval voor alle Service Bus-statistieken is 1 minuut.

## <a name="request-metrics"></a>Statistieken aanvragen

Telt het aantal aanvragen voor gegevens- en beheerbewerkingen.

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Binnenkomende aanvragen| Het aantal aanvragen dat gedurende een bepaalde periode aan de servicebusservice wordt gedaan. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Succesvolle aanvragen|Het aantal succesvolle aanvragen voor de Service Bus-service gedurende een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Serverfouten|Het aantal aanvragen dat niet is verwerkt als gevolg van een fout in de servicebusservice gedurende een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Gebruikersfouten (zie de volgende subsectie)|Het aantal aanvragen dat niet is verwerkt vanwege gebruikersfouten gedurende een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Gewurgde aanvragen|Het aantal aanvragen dat is beperkt omdat het gebruik is overschreden.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

### <a name="user-errors"></a>Gebruikersfouten

De volgende twee soorten fouten worden geclassificeerd als gebruikersfouten:

1. Client-side fouten (In HTTP dat zou zijn 400 fouten).
2. Fouten die optreden tijdens het verwerken van berichten, zoals [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Berichtstatistieken

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Binnenkomende berichten|Het aantal gebeurtenissen of berichten dat over een bepaalde periode naar Service Bus is verzonden.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Uitgaande berichten|Het aantal gebeurtenissen of berichten dat servicebus heeft ontvangen over een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
| Berichten| Aantal berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Dimensie: EntityName |
| ActiveMessages| Aantal actieve berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Dimensie: EntityName |
| Dode-geletterde berichten| Aantal dode-geletterde berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/>Dimensie: EntityName |
| Geplande berichten| Aantal geplande berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld  <br/> Dimensie: EntityName |

> [!NOTE]
> Waarden voor de volgende statistieken zijn point-in-time-waarden. Binnenkomende berichten die direct na dat point-in-time zijn verbruikt, worden mogelijk niet weergegeven in deze statistieken. 
> - Berichten
> - Actieve berichten 
> - Dode-geletterde berichten 
> - Geplande berichten 

## <a name="connection-metrics"></a>Verbindingsstatistieken

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Actieve verbindingen|Het aantal actieve verbindingen op een naamruimte en op een entiteit.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="resource-usage-metrics"></a>Statistieken voor resourcegebruik

> [!NOTE] 
> De volgende statistieken zijn alleen beschikbaar met de **premiumlaag.** 
> 
> De belangrijkste statistieken die moeten worden gecontroleerd voor eventuele uitval voor een premium-laagnaamruimte zijn: **CPU-gebruik per naamruimte** en **geheugengrootte per naamruimte.** [Stel waarschuwingen in](../azure-monitor/platform/alerts-metric.md) voor deze statistieken met Azure Monitor.
> 
> De andere statistiek die u controleren is: **aanzet tot throttled requests**. Het moet geen probleem zijn, hoewel zolang de naamruimte blijft binnen het geheugen, CPU, en bemiddelde verbindingen grenzen. Zie [Beperking in Azure Service Bus Premium-laag](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier) voor meer informatie

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|CPU-gebruik per naamruimte|Het percentage CPU-gebruik van de naamruimte.<br/><br/> Eenheid: Percentage <br/> Aggregatietype: maximum <br/> Dimensie: EntityName|
|Geheugengroottegebruik per naamruimte|Het percentage geheugengebruik van de naamruimte.<br/><br/> Eenheid: Percentage <br/> Aggregatietype: maximum <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Afmetingen met statistieken

Azure Service Bus ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Het toevoegen van dimensies aan uw statistieken is optioneel. Als u geen dimensies toevoegt, worden metrische gegevens opgegeven op het naamruimteniveau. 

|Dimensienaam|Beschrijving|
| ------------------- | ----------------- |
|Entiteitsnaam| Service Bus ondersteunt berichtenentiteiten onder de naamruimte.|

## <a name="set-up-alerts-on-metrics"></a>Waarschuwingen instellen voor statistieken

1. Selecteer op het tabblad **Statistieken** van de pagina **Naamruimte servicebus** de optie **Waarschuwingen configureren**. 

    ![Pagina Statistieken - Menu Waarschuwingen configureren](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecteer de **optie Doel selecteren** en ga de volgende acties uitvoeren op de pagina Een resource **selecteren:** 
    1. Selecteer **Naamruimten servicebus** voor het veld **Filter op resourcetype.** 
    2. Selecteer uw abonnement voor het veld **Filteren op abonnement.**
    3. Selecteer de **naamruimte van** de servicebus in de lijst. 
    4. Selecteer **Done**. 
    
        ![Naamruimte selecteren](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecteer **Criteria toevoegen**en doe de volgende acties op de pagina **Signaallogica configureren:**
    1. Selecteer **Statistieken** voor **signaaltype**. 
    2. Selecteer een signaal. Bijvoorbeeld: **Servicefouten**. 

        ![Serverfouten selecteren](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecteer **Groter dan** voor **voorwaarde**.
    2. Selecteer **Totaal** voor **tijdaggregatie**. 
    3. Voer **5** in voor **Drempelwaarde**. 
    4. Selecteer **Done**.    

        ![Voorwaarde opgeven](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Vouw **op** de pagina Regel maken **waarschuwingsgegevens definiëren**uit en ga de volgende acties uitvoeren:
    1. Voer een **naam** in voor de waarschuwing. 
    2. Voer een **beschrijving** in voor de waarschuwing.
    3. Selecteer **de ernst** voor de waarschuwing. 

        ![Meldingsdetails](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Vouw **op** de pagina Regel maken de **actiegroep definiëren**uit, selecteer **Nieuwe actiegroep**en doe de volgende acties op de pagina **Actiegroep toevoegen**. 
    1. Voer een naam in voor de actiegroep.
    2. Voer een korte naam in voor de actiegroep. 
    3. Selecteer uw abonnement. 
    4. Selecteer een resourcegroep. 
    5. Voer voor deze walkthrough **e-mail verzenden** in voor **ACTIENAAM**.
    6. Selecteer **E-mail/sms/push/voice** voor **ACTIETYPE**. 
    7. Selecteer **Details bewerken**. 
    8. Ga op de pagina **E-mail/SMS/Push/Voice** de volgende acties uitvoeren:
        1. Selecteer **E-mail**. 
        2. Typ het **e-mailadres**. 
        3. Selecteer **OK**.

            ![Meldingsdetails](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Selecteer **OK**op de pagina **Actiegroep toevoegen** . 
1. Selecteer **op** de pagina Regel maken de optie **Waarschuwingsregel maken**. 

    ![Knop Waarschuwingsregel maken](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


