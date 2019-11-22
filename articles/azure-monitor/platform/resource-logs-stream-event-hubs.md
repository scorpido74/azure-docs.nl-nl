---
title: Azure-resource logboeken streamen naar een Event Hub
description: Informatie over het streamen van Azure-bron logboeken naar een Event Hub voor het verzenden van gegevens naar externe systemen, zoals Siem's van derden en andere log Analytics-oplossingen.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 680570c5102f656b2b2d2e05f9e08f51fe892f44
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304941"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Azure-resource logboeken streamen naar Azure Event Hubs
[Resource logboeken](resource-logs-overview.md) in azure bieden uitgebreide, frequente gegevens over de interne werking van een Azure-resource. In dit artikel wordt beschreven hoe u bron logboeken streamt naar Event hubs om gegevens te verzenden naar externe systemen, zoals Siem's van derden en andere log Analytics-oplossingen.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>Wat u kunt doen met resource logboeken die worden verzonden naar een Event Hub
Stream-bron Logboeken in azure naar Event hubs om de volgende functionaliteit te bieden:

* **Stroom logboeken naar logboek registratie van derden en telemetrie-systemen** : stream al uw bron logboeken naar een enkele Event hub om logboek gegevens naar een Siem of log Analytics-hulp programma van derden te verzenden.
* **Bouw een aangepast telemetrie-en logboek registratie platform** : met de uiterst schaal bare functie voor publiceren en abonneren van Event hubs kunt u resource logboeken flexibel opnemen in een aangepast teletry-platform. Zie [het ontwerp en de grootte van een telemetrie-platform op wereld wijd schalen op Azure Event hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.

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
U moet [een event hub maken](../../event-hubs/event-hubs-create.md) als u er nog geen hebt. Als u eerder bron logboeken naar deze Event Hubs naam ruimte hebt gestreamd, wordt dat Event Hub opnieuw gebruikt.

Het beleid voor gedeelde toegang voor de naam ruimte definieert de machtigingen die het streaming-mechanisme heeft. Streaming naar Event Hubs vereist machtigingen voor beheren, verzenden en Luis teren. U kunt beleid voor gedeelde toegang maken of wijzigen in de Azure Portal op het tabblad configureren voor uw Event Hubs naam ruimte.

Als u de diagnostische instelling wilt bijwerken zodat deze streaming bevat, moet u de machtiging ListKey hebben voor die Event Hubs autorisatie regel. De naam ruimte van de Event Hubs hoeft zich niet te bevinden in hetzelfde abonnement als het abonnement dat Logboeken verzendt, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang heeft tot beide abonnementen en beide abonnementen zich in dezelfde AAD-Tenant bevinden.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Bron logboeken worden niet standaard verzameld. U kunt ze verzenden naar een Event Hub en andere bestemmingen door een diagnostische instelling te maken voor een Azure-resource. Zie [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md) voor meer informatie.

## <a name="stream-data-from-compute-resources"></a>Gegevens streamen van Compute-resources
Het proces in dit artikel is bedoeld voor niet-reken resources zoals beschreven in [overzicht van Azure-resource logboeken](diagnostic-settings.md).
Bron logboeken streamen vanuit Azure Compute-resources met behulp van de Windows Azure Diagnostics-agent. Zie [Azure Diagnostics gegevens streamen in het warme pad met behulp van Event hubs](diagnostics-extension-stream-event-hubs.md) voor meer informatie.


## <a name="consuming-log-data-from-event-hubs"></a>Logboek gegevens van Event hubs gebruiken
Wanneer u bron logboeken van Event hubs gebruikt, is het een JSON-indeling met de elementen in de volgende tabel.

| Element naam | Beschrijving |
| --- | --- |
| records |Een matrix van alle logboek gebeurtenissen in deze nettolading. |
| tijd |Tijdstip waarop de gebeurtenis heeft plaatsgevonden. |
| category |Logboek categorie voor deze gebeurtenis. |
| resourceId |De resource-ID van de resource die deze gebeurtenis heeft gegenereerd. |
| operationName |Naam van de bewerking. |
| niveau |Optioneel. Hiermee wordt het logboek gebeurtenis niveau aangegeven. |
| properties |De eigenschappen van de gebeurtenis. Deze verschillen voor elke Azure-service, zoals beschreven [ ]()in. |


Hieronder ziet u voor beelden van uitvoer gegevens van Event Hubs:

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

* [Stream Azure Active Directory-logboeken met Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Meer informatie over Azure-resource logboeken](resource-logs-overview.md).
* [Aan de slag met Event hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

