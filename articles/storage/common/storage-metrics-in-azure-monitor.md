---
title: Azure Storage metrische gegevens in Azure Monitor | Microsoft Docs
description: Meer informatie over de nieuwe metrische gegevens die worden aangeboden via Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: f4c76539f6b14c3fb5ad5dba8fc8c8df514edfe4
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526838"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metrische gegevens van Azure Storage in Azure Monitor

Met metrische gegevens over Azure Storage kunt u gebruiks trends en traceer aanvragen analyseren en problemen met uw opslag account vaststellen.

Azure Monitor biedt uniforme gebruikers interfaces voor bewaking in verschillende Azure-Services. Zie [Azure monitor](../../monitoring-and-diagnostics/monitoring-overview.md)voor meer informatie. Azure Storage integreert Azure Monitor door metrische gegevens naar het Azure Monitor platform te verzenden.

## <a name="access-metrics"></a>Toegang tot metrische gegevens

Azure Monitor biedt meerdere manieren voor toegang tot metrische gegevens. U kunt ze openen via de [Azure Portal](https://portal.azure.com), de Azure monitor-API'S (rest en .net) en analyse oplossingen zoals Event hubs. Zie [Azure monitor metrische](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)gegevens voor meer informatie.

Metrieken zijn standaard ingeschakeld en u hebt toegang tot de afgelopen 93 dagen aan gegevens. Als u behouden van gegevens voor een langere periode wilt, kunt u metrische gegevens om een Azure Storage-account te archiveren. Dit wordt geconfigureerd in [Diagnostische instellingen](../../azure-monitor/platform/platform-logs-overview.md) in azure monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Toegang tot metrische gegevens in de Azure Portal

U kunt metrische gegevens gedurende een bepaalde periode bewaken in de Azure Portal. In het volgende voor beeld ziet u hoe **trans acties** worden weer gegeven op account niveau.

![scherm opname van de toegang tot metrische gegevens in de Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Voor metrische gegevens ondersteunen dimensies, kunt u metrische gegevens filteren met de gewenste dimensie waarde. In het volgende voor beeld ziet u hoe **trans acties** worden weer gegeven op account niveau voor een specifieke bewerking door waarden te selecteren voor de **API-naam** dimensie.

![scherm opname van de toegang tot metrische gegevens met dimensie in de Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Toegang tot metrische gegevens met de REST API

Azure Monitor biedt [rest-api's](/rest/api/monitor/) om metrische definitie en waarden te lezen. In deze sectie wordt beschreven hoe u de metrische gegevens van de opslag kunt lezen. De resource-ID wordt gebruikt in alle REST API'S. Meer informatie vindt u in de informatie over de resource-ID voor services in Storage.

In het volgende voor beeld ziet u hoe u [ArmClient](https://github.com/projectkudu/ARMClient) gebruikt op de opdracht regel om het testen met de rest API te vereenvoudigen.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Definitie van de metrische gegevens op account niveau met de REST API

In het volgende voor beeld ziet u hoe metrische definitie op account niveau wordt weer gegeven:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Als u de metrische definities voor blob, tabel, bestand of wachtrij wilt weer geven, moet u voor elke service een andere resource-id opgeven met de API.

Het antwoord bevat de metrische definitie in de JSON-indeling:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Meet waarden op account niveau lezen met de REST API

In het volgende voor beeld ziet u hoe u metrische gegevens kunt lezen op account niveau:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Als u in het bovenstaande voor beeld metrische waarden voor blob, tabel, bestand of wachtrij wilt lezen, moet u voor elke service een andere resource-id opgeven met de API.

Het volgende antwoord bevat metrische waarden in JSON-indeling:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Toegang tot metrische gegevens met de .NET SDK

Azure Monitor biedt [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) de definitie en waarden van metrische gegevens kunnen lezen. De [voorbeeld code](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) laat zien hoe u de SDK met verschillende para meters kunt gebruiken. U moet `0.18.0-preview` of een hogere versie gebruiken voor metrische gegevens over de opslag. Resource-ID wordt gebruikt in .NET SDK. Meer informatie vindt u in de informatie over de resource-ID voor services in Storage.

In het volgende voor beeld ziet u hoe u Azure Monitor .NET SDK gebruikt om metrische gegevens over de opslag te lezen.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Definitie van de metrische gegevens op account niveau weer geven met de .NET SDK

In het volgende voor beeld ziet u hoe metrische definitie op account niveau wordt weer gegeven:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Als u de metrische definities voor blob, tabel, bestand of wachtrij wilt weer geven, moet u voor elke service een andere resource-id opgeven met de API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Metrische waarden lezen met de .NET SDK

In het volgende voor beeld ziet u hoe `UsedCapacity` gegevens worden gelezen op account niveau:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Als u in het bovenstaande voor beeld metrische waarden voor blob, tabel, bestand of wachtrij wilt lezen, moet u voor elke service een andere resource-id opgeven met de API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Multi-dimensionale metrische waarden lezen met de .NET SDK

Voor multidimensionale metrieken moet u het filter voor meta gegevens definiëren als u metrische gegevens op specifieke dimensie waarde wilt lezen.

In het volgende voor beeld ziet u hoe u metrische gegevens kunt lezen op de metriek met ondersteuning voor meerdere dimensies:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Resource-ID voor services in Azure Storage

De resource-ID is een unieke id van een resource in Azure. Wanneer u de Azure Monitor-REST API gebruikt om metrische definities of waarden te lezen, moet u Resource-ID gebruiken voor de resource waarop u wilt werken. De resource-ID-sjabloon heeft de volgende indeling:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Opslag biedt metrische gegevens op het niveau van het opslag account en het service niveau met Azure Monitor. U kunt bijvoorbeeld metrische gegevens ophalen voor alleen Blob Storage. Elk niveau heeft een eigen Resource-ID, die wordt gebruikt om de metrische gegevens voor dat niveau op te halen.

### <a name="resource-id-for-a-storage-account"></a>Resource-ID voor een opslag account

Hieronder ziet u de indeling voor het opgeven van de resource-ID voor een opslag account.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Resource-ID voor de opslag Services

Hieronder ziet u de indeling voor het opgeven van de resource-ID voor elk van de opslag Services.

* Resource-ID Blob service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Resource-ID Table service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Resource-ID Queue-service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Resource-ID van bestands service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Resource-ID in Azure Monitor REST API

Hieronder ziet u het patroon dat wordt gebruikt bij het aanroepen van de Azure Monitor REST API.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metrische gegevens over capaciteit
Waarden voor capaciteits metrieken worden elk uur verzonden naar Azure Monitor. De waarden worden dagelijks vernieuwd. De tijdgranulariteit definieert het tijds interval waarvoor metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens over capaciteit zijn één uur (PT1H).

Azure Storage biedt de volgende metrische gegevens over capaciteit in Azure Monitor.

### <a name="account-level"></a>Account niveau

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| UsedCapacity | De hoeveelheid opslag die wordt gebruikt door het opslag account. Voor standaardopslagaccounts is dit de som van de capaciteit die wordt gebruikt door blob, table, file en queue. Voor premium-opslagaccounts en blob-opslagaccounts is dit hetzelfde als BlobCapacity. <br/><br/> Eenheid: Bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| BlobCapacity | Het totaal van de Blob-opslag die in het opslag account wordt gebruikt. <br/><br/> Eenheid: Bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 <br/> Dimensies: **BlobType**en **BlobTier** ([definitie](#metrics-dimensions)) |
| BlobCount    | Het aantal BLOB-objecten dat is opgeslagen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 <br/> Dimensies: **BlobType**en **BlobTier** ([definitie](#metrics-dimensions)) |
| ContainerCount    | Het aantal containers in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| IndexCapacity     | De hoeveelheid opslag die wordt gebruikt door ADLS Gen2 hiërarchische index <br/><br/> Eenheid: Bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

### <a name="table-storage"></a>Table Storage

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| TableCapacity | De hoeveelheid tabel opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: Bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| TableCount   | Het aantal tabellen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| TableEntityCount | Het aantal tabel entiteiten in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| QueueCapacity | De hoeveelheid wachtrij opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: Bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| QueueCount   | Het aantal wacht rijen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| QueueMessageCount | Het aantal niet-verlopen wachtrij berichten in het opslag account. <br/><br/>Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

### <a name="file-storage"></a>File Storage

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| FileCapacity | De hoeveelheid bestands opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: Bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| FileCount   | Het aantal bestanden in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| FileShareCount | Het aantal bestands shares in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

## <a name="transaction-metrics"></a>Metrische gegevens van trans actie

Metrische gegevens over trans acties worden verzonden voor elke aanvraag naar een opslag account van Azure Storage naar Azure Monitor. In het geval van geen activiteit in uw opslag account, worden er geen gegevens in de periode weer gegeven. Alle metrische gegevens over trans acties zijn beschikbaar op zowel account-als service niveau (Blob-opslag, tabel opslag, Azure Files en wachtrij opslag). De tijd korrel definieert het tijds interval dat metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens van de trans actie zijn PT1H en PT1M.

Azure Storage levert de volgende metrische gegevens over trans acties in Azure Monitor.

| Naam van meetwaarde | Beschrijving |
| ------------------- | ----------------- |
| Transacties | Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. <br/><br/> Eenheid: aantal <br/> Aggregatietype: totaal <br/> Toepasselijke dimensies: ResponseType, geotype, ApiName en Authentication ([definitie](#metrics-dimensions))<br/> Waarde-voor beeld: 1024 |
| Inkomend verkeer | De hoeveelheid inkomende gegevens. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| Uitgaand verkeer | De hoeveelheid uitgaande gegevens. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: totaal <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| SuccessServerLatency | De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency. <br/><br/> Eenheid: milliseconden <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| SuccessE2ELatency | De gemiddelde end-to-end-latentie van geslaagde aanvragen aan een opslagservice of de opgegeven API-bewerking. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen. <br/><br/> Eenheid: milliseconden <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| Beschikbaarheid | Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde voor het totale aantal factureerbare aanvragen te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. <br/><br/> Eenheid: percentage <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 99,99 |

## <a name="metrics-dimensions"></a>Metrische gegevens over dimensies

Azure Storage ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor.

| Dimensie naam | Beschrijving |
| ------------------- | ----------------- |
| **BlobType** | Het type BLOB voor alleen metrische gegevens van blobs. De ondersteunde waarden zijn **BlockBlob**, **PageBlob**en **Azure data Lake Storage**. Toevoeg-blob is opgenomen in BlockBlob. |
| **BlobTier** | Azure Storage biedt verschillende toegangs lagen, waarmee u gegevens van blob-objecten op de meest rendabele manier kunt opslaan. Meer weer geven in [Azure Storage BLOB-laag](../blobs/storage-blob-storage-tiers.md). De ondersteunde waarden zijn onder andere: <br/> <li>**Warm**: warme laag</li> <li>**Cool**: cool-laag</li> <li>**Archief**: laag van archief</li> <li>**Premium**: Premium-laag voor blok-BLOB</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: laag typen voor Premium-pagina-BLOB</li> <li>**Standaard**: laag type voor de standaard pagina-BLOB</li> <li>**Ongetierd**: laag type voor algemeen gebruik v1-opslag account</li> |
| **Geotype** | Trans actie van het primaire of secundaire cluster. De beschik bare waarden zijn onder andere **primaire** en **secundaire**. Dit is van toepassing op lees toegang geografisch redundante opslag (RA-GRS) bij het lezen van objecten van een secundaire Tenant. |
| **ResponseType** | Antwoord type voor de trans actie. De beschikbare waarden zijn onder meer: <br/><br/> <li>**ServerOtherError**: alle andere fouten aan de server zijde behalve beschreven </li> <li>**ServerBusyError**: een geverifieerde aanvraag die een HTTP 503-status code heeft geretourneerd. </li> <li>**ServerTimeoutError**: er is een time-out voor geverifieerde aanvraag ontvangen die een HTTP 500-status code heeft geretourneerd. De time-out is opgetreden vanwege een serverfout. </li> <li>**AuthorizationError**: een geverifieerde aanvraag die is mislukt vanwege ongeoorloofde toegang tot gegevens of een autorisatie fout. </li> <li>**NetworkError**: geverifieerde aanvraag die is mislukt vanwege netwerk fouten. Treedt meestal op als dooreen client voortijdig een verbinding wordt verbroken voordat de time-out voorbij is. </li>  <li>**ClientAccountBandwidthThrottlingError**: de aanvraag wordt beperkt over de band breedte voor het overschrijden van de [schaalbaarheids limieten van het opslag account](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: de aanvraag wordt beperkt op aanvraag snelheid voor overschrijding van de [schaalbaarheids limieten van het opslag account](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: andere fout met beperking aan client zijde. ClientAccountBandwidthThrottlingError en ClientAccountRequestThrottlingError zijn uitgesloten.</li> <li>**ClientTimeoutError**: er is een time-out voor geverifieerde aanvraag ontvangen die een HTTP 500-status code heeft geretourneerd. Als de time-out van het clientnetwerk of van de aanvraag is ingesteld op een lagere waarde dan door de opslagservice wordt verwacht, is er sprake van een verwachte time-out. Anders wordt deze als ServerTimeoutError gerapporteerd.</li> </li> <li>**ClientOtherError**: alle andere fouten aan de client zijde behalve de beschreven. </li> <li>**Geslaagd**: aanvraag geslaagd</li> <li> **SuccessWithThrottling**: de aanvraag is voltooid wanneer een SMB-client wordt beperkt tijdens de eerste poging (en), maar na nieuwe pogingen slaagt.</li> |
| **ResponseType** | Antwoord type voor de trans actie. De beschikbare waarden zijn onder meer: <br/><br/> <li>**ServerOtherError**: alle andere fouten aan de server zijde behalve beschreven </li> <li>**ServerBusyError**: een geverifieerde aanvraag die een HTTP 503-status code heeft geretourneerd. </li> <li>**ServerTimeoutError**: er is een time-out voor geverifieerde aanvraag ontvangen die een HTTP 500-status code heeft geretourneerd. De time-out is opgetreden vanwege een serverfout. </li> <li>**AuthorizationError**: een geverifieerde aanvraag die is mislukt vanwege ongeoorloofde toegang tot gegevens of een autorisatie fout. </li> <li>**NetworkError**: geverifieerde aanvraag die is mislukt vanwege netwerk fouten. Treedt meestal op als dooreen client voortijdig een verbinding wordt verbroken voordat de time-out voorbij is. </li>  <li>**ClientAccountBandwidthThrottlingError**: de aanvraag wordt beperkt over de band breedte voor het overschrijden van de [schaalbaarheids limieten van het opslag account](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: de aanvraag wordt beperkt op aanvraag snelheid voor overschrijding van de [schaalbaarheids limieten van het opslag account]().<li>**ClientThrottlingError**: andere fout met beperking aan client zijde. ClientAccountBandwidthThrottlingError en ClientAccountRequestThrottlingError zijn uitgesloten.</li> <li>**ClientTimeoutError**: er is een time-out voor geverifieerde aanvraag ontvangen die een HTTP 500-status code heeft geretourneerd. Als de time-out van het clientnetwerk of van de aanvraag is ingesteld op een lagere waarde dan door de opslagservice wordt verwacht, is er sprake van een verwachte time-out. Anders wordt deze als ServerTimeoutError gerapporteerd.</li> </li> <li>**ClientOtherError**: alle andere fouten aan de client zijde behalve de beschreven. </li> <li>**Geslaagd**: aanvraag geslaagd</li> <li> **SuccessWithThrottling**: de aanvraag is voltooid wanneer een SMB-client wordt beperkt tijdens de eerste poging (en), maar na nieuwe pogingen slaagt.</li> |
| **ApiName** | De naam van de bewerking. Bijvoorbeeld: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Zie [document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor alle bewerkings namen. |
| **Verificatie** | Het verificatie type dat wordt gebruikt in trans acties. De beschikbare waarden zijn onder meer: <br/> <li>**AccountKey**: de trans actie is geverifieerd met de sleutel van het opslag account.</li> <li>**SAS**: de trans actie wordt geverifieerd met hand tekeningen voor gedeelde toegang.</li> <li>**OAuth**: de trans actie wordt geverifieerd met OAuth-toegangs tokens.</li> <li>**Anoniem**: de trans actie is anoniem aangevraagd. Het bevat geen Preflight-aanvragen.</li> <li>**AnonymousPreflight**: de trans actie is een Preflight-aanvraag.</li> |

Voor de metrische gegevens die maat staven ondersteunen, moet u de dimensie waarde opgeven om de bijbehorende metrische waarden weer te geven. Als u bijvoorbeeld de **transactie** waarde voor geslaagde reacties bekijkt, moet u de **ResponseType** -dimensie filteren op **geslaagd**. Of als u de **BlobCount** -waarde voor blok-BLOB bekijkt, moet u de dimensie **BlobType** filteren met **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuïteit van de service van verouderde metrische gegevens

Verouderde metrische gegevens zijn beschikbaar in combi natie met Azure Monitor beheerde metrische gegevens. De ondersteuning blijft hetzelfde totdat Azure Storage de service op verouderde metrische gegevens beëindigt.

## <a name="faq"></a>Veelgestelde vragen

**Ondersteunt de nieuwe metrische gegevens het klassieke opslag account?**

Nee, nieuwe metrische gegevens in Azure Monitor bieden alleen ondersteuning voor Azure Resource Manager-opslag accounts. Als u metrische gegevens voor opslag accounts wilt gebruiken, moet u migreren naar Azure Resource Manager Storage-account. Zie [migreren naar Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Ondersteunt Azure Storage metrische gegevens voor Managed Disks of niet-beheerde schijven?**

Nee, Azure Compute ondersteunt de metrische gegevens op schijven. Zie het [artikel](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) voor meer informatie.

**Hoe kunt u klassieke metrische gegevens toewijzen en migreren met nieuwe metrische gegevens?**

U vindt een gedetailleerde toewijzing tussen de klassieke metrische gegevens en nieuwe meet waarden in [Azure Storage metrische gegevens migratie](./storage-metrics-migration.md).

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
