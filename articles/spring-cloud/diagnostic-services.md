---
title: Logboeken en statistieken analyseren in Azure Spring Cloud | Microsoft Documenten
description: Meer informatie over het analyseren van diagnostische gegevens in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: 544de1b4ac46a58d533f71a46266807a3b93820a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920039"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Logboeken en statistieken analyseren met diagnostische instellingen

Met behulp van de diagnostische functionaliteit van Azure Spring Cloud u logboeken en statistieken analyseren met een van de volgende services:

* Gebruik Azure Log Analytics, waarbij de gegevens naar Azure Storage worden geschreven. Er is een vertraging bij het exporteren van logboeken naar Log Analytics.
* Sla logboeken op in een opslagaccount voor controle of handmatige inspectie. U de bewaartijd (in dagen) opgeven.
* Stream logboeken naar uw gebeurtenishub voor opname door een service van derden of aangepaste analyseoplossing.

Kies de logboekcategorie en metrische categorie die u wilt controleren.

## <a name="logs"></a>Logboeken

|Logboek | Beschrijving |
|----|----|
| **ApplicationConsole** | Consolelogboek van alle klanttoepassingen. | 
| **Systeemlogboeken** | Momenteel worden alleen [Spring Cloud Config](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) Server-logboeken in deze categorie. |

## <a name="metrics"></a>Metrische gegevens

Zie Spring Cloud Metrics [voor](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options)een volledige lijst met statistieken.

Om aan de slag te gaan, u een van deze services inschakelen om de gegevens te ontvangen. Zie [Aan de slag met Logboekanalyse in Azure Monitor](../azure-monitor/log-query/get-started-portal.md)voor meer informatie over het configureren van Logboekanalyse. 

## <a name="configure-diagnostics-settings"></a>Diagnostische instellingen configureren

1. Ga in de Azure-portal naar uw Azure Spring Cloud-exemplaar.
1. Selecteer de optie **Diagnostische instellingen** en selecteer Vervolgens de instelling Diagnostische **gegevens toevoegen**.
1. Voer een naam in voor de instelling en kies vervolgens waar u de logboeken wilt verzenden. U een combinatie van de volgende drie opties selecteren:
    * **Archiveren naar een opslagaccount**
    * **Streamen naar een gebeurtenishub**
    * **Verzenden naar Log Analytics**

1. Kies welke logboekcategorie en metrische categorie u wilt controleren en geef vervolgens de bewaartijd (in dagen) op. De bewaartijd is alleen van toepassing op het opslagaccount.
1. Selecteer **Opslaan**.

> [!NOTE]
> Er kan een verschil van maximaal 15 minuten zijn tussen het moment waarop logboeken of statistieken worden uitgestoten en wanneer ze worden weergegeven in uw opslagaccount, uw gebeurtenishub of Logboekanalyse.

## <a name="view-the-logs-and-metrics"></a>Bekijk de logboeken en statistieken
Er zijn verschillende methoden om logboeken en statistieken te bekijken zoals beschreven onder de volgende koppen.

### <a name="use-logs-blade"></a>Logs-blad gebruiken

1. Ga in de Azure-portal naar uw Azure Spring Cloud-exemplaar.
1. Als u het deelvenster **Logboekzoeken wilt openen,** selecteert u **Logboeken**.
1. In het zoekvak **Logboek**
   * Als u logboeken wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Als u statistieken wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Als u het zoekresultaat wilt weergeven, selecteert u **Uitvoeren**.

### <a name="use-log-analytics"></a>Log Analytics gebruiken

1. Selecteer **Logboekanalyse**in de Azure-portal in het linkerdeelvenster .
1. Selecteer de werkruimte Log Analytics die u hebt gekozen toen u uw diagnose-instellingen hebt toegevoegd.
1. Als u het deelvenster **Logboekzoeken wilt openen,** selecteert u **Logboeken**.
1. Zoekvak **Log**
   * als u logboeken wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Als u statistieken wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Als u het zoekresultaat wilt weergeven, selecteert u **Uitvoeren**.
