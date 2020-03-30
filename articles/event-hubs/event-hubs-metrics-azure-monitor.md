---
title: Statistieken in Azure Monitor - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u informatie over het gebruik van Azure Monitoring om Azure Event Hubs te controleren
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162647"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metrische gegevens van Azure Event Hubs in Azure Monitor

Met statistieken over gebeurtenishubs u de status van de bronnen van Event Hubs in uw Azure-abonnement weergeven. Met een uitgebreide set metrische gegevens u de algehele status van uw gebeurtenishubs niet alleen op naamruimteniveau, maar ook op entiteitsniveau beoordelen. Deze statistieken kunnen belangrijk zijn omdat ze u helpen om de status van uw gebeurtenishubs te controleren. Statistieken kunnen ook helpen bij het oplossen van problemen met de oorzaak van de oorzaak zonder dat u contact hoeft op te nemen met Azure-ondersteuning.

Azure Monitor biedt uniforme gebruikersinterfaces voor bewaking in verschillende Azure-services. Zie [Controle in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) en de Azure [Monitor-statistieken ophalen met het voorbeeld .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op GitHub voor meer informatie.

## <a name="access-metrics"></a>Toegangsstatistieken

Azure Monitor biedt meerdere manieren om toegang te krijgen tot statistieken. U via de [Azure-portal](https://portal.azure.com)toegang krijgen tot statistieken of de Azure Monitor API's (REST en .NET) en analyseoplossingen zoals Log Analytics en Event Hubs gebruiken. Zie [Gegevens controleren die zijn verzameld door Azure Monitor](../azure-monitor/platform/data-platform.md)voor meer informatie.

Statistieken zijn standaard ingeschakeld en u hebt toegang tot de meest recente 30 dagen aan gegevens. Als u gegevens voor een langere periode moet bewaren, u metrische gegevens archiveren naar een Azure Storage-account. Dit is geconfigureerd in [diagnostische instellingen](../azure-monitor/platform/diagnostic-settings.md) in Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Toegang tot statistieken in de portal

U statistieken in de loop van de tijd controleren in de [Azure-portal.](https://portal.azure.com) In het volgende voorbeeld ziet u hoe u succesvolle aanvragen en binnenkomende aanvragen op accountniveau weergeven:

![Bekijk succesvolle statistieken][1]

U ook rechtstreeks via de naamruimte statistieken openen. Selecteer hiervoor uw naamruimte en klik op **Statistieken**. Als u statistieken wilt weergeven die zijn gefilterd op het bereik van de gebeurtenishub, selecteert u de gebeurtenishub en klikt u op **Statistieken**.

Voor statistieken die dimensies ondersteunen, moet u filteren met de gewenste dimensiewaarde zoals weergegeven in het volgende voorbeeld:

![Filter met dimensiewaarde][2]

## <a name="billing"></a>Billing

Het gebruik van metrische gegevens in Azure Monitor is momenteel gratis. Als u echter aanvullende oplossingen gebruikt die metrische gegevens opnemen, wordt u mogelijk gefactureerd door deze oplossingen. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. U wordt ook gefactureerd door Azure als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

De volgende statistieken geven u een overzicht van de status van uw service. 

> [!NOTE]
> We zijn verschillende statistieken aan het deprecating omdat ze onder een andere naam worden verplaatst. Hiervoor u uw referenties bijwerken. Statistieken die zijn gemarkeerd met het trefwoord 'afgeschaft' worden in de toekomst niet ondersteund.

Alle metrische waarden worden elke minuut naar Azure Monitor verzonden. De tijdgranulariteit definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. Het ondersteunde tijdsinterval voor alle statistieken van Event Hubs is 1 minuut.

## <a name="request-metrics"></a>Statistieken aanvragen

Telt het aantal aanvragen voor gegevens- en beheerbewerkingen.

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Binnenkomende aanvragen  | Het aantal aanvragen dat gedurende een bepaalde periode naar de Azure Event Hubs-service is gedaan. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName |
| Succesvolle aanvragen    | Het aantal geslaagde aanvragen voor de Azure Event Hubs-service gedurende een bepaalde periode. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName |
| Serverfouten  | Het aantal aanvragen dat niet is verwerkt als gevolg van een fout in de Azure Event Hubs-service gedurende een bepaalde periode. <br/><br/>Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName |
|Gebruikersfouten |Het aantal aanvragen dat niet is verwerkt vanwege gebruikersfouten gedurende een bepaalde periode.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Overschrijdingsfouten quotum |Het aantal aanvragen overschreed het beschikbare quotum. Zie [dit artikel](event-hubs-quotas.md) voor meer informatie over quota voor gebeurtenishubs.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="throughput-metrics"></a>Doorvoerstatistieken

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Gewurgde aanvragen |Het aantal aanvragen dat is beperkt omdat het gebruik van de doorvoereenheid is overschreden.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="message-metrics"></a>Berichtstatistieken

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Binnenkomende berichten |Het aantal gebeurtenissen of berichten dat gedurende een bepaalde periode naar gebeurtenishubs wordt verzonden.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Uitgaande berichten |Het aantal gebeurtenissen of berichten dat gedurende een bepaalde periode is opgehaald uit gebeurtenishubs.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Binnenkomende bytes |Het aantal bytes dat gedurende een bepaalde periode naar de Azure Event Hubs-service is verzonden.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Uitgaande bytes |Het aantal bytes dat gedurende een bepaalde periode is opgehaald uit de Azure Event Hubs-service.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="connection-metrics"></a>Verbindingsstatistieken

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Actieve verbindingen |Het aantal actieve verbindingen op een naamruimte en op een entiteit.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Geopende verbindingen |Het aantal open verbindingen.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Verbindingen gesloten |Het aantal gesloten verbindingen.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="event-hubs-capture-metrics"></a>Statistieken voor het vastleggen van gebeurtenishubs

U statistieken voor het vastleggen van gebeurtenishubs controleren wanneer u de opnamefunctie voor uw gebeurtenishubs inschakelt. De volgende statistieken beschrijven wat u controleren met Vastleggen ingeschakeld.

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Achterstand vastleggen |Het aantal bytes dat nog moet worden vastgelegd naar de gekozen bestemming.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Vastgelegde berichten |Het aantal berichten of gebeurtenissen dat gedurende een bepaalde periode naar de gekozen bestemming wordt vastgelegd.<br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|
|Vastgelegde bytes |Het aantal bytes dat gedurende een bepaalde periode naar de gekozen bestemming wordt vastgelegd.<br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Afmetingen met statistieken

Azure Event Hubs ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Het toevoegen van dimensies aan uw statistieken is optioneel. Als u geen dimensies toevoegt, worden metrische gegevens opgegeven op het naamruimteniveau. 

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
|Entiteitsnaam| Event Hubs ondersteunt de entiteiten van de gebeurtenishub onder de naamruimte.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor-integratie met SIEM-hulpprogramma's
Als u uw bewakingsgegevens (activiteitslogboeken, diagnostische logboeken, enz.) routeren naar een gebeurtenishub met Azure Monitor, zodat u eenvoudig integreren met SIEM-hulpprogramma's (Security Information and Event Management). Zie de volgende artikelen/blogberichten voor meer informatie:

- [Azure-bewakingsgegevens streamen naar een gebeurtenishub voor verbruik door een extern hulpprogramma](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Inleiding tot Azure-logboekintegratie](../security/fundamentals/azure-log-integration-overview.md)
- [Azure Monitor gebruiken om te integreren met SIEM-hulpprogramma's](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

In het scenario waarin een SIEM-tool loggegevens van een gebeurtenishub verbruikt, als u geen binnenkomende berichten ziet of als u binnenkomende berichten ziet, maar geen uitgaande berichten in de grafiek met statistieken, voert u de volgende stappen uit:

- Als er **geen binnenkomende berichten**zijn, betekent dit dat de Azure Monitor-service geen logboeken voor controle/diagnose naar de gebeurtenishub verplaatst. Open in dit scenario een ondersteuningsticket met het Azure Monitor-team. 
- als er binnenkomende berichten zijn, maar **geen uitgaande berichten,** betekent dit dat de SIEM-toepassing de berichten niet leest. Neem contact op met de SIEM-provider om te bepalen of de configuratie van de gebeurtenishub die toepassingen correct is.


## <a name="next-steps"></a>Volgende stappen

* Zie het [overzicht Azure Monitoring](../monitoring-and-diagnostics/monitoring-overview.md).
* [Azure Monitor-statistieken ophalen met .NET-voorbeeld](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op GitHub. 

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met een Event Hubs-zelfstudie
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeeldtoepassingen die gebruikmaken van Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



