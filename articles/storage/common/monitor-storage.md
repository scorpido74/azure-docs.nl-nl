---
title: Azure Storage bewaken | Microsoft Docs
description: Meer informatie over het bewaken van de prestaties en beschik baarheid van Azure Storage.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring
ms.openlocfilehash: 2f6320bf4606dc9357ac676b3bc5d7421b24a36c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86182709"
---
# <a name="monitor-azure-storage"></a>Azure Storage bewaken

Wanneer u essentiële toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze bronnen controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure Storage en hoe u de functies van Azure Monitor kunt gebruiken om waarschuwingen voor deze gegevens te analyseren.

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Zie [Deze pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)als u zich wilt inschrijven voor de preview-versie. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen en tabellen. Deze functie is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Overzicht van monitor

De **overzichts** pagina in de Azure portal voor elke opslag resource bevat een korte weer gave van het resource gebruik, zoals aanvragen en facturering per uur. Deze informatie is nuttig, maar er is slechts een kleine hoeveelheid bewakings gegevens beschikbaar. Een deel van deze gegevens wordt automatisch verzameld en is beschikbaar voor analyse zodra u de opslag bron maakt. U kunt extra typen gegevens verzameling inschakelen met een bepaalde configuratie.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Storage maakt bewakings gegevens door gebruik te maken van [Azure monitor](../../azure-monitor/overview.md). Dit is een volledige stack monitoring-service in Azure. Azure Monitor biedt een volledige set functies voor het bewaken van uw Azure-resources en-resources in andere Clouds en on-premises. 

Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md)voor meer informatie over Azure monitor. In het artikel worden de volgende onderwerpen beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld uit Azure Storage. Voor beelden laten zien hoe u gegevens verzameling kunt configureren en deze gegevens kunt analyseren met Azure-hulpprogram ma's.

## <a name="monitor-data-from-azure-storage"></a>Gegevens van Azure Storage controleren

