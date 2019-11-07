---
title: Logboeken en metrische gegevens analyseren in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het analyseren van diagnostische gegevens in azure lente Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: ebe438bd2dc5b4921ce733001f3c9df19bc592fe
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607865"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Logboeken en metrische gegevens analyseren met Diagnostische instellingen

Door de diagnostische functionaliteit van Azure lente-Cloud te gebruiken, kunt u Logboeken en metrische gegevens analyseren met een van de volgende services:

* Gebruik Azure Log Analytics, waarbij de gegevens onmiddellijk worden geschreven zonder eerst naar de opslag te hoeven schrijven.
* Sla ze op in een opslag account voor controle of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven.
* U kunt ze streamen naar uw Event Hub voor opname door een service van derden of een aangepaste analyse oplossing.

Als u aan de slag wilt gaan, schakelt u een van deze services in om de gegevens te ontvangen. Raadpleeg aan de [slag met log Analytics in azure monitor](../azure-monitor/log-query/get-started-portal.md)voor meer informatie over het configureren van log Analytics. 

## <a name="configure-diagnostics-settings"></a>Diagnostische instellingen configureren

1. Ga in het Azure Portal naar uw Azure veer Cloud-exemplaar.
1. Selecteer de optie **Diagnostische instellingen** en selecteer **instelling diagnostische gegevens toevoegen**.
1. Voer een naam in voor de instelling en kies vervolgens waar u de logboeken wilt verzenden. U kunt een combi natie van de volgende drie opties selecteren:
    * **Archiveren naar een opslag account**
    * **Streamen naar een Event Hub**
    * **Verzenden naar Log Analytics**

1. Kies welke logboek categorie en metrische categorie u wilt bewaken en geef vervolgens de Bewaar tijd op (in dagen). De Bewaar tijd is alleen van toepassing op het opslag account.
1. Selecteer **Opslaan**.

> [!NOTE]
> Er kan een onderbreking van Maxi maal 15 minuten zijn tussen het moment dat Logboeken of metrische gegevens worden verzonden en wanneer ze worden weer gegeven in uw opslag account, uw Event Hub of Log Analytics.

## <a name="view-the-logs"></a>De logboeken weer geven

### <a name="use-log-analytics"></a>Log Analytics gebruiken

1. Selecteer **log Analytics**in het linkerdeel venster van de Azure Portal.
1. Selecteer de Log Analytics werk ruimte die u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Selecteer **Logboeken**om het deel venster **Zoeken in Logboeken** te openen.
1. Voer in het vak Zoeken in **logboek** een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Als u het Zoek resultaat wilt weer geven, selecteert u **uitvoeren**.
1. U kunt de logboeken van de specifieke toepassing of het exemplaar doorzoeken door een filter voorwaarde in te stellen:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Zie [Azure monitor-logboek query's](../azure-monitor/log-query/query-language.md)voor meer informatie over de query taal die wordt gebruikt in log Analytics.

### <a name="use-your-storage-account"></a>Uw opslag account gebruiken 

1. Selecteer **opslag accounts**in de Azure Portal in het linkerdeel venster.

1. Selecteer het opslag account dat u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Als u het deel venster **BLOB container** wilt openen, selecteert u **blobs**.
1. Als u toepassings logboeken wilt bekijken, zoekt u naar een container met de naam **Insights-logs-applicationconsole**.
1. Als u metrische gegevens van de toepassing wilt controleren, zoekt u naar een container met de naam **Insights-metrische gegevens-pt1m**.

Zie voor meer informatie over het verzenden van diagnostische gegevens naar een opslag account [Diagnostische gegevens opslaan en weer geven in azure Storage](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Uw Event Hub gebruiken

1. Selecteer **Event hubs**in het linkerdeel venster van de Azure Portal.

1. Zoek en selecteer de Event Hub die u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Selecteer **Event hubs**om het deel venster **Event hub-lijst** te openen.
1. Als u toepassings logboeken wilt bekijken, zoekt u naar een Event Hub genaamd **Insights-logs-applicationconsole**.
1. Als u metrische gegevens van de toepassing wilt bekijken, zoekt u naar een Event Hub met de naam **Insights-metrische gegevens-pt1m**.

Voor meer informatie over het verzenden van diagnostische gegevens naar een Event Hub raadpleegt u [Azure Diagnostics gegevens streamen in het warme pad met behulp van Event hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>De logboeken analyseren

Azure Log Analytics biedt Kusto, zodat u een query kunt uitvoeren op uw logboeken voor analyse. Raadpleeg de [log Analytics zelf studie](../azure-monitor/log-query/get-started-portal.md)voor een snelle inleiding in het uitvoeren van query's op Logboeken met behulp van Kusto.

Toepassings logboeken bieden essentiële informatie over de status, prestaties en meer van de toepassing. In de volgende secties vindt u enkele eenvoudige query's waarmee u inzicht krijgt in de huidige en eerdere status van uw toepassing.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Toepassings logboeken van Azure lente-Cloud weer geven

Voer de volgende query uit om een lijst met toepassings logboeken te bekijken vanuit Azure lente-Cloud, gesorteerd op tijd met de meest recente logboeken die het eerst worden weer gegeven:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Logboek vermeldingen weer geven die fouten of uitzonde ringen bevatten

Voer de volgende query uit om ongesorteerde logboek vermeldingen te controleren waarin een fout of uitzonde ring wordt vermeld:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Gebruik deze query om fouten te vinden of wijzig de query termen om specifieke fout codes of uitzonde ringen op te sporen. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Het aantal fouten en uitzonde ringen weer geven dat in het afgelopen uur is gerapporteerd door uw toepassing

Voer de volgende query uit om een cirkel diagram te maken waarin het aantal fouten en uitzonde ringen wordt weer gegeven dat door uw toepassing in het afgelopen uur is geregistreerd:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Meer informatie over het uitvoeren van query's in toepassings logboeken

Azure Monitor biedt uitgebreide ondersteuning voor het uitvoeren van query's in toepassings logboeken met behulp van Log Analytics. Zie aan de [slag met logboek query's in azure monitor](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over deze service. Zie [overzicht van logboek query's in azure monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over het bouwen van query's voor het analyseren van uw toepassings Logboeken.
