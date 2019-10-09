---
title: Logboeken en metrische gegevens analyseren in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het analyseren van diagnostische gegevens in azure lente Cloud
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038884"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Logboeken en metrische gegevens analyseren met Diagnostische instellingen

Met de diagnostische functionaliteit van Azure lente Cloud kunt u Logboeken en metrische gegevens analyseren met behulp van een van de volgende services:

* Analyseer ze met Azure Log Analytics, waarbij de gegevens onmiddellijk naar Azure worden geschreven Log Analytics zonder dat u eerst de gegevens naar de opslag hoeft te schrijven.
* Sla ze op in een opslag account voor controle of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven.
* Stream ze naar Event Hubs voor opname door een service van derden of een aangepaste analyse oplossing.

Als u aan de slag wilt gaan, moet u een van deze services inschakelen voor het ontvangen van de gegevens.  Raadpleeg [deze zelf studie](../azure-monitor/log-query/get-started-portal.md)voor meer informatie over het configureren van log Analytics.  

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

1. Ga naar uw Azure veer Cloud-exemplaar in het Azure Portal.
1. Selecteer de menu optie **Diagnostische instellingen** .
1. Selecteer de knop **Diagnostische instelling toevoegen** .
1. Voer een naam in voor de instelling en kies waar u de logboeken wilt verzenden. U kunt een combi natie van de volgende drie opties selecteren:
    * Archiveren naar een opslagaccount
    * Streamen naar een Event Hub
    * Verzenden naar Log Analytics

1. Kies de categorie van de logboek categorie en de metrische gegevens die u wilt bewaken en geef de Bewaar tijd op (in dagen). De retentie tijd is alleen van toepassing op het opslag account.
1. Selecteer **Opslaan** om de instelling toe te passen.

> [!NOTE]
> Het kan tot vijf tien minuten duren voordat Logboeken of metrische gegevens worden verzonden en wanneer ze worden weer gegeven in een opslag account/Event hub/Log Analytics.

## <a name="viewing-logs"></a>Logboeken weergeven

### <a name="using-log-analytics"></a>Log Analytics gebruiken

1. Selecteer in het Azure Portal Log Analytics in het navigatie menu aan de linkerkant.
1. Selecteer de Log Analytics werk ruimte die u hebt gekozen bij het toevoegen van diagnostische instellingen.
1. Selecteer `Logs` om de Blade zoeken in Logboeken te openen.
1. Geef een eenvoudige query op in het zoekvak voor Logboeken.  Bijvoorbeeld:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Selecteer `Run` om het Zoek resultaat weer te geven.
1. U kunt de logboeken van de specifieke toepassing of het exemplaar doorzoeken door een filter voorwaarde in te stellen:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Meer informatie over de query taal die wordt gebruikt in Log Analytics [in dit artikel](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>Logboeken en metrische gegevens in het opslag account gebruiken

1. Selecteer in het Azure Portal opslag accounts in het navigatie menu aan de linkerkant.
1. Selecteer het opslag account dat u hebt gekozen bij het toevoegen van diagnostische instellingen.
1. Selecteer `Blobs`-vermelding om de Blade BLOB-container te openen.
1. Zoek een container met de naam `insights-logs-applicationconsole` om toepassings logboeken te controleren.
1. Zoek een container met de naam `insights-metrics-pt1m` om de metrische gegevens van de toepassing te controleren.

[Meer informatie over het verzenden van diagnostische gegevens naar een opslag account.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Event Hubs gebruiken

1. Selecteer in het Azure Portal Event Hubs in het navigatie menu aan de linkerkant.
1. Zoek en selecteer de Event Hubs die u hebt gekozen bij het toevoegen van diagnostische instellingen.
1. Selecteer `Event Hubs` om de Blade Event hub-lijst te openen.
1. Zoek een Event Hub met de naam `insights-logs-applicationconsole` om toepassings logboeken te controleren.
1. Zoek een Event Hub met de naam `insights-metrics-pt1m` om metrische gegevens van de toepassing te controleren.

Meer [informatie over het verzenden van diagnostische gegevens naar een event hub.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyzing-logs"></a>Logboeken analyseren

Azure Log Analytics biedt Kusto zodat u een query kunt uitvoeren op uw logboeken voor analyse.  Raadpleeg de [log Analytics zelf studie](../azure-monitor/log-query/get-started-portal.md) voor een snelle inleiding tot het uitvoeren van query's op Logboeken met behulp van Kusto.

Toepassings logboeken bieden essentiële informatie over de status, prestaties en meer van de toepassing.  Hieronder vindt u enkele eenvoudige query's waarmee u inzicht krijgt in de huidige en eerdere status van uw toepassing.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Toepassings logboeken van Azure lente-Cloud weer geven

Als u een lijst met toepassings logboeken wilt bekijken vanuit een Azure lente-Cloud, gesorteerd op tijd met de meest recente logboeken die het eerst worden weer gegeven:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Logboek vermeldingen weer geven die fouten of uitzonde ringen bevatten

Met deze query kunt u logboek vermeldingen bekijken waarin een fout of uitzonde ring wordt vermeld.  De resultaten worden niet gesorteerd.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Gebruik deze query om fouten te vinden of wijzig de query termen om specifieke fout codes of uitzonde ringen op te sporen.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Het aantal fouten en uitzonde ringen weer geven dat in het afgelopen uur is gerapporteerd door uw toepassing

Met deze query maakt u een cirkel diagram waarin het aantal fouten en uitzonde ringen wordt weer gegeven dat de toepassing in het afgelopen uur heeft geregistreerd:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Meer informatie over het uitvoeren van query's in toepassings logboeken

Azure Monitor biedt uitgebreide ondersteuning voor het uitvoeren van query's in toepassings logboeken met behulp van Log Analytics.  Raadpleeg de zelf studie over [logboek query's](../azure-monitor/log-query/get-started-queries.md) met Azure monitor voor meer informatie over deze service. Het [overzicht van logboek query's in azure monitor](../azure-monitor/log-query/log-query-overview.md) biedt meer informatie over het bouwen van query's voor het analyseren van uw toepassings Logboeken.
