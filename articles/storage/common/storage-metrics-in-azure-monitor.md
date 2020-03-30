---
title: Azure Storage-statistieken in Azure Monitor | Microsoft Documenten
description: Meer informatie over de nieuwe statistieken die worden aangeboden vanuit Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247092"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metrische gegevens van Azure Storage in Azure Monitor

Met statistieken over Azure Storage u gebruikstrends analyseren, aanvragen traceren en problemen met uw opslagaccount diagnosticeren.

Azure Monitor biedt uniforme gebruikersinterfaces voor bewaking voor verschillende Azure-services. Zie [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)voor meer informatie. Azure Storage integreert Azure Monitor door metrische gegevens naar het Azure Monitor-platform te verzenden.

## <a name="access-metrics"></a>Toegangsstatistieken

Azure Monitor biedt meerdere manieren om toegang te krijgen tot statistieken. U hebt toegang tot ze via de [Azure-portal,](https://portal.azure.com)de Azure Monitor API's (REST en .NET) en analyseoplossingen zoals Gebeurtenishubs. Zie [Azure Monitor Metrics](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)voor meer informatie .

Statistieken zijn standaard ingeschakeld en u hebt toegang tot de afgelopen 93 dagen aan gegevens. Als u gegevens voor een langere periode moet bewaren, u metrische gegevens archiveren naar een Azure Storage-account. Dit is geconfigureerd in [diagnostische instellingen](../../azure-monitor/platform/platform-logs-overview.md) in Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Toegangsstatistieken in de Azure-portal

U statistieken in de loop van de tijd controleren in de Azure-portal. In het volgende voorbeeld ziet u hoe **u Transacties** op accountniveau weergeven.

![schermafbeelding van toegang tot statistieken in de Azure-portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Voor metrische gegevens die dimensies ondersteunen, u metrische filters filteren met de gewenste dimensiewaarde. In het volgende voorbeeld ziet u hoe **u Transacties** op accountniveau weergeven op een specifieke bewerking door waarden te selecteren voor de dimensie **API-naam.**

![schermafbeelding van toegang tot metrische gegevens met dimensie in de Azure-portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Toegang tot statistieken met de REST API

Azure Monitor biedt [REST API's](/rest/api/monitor/) om metrische definitie en waarden te lezen. In deze sectie ziet u hoe u de opslagstatistieken lezen. Resource-ID wordt gebruikt in alle REST API's. Lees Resource-ID voor services in Opslag voor meer informatie.

In het volgende voorbeeld ziet u hoe [u ArmClient](https://github.com/projectkudu/ARMClient) op de opdrachtregel gebruiken om het testen met de REST API te vereenvoudigen.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Metrische definitie op accountniveau met de REST-API

In het volgende voorbeeld ziet u hoe u metrische definitie op accountniveau weergeven:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Als u de metrische definities voor blob, tabel, bestand of wachtrij wilt weergeven, moet u voor elke service met de API verschillende resource-id's opgeven.

Het antwoord bevat de metrische definitie in JSON-indeling:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Metrische waarden op accountniveau lezen met de REST-API

In het volgende voorbeeld ziet u hoe u metrische gegevens op accountniveau lezen:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Als u in bovenstaande voorbeeld metrische waarden voor blob, tabel, bestand of wachtrij wilt lezen, moet u voor elke service met de API verschillende resource-id's opgeven.

Het volgende antwoord bevat metrische waarden in de JSON-notatie:

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

### <a name="access-metrics-with-the-net-sdk"></a>Toegang tot statistieken met de .NET SDK

Azure Monitor biedt [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) om metrische definitie en waarden te lezen. De [voorbeeldcode](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) laat zien hoe u de SDK met verschillende parameters gebruiken. U moet `0.18.0-preview` de of nieuwe versie gebruiken voor opslagstatistieken. Resource-id wordt gebruikt in .NET SDK. Lees Resource-ID voor services in Opslag voor meer informatie.

In het volgende voorbeeld ziet u hoe u Azure Monitor .NET SDK gebruiken om opslagstatistieken te lezen.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Metrische definitie op accountniveau met de .NET SDK

In het volgende voorbeeld ziet u hoe u metrische definitie op accountniveau weergeven:

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

Als u de metrische definities voor blob, tabel, bestand of wachtrij wilt weergeven, moet u voor elke service met de API verschillende resource-id's opgeven.

#### <a name="read-metric-values-with-the-net-sdk"></a>Metrische waarden lezen met de .NET SDK

In het volgende voorbeeld `UsedCapacity` ziet u hoe u gegevens op accountniveau lezen:

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

Als u in bovenstaande voorbeeld metrische waarden voor blob, tabel, bestand of wachtrij wilt lezen, moet u voor elke service met de API verschillende resource-id's opgeven.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Multidimensionale metrische waarden lezen met de .NET SDK

Voor multidimensionale metrische gegevens moet u metagegevensfilter definiëren als u metrische gegevens over de specifieke dimensiewaarde wilt lezen.

In het volgende voorbeeld ziet u hoe u metrische gegevens leest over de metrische ondersteuning voor meerdere dimensies:

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Resource-id voor services in Azure Storage begrijpen

Resource-id is een unieke id van een bron in Azure. Wanneer u de Azure Monitor REST API gebruikt om metrische gegevensdefinities of waarden te lezen, moet u resource-id gebruiken voor de resource waarop u van plan bent te werken. De sjabloon resource-id volgt deze indeling:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Storage biedt statistieken op zowel opslagaccountniveau als serviceniveau met Azure Monitor. U bijvoorbeeld statistieken ophalen voor alleen Blob-opslag. Elk niveau heeft zijn eigen resource-ID, die wordt gebruikt om de statistieken voor alleen dat niveau op te halen.

### <a name="resource-id-for-a-storage-account"></a>Resource-id voor een opslagaccount

Als volgt ziet u de indeling voor het opgeven van de Resource-id voor een opslagaccount.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Resource-id voor de opslagservices

In de volgende gegevens ziet u de indeling voor het opgeven van de resource-id voor elk van de opslagservices.

* Klodderservicebron-id
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Resource-id van tabelservice
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Resource-id voor wachtrijservice
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Bron-id van bestandsservice
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Resource-id in Azure Monitor REST API

Het volgende toont het patroon dat wordt gebruikt bij het aanroepen van de Azure Monitor REST API.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metrische capaciteitsgegevens
Waarden voor capaciteitsstatistieken worden elk uur naar Azure Monitor verzonden. De waarden worden dagelijks vernieuwd. De tijdskorrel definieert het tijdsinterval waarvoor metrische waarden worden weergegeven. De ondersteunde tijdskorrel voor alle capaciteitsstatistieken is een uur (PT1H).

Azure Storage biedt de volgende capaciteitsstatistieken in Azure Monitor.

### <a name="account-level"></a>Accountniveau

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Gebruikte capaciteit | De hoeveelheid opslagruimte die door de opslagrekening wordt gebruikt. Voor standaardopslagaccounts is dit de som van de capaciteit die wordt gebruikt door blob, table, file en queue. Voor premium-opslagaccounts en blob-opslagaccounts is dit hetzelfde als BlobCapacity. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| BlobCapaciteit | Het totaal aantal Blob-opslag dat wordt gebruikt in het opslagaccount. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 <br/> Afmetingen: **BlobType**en **BlobTier** ([Definitie](#metrics-dimensions)) |
| BlobCount    | Het aantal blobobjecten dat is opgeslagen in het opslagaccount. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 <br/> Afmetingen: **BlobType**en **BlobTier** ([Definitie](#metrics-dimensions)) |
| ContainerCount ContainerCount    | Het aantal containers op de opslagrekening. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| Indexcapaciteit     | De hoeveelheid opslagruimte die wordt gebruikt door de Hiërarchische Index van ADLS Gen2 <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |

### <a name="table-storage"></a>Table Storage

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Tabelcapaciteit | De hoeveelheid tabelopslag die door de opslagrekening wordt gebruikt. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| Tabeltelling   | Het aantal tabellen in het opslagaccount. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| Tabelentiteittelling | Het aantal tabelentiteiten in het opslagaccount. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Wachtrijcapaciteit | De hoeveelheid wachtrijopslag die wordt gebruikt door het opslagaccount. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| Aantal wachtrijen   | Het aantal wachtrijen in het opslagaccount. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| Aantal queuemessage's | Het aantal niet-verlopen wachtrijberichten in het opslagaccount. <br/><br/>Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |

### <a name="file-storage"></a>File Storage

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Bestandscapaciteit | De hoeveelheid bestandsopslag die wordt gebruikt door de opslagrekening. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| FileCount   | Het aantal bestanden in het opslagaccount. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |
| FileShareCount | Het aantal bestandsshares op de opslagrekening. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Gemiddeld <br/> Waardevoorbeeld: 1024 |

## <a name="transaction-metrics"></a>Metrische gegevens voor transacties

Transactiestatistieken worden op elk verzoek naar een opslagaccount van Azure Storage naar Azure Monitor uitgezonden. In het geval van geen activiteit op uw opslagaccount, zijn er geen gegevens over transactiestatistieken in de periode. Alle transactiestatistieken zijn beschikbaar op account- en serviceniveau (Blob-opslag, tabelopslag, Azure-bestanden en wachtrijopslag). De tijdskorrel definieert het tijdsinterval dat metrische waarden worden weergegeven. De ondersteunde tijdkorrels voor alle transactiestatistieken zijn PT1H en PT1M.

Azure Storage biedt de volgende transactiestatistieken in Azure Monitor.

| Metrische naam | Beschrijving |
| ------------------- | ----------------- |
| Transacties | Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. <br/><br/> Eenheid: Aantal <br/> Aggregatietype: Totaal <br/> Toepasselijke dimensies: ResponseType, GeoType, ApiName en Authentication[(Definitie)](#metrics-dimensions)<br/> Waardevoorbeeld: 1024 |
| Inkomend verkeer | De hoeveelheid inkomende gegevens. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Toepasselijke dimensies: GeoType, ApiName en Verificatie ([Definitie](#metrics-dimensions)) <br/> Waardevoorbeeld: 1024 |
| Uitgaand verkeer | De hoeveelheid uitgaande gegevens. Dit nummer omvat een uitgang naar een externe client vanuit Azure Storage en een uitgang binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer. <br/><br/> Eenheid: Bytes <br/> Aggregatietype: Totaal <br/> Toepasselijke dimensies: GeoType, ApiName en Verificatie ([Definitie](#metrics-dimensions)) <br/> Waardevoorbeeld: 1024 |
| SuccessServerLatentie | De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency. <br/><br/> Eenheid: Milliseconden <br/> Aggregatietype: Gemiddeld <br/> Toepasselijke dimensies: GeoType, ApiName en Verificatie ([Definitie](#metrics-dimensions)) <br/> Waardevoorbeeld: 1024 |
| SuccesE2ELatentie | De gemiddelde end-to-end-latentie van geslaagde aanvragen aan een opslagservice of de opgegeven API-bewerking. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen. <br/><br/> Eenheid: Milliseconden <br/> Aggregatietype: Gemiddeld <br/> Toepasselijke dimensies: GeoType, ApiName en Verificatie ([Definitie](#metrics-dimensions)) <br/> Waardevoorbeeld: 1024 |
| Beschikbaarheid | Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde voor het totale aantal factureerbare aanvragen te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. <br/><br/> Eenheid: Percentage <br/> Aggregatietype: Gemiddeld <br/> Toepasselijke dimensies: GeoType, ApiName en Verificatie ([Definitie](#metrics-dimensions)) <br/> Waardevoorbeeld: 99,99 |

## <a name="metrics-dimensions"></a>Afmetingen met statistieken

Azure Storage ondersteunt volgende dimensies voor metrische gegevens in Azure Monitor.

| Dimensienaam | Beschrijving |
| ------------------- | ----------------- |
| **BlobType** | Het type blob voor alleen Blob-statistieken. De ondersteunde waarden zijn **BlockBlob,** **PageBlob**en **Azure Data Lake Storage**. Blob toevoegen is opgenomen in BlockBlob. |
| **BlobTier** | Azure-opslag biedt verschillende toegangslagen, waarmee u blob-objectgegevens op de meest kosteneffectieve manier opslaan. Bekijk meer in [de bloblaag azure storage](../blobs/storage-blob-storage-tiers.md). De ondersteunde waarden zijn: <br/> <li>**Hot**: Hot tier</li> <li>**Cool**: Cool tier</li> <li>**Archief**: Archieflaag</li> <li>**Premium:** Premium-laag voor blokblob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Laagtypen voor blob met premium pagina's</li> <li>**Standaard:** laagtype voor standaardpagina Blob</li> <li>**Untiered**: Tier type voor algemene doeleinden v1 opslagaccount</li> |
| **GeoType (GeoType)** | Transactie vanuit het primaire of secundaire cluster. De beschikbare waarden omvatten **primair** en **secundair**. Het is van toepassing op Read Access Geo Redundant Storage (RA-GRS) bij het lezen van objecten van secundaire tenant. |
| **ResponseType** | Type transactierespons. De beschikbare waarden zijn onder meer: <br/><br/> <li>**ServerOtherError:** Alle andere serverfouten behalve beschreven fouten </li> <li>**ServerBusyError:** geverifieerde aanvraag die een HTTP 503-statuscode heeft geretourneerd. </li> <li>**ServerTimeoutError:** getimede geverifieerde aanvraag die een HTTP 500-statuscode heeft geretourneerd. De time-out is opgetreden vanwege een serverfout. </li> <li>**AuthorizationError:** Geverifieerde aanvraag die is mislukt als gevolg van ongeautoriseerde toegang tot gegevens of een autorisatiefout. </li> <li>**NetworkError:** geverifieerde aanvraag die is mislukt als gevolg van netwerkfouten. Treedt meestal op als dooreen client voortijdig een verbinding wordt verbroken voordat de time-out voorbij is. </li>  <li>**ClientAccountBandwidthThrottlingError**: De aanvraag wordt beperkt op bandbreedte voor het overschrijden van [schaalbaarheidslimieten voor opslagaccount.](scalability-targets-standard-account.md)</li><li>**ClientAccountRequestThrottlingError**: De aanvraag wordt beperkt op aanvraagsnelheid voor het overschrijden van [schaalbaarheidslimieten](scalability-targets-standard-account.md)voor opslagaccount .<li>**ClientThrottlingError:** andere fout met beperking aan de clientzijde. ClientAccountBandwidthThrottlingError en ClientAccountRequestThrottlingError zijn uitgesloten.</li> <li>**ClientTimeoutError:** een getimede geverifieerde aanvraag die een HTTP 500-statuscode heeft geretourneerd. Als de time-out van het clientnetwerk of van de aanvraag is ingesteld op een lagere waarde dan door de opslagservice wordt verwacht, is er sprake van een verwachte time-out. Anders wordt deze als ServerTimeoutError gerapporteerd.</li> </li> <li>**ClientOtherError:** Alle andere fouten aan de clientzijde, behalve beschreven. </li> <li>**Succes**: Succesvolle aanvraag</li> <li> **SuccessWithThrottling**: Succesvolle aanvraag wanneer een SMB-client wordt beperkt in de eerste poging(en), maar slaagt na pogingen.</li> |
| **ApiName** | De naam van de operatie. Bijvoorbeeld: <br/> <li>**Container maken**</li> <li>**DeleteBlob**</li> <li>**GetBlob (GetBlob)**</li> Zie [document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor alle bewerkingsnamen . |
| **Verificatie** | Verificatietype dat wordt gebruikt in transacties. De beschikbare waarden zijn onder meer: <br/> <li>**AccountKey:** De transactie wordt geverifieerd met de opslagrekeningsleutel.</li> <li>**SAS**: De transactie is geverifieerd met handtekeningen voor gedeelde toegang.</li> <li>**OAuth**: De transactie is geverifieerd met OAuth-toegangstokens.</li> <li>**Anoniem**: De transactie wordt anoniem aangevraagd. Het bevat geen preflight-aanvragen.</li> <li>**AnoniemPreflight**: De transactie is preflight aanvraag.</li> |

Voor de statistieken die dimensies ondersteunen, moet u de dimensiewaarde opgeven om de bijbehorende metrische waarden weer te geven. Als u bijvoorbeeld de waarde **van transacties** voor succesvolle reacties bekijkt, moet u de dimensie **ResponseType** filteren met **Succes**. Of als u de waarde **blobcount** voor Blokblob bekijkt, moet u de **blobtypedimensie** filteren met **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Servicecontinuïteit van legacy metrics

Oudere statistieken zijn beschikbaar parallel met azure monitor beheerde statistieken. De ondersteuning blijft hetzelfde totdat Azure Storage de service beëindigt op oudere statistieken.

## <a name="faq"></a>Veelgestelde vragen

**Ondersteunen nieuwe statistieken classic storage-account?**

Nee, nieuwe statistieken in Azure Monitor ondersteunen alleen Azure Resource Manager-opslagaccounts. Als u metrische gegevens wilt gebruiken voor opslagaccounts, moet u migreren naar het Azure Resource Manager Storage-account. Zie [Migreren naar Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Ondersteunt Azure Storage metrische gegevens voor beheerde schijven of niet-beheerde schijven?**

Nee, Azure Compute ondersteunt de statistieken op schijven. Zie [artikel](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) voor meer informatie.

**Hoe klassieke statistieken in kaart brengen en migreren met nieuwe statistieken?**

U vindt gedetailleerde toewijzing tussen klassieke statistieken en nieuwe statistieken in [azure storage metrics migration](./storage-metrics-migration.md).

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