Azure Storage worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources, die worden beschreven in [gegevens van Azure-resources bewaken](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Zie [Azure Storage monitoring data Reference](monitor-storage-reference.md)(Engelstalig) voor meer informatie over de logboeken en metrische gegevens die door Azure Storage zijn gemaakt.

Metrische gegevens en Logboeken in Azure Monitor ondersteunen alleen Azure Resource Manager opslag accounts. Azure Monitor biedt geen ondersteuning voor klassieke opslag accounts. Als u metrische gegevens of logboeken wilt gebruiken in een klassiek opslag account, moet u migreren naar een Azure Resource Manager Storage-account. Zie [migreren naar Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Als u wilt, kunt u de klassieke metrische gegevens en logboeken blijven gebruiken. In feite zijn klassieke metrische gegevens en logboeken beschikbaar in combi natie met metrische gegevens en Logboeken in Azure Monitor. De ondersteuning blijft aanwezig totdat Azure Storage de service op verouderde metrische gegevens en logboeken beëindigt.

### <a name="logs-in-azure-monitor-preview"></a>Logboeken in Azure Monitor (preview-versie)

Logboek vermeldingen worden alleen gemaakt als er aanvragen worden gedaan voor het service-eind punt. Als een opslag account bijvoorbeeld activiteit heeft in het BLOB-eind punt, maar niet in de tabel-of wachtrij-eind punten, worden alleen logboeken gemaakt die betrekking hebben op de BLOB-service. Azure Storage logboeken bevatten gedetailleerde informatie over geslaagde en mislukte aanvragen voor een opslag service. Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op de beste basis geregistreerd.

#### <a name="log-authenticated-requests"></a>Geverifieerde aanvragen registreren

 De volgende typen geverifieerde aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Mislukte aanvragen, inclusief time-out-, beperkings-, netwerk-, autorisatiefouten en overige fouten
- Aanvragen die gebruikmaken van een Shared Access Signature (SAS) of OAuth, met inbegrip van mislukte en geslaagde aanvragen
- Aanvragen voor Analytics-gegevens (klassieke logboek gegevens in de **$logs** container en metrische gegevens van klassen in de **$metric** tabellen)

Aanvragen die worden gedaan door de opslag service zelf, zoals het maken of verwijderen van een logboek, worden niet geregistreerd. Zie voor een volledige lijst met geregistreerde gegevens opslag logboeken [en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en [opslag logboek indeling](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Anonieme aanvragen registreren

 De volgende typen anonieme aanvragen worden geregistreerd:

- Geslaagde aanvragen
- Serverfouten
- Time-outfouten voor zowel de client als de server
- Kan geen aanvragen ophalen met de fout code 304 (niet gewijzigd)

Alle andere mislukte anonieme aanvragen worden niet geregistreerd. Zie voor een volledige lijst met geregistreerde gegevens opslag logboeken [en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) en [opslag logboek indeling](monitor-storage-reference.md).

## <a name="configuration"></a>Configuratie

De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld, maar u moet een diagnostische instelling maken om bron logboeken te verzamelen of deze buiten Azure Monitor door te sturen. Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../../azure-monitor/platform/diagnostic-settings.md)voor het proces voor het maken van een diagnostische instelling met behulp van de Azure Portal, de Azure CLI of Power shell.

Wanneer u een diagnostische instelling maakt, kiest u het type opslag waarvoor u logboeken wilt inschakelen, zoals een blob, wachtrij, tabel of bestand. Data Lake Storage Gen2 wordt niet weer gegeven als een opslag type. Dat komt omdat Data Lake Storage Gen2 een reeks mogelijkheden die beschikbaar is voor Blob Storage. 

Als u de diagnostische instelling in de Azure Portal maakt, kunt u de resource uit een lijst selecteren. Als u Power shell of de Azure CLI gebruikt, moet u de resource-ID van het opslag type gebruiken. U kunt de resource-ID vinden in de Azure Portal door de pagina **Eigenschappen** van uw opslag account te openen.

U moet ook de categorieën van bewerkingen opgeven waarvoor u logboeken wilt verzamelen. De categorieën voor Azure Storage worden weer gegeven in deze tabel.

| Categorie | Beschrijving |
|:---|:---|
| StorageRead | Lees bewerkingen op blobs. |
| StorageWrite | Schrijf bewerkingen op blobs. |
| StorageDelete | Verwijder bewerkingen op blobs. |

## <a name="analyze-metric-data"></a>Metrische gegevens analyseren

U kunt metrische gegevens analyseren voor Azure Storage met metrische gegevens uit andere Azure-Services met behulp van Metrics Explorer. Open Metrics Explorer door **metrische gegevens** te kiezen in het menu **Azure monitor** . Zie aan de slag [met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)voor meer informatie over het gebruik van dit hulp programma. 

In dit voor beeld ziet u hoe **trans acties** worden weer gegeven op account niveau.

![Scherm opname van de toegang tot metrische gegevens in de Azure Portal](./media/monitor-storage/access-metrics-portal.png)

Voor metrische gegevens die dimensies ondersteunen, kunt u de metriek filteren met de gewenste dimensie waarde. In dit voor beeld ziet u hoe **trans acties** worden weer gegeven op account niveau voor een specifieke bewerking door waarden te selecteren voor de **API-naam** dimensie.

![Scherm opname van de toegang tot metrische gegevens met dimensie in de Azure Portal](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Zie [metrische dimensies](monitor-storage-reference.md#metrics-dimensions)voor een volledige lijst met de dimensies die Azure Storage ondersteunt.

Alle metrische gegevens voor Azure Storage bevinden zich in deze naam ruimten:

- Microsoft.Storage/storageAccounts
- Micro soft. Storage/Storage accounts/blobServices
- Micro soft. Storage/Storage accounts/fileServices
- Micro soft. Storage/Storage accounts/queueServices
- Micro soft. Storage/Storage accounts/tableServices

Zie [Azure monitor ondersteunde metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)voor een lijst met alle Azure monitor metrische gegevens over ondersteuning, waaronder Azure Storage.


### <a name="access-metrics"></a>Toegangs gegevens

> [!TIP]
> Als u voor beelden van Azure CLI of .NET wilt bekijken, kiest u de overeenkomstige tabbladen die hier worden vermeld.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>De metrische definitie weer geven

U kunt de metrische definitie van uw opslag account of de afzonderlijke opslag service, zoals de blob, het bestand, de tabel of de wachtrij service, weer geven. Gebruik de cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

In dit voor beeld vervangt u de `<resource-ID>` tijdelijke aanduiding door de resource-id van het hele opslag account of de resource-id van een afzonderlijke opslag service, zoals de blob, het bestand, de tabel of de wachtrij service. U kunt deze resource-Id's vinden op de pagina **Eigenschappen** van uw opslag account in de Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Metrische waarden lezen

U kunt metrische waarden op account niveau van uw opslag account of de afzonderlijke opslag service, zoals de blob, het bestand, de tabel of de wachtrij service, lezen. Gebruik de cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>De metrische definitie op account niveau weer geven

U kunt de metrische definitie van uw opslag account of de afzonderlijke opslag service, zoals de blob, het bestand, de tabel of de wachtrij service, weer geven. Gebruik de opdracht [AZ monitor Metrics List-Definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) .
 
In dit voor beeld vervangt u de `<resource-ID>` tijdelijke aanduiding door de resource-id van het hele opslag account of de resource-id van een afzonderlijke opslag service, zoals de blob, het bestand, de tabel of de wachtrij service. U kunt deze resource-Id's vinden op de pagina **Eigenschappen** van uw opslag account in de Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Metrische waarden op account niveau lezen

U kunt de metrische waarden van uw opslag account of de afzonderlijke opslag service lezen, zoals de blob, het bestand, de tabel of de wachtrij service. Gebruik de opdracht [AZ monitor Metrics List](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor biedt de [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) de definitie en waarden van metrische gegevens te lezen. De [voorbeeld code](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) laat zien hoe u de SDK met verschillende para meters kunt gebruiken. U moet `0.18.0-preview` of een latere versie gebruiken voor metrische opslag gegevens.
 
In deze voor beelden vervangt u de `<resource-ID>` tijdelijke aanduiding door de resource-id van het hele opslag account of de resource-id van een afzonderlijke opslag service, zoals de blob, het bestand, de tabel of de wachtrij service. U kunt deze resource-Id's vinden op de pagina **Eigenschappen** van uw opslag account in de Azure Portal.

Vervang de `<subscription-ID>` variabele door de id van uw abonnement. `<tenant-ID>` `<application-ID>` `<AccessKey>` Zie [de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang heeft tot resources](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)voor meer informatie over het verkrijgen van waarden voor, en. 

#### <a name="list-the-account-level-metric-definition"></a>De metrische definitie op account niveau weer geven

In het volgende voor beeld ziet u hoe u een metrische definitie kunt weer geven op account niveau:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="read-account-level-metric-values"></a>Metrische waarden op account niveau lezen

In het volgende voor beeld ziet u hoe u gegevens kunt lezen `UsedCapacity` op account niveau:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="read-multidimensional-metric-values"></a>Metrische waarden voor meerdere dimensies lezen

Voor multidimensionale metrieken moet u meta gegevens filters definiëren als u metrische gegevens voor specifieke dimensie waarden wilt lezen.

In het volgende voor beeld ziet u hoe u metrische gegevens kunt lezen over de metrische waarde die meerdere dimensies ondersteunt:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyze-log-data"></a>Logboekgegevens analyseren

U kunt bron Logboeken openen als een BLOB in een opslag account, als gebeurtenis gegevens of via logboek analyse query's.

Zie [Azure Storage monitoring data Reference](monitor-storage-reference.md)(Engelstalig) voor een gedetailleerde Naslag informatie over de velden die in deze logboeken worden weer gegeven.

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Zie [Deze pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)als u zich wilt inschrijven voor de preview-versie. Met deze preview-versie kunt u logboeken maken voor blobs (waaronder Azure Data Lake Storage Gen2), bestanden, wacht rijen, tabellen, Premium Storage-accounts in versie 1 voor algemeen gebruik en voor algemeen gebruik v2-opslag accounts. Klassieke opslag accounts worden niet ondersteund.

### <a name="access-logs-in-a-storage-account"></a>Logboeken openen in een opslag account

Logboeken worden weer gegeven als blobs die zijn opgeslagen in een container in het doel-opslag account. Gegevens worden verzameld en opgeslagen in één BLOB als een door een regel gescheiden JSON-nettolading. De naam van de BLOB volgt deze naamgevings Conventie:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Hier volgt een voorbeeld:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>Toegang tot logboeken in een Event Hub

Logboeken die naar een Event Hub worden verzonden, worden niet als een bestand opgeslagen, maar u kunt controleren of de Event Hub de logboek gegevens hebben ontvangen. Ga in het Azure Portal naar uw Event Hub en controleer of het aantal **inkomende berichten** groter is dan nul. 

![Auditlogboeken](media/monitor-storage/event-hub-log.png)

U kunt logboek gegevens die naar uw Event Hub worden verzonden, openen en lezen met behulp van beveiligings informatie en hulpprogram ma's voor gebeurtenis beheer en controle. Zie [Wat kan ik doen met de bewakings gegevens die worden verzonden naar mijn Event hub?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)voor meer informatie.

### <a name="access-logs-in-a-log-analytics-workspace"></a>Logboeken openen in een Log Analytics-werk ruimte

U kunt logboeken die zijn verzonden naar een Log Analytics-werk ruimte openen met behulp van Azure Monitor-logboek query's.

Zie [aan de slag met log Analytics in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)voor meer informatie.

De gegevens worden opgeslagen in deze tabellen.

| Tabel | Beschrijving |
|:---|:---|
|StorageBlobLogs | Logboeken waarin de activiteit in Blob Storage wordt beschreven. |
|StorageFileLogs | Logboeken waarin de activiteit in bestands shares wordt beschreven. |
|StorageQueueLogs | Logboeken waarin de activiteiten in wacht rijen worden beschreven.|
|StorageTableLogs| Logboeken waarin de activiteit in tabellen wordt beschreven.|

Logboeken voor Data Lake Storage Gen2 worden niet weer gegeven in een toegewezen tabel. Dat komt omdat Data Lake Storage Gen2 niet service is. Het is een reeks mogelijkheden die u kunt inschakelen voor een Blob Storage-account. Als u deze mogelijkheden hebt ingeschakeld, worden logboeken nog steeds weer gegeven in de tabel StorageBlobLogs. 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Storage Log Analytics query's in Azure Monitor

Hier volgen enkele query's die u kunt invoeren in de **Zoek** balk van het logboek om uw Azure Storage-accounts te bewaken. Deze query's werken met de [nieuwe taal](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Gebruik deze query's om uw Azure Storage-accounts te bewaken:

* De 10 meest voorkomende fouten in de afgelopen drie dagen weer geven.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* De 10 belangrijkste bewerkingen die de meeste fouten hebben veroorzaakt, weer geven in de afgelopen drie dagen.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* De Top 10-bewerkingen met de langste end-to-end-latentie in de afgelopen drie dagen weer geven.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Om alle bewerkingen weer te geven die de afgelopen drie dagen beperkings fouten aan de server zijde hebben veroorzaakt.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Alle aanvragen met anonieme toegang in de afgelopen drie dagen weer geven.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Een cirkel diagram maken van de bewerkingen die in de afgelopen drie dagen zijn gebruikt.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Veelgestelde vragen

**Ondersteunt Azure Storage metrische gegevens voor Managed Disks of niet-beheerde schijven?**

Nee. Azure Compute ondersteunt de metrische gegevens op schijven. Zie [metrische gegevens per schijf voor beheerde en onbeheerde schijven](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Voor een verwijzing naar de logboeken en metrische gegevens die door Azure Storage zijn gemaakt, raadpleegt u de [Naslag informatie over Azure Storage bewaking](monitor-storage-reference.md).
- Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md)voor meer informatie over het bewaken van Azure-resources.
- Zie [Azure Storage metrische gegevens migratie](./storage-metrics-migration.md)voor meer informatie over de migratie van metrische gegevens.
