---
title: 'Zelfstudie: Gegevens met inname van monitoring in Azure Data Explorer zonder code'
description: In deze zelfstudie leert u hoe u bewakingsgegevens innemen in Azure Data Explorer zonder één regel code en die gegevens opvragen.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198045"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Zelfstudie: Gegevens over het innemen en querybewaking in Azure Data Explorer 

In deze zelfstudie leert u hoe u zonder code te schrijven gegevens uit diagnostische logboeken en activiteitenlogboeken in een Azure Data Explorer-cluster kunt opnemen. Met deze eenvoudige methode voor gegevensopname kunt u snel beginnen met het uitvoeren van query's voor Azure Data Explorer om gegevens te analyseren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak tabellen en toewijzing van opname in een Azure Data Explorer-database.
> * Maak de opgenomen gegevens op met behulp van updatebeleid.
> * Maak een [event hub](/azure/event-hubs/event-hubs-about) en koppel deze aan Azure Data Explorer.
> * Gegevens streamen naar een gebeurtenishub vanuit [diagnostische gegevens en logboeken en activiteitslogboeken](/azure/azure-monitor/platform/diagnostic-settings) van Azure Monitor. [activity logs](/azure/azure-monitor/platform/activity-logs-overview)
> * Vraag de opgenomen gegevens op met behulp van Azure Data Explorer.

> [!NOTE]
> Maak alle resources in dezelfde Azure-locatie of -regio. 

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md). In deze zelfstudie is *TestDatabase* de databasenaam.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor-gegevensprovider: diagnostische statistieken en logboeken en activiteitslogboeken

Bekijk en begrijp hieronder de gegevens van de diagnostische gegevens en logboeken en activiteitslogboeken van Azure Monitor. U maakt een innamepijplijn op basis van deze gegevensschema's. Houd er rekening mee dat elke gebeurtenis in een logboek een array met records heeft. Deze reeks records wordt later in de zelfstudie gesplitst.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Voorbeelden van diagnostische statistieken en logboeken en activiteitslogboeken

Diagnostische azure-statistieken en logboeken en activiteitslogboeken worden uitgestoten door een Azure-service en gegevens verstrekken over de werking van die service. 

# <a name="diagnostic-metrics"></a>[Diagnostische statistieken](#tab/diagnostic-metrics)
#### <a name="example"></a>Voorbeeld

Diagnostische statistieken worden samengevoegd met een tijdskorrel van 1 minuut. Hieronder volgt een voorbeeld van een azure data explorer-statistiekgebeurtenisschema over queryduur:

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

# <a name="diagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
#### <a name="example"></a>Voorbeeld

