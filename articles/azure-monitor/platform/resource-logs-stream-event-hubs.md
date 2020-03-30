---
title: Azure-platformlogboeken streamen naar een gebeurtenishub
description: Meer informatie over het streamen van Azure-bronlogboeken naar een gebeurtenishub om gegevens naar externe systemen te verzenden, zoals SPM's van derden en andere oplossingen voor logboekanalyse.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658911"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Azure-platformlogboeken streamen naar Azure Event Hubs
[Platformlogboeken](platform-logs-overview.md) in Azure, inclusief Azure Activity log en resource logs, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn.  In dit artikel worden logboeken van streamingplatforms naar gebeurtenishubs beschreven om gegevens naar externe systemen te verzenden, zoals SPM's van derden en andere oplossingen voor logboekanalyse.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>Wat u doen met platformlogboeken die naar een gebeurtenishub worden verzonden
Stream platformlogboeken in Azure naar gebeurtenishubs om de volgende functionaliteit te bieden:

* **Stream logboeken naar logboekregistratie- en telemetriesystemen** van derden : stream al uw platformlogboeken naar één gebeurtenishub om logboekgegevens te pipeteren naar een SIEM- of loganalysetool van derden.
  
* **Bouw een aangepast telemetrie- en loggingplatform** - Het zeer schaalbare publicatie-abonneren de aard van event hubs u flexibel inslikken platform logs in een aangepaste teletry platform. Zie [Het ontwerpen en dimensioneren van een telemetrieplatform op globale schaal op Azure-gebeurtenishubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) voor meer informatie.

* **Bekijk de status van de service door gegevens te streamen naar Power BI** : gebruik gebeurtenishubs, streamanalytics en Power BI om uw diagnostische gegevens om te zetten in bijna realtime inzichten over uw Azure-services. Zie [Stream Analytics en Power BI: een realtime analysedashboard voor het streamen van gegevens](../../stream-analytics/stream-analytics-power-bi-dashboard.md) voor meer informatie over deze oplossing.

    De volgende SQL-code is een voorbeeldstreamanalysequery die u gebruiken om alle loggegevens in te delen in een Power BI-tabel:
    
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
U moet [een gebeurtenishub maken](../../event-hubs/event-hubs-create.md) als u er nog geen hebt. Als u al een diagnostische instelling hebt met deze naamruimte voor gebeurtenishubs, wordt die gebeurtenishub opnieuw gebruikt.

Het beleid voor gedeelde toegang voor de naamruimte definieert de machtigingen die het streamingmechanisme heeft. Voor streaming naar gebeurtenishubs zijn machtigingen voor beheren, verzenden en luisteren vereist. U beleid voor gedeelde toegang maken of wijzigen in de Azure-portal onder het tabblad Configureren voor de naamruimte van uw gebeurtenishubs.

Als u de diagnostische instelling wilt bijwerken om streaming op te nemen, moet u de machtiging ListKey hebben voor de autorisatieregel gebeurtenishubs. De naamruimte van Gebeurtenishubs hoeft niet in hetzelfde abonnement te zitten als het abonnement dat logboeken uitzendt, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang heeft tot beide abonnementen en beide abonnementen in dezelfde AAD-tenant staan.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Stuur platformlogboeken naar een gebeurtenishub en andere bestemmingen door een diagnostische instelling voor een Azure-bron te maken. Zie [Diagnostische instelling maken om logboeken en statistieken in Azure](diagnostic-settings.md) te verzamelen voor meer informatie.

## <a name="collect-data-from-compute-resources"></a>Gegevens verzamelen uit rekenbronnen
Diagnostische instellingen verzamelen resourcelogs voor Azure-compute resources, net als elke andere resource, maar niet voor hun gastbesturingssysteem of workloads. Als u deze gegevens wilt verzamelen, installeert u de [agent Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Loggegevens van gebeurtenishubs consumeren
Platformlogboeken van gebeurtenishubs worden verbruikt in JSON-indeling met de elementen in de volgende tabel.

| Naam van element | Beschrijving |
| --- | --- |
| Records |Een array van alle log gebeurtenissen in deze payload. |
| tijd |Het tijdstip waarop de gebeurtenis heeft plaatsgevonden. |
| category |Logboekcategorie voor deze gebeurtenis. |
| resourceId |Resource-id van de resource die deze gebeurtenis heeft gegenereerd. |
| operationName |Naam van de bewerking. |
| niveau |Optioneel. Hiermee geeft u het gebeurtenisniveau van het logboek aan. |
| properties |Eigenschappen van het evenement. Deze verschillen voor elke Azure-service zoals beschreven in [ ](). |


Hieronder volgen voorbeelduitvoergegevens van gebeurtenishubs voor een bronlogboek:

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

* [Lees meer over resourcelogs](platform-logs-overview.md).
* [Diagnostische instelling maken om logboeken en statistieken in Azure te verzamelen.](diagnostic-settings.md)
* [Azure Active Directory-logboeken streamen met Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Ga aan de slag met Event Hubs.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