1. U de logboeken van de specifieke toepassing of instantie doorzoeken door een filtervoorwaarde in te stellen:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`is hoofdlettergevoelig, `=~` maar is dat niet.

Zie [Azure Monitor-logboekquery's](../azure-monitor/log-query/query-language.md)voor meer informatie over de querytaal die wordt gebruikt in Log Analytics.

### <a name="use-your-storage-account"></a>Uw opslagaccount gebruiken 

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Opslagaccounts**.

1. Selecteer het opslagaccount dat u hebt gekozen toen u uw diagnose-instellingen hebt toegevoegd.
1. Als u het deelvenster **Kblobcontainer** wilt openen, selecteert u **Klodders**.
1. Als u toepassingslogboeken wilt controleren, zoekt u naar een container die **insights-logs-applicationconsole**wordt genoemd.
1. Als u toepassingsstatistieken wilt bekijken, zoekt u naar een container met de naam **insights-metrics-pt1m.**

Zie [Diagnostische gegevens opslaan en weergeven in Azure Storage](../storage/common/storage-introduction.md)voor meer informatie over het verzenden van diagnostische gegevens naar een opslagaccount.

### <a name="use-your-event-hub"></a>Uw gebeurtenishub gebruiken

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Gebeurtenishubs**.

1. Zoek naar en selecteer de gebeurtenishub die u hebt gekozen toen u uw diagnose-instellingen hebt toegevoegd.
1. Als u het deelvenster **Lijst met gebeurtenishubs wilt openen,** selecteert u **Gebeurtenishubs**.
1. Als u toepassingslogboeken wilt controleren, zoekt u naar een gebeurtenishub genaamd **insights-logs-applicationconsole**.
1. Als u toepassingsstatistieken wilt bekijken, zoekt u naar een gebeurtenishub genaamd **insights-metrics-pt1m.**

Zie [Azure Diagnostics-gegevens streamen in het hot path met Gebeurtenishubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)voor meer informatie over het verzenden van diagnostische gegevens naar een gebeurtenishub.

## <a name="analyze-the-logs"></a>De logboeken analyseren

Azure Log Analytics wordt uitgevoerd met een Kusto-engine, zodat u uw logboeken opvragen voor analyse. Voor een snelle inleiding tot het opvragen van logboeken met Kusto, controleert u de [zelfstudie Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Toepassingslogboeken bieden essentiële informatie en uitgebreide logboeken over de status, prestaties en meer van uw toepassing. In de volgende secties zijn enkele eenvoudige query's om u te helpen de huidige en vroegere statussen van uw toepassing te begrijpen.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Toepassingslogboeken weergeven vanuit Azure Spring Cloud

Als u een lijst met toepassingslogboeken uit Azure Spring Cloud wilt controleren, gesorteerd op tijd met de meest recente logboeken die als eerste worden weergegeven, voert u de volgende query uit:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Logboeken items weergeven met fouten of uitzonderingen

Voer de volgende query uit om ongesorteerde logboekvermeldingen met een fout of uitzondering te controleren:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Gebruik deze query om fouten te vinden of wijzig de querytermen om specifieke foutcodes of uitzonderingen te vinden. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Het aantal fouten en uitzonderingen weergeven dat uw toepassing in het afgelopen uur heeft gerapporteerd

Voer de volgende query uit om een cirkeldiagram te maken met het aantal fouten en uitzonderingen dat in het afgelopen uur door uw toepassing is geregistreerd:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Meer informatie over het opvragen van toepassingslogboeken

Azure Monitor biedt uitgebreide ondersteuning voor het opvragen van toepassingslogboeken met behulp van Log Analytics. Zie Aan de slag [met logboekquery's in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over deze service. Zie [Overzicht van logboekquery's in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over het bouwen van query's om uw toepassingslogboeken te analyseren.
