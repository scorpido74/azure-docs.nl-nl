---
title: Azure-platform logboeken streamen naar een Event Hub
description: Informatie over het streamen van Azure-bron logboeken naar een Event Hub voor het verzenden van gegevens naar externe systemen, zoals Siem's van derden en andere log Analytics-oplossingen.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 00dcc1c1a1d823ab0f2497e47641916d391ee37b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750356"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Azure-platform logboeken streamen naar Azure Event Hubs
[Platform logboeken](platform-logs-overview.md) in azure, inclusief Azure-activiteiten logboek en resource logboeken, bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn.  In dit artikel worden streaming-platform logboeken voor Event hubs beschreven om gegevens te verzenden naar externe systemen, zoals Siem's van derden en andere log Analytics-oplossingen.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Wat u kunt doen met platform logboeken die worden verzonden naar een Event Hub
Het streamen van platform Logboeken in azure naar Event hubs om de volgende functionaliteit te bieden:

* **Stroom logboeken naar logboek registratie van derden en telemetrie-systemen** : stream al uw platform logboeken naar één event hub om logboek gegevens naar een Siem of log Analytics-hulp programma van derden te verzenden.
  
* **Bouw een aangepast telemetrie-en logboek registratie platform** : met de uiterst schaal bare functie voor publiceren en abonneren van Event hubs kunt u platform logboeken flexibel vastleggen in een aangepast teletry-platform. Zie [het ontwerp en de grootte van een telemetrie-platform op wereld wijd schalen op Azure Event hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.

* **Bekijk de service status door gegevens te streamen naar Power bi** : gebruik Event Hubs, Stream Analytics en Power bi om uw diagnostische gegevens te transformeren naar bijna realtime inzichten op uw Azure-Services. Zie [Stream Analytics en Power BI: een real-time analyse dashboard voor het streamen van gegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) voor meer informatie over deze oplossing.

    De volgende SQL-code is een voor beeld van een Stream Analytics query die u kunt gebruiken voor het parseren van alle logboek gegevens in een Power BI tabel:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Vereisten
U moet [een event hub maken](../../event-hubs/event-hubs-create.md) als u er nog geen hebt. Als u al een diagnostische instelling hebt met behulp van deze Event Hubs naam ruimte, wordt dat Event Hub opnieuw gebruikt.

Het beleid voor gedeelde toegang voor de naam ruimte definieert de machtigingen die het streaming-mechanisme heeft. Streaming naar Event Hubs vereist machtigingen voor beheren, verzenden en Luis teren. U kunt beleid voor gedeelde toegang maken of wijzigen in de Azure Portal op het tabblad configureren voor uw Event Hubs naam ruimte.

Als u de diagnostische instelling wilt bijwerken zodat deze streaming bevat, moet u de machtiging ListKey hebben voor die Event Hubs autorisatie regel. De naam ruimte van de Event Hubs hoeft zich niet te bevinden in hetzelfde abonnement als het abonnement dat Logboeken verzendt, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang heeft tot beide abonnementen en beide abonnementen zich in dezelfde AAD-Tenant bevinden.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
U kunt platform logboeken naar een Event Hub en andere bestemmingen verzenden door een diagnostische instelling voor een Azure-resource te maken. Zie [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md) voor meer informatie.

## <a name="collect-data-from-compute-resources"></a>Gegevens verzamelen van Compute-resources
Met Diagnostische instellingen worden bron logboeken verzameld voor Azure-reken resources, zoals elke andere resource, maar niet het gast besturingssysteem of de werk belastingen. Als u deze gegevens wilt verzamelen, moet u de [log Analytics-agent](log-analytics-agent.md)installeren. 


## <a name="consuming-log-data-from-event-hubs"></a>Logboek gegevens van Event hubs gebruiken
Platform logboeken van Event hubs worden gebruikt in JSON-indeling met de elementen in de volgende tabel.

| Naam van element | Beschrijving |
| --- | --- |
| records |Een matrix van alle logboek gebeurtenissen in deze nettolading. |
| tijd |Tijdstip waarop de gebeurtenis heeft plaatsgevonden. |
| category |Logboek categorie voor deze gebeurtenis. |
| resourceId |De resource-ID van de resource die deze gebeurtenis heeft gegenereerd. |
| operationName |Naam van de bewerking. |
| level |Optioneel. Hiermee wordt het logboek gebeurtenis niveau aangegeven. |
| properties |De eigenschappen van de gebeurtenis. Deze verschillen voor elke Azure-service, zoals beschreven [ ]()in. |


Hieronder ziet u voor beelden van uitvoer gegevens van Event Hubs voor een bron logboek:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over resource logboeken](platform-logs-overview.md).
* [Maak een diagnostische instelling voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md).
* [Stream Azure Active Directory-logboeken met Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Aan de slag met Event hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

