---
title: Metrische gegevens in Azure Monitor-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het gebruik van Azure monitoring voor het bewaken van Azure Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 76df9920c517e8370409bca26e5a6533c7253c1e
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314594"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metrische gegevens van Azure Event Hubs in Azure Monitor

Event Hubs metrische gegevens geven u de status van Event Hubs resources in uw Azure-abonnement. Met een uitgebreide set met metrische gegevens kunt u de algemene status van uw event hubs niet alleen op het niveau van de naam ruimte beoordelen, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van uw event hubs bewaken. Metrische gegevens kunnen ook helpen bij het oplossen van problemen met de hoofd oorzaak zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Azure Monitor biedt uniforme gebruikers interfaces voor bewaking in verschillende Azure-Services. Zie voor meer informatie [bewaking in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en het voor beeld [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op github.

## <a name="access-metrics"></a>Toegangs gegevens

Azure Monitor biedt meerdere manieren om toegang te krijgen tot metrische gegevens. U hebt toegang tot metrische gegevens via de [Azure Portal](https://portal.azure.com), of u kunt gebruikmaken van de Azure monitor API'S (rest en .net) en analyse oplossingen, zoals Log Analytics en Event hubs. Zie [bewaking van gegevens die zijn verzameld door Azure monitor](../azure-monitor/platform/data-platform.md)voor meer informatie.

Metrische gegevens zijn standaard ingeschakeld, en u kunt toegang krijgen tot de meest recente 30 dagen. Als u gegevens gedurende een langere periode wilt behouden, kunt u metrische gegevens archiveren naar een Azure Storage-account. Dit wordt geconfigureerd in [Diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in azure monitor.


## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens gedurende een bepaalde periode bewaken in de [Azure Portal](https://portal.azure.com). In het volgende voor beeld ziet u hoe u geslaagde aanvragen en inkomende aanvragen kunt weer geven op account niveau:

![Geslaagde metrische gegevens weer geven][1]

U kunt ook rechtstreeks toegang krijgen tot metrische gegevens via de naam ruimte. Hiertoe selecteert u uw naam ruimte en klikt u op **metrische gegevens**. Als u de metrische gegevens wilt weer geven die zijn gefilterd op het bereik van de Event Hub, selecteert u de Event Hub en klikt u vervolgens op **metrische gegevens**.

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde, zoals weer gegeven in het volgende voor beeld:

![Filteren met dimensie waarde][2]

## <a name="billing"></a>Billing

Het gebruik van metrische gegevens in Azure Monitor is momenteel gratis. Als u echter aanvullende oplossingen gebruikt waarmee metrische gegevens worden opgenomen, worden deze oplossingen mogelijk gefactureerd. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. U wordt ook gefactureerd door Azure als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

Met de volgende metrische gegevens krijgt u een overzicht van de status van uw service. 

> [!NOTE]
> Er worden verschillende metrische gegevens overgezet omdat deze onder een andere naam worden geplaatst. Hiervoor moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutel woord ' afgeschaft ' worden niet verder ondersteund.

Alle waarden voor metrische gegevens worden elke minuut naar Azure Monitor verzonden. De granulatie tijd definieert het tijds interval waarvoor metrische waarden worden weer gegeven. Het ondersteunde tijds interval voor alle Event Hubs meet waarden is 1 minuut.

## <a name="request-metrics"></a>Metrische gegevens aanvragen

Telt het aantal gegevens-en beheer bewerkings aanvragen.

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
| Binnenkomende aanvragen  | Het aantal aanvragen voor de Azure Event Hubs-service gedurende een opgegeven periode. <br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName |
| Geslaagde aanvragen    | Het aantal geslaagde aanvragen voor de Azure Event Hubs-service gedurende een opgegeven periode. <br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName |
| Server fouten  | Het aantal niet-verwerkte aanvragen vanwege een fout in de Azure Event Hubs-service gedurende een opgegeven periode. <br/><br/>Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName |
|Gebruikers fouten |Het aantal aanvragen dat niet is verwerkt wegens gebruikers fouten gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Quota overschreden fouten |Het aantal aanvragen heeft het beschik bare quotum overschreden. Raadpleeg [dit artikel](event-hubs-quotas.md) voor meer informatie over Event hubs quota's.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="throughput-metrics"></a>Metrische gegevens over door Voer

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|Vertraagde aanvragen |Het aantal aanvragen dat is beperkt omdat het gebruik van de doorvoereenheid is overschreden.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="message-metrics"></a>Metrische gegevens van bericht

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|Inkomende berichten |Het aantal gebeurtenissen of berichten dat is verzonden naar Event Hubs gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Uitgaande berichten |Het aantal gebeurtenissen of berichten dat is opgehaald uit Event Hubs gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Binnenkomende bytes |Het aantal bytes dat is verzonden naar de Azure Event Hubs-service gedurende een opgegeven periode.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Uitgaande bytes |Het aantal bytes dat is opgehaald uit de Azure Event Hubs-service gedurende een opgegeven periode.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="connection-metrics"></a>Verbindings gegevens

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|ActiveConnections |Het aantal actieve verbindingen op een naam ruimte en op een entiteit.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Geopende verbindingen |Het aantal geopende verbindingen.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Gesloten verbindingen |Het aantal gesloten verbindingen.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="event-hubs-capture-metrics"></a>Metrische gegevens van vastleggen Event Hubs

U kunt de metrische gegevens van Event Hubs vastleggen controleren wanneer u de functie vastleggen inschakelt voor uw event hubs. De volgende metrische gegevens beschrijven wat u kunt bewaken met vastleggen ingeschakeld.

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|Achterstand vastleggen |Het aantal bytes dat nog moet worden vastgelegd voor de gekozen bestemming.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Vastgelegde berichten |Het aantal berichten of gebeurtenissen dat gedurende een opgegeven periode is vastgelegd voor de gekozen bestemming.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|Vastgelegde bytes |Het aantal bytes dat is vastgelegd voor de gekozen bestemming gedurende een opgegeven periode.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Metrische dimensies

Azure Event Hubs ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Het toevoegen van dimensies aan uw metrische gegevens is optioneel. Als u geen dimensies toevoegt, worden metrische gegevens opgegeven op het niveau van de naam ruimte. 

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|EntityName| Event Hubs ondersteunt de Event Hub entiteiten in de naam ruimte.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integratie met SIEM-hulpprogram ma's Azure Monitor
Door uw bewakings gegevens (activiteiten logboeken, Diagnostische logboeken, enzovoort) te routeren naar een Event Hub met Azure Monitor kunt u gemakkelijk integreren met de hulpprogram ma's van Security Information and Event Management (SIEM). Raadpleeg de volgende artikelen/blog berichten voor meer informatie:

- [Azure-bewakings gegevens streamen naar een Event Hub voor gebruik door een extern hulp programma](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Inleiding tot Azure Log Integration](../security/fundamentals/azure-log-integration-overview.md)
- [Azure Monitor gebruiken om te integreren met SIEM-hulpprogramma's](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

In het scenario waarin een SIEM-hulp programma logboek gegevens van een Event Hub verbruikt, als u geen inkomende berichten ziet of als u inkomende berichten ziet, maar geen uitgaande berichten in de grafiek metrische gegevens, volgt u deze stappen:

- Als er **geen inkomende berichten**zijn, betekent dit dat de Azure Monitor-service geen controle-en Diagnostische logboeken naar de Event hub verplaatst. In dit scenario opent u een ondersteunings ticket met het Azure Monitor team. 
- Als er inkomende berichten zijn, maar **geen uitgaande berichten**, betekent dit dat de Siem-toepassing de berichten niet leest. Neem contact op met de SIEM-provider om te bepalen of de configuratie van de Event Hub deze toepassingen juist is.


## <a name="next-steps"></a>Volgende stappen

* Zie het [overzicht van Azure-bewaking](../monitoring-and-diagnostics/monitoring-overview.md).
* [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -voor beeld op github. 

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met een Event Hubs-zelfstudie
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



