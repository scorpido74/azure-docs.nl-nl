---
title: 'Zelf studie: bewakings gegevens opnemen in azure Data Explorer zonder één regel code'
description: In deze zelf studie leert u hoe u bewakings gegevens kunt opnemen in azure Data Explorer zonder één regel code en hoe u deze gegevens doorzoekt.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: c160f04ef7120a6c90991d8e6ecdf98b2f0d348e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836556"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Zelf studie: gegevens opnemen en controleren in azure Data Explorer 

In deze zelfstudie leert u hoe u zonder code te schrijven gegevens uit diagnostische logboeken en activiteitenlogboeken in een Azure Data Explorer-cluster kunt opnemen. Met deze eenvoudige methode voor gegevensopname kunt u snel beginnen met het uitvoeren van query's voor Azure Data Explorer om gegevens te analyseren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak tabellen en toewijzing van opname in een Azure Data Explorer-database.
> * Maak de opgenomen gegevens op met behulp van updatebeleid.
> * Maak een [event hub](/azure/event-hubs/event-hubs-about) en koppel deze aan Azure Data Explorer.
> * Gegevens streamen naar een Event Hub van Azure Monitor [diagnose gegevens en logboeken](/azure/azure-monitor/platform/diagnostic-settings) en [activiteiten logboeken](/azure/azure-monitor/platform/activity-logs-overview).
> * Vraag de opgenomen gegevens op met behulp van Azure Data Explorer.

> [!NOTE]
> Maak alle resources in dezelfde Azure-locatie of -regio. 

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md). In deze zelfstudie is *TestDatabase* de databasenaam.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor Data Provider: diagnostische gegevens en logboeken en activiteiten logboeken

Bekijk en inzicht in de gegevens die worden verschaft door de Azure Monitor diagnostische meet waarden en logboeken en activiteiten logboeken hieronder. U maakt een opname pijplijn op basis van deze gegevens schema's. Houd er rekening mee dat elke gebeurtenis in een logboek een matrix met records heeft. Deze matrix met records wordt later in de zelf studie gesplitst.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Voor beelden van diagnostische gegevens en logboeken en activiteiten logboeken