Hieronder volgt een voorbeeld van een [diagnostisch innamelogboek](using-diagnostic-logs.md#diagnostic-logs-schema)van Azure Data Explorer:

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
# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)
#### <a name="example"></a>Voorbeeld

Azure-activiteitslogboeken zijn logboeken op abonnementsniveau die inzicht geven in de bewerkingen die worden uitgevoerd op resources in uw abonnement. Hieronder volgt een voorbeeld van een activiteitslogboekgebeurtenis voor het controleren van de toegang:

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

De structuur van de Azure Monitor-logboeken is niet tabelvormig. U manipuleert de gegevens en breidt elke gebeurtenis uit naar een of meer records. De ruwe gegevens worden ingenomen in een tussenliggende tabel met de naam *ActivityLogsRawRecords* voor activiteitslogboeken en *DiagnosticRawRecords* voor diagnostische statistieken en logboeken. Dat is het moment waarop de gegevens worden bewerkt en uitgebreid. Met behulp van een updatebeleid worden de uitgebreide gegevens vervolgens opgenomen in de *activitylogs-tabel* voor activiteitslogboeken, *DiagnosticMetrics* voor diagnostische statistieken en *DiagnosticLogs* voor diagnostische logboeken. Dit betekent dat u twee afzonderlijke tabellen moet maken voor het opnemen van activiteitslogboeken en drie afzonderlijke tabellen voor het innemen van diagnostische statistieken en logboeken.

Gebruik de web-UI van Azure Data Explorer om de doeltabellen te maken in de Azure Data Explorer-database.

# <a name="diagnostic-metrics"></a>[Diagnostische statistieken](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Tabellen maken voor de diagnostische statistieken

1. Maak in de *TestDatabase-database* een tabel met de naam *DiagnosticMetrics* om de diagnostische metrische gegevens records op te slaan. Gebruik de volgende `.create table`-besturingselementopdracht:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecteer **Uitvoeren** om de tabel te maken.

    ![Query uitvoeren](media/ingest-data-no-code/run-query.png)

1. Maak de tussenliggende gegevenstabel met de naam *DiagnosticRawRecords* in de *TestDatabase-database* voor gegevensmanipulatie met behulp van de volgende query. Selecteer **Uitvoeren** om de tabel te maken.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Stel [nulretentiebeleid](/azure/kusto/management/retention-policy) in voor de tussenliggende tabel:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Tabellen maken voor de diagnostische logboeken 

1. Maak in de *TestDatabase-database* een tabel met de naam *DiagnosticLogs* om de diagnostische logboekrecords op te slaan. Gebruik de volgende `.create table`-besturingselementopdracht:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Selecteer **Uitvoeren** om de tabel te maken.

1. Maak de tussenliggende gegevenstabel met de naam *DiagnosticRawRecords* in de *TestDatabase-database* voor gegevensmanipulatie met behulp van de volgende query. Selecteer **Uitvoeren** om de tabel te maken.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Stel [nulretentiebeleid](/azure/kusto/management/retention-policy) in voor de tussenliggende tabel:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Tabellen maken voor de activiteitslogboeken 

1. Maak een tabel met de naam *ActivityLogs* in de *TestDatabase-database* om activiteitenlogboekrecords te ontvangen. Voer de volgende Azure Data Explorer-query uit om de tabel te maken:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Maak de tussenliggende gegevenstabel met de naam *ActivityLogsRawRecords* in de *TestDatabase*-database voor het manipuleren van gegevens:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Stel [nulretentiebeleid](/azure/kusto/management/retention-policy) in voor de tussenliggende tabel:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Tabeltoewijzingen maken

 Omdat de tabelindeling `json` is, is gegevenstoewijzing vereist. Met de `json`-toewijzingen wordt elk json-pad toegewezen aan de naam van een tabelkolom.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnostische statistieken / Diagnostische logboeken](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Diagnostische statistieken en logboeken toewijzen aan de tabel

Als u de diagnostische statistiek en logboekgegevens wilt toewijzen aan de tabel, gebruikt u de volgende query:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Activiteitslogboeken toewijzen aan de tabel

Als u de gegevens van het activiteitenlogboek wilt toewijzen aan de tabel, gebruikt u de volgende query:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Het updatebeleid voor metrische gegevens en logboekgegevens maken

# <a name="diagnostic-metrics"></a>[Diagnostische statistieken](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Beleid voor gegevensupdate maken voor diagnostische statistieken

1. Maak een [functie](/azure/kusto/management/functions) waarmee de verzameling diagnostische metrische records wordt uitgebreid, zodat elke waarde in de verzameling een aparte rij ontvangt. Gebruik [`mv-expand`](/azure/kusto/query/mvexpandoperator) de operator:
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

2. Voeg het [updatebeleid](/azure/kusto/concepts/updatepolicy) toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuw ingenomen gegevens in de tussentijdse gegevenstabel *DiagnosticRawRecords* en worden de resultaten opgenomen in de tabel *DiagnosticMetrics:*

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Beleid voor gegevensupdate maken voor diagnostische logboeken

1. Maak een [functie](/azure/kusto/management/functions) waarmee de verzameling diagnostische logboekenrecords wordt uitgebreid, zodat elke waarde in de verzameling een aparte rij ontvangt. U schakelt opnamelogboeken in op een Azure Data Explorer-cluster en gebruikt [het schema voor innamelogboeken](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). U maakt één tabel voor geslaagden en voor mislukte opname, terwijl sommige velden leeg zijn voor geslaagde inname (errorcode bijvoorbeeld). Gebruik [`mv-expand`](/azure/kusto/query/mvexpandoperator) de operator:

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

2. Voeg het [updatebeleid](/azure/kusto/concepts/updatepolicy) toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuw ingenomen gegevens in de tussentijdse gegevenstabel *DiagnosticRawRecords* en worden de resultaten opgenomen in de tabel *DiagnosticLogs:*

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Beleid voor gegevensupdate maken voor activiteitenlogboeken

1. Maak een [functie](/azure/kusto/management/functions) waarmee de verzameling activiteitenlogboekrecords wordt uitgebreid, zodat elke waarde in de verzameling een aparte rij ontvangt. Gebruik [`mv-expand`](/azure/kusto/query/mvexpandoperator) de operator:

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

2. Voeg het [updatebeleid](/azure/kusto/concepts/updatepolicy) toe aan de doeltabel. Met dit beleid wordt de query automatisch uitgevoerd op nieuw ingenomen gegevens in de intermediaire gegevenstabel *ActivityLogsRawRecords* en worden de resultaten opgenomen in de tabel *ActivityLogs:*

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Een Azure Event Hubs-naamruimte maken

Azure-diagnostische instellingen maken het exporteren van statistieken en logboeken naar een opslagaccount of naar een gebeurtenishub mogelijk. In deze zelfstudie sturen we de statistieken en logboeken via een gebeurtenishub. In de volgende stappen maakt u een naamruimte voor gebeurtenishubs en een gebeurtenishub voor de diagnostische statistieken en logboeken. Azure Monitor maakt de event hub *insights-operational-logs* voor de activiteitenlogboeken.

1. Maak een event hub met behulp van een Azure Resource Manager-sjabloon in de Microsoft Azure-portal. Klik met de rechtermuisknop op **Implementeren in Azure** en selecteer **In nieuw venster openen** om de rest van de stappen in dit artikel te volgen. Klik op de knop **Implementeren in Azure** om de Azure-portal te openen.

    [![Knop Implementeren naar Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Maak een Event Hubs-naamruimte en een event hub voor de diagnostische logboeken.

    ![Een event hub maken](media/ingest-data-no-code/event-hub.png)

1. Vul in het formulier de volgende gegevens in. Gebruik de standaardwaarden voor alle instellingen die niet worden vermeld in de volgende tabel.

    **Instelling** | **Voorgestelde waarde** | **Beschrijving**
    |---|---|---|
    | **Abonnement** | *Uw abonnement* | Selecteer het Azure-abonnement dat u wilt gebruiken voor de Event Hub.|
    | **Resourcegroep** | *test-resource-groep* | Maak een nieuwe resourcegroep. |
    | **Locatie** | Selecteer de regio die het beste voldoet aan uw behoeften. | Maak de Event Hubs-naamruimte op dezelfde locatie als andere resources.
    | **Naam van naamruimte** | *AzureMonitoringData* | Kies een unieke naam waarmee de naamruimte kan worden geïdentificeerd.
    | **Event hub-naam** | *Diagnostische gegevens* | De Event Hub bevindt zich onder de naamruimte, wat een unieke bereikcontainer biedt. |
    | **Naam van consumentengroep** | *adxpipeline* | Maak een naam voor een consumentengroep. Met consumentengroepen kunnen meerdere gebruikstoepassingen elk een afzonderlijke weergave van de gebeurtenisstroom hebben. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Azure Monitor-statistieken en logboeken verbinden met uw gebeurtenishub

Nu moet u uw diagnostische statistieken en logboeken en uw activiteitslogboeken koppelen aan de gebeurtenishub.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnostische statistieken / Diagnostische logboeken](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Diagnostische statistieken en logboeken koppelen aan uw gebeurtenishub

Selecteer een resource van waaruit u metrische gegevens wilt exporteren. Verschillende resourcetypen ondersteunen het exporteren van diagnostische gegevens, waaronder naamruimte van Gebeurtenishubs, Azure Key Vault, Azure IoT Hub en Azure Data Explorer-clusters. In deze zelfstudie gebruiken we een Azure Data Explorer-cluster als onze bron, we controleren queryprestatiestatistieken en resultatenlogboeken.

1. Selecteer uw Kusto-cluster in de Azure-portal.
1. Selecteer **Diagnostische instellingen** en selecteer vervolgens de link **Diagnostische gegevens inschakelen**. 

    ![Diagnostische instellingen](media/ingest-data-no-code/diagnostic-settings.png)

1. Het deelvenster **Diagnostische instellingen** wordt geopend. Voer de volgende stappen uit:
   1. Geef de gegevens uit uw diagnostische logboeken de naam *ADXExportedData*.
   1. Schakel onder **LOG**de selectievakjes **Geslaagdinname** en **FailedIngestion** in.
   1. Schakel **onder METRIC**het selectievakje **Queryprestaties** in.
   1. Schakel het selectievakje **Streamen naar een event hub** in.
   1. Selecteer **Configureren**.

      ![Het deelvenster Diagnostische instellingen](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Configureer in het deelvenster **Een event hub selecteren** hoe gegevens uit diagnostisch logboeken moeten worden geëxporteerd naar de event hub die u hebt gemaakt:
    1. Selecteer in de lijst **Naamruimte van de Event Hub selecteren** de optie *AzureMonitoringData*.
    1. Selecteer *diagnostische gegevens*in de lijst Naam van **de gebeurtenishub** selecteren .
    1. Selecteer in de lijst **Naam van het Event Hub-beleid selecteren** de optie **RootManagerSharedAccessKey**.
    1. Selecteer **OK**.

1. Selecteer **Opslaan**.

# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)
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

Nu moet u de gegevensverbindingen maken voor uw diagnostische statistieken en logboeken en activiteitslogboeken.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>De gegevensverbinding maken voor diagnostische statistieken en logboeken en activiteitslogboeken

1. In het Azure Data Explorer-cluster met de naam *kustodocs* selecteert u **Databases** in het menu links.
1. In het venster **Databases** selecteert u uw *TestDatabase*-database.
1. Selecteer **Gegevensopname** in het menu links.
1. Klik in het venster **Gegevensopname** op **+ Gegevensverbinding toevoegen**.
1. Voer in het venster **Gegevensverbinding** de volgende informatie in:

    ![Event hub-gegevensverbinding](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Diagnostische statistieken / Diagnostische logboeken](#tab/diagnostic-metrics+diagnostic-logs) 

1. Gebruik de volgende instellingen in het venster **Gegevensverbinding**:

    Gegevensbron:

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | **Naam van gegevensverbinding** | *DiagnosticsLogsConnection* | De naam van de verbinding die u wilt maken in Azure Data Explorer.|
    | **Naamruimte voor gebeurtenishub** | *AzureMonitoringData* | De naam die u eerder hebt gekozen om de naamruimte te identificeren. |
    | **Event Hub** | *Diagnostische gegevens* | De Event Hub die u hebt gemaakt. |
    | **Consumentengroep** | *adxpipeline* | De consumentengroep die u hebt gedefinieerd in de gemaakte Event Hub. |
    | | |

    Doeltabel:

    Er zijn twee opties voor de routering: *statische* en *dynamische*. Voor deze zelfstudie gebruikt u statische routering (standaardinstelling), waarbij u de tabelnaam, gegevensindeling en toewijzing opgeeft. Laat **Mijn gegevens omvatten routeringsgegevens** uitgeschakeld.

     **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | **Tabel** | *DiagnosticRawRecords* | De tabel die u hebt gemaakt in de *TestDatabase*-database. |
    | **Gegevensindeling** | *Json* | De indeling die in de tabel wordt gebruikt. |
    | **Kolomtoewijzing** | *DiagnosticRawRecordsMapping* | De toewijzing die u hebt gemaakt in de *TestDatabase-database,* die binnenkomende JSON-gegevens toeschrijft aan de kolomnamen en gegevenstypen van de tabel *DiagnosticRawRecords.*|
    | | |

1. Selecteer **Maken**.  

# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)

1. Gebruik de volgende instellingen in het venster **Gegevensverbinding**:

    Gegevensbron:

    **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | **Naam van gegevensverbinding** | *ActivityLogsConnection* | De naam van de verbinding die u wilt maken in Azure Data Explorer.|
    | **Naamruimte voor gebeurtenishub** | *AzureMonitoringData* | De naam die u eerder hebt gekozen om de naamruimte te identificeren. |
    | **Event Hub** | *insights-operational-logs* | De Event Hub die u hebt gemaakt. |
    | **Consumentengroep** | *$Default* | De standaard consumentengroep. Zo nodig kunt u een andere consumentengroep maken. |
    | | |

    Doeltabel:

    Er zijn twee opties voor de routering: *statische* en *dynamische*. Voor deze zelfstudie gebruikt u statische routering (standaardinstelling), waarbij u de tabelnaam, gegevensindeling en toewijzing opgeeft. Laat **Mijn gegevens omvatten routeringsgegevens** uitgeschakeld.

     **Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**
    |---|---|---|
    | **Tabel** | *ActivityLogsRawRecords* | De tabel die u hebt gemaakt in de *TestDatabase*-database. |
    | **Gegevensindeling** | *Json* | De indeling die in de tabel wordt gebruikt. |
    | **Kolomtoewijzing** | *ActivityLogsRawRecordsMapping* | De toewijzing die u hebt gemaakt in de *TestDatabase*-database, waarmee binnenkomende JSON-gegevens worden toegewezen aan de kolomnamen en gegevenstypen van de tabel *ActivityLogsRawRecords*.|
    | | |

1. Selecteer **Maken**.  
---

## <a name="query-the-new-tables"></a>Query uitvoeren op de nieuwe tabellen

U hebt nu een pijplijn met een gegevensstroom. Opname via het cluster duurt standaard 5 minuten. Laat de gegevensstroom daarom een paar minuten zijn gang gaan voordat u een query gaat uitvoeren.

# <a name="diagnostic-metrics"></a>[Diagnostische statistieken](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>De tabel diagnostische statistieken opvragen

Met de volgende query worden queryduurgegevens geanalyseerd uit diagnostische metrische records in Azure Data Explorer:

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

# <a name="diagnostic-logs"></a>[Diagnostische logboeken](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>De tabel diagnostische logboeken opvragen

Deze pijplijn produceert inname via een gebeurtenishub. U bekijkt de resultaten van deze inname.
In de volgende query wordt geanalyseerd hoeveel inname in `Database`een `Table` `IngestionSourcePath` minuut is gegenereerd, inclusief een voorbeeld van , en voor elk interval:

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

# <a name="activity-logs"></a>[Activiteitenlogboeken](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>De tabel activiteitslogboeken opvragen

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

* Lees meer query's voor de gegevens die u uit Azure Data Explorer hebt gehaald met [Schrijfquery's voor Azure Data Explorer.](write-queries.md)
* [Innamebewerkingen van Azure Data Explorer controleren met diagnostische logboeken](using-diagnostic-logs.md)
* [Metrische gegevens gebruiken om clusterstatus te bewaken](using-metrics.md)