Diagnostische gegevens en logboeken en activiteiten logboeken van Azure worden verzonden door een Azure-service en bieden gegevens over de werking van die service. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostische gegevens](#tab/diagnostic-metrics)
#### <a name="example"></a>Voorbeeld

Diagnostische gegevens worden geaggregeerd met een tijd korrel van 1 minuut. Hieronder volgt een voor beeld van een Azure Data Explorer metrische-gebeurtenis schema op query duur:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
#### <a name="example"></a>Voorbeeld

Hieronder volgt een voor beeld van een Azure Data Explorer [Diagnostic-opname logboek](using-diagnostic-logs.md#diagnostic-logs-schema):

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)
#### <a name="example"></a>Voorbeeld

Azure-activiteiten logboeken zijn logboeken op abonnements niveau die inzicht bieden in de bewerkingen die worden uitgevoerd op de resources in uw abonnement. Hieronder volgt een voor beeld van een gebeurtenis voor activiteiten logboek voor het controleren van de toegang:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Een opnamepijplijn instellen in Azure Data Explorer

Het instellen van een Azure Data Explorer-pijplijn omvat verschillende stappen, zoals [het maken van een tabel en gegevensopname](/azure/data-explorer/ingest-sample-data#ingest-data). U kunt de gegevens ook bewerken, toewijzen en bijwerken.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Verbinding maken met de web-UI van Azure Data Explorer

Selecteer **Query** in uw *TestDatabase*-database van Azure Data Explorer om de web-UI van Azure Data Explorer te openen.

![Querypagina](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>De doeltabellen maken

De structuur van de Azure Monitor Logboeken is niet in tabel vorm. U manipuleert de gegevens en vouwt elke gebeurtenis uit naar een of meer records. De onbewerkte gegevens worden opgenomen in een tussenliggende tabel met de naam *ActivityLogsRawRecords* voor activiteiten logboeken en *DiagnosticRawRecords* voor diagnostische metrieken en Logboeken. Dat is het moment waarop de gegevens worden bewerkt en uitgebreid. Als u een update beleid gebruikt, worden de uitgevouwen gegevens vervolgens opgenomen in de tabel *ActivityLogs* voor activiteiten logboeken, *DiagnosticMetrics* voor diagnostische meet gegevens en *DiagnosticLogs* voor Diagnostische logboeken. Dit betekent dat u twee afzonderlijke tabellen moet maken voor het opnemen van activiteiten logboeken en drie afzonderlijke tabellen voor het opnemen van diagnostische gegevens en Logboeken.

Gebruik de web-UI van Azure Data Explorer om de doeltabellen te maken in de Azure Data Explorer-database.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostische gegevens](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Tabellen maken voor de diagnostische gegevens

1. Maak in de *TestDatabase* -Data Base een tabel met de naam *DiagnosticMetrics* om de gegevens van de diagnostische gegevens op te slaan. Gebruik de volgende `.create table`-besturingselementopdracht:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecteer **Uitvoeren** om de tabel te maken.

    ![Query uitvoeren](media/ingest-data-no-code/run-query.png)

1. Maak met behulp van de volgende query de tussenliggende gegevens tabel met de naam *DiagnosticRawRecords* in de *TestDatabase* -data base om gegevens te manipuleren. Selecteer **Uitvoeren** om de tabel te maken.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Stel het [Bewaar beleid](/azure/kusto/management/retention-policy) nul in voor de tussenliggende tabel:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Tabellen maken voor de diagnostische logboeken 

1. Maak in de *TestDatabase* -Data Base een tabel met de naam *DiagnosticLogs* om de diagnostische logboek records op te slaan. Gebruik de volgende `.create table`-besturingselementopdracht:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Selecteer **Uitvoeren** om de tabel te maken.

1. Maak met behulp van de volgende query de tussenliggende gegevens tabel met de naam *DiagnosticRawRecords* in de *TestDatabase* -data base om gegevens te manipuleren. Selecteer **Uitvoeren** om de tabel te maken.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Stel het [Bewaar beleid](/azure/kusto/management/retention-policy) nul in voor de tussenliggende tabel:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Tabellen maken voor de activiteiten logboeken 

1. Maak een tabel met de naam *ActivityLogs* in de *TestDatabase* -data base om activiteiten logboek records te ontvangen. Voer de volgende Azure Data Explorer-query uit om de tabel te maken:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Maak de tussenliggende gegevenstabel met de naam *ActivityLogsRawRecords* in de *TestDatabase*-database voor het manipuleren van gegevens:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Stel het [Bewaar beleid](/azure/kusto/management/retention-policy) nul in voor de tussenliggende tabel:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Tabeltoewijzingen maken

 Omdat de tabelindeling `json` is, is gegevenstoewijzing vereist. Met de `json`-toewijzingen wordt elk json-pad toegewezen aan de naam van een tabelkolom.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostische gegevens/Diagnostische logboeken](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Diagnostische gegevens en logboeken aan de tabel toewijzen

Gebruik de volgende query om de diagnostische metriek en logboek gegevens aan de tabel toe te wijzen:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Activiteiten logboeken toewijzen aan de tabel

Als u de gegevens van het activiteiten logboek wilt toewijzen aan de tabel, gebruikt u de volgende query:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Het update beleid voor metrische en logboek gegevens maken

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostische gegevens](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Beleid voor gegevens updates maken voor metrische informatie over diagnoses

1. Maak een [functie](/azure/kusto/management/functions) die de verzameling diagnostische metrische records uitbreidt, zodat elke waarde in de verzameling een afzonderlijke rij ontvangt. Gebruik de operator [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Voeg het [updatebeleid](/azure/kusto/concepts/updatepolicy) toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuwe opgenomen gegevens in de tussenliggende gegevens tabel *DiagnosticRawRecords* en worden de resultaten opgenomen in de tabel *DiagnosticMetrics* :

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Beleid voor gegevens updates voor Diagnostische logboeken maken

1. Maak een [functie](/azure/kusto/management/functions) die de verzameling diagnostische logboek records uitbreidt, zodat elke waarde in de verzameling een afzonderlijke rij ontvangt. U kunt opname logboeken inschakelen op een Azure Data Explorer-cluster en het [schema opname logboeken](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)gebruiken. U maakt één tabel voor geslaagde en mislukte opname, terwijl sommige velden leeg zijn voor geslaagde opname (error code bijvoorbeeld). Gebruik de operator [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Voeg het [updatebeleid](/azure/kusto/concepts/updatepolicy) toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuwe opgenomen gegevens in de tussenliggende gegevens tabel *DiagnosticRawRecords* en worden de resultaten opgenomen in de tabel *DiagnosticLogs* :

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Beleid voor gegevens updates voor activiteiten logboeken maken

1. Maak een [functie](/azure/kusto/management/functions) die de verzameling activiteiten logboek records uitbreidt, zodat elke waarde in de verzameling een afzonderlijke rij ontvangt. Gebruik de operator [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Voeg het [updatebeleid](/azure/kusto/concepts/updatepolicy) toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuwe opgenomen gegevens in de tussenliggende gegevens tabel *ActivityLogsRawRecords* en worden de resultaten opgenomen in de tabel *ActivityLogs* :

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Een Azure Event Hubs-naamruimte maken

Met Diagnostische instellingen voor Azure kunt u metrische gegevens en logboeken exporteren naar een opslag account of een Event Hub. In deze zelf studie sturen we de metrische gegevens en logboeken via een Event Hub. U maakt een Event Hubs naam ruimte en een Event Hub voor de metrische gegevens van de diagnose en Logboeken in de volgende stappen. Azure Monitor maakt de event hub *insights-operational-logs* voor de activiteitenlogboeken.

1. Maak een event hub met behulp van een Azure Resource Manager-sjabloon in de Microsoft Azure-portal. Klik met de rechtermuisknop op **Implementeren in Azure** en selecteer **In nieuw venster openen** om de rest van de stappen in dit artikel te volgen. Klik op de knop **Implementeren in Azure** om de Azure-portal te openen.

    [![De knop Implementeren in Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Maak een Event Hubs-naamruimte en een event hub voor de diagnostische logboeken.

    ![Een event hub maken](media/ingest-data-no-code/event-hub.png)

1. Vul in het formulier de volgende gegevens in. Gebruik de standaardwaarden voor alle instellingen die niet worden vermeld in de volgende tabel.

    **Instelling** | **Voorgestelde waarde** | **Beschrijving**
    |---|---|---|
    | **Abonnement** | *Uw abonnement* | Selecteer het Azure-abonnement dat u wilt gebruiken voor de Event Hub.|
    | **Resourcegroep** | *test-resource-group* | Maak een nieuwe resourcegroep. |
    | **Locatie** | Selecteer de regio die het beste voldoet aan uw behoeften. | Maak de Event Hubs-naamruimte op dezelfde locatie als andere resources.
    | **Naam van naamruimte** | *AzureMonitoringData* | Kies een unieke naam waarmee de naamruimte kan worden geïdentificeerd.
    | **Event hub-naam** | *DiagnosticData* | De Event Hub bevindt zich onder de naamruimte, wat een unieke bereikcontainer biedt. |
    | **Naam van consumentengroep** | *adxpipeline* | Maak een naam voor een consumentengroep. Met consumentengroepen kunnen meerdere gebruikstoepassingen elk een afzonderlijke weergave van de gebeurtenisstroom hebben. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Azure Monitor metrische gegevens en logboeken verbinden met uw Event Hub

Nu moet u uw diagnostische gegevens en logboeken en uw activiteiten logboeken verbinden met de Event Hub.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostische gegevens/Diagnostische logboeken](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Diagnostische gegevens en logboeken verbinden met uw Event Hub

Selecteer een resource van waaruit u metrische gegevens wilt exporteren. Verschillende resource typen ondersteunen het exporteren van diagnostische gegevens, waaronder Event Hubs naam ruimte, Azure Key Vault, Azure IoT Hub en Azure Data Explorer-clusters. In deze zelf studie gebruiken we een Azure Data Explorer-cluster als resource, worden de metrische gegevens over query prestaties en logboeken met opname resultaten gecontroleerd.

1. Selecteer uw Kusto-cluster in de Azure-portal.
1. Selecteer **Diagnostische instellingen** en selecteer vervolgens de link **Diagnostische gegevens inschakelen**. 

    ![Diagnostische instellingen](media/ingest-data-no-code/diagnostic-settings.png)

1. Het deelvenster **Diagnostische instellingen** wordt geopend. Voer de volgende stappen uit:
   1. Geef de gegevens uit uw diagnostische logboeken de naam *ADXExportedData*.
   1. Schakel onder **logboek**de selectie vakjes **SucceededIngestion** en **FailedIngestion** in.
   1. Schakel onder **metrische gegevens**het selectie vakje **query prestaties** in.
   1. Schakel het selectievakje **Streamen naar een event hub** in.
   1. Selecteer **Configureren**.

      ![Het deelvenster Diagnostische instellingen](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Configureer in het deelvenster **Een event hub selecteren** hoe gegevens uit diagnostisch logboeken moeten worden geëxporteerd naar de event hub die u hebt gemaakt:
    1. Selecteer in de lijst **Naamruimte van de Event Hub selecteren** de optie *AzureMonitoringData*.
    1. Selecteer in de lijst **Event hub naam selecteren** de optie *DiagnosticData*.
    1. Selecteer in de lijst **Naam van het Event Hub-beleid selecteren** de optie **RootManagerSharedAccessKey**.
    1. Selecteer **OK**.

1. Selecteer **Opslaan**.

# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Activiteitenlogboeken verbinden met uw event hub

1. Selecteer **Activiteitenlogboek** in het linkermenu van de Microsoft Azure-portal.
1. Het venster **Activiteitenlogboek** wordt geopend. Selecteer **Exporteren naar Event Hub**.

    ![Het venster Activiteitenlogboek](media/ingest-data-no-code/activity-log.png)

1. Het venster **Activiteitenlogboek exporteren** wordt geopend:
 
    ![Het venster Activiteitenlogboek exporteren](media/ingest-data-no-code/export-activity-log.png)

1. Voer de volgende stappen uit in het venster **Activiteitenlogboek exporteren**:
      1. Selecteer uw abonnement.
      1. Kies in de lijst **Regio's** de optie **Alles selecteren**.
      1. Selecteer het selectievakje **Exporteren naar een Event Hub**.
      1. Kies **Selecteer een service bus-naamruimte** om het deelvenster **Een event hub selecteren** te openen.
      1. Selecteer uw abonnement in het deelvenster **Event Hub selecteren**.
      1. Selecteer in de lijst **Naamruimte van de Event Hub selecteren** de optie *AzureMonitoringData*.
      1. Selecteer in de lijst **Naam van het Event Hub-beleid selecteren** de naam van het standaardbeleid voor de event hub.
      1. Selecteer **OK**.
      1. Selecteer **Opslaan** in de linkerbovenhoek van het venster.
   Er wordt een event hub met de naam van de *insights-operational-logs* gemaakt.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Zie hoe gegevens naar uw event hubs gaan

1. Wacht een paar minuten totdat de verbinding is gedefinieerd en het exporteren van het activiteitenlogboek naar de event hub is voltooid. Ga naar uw Event Hubs-naamruimte om de event hubs te zien die u hebt gemaakt.

    ![Gemaakte event hubs](media/ingest-data-no-code/event-hubs-created.png)

1. Zie hoe gegevens naar uw event hub gaan:

    ![Gegevens van de event hub](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Een event hub verbinden met Azure Data Explorer

U moet nu de gegevens verbindingen maken voor uw diagnostische metrieken en logboeken en activiteiten Logboeken.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>De gegevens verbinding maken voor de diagnostische metrieken en logboeken en activiteiten logboeken

1. In het Azure Data Explorer-cluster met de naam *kustodocs* selecteert u **Databases** in het menu links.
1. In het venster **Databases** selecteert u uw *TestDatabase*-database.
1. Selecteer **Gegevensopname** in het menu links.
1. Klik in het venster **Gegevensopname** op **+ Gegevensverbinding toevoegen**.
1. Voer in het venster **Gegevensverbinding** de volgende informatie in:

    ![Event hub-gegevensverbinding](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Diagnostische gegevens/Diagnostische logboeken](#tab/diagnostic-metrics+diagnostic-logs) 

1. Gebruik de volgende instellingen in het venster **Gegevensverbinding**:

    Gegevensbron:

    **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | **Naam van gegevensverbinding** | *DiagnosticsLogsConnection* | De naam van de verbinding die u wilt maken in Azure Data Explorer.|
    | **Event hub-naamruimte** | *AzureMonitoringData* | De naam die u eerder hebt gekozen om de naamruimte te identificeren. |
    | **Event hub** | *DiagnosticData* | De Event Hub die u hebt gemaakt. |
    | **Consumentengroep** | *adxpipeline* | De consumentengroep die u hebt gedefinieerd in de gemaakte Event Hub. |
    | | |

    Doeltabel:

    Er zijn twee opties voor de routering: *statische* en *dynamische*. Voor deze zelfstudie gebruikt u statische routering (standaardinstelling), waarbij u de tabelnaam, gegevensindeling en toewijzing opgeeft. Laat **Mijn gegevens omvatten routeringsgegevens** uitgeschakeld.

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | **Tabel** | *DiagnosticRawRecords* | De tabel die u hebt gemaakt in de *TestDatabase*-database. |
    | **Gegevensindeling** | *JSON* | De indeling die in de tabel wordt gebruikt. |
    | **Toewijzen van kolommen** | *DiagnosticRawRecordsMapping* | De toewijzing die u hebt gemaakt in de *TestDatabase* -data base, waarmee inkomende JSON-gegevens worden toegewezen aan de kolom namen en gegevens typen van de tabel *DiagnosticRawRecords* .|
    | | |

1. Selecteer **Maken**.  

# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)

1. Gebruik de volgende instellingen in het venster **Gegevensverbinding**:

    Gegevensbron:

    **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | **Naam van gegevensverbinding** | *ActivityLogsConnection* | De naam van de verbinding die u wilt maken in Azure Data Explorer.|
    | **Event hub-naamruimte** | *AzureMonitoringData* | De naam die u eerder hebt gekozen om de naamruimte te identificeren. |
    | **Event hub** | *insights-operational-logs* | De Event Hub die u hebt gemaakt. |
    | **Consumentengroep** | *$Default* | De standaard consumentengroep. Zo nodig kunt u een andere consumentengroep maken. |
    | | |

    Doeltabel:

    Er zijn twee opties voor de routering: *statische* en *dynamische*. Voor deze zelfstudie gebruikt u statische routering (standaardinstelling), waarbij u de tabelnaam, gegevensindeling en toewijzing opgeeft. Laat **Mijn gegevens omvatten routeringsgegevens** uitgeschakeld.

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | **Tabel** | *ActivityLogsRawRecords* | De tabel die u hebt gemaakt in de *TestDatabase*-database. |
    | **Gegevensindeling** | *JSON* | De indeling die in de tabel wordt gebruikt. |
    | **Toewijzen van kolommen** | *ActivityLogsRawRecordsMapping* | De toewijzing die u hebt gemaakt in de *TestDatabase*-database, waarmee binnenkomende JSON-gegevens worden toegewezen aan de kolomnamen en gegevenstypen van de tabel *ActivityLogsRawRecords*.|
    | | |

1. Selecteer **Maken**.  
---

## <a name="query-the-new-tables"></a>Query uitvoeren op de nieuwe tabellen

U hebt nu een pijplijn met een gegevensstroom. Opname via het cluster duurt standaard 5 minuten. Laat de gegevensstroom daarom een paar minuten zijn gang gaan voordat u een query gaat uitvoeren.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Diagnostische gegevens](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Query's uitvoeren op de tabel met metrische gegevens van diagnose

Met de volgende query worden gegevens over de duur van query's geanalyseerd uit records met diagnostische gegevens in azure Data Explorer:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Queryresultaten:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Query's uitvoeren in de tabel met Diagnostische logboeken

Deze pijp lijn produceert opname door middel van een Event Hub. U gaat de resultaten van deze opnamesen bekijken.
Met de volgende query wordt geanalyseerd hoeveel opnameies in een minuut zijn samengevoegd, inclusief een voor beeld van `Database`, `Table` en `IngestionSourcePath` voor elk interval:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Queryresultaten:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[Activiteiten logboeken](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Query's uitvoeren op de tabel activiteiten logboeken

De volgende query analyseert gegevens uit records van activiteitenlogboeken in Azure Data Explorer:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Queryresultaten:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het schrijven van veel meer query's voor de gegevens die u uit Azure hebt geëxtraheerd Data Explorer met behulp van [Schrijf query's voor Azure Data Explorer](write-queries.md).
* [Azure Data Explorer opname bewerkingen bewaken met Diagnostische logboeken](using-diagnostic-logs.md)
* [Metrische gegevens gebruiken voor het bewaken van de cluster status](using-metrics.md)
