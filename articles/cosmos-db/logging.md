---
title: Registratie in diagnoselogboek in Azure Cosmos DB
description: Meer informatie over de verschillende manieren om gegevens die zijn opgeslagen in Azure Cosmos DB te registreren en te controleren.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: bdbc50983708327cf5d3857282c92fcab1c28b09
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930535"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Registratie in diagnoselogboek in Azure Cosmos DB 

Nadat u een of meer Azure Cosmos-data bases hebt gebruikt, wilt u wellicht controleren hoe en wanneer uw data bases worden geopend. Dit artikel bevat een overzicht van de logboeken die beschikbaar zijn op het Azure-platform. U leert hoe u diagnostische logboek registratie inschakelt voor bewakings doeleinden om logboeken te verzenden naar [Azure Storage](https://azure.microsoft.com/services/storage/), logboeken te streamen naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/)en logboeken te exporteren naar [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Logboeken die beschikbaar zijn in azure

Voordat we weten hoe u uw Azure Cosmos DB-account kunt controleren, laten we een aantal dingen zien over logboek registratie en controle. Er zijn verschillende typen logboeken op het Azure-platform. Er zijn [Azure-activiteiten logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)van Azure, [Azure-metrische gegevens](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), gebeurtenissen, bewaking van heartbeats, logboeken voor bewerkingen, enzovoort. Er is een verdwaald van Logboeken. U ziet de volledige lijst met Logboeken in [Azure monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) in de Azure Portal. 

In de volgende afbeelding ziet u de verschillende soorten Azure-logboeken die beschikbaar zijn:

![Verschillende soorten Azure-logboeken](./media/logging/azurelogging.png)

In de installatie kopie vertegenwoordigen de **reken resources** de Azure-resources waarvoor u toegang hebt tot het micro soft-gast besturingssysteem. Zo worden Azure Virtual Machines, schaal sets voor virtuele machines, Azure Container Service, enzovoort, beschouwd als reken resources. Reken bronnen genereren activiteiten logboeken, Diagnostische logboeken en toepassings Logboeken. Raadpleeg de [bronnen van bewakings gegevens in azure](../azure-monitor/platform/data-sources.md) article voor meer informatie.

De **niet-reken resources** zijn resources waarin u geen toegang hebt tot het onderliggende besturings systeem en rechtstreeks met de resource kunt werken. Bijvoorbeeld netwerk beveiligings groepen, Logic Apps, enzovoort. Azure Cosmos DB is een niet-reken resource. U kunt Logboeken voor niet-reken resources weer geven in het activiteiten logboek of de optie Diagnostische logboeken inschakelen in de portal. Raadpleeg de [gegevens bronnen in azure monitor](../azure-monitor/platform/data-sources.md) artikel voor meer informatie.

Het activiteiten logboek registreert de bewerkingen op abonnements niveau voor Azure Cosmos DB. Bewerkingen zoals Listkeys ophalen, write DatabaseAccounts en meer worden geregistreerd. Diagnostische logboeken bieden gedetailleerdere logboek registratie en bieden u de mogelijkheid om DataPlaneRequests te registreren (maken, lezen, Query's, enzovoort) en MongoRequests.


In dit artikel richten we ons op het Azure-activiteiten logboek, Azure Diagnostische logboeken en Azure-metrische gegevens. Wat is het verschil tussen deze drie logboeken? 

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek

Het Azure-activiteiten logboek is een abonnements logboek dat inzicht biedt in gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. Het activiteiten logboek rapporteert Control-vlieg gebeurtenissen voor uw abonnementen in de categorie beheer. U kunt het activiteiten logboek gebruiken om het ' What, wie en wanneer ' te bepalen voor schrijf bewerkingen (PUT, POST, DELETE) op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen. 

Het activiteiten logboek wijkt af van de diagnostische Logboeken. Het activiteiten logboek bevat gegevens over de bewerkingen van een resource van buiten (het _besturings vlak_). In de Azure Cosmos DB context bevinden de Control-bewerkingen container maken, lijst sleutels, sleutels verwijderen, lijst van data base, enzovoort. Diagnostische logboeken worden verzonden door een resource en geven informatie over de werking van die resource (het _gegevens vlak_). Enkele voor beelden van de gegevenslaag bewerkingen in het diagnostische logboek zijn verwijderen, invoegen en ReadFeed.

Activiteiten Logboeken (beheer vlak bewerkingen) kunnen rijker zijn en kunnen het volledige e-mail adres van de beller, het IP-adres van de aanroeper, de naam van de bewerking, TenantId en meer bevatten. Het activiteiten logboek bevat verschillende [soorten](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) gegevens. Zie [Azure Activity Log-gebeurtenis schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema)voor volledige informatie over de schema's van deze categorieën. Diagnostische logboeken kunnen echter beperkend zijn als persoonlijke gegevens uit deze logboeken worden verwijderd. Mogelijk hebt u het IP-adres van de aanroeper, maar de laatste octant wordt verwijderd.

### <a name="azure-metrics"></a>Metrische gegevens van Azure

[Azure-metrische](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) gegevens hebben het belangrijkste type Azure-telemetriegegevens (ook wel _prestatie meter items_genoemd) die worden verzonden door de meeste Azure-resources. Met metrische gegevens kunt u informatie bekijken over de door Voer, opslag, consistentie, Beschik baarheid en de latentie van uw Azure Cosmos DB-resources. Zie voor meer informatie [bewaking en fout opsporing met metrische gegevens in azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Diagnostische logboeken van Azure

Diagnostische logboeken van Azure worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. Deze logboeken worden per aanvraag vastgelegd. De inhoud van deze logboeken is afhankelijk van het bron type. Diagnostische logboeken op resource niveau verschillen ook van Diagnostische logboeken op het niveau van het gast besturingssysteem. Diagnostische logboeken van het gast besturingssysteem worden verzameld door een agent die wordt uitgevoerd binnen een virtuele machine of een ander ondersteund resource type. Voor Diagnostische logboeken op resource niveau zijn geen agents en resource-specifieke gegevens van het Azure-platform zelf vereist. Met diagnostische gegevens van het besturings systeem op het niveau van het gast logboek wordt een opname gemaakt van de bewerkings-en toepassingen op een virtuele machine.

![Diagnostische logboek registratie voor opslag, Event Hubs of Azure Monitor-logboeken](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Wat wordt er geregistreerd door Azure Diagnostics-logboeken?

* Alle geverifieerde back-end-aanvragen (TCP/REST) voor alle Api's worden geregistreerd, met inbegrip van mislukte aanvragen als gevolg van toegangs machtigingen, systeem fouten of ongeldige aanvragen. Ondersteuning voor door de gebruiker geïnitieerde Graph-, Cassandra-en Table-API-aanvragen zijn momenteel niet beschikbaar.
* Bewerkingen op de database zelf, waaronder CRUD-bewerkingen op alle documenten, containers en -databases.
* Bewerkingen op account sleutels, zoals het maken, wijzigen of verwijderen van de sleutels.
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Bijvoorbeeld aanvragen die geen Bearer-token hebben of onjuist zijn of verlopen of een ongeldig token hebben.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Logboek registratie inschakelen in de Azure Portal

Gebruik de volgende stappen om diagnostische logboek registratie in te scha kelen in de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

1. Navigeer naar uw Azure Cosmos-account. Open het deel venster **Diagnostische instellingen** en selecteer optie **Diagnostische instelling toevoegen** .

    ![Schakel diagnostische logboek registratie in voor Azure Cosmos DB in het Azure Portal](./media/logging/turn-on-portal-logging.png)

1. Vul op de pagina **Diagnostische instellingen** het formulier in met de volgende details: 

    * **Naam**: Voer een naam in voor de logboeken die u wilt maken.

    * U kunt de logboeken opslaan in de volgende services:

      * **Archiveren naar een opslag account**: als u deze optie wilt gebruiken, hebt u een bestaand opslag account nodig om verbinding te maken met. Zie het artikel [een opslag account maken](../storage/common/storage-create-storage-account.md) voor meer informatie over het maken van een nieuw opslag account in de portal. Ga vervolgens terug naar het deel venster Diagnostische instellingen voor Azure Cosmos db in de portal om uw opslag account te selecteren. Het kan enkele minuten duren voordat zojuist gemaakte opslag accounts worden weer gegeven in de vervolg keuzelijst.

      * **Streamen naar een event hub**: als u deze optie wilt gebruiken, hebt u een bestaande Event hubs naam ruimte en Event hub om verbinding te maken met. Zie [een event hubs naam ruimte en een event hub maken met behulp van de Azure Portal](../event-hubs/event-hubs-create.md)om een event hubs naam ruimte te maken. Ga vervolgens terug naar deze pagina in de portal om de Event hub-naam ruimte en de naam van het beleid te selecteren.

      * **Verzenden naar log Analytics**: als u deze optie wilt gebruiken, moet u een bestaande werk ruimte gebruiken of een nieuwe log Analytics-werk ruimte maken door de stappen te volgen om [een nieuwe werk ruimte te maken](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) in de portal. 

   * U kunt de volgende gegevens in een logboek registreren:

      * **DataPlaneRequests**: Selecteer deze optie als u wilt dat de back-end-aanvragen worden geregistreerd bij alle api's, waaronder SQL-, Graph-, MongoDb-, Cassandra-en Table-API-accounts in azure Cosmos db. Als u een opslag account archiveert, kunt u de Bewaar periode voor de diagnostische logboeken selecteren. Logboeken worden automatisch verwijderd nadat de Bewaar periode is verstreken. De volgende JSON-gegevens zijn een voorbeeld uitvoer van gegevens die zijn geregistreerd met DataPlaneRequests. De belangrijkste eigenschappen die u moet weten, zijn: Requestcharge, status code, clientIPaddress en partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Selecteer deze optie om door de gebruiker geïnitieerde aanvragen van de front-end te registreren om aanvragen voor de Azure Cosmos DB-API voor MongoDb te leveren. MongoDB-aanvragen worden weer gegeven in MongoRequests en DataPlaneRequests. Als u een opslag account archiveert, kunt u de Bewaar periode voor de diagnostische logboeken selecteren. Logboeken worden automatisch verwijderd nadat de Bewaar periode is verstreken. De volgende JSON-gegevens zijn een voorbeeld uitvoer van gegevens die zijn geregistreerd met MongoRequests. De belangrijkste eigenschappen die u moet weten, zijn: Requestcharge, opcode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Selecteer deze optie om de uitgevoerde query tekst te registreren.  De volgende JSON-gegevens zijn een voorbeeld uitvoer van gegevens die zijn geregistreerd met QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **PartitionKeyStatistics**: in dit logboek worden de statistieken van de partitie sleutels gerapporteerd. Op dit moment worden de statistieken weer gegeven met de opslag grootte (KB) van de partitie sleutels. Het logboek wordt verzonden op basis van de eerste drie partitie sleutels die de meeste gegevens opslag in beslag nemen.

       ```
       { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
       ```

      * **Metrische aanvragen**: Selecteer deze optie om uitgebreide gegevens op te slaan in metrische data van [Azure](../azure-monitor/platform/metrics-supported.md). Als u een opslag account archiveert, kunt u de Bewaar periode voor de diagnostische logboeken selecteren. Logboeken worden automatisch verwijderd nadat de Bewaar periode is verstreken.

3. Selecteer **Opslaan**.

    Als er een fout bericht wordt weer gegeven met de melding dat de diagnostische gegevens voor \<werkruimte naam niet kunnen worden bijgewerkt >. De abonnements-id van het abonnement \<> niet is geregistreerd voor gebruik van micro soft. Insights, "Volg de instructies [voor het oplossen van Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) om het account te registreren en voer deze procedure opnieuw uit.

    Als u wilt wijzigen hoe uw Diagnostische logboeken op elk gewenst moment in de toekomst worden opgeslagen, keert u terug naar deze pagina om de diagnostische logboek instellingen voor uw account te wijzigen.

## <a name="turn-on-logging-by-using-azure-cli"></a>Logboek registratie inschakelen met behulp van Azure CLI

Gebruik de volgende opdrachten om metrische gegevens en diagnostische logboek registratie in te scha kelen met behulp van Azure CLI:

- Als u de opslag van Diagnostische logboeken in een opslag account wilt inschakelen, gebruikt u deze opdracht:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   De `resource` is de naam van het Azure Cosmos DB-account. De resource heeft de indeling '/Subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/< Azure_Cosmos_account_name > ' de `storage-account` is de naam van het opslag account waarnaar u de logboeken wilt verzenden. U kunt andere logboeken registreren door de parameter waarden van de categorie bij te werken naar "MongoRequests" of "DataPlaneRequests". 

- Gebruik deze opdracht om streaming van Diagnostische logboeken naar een Event Hub in te scha kelen:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   De `resource` is de naam van het Azure Cosmos DB-account. De `event-hub-rule` is de Event Hub regel-ID. 

- Als u het verzenden van Diagnostische logboeken naar een Log Analytics-werk ruimte wilt inschakelen, gebruikt u deze opdracht:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

U kunt deze para meters combi neren om meerdere uitvoer opties in te scha kelen.

## <a name="turn-on-logging-by-using-powershell"></a>Logboek registratie inschakelen met behulp van Power shell

Als u diagnostische logboek registratie wilt inschakelen met behulp van Power shell, hebt u Azure Power shell nodig met een minimale versie van 1.0.1.

Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement.

Als u Azure PowerShell al hebt geïnstalleerd en u niet weet wat de versie is, kunt u het Power shell-console type `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Verbinding maken met uw abonnementen
Start een Azure PowerShell-sessie en meld u aan bij uw Azure-account met de volgende opdracht:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen op die aan dit account zijn gekoppeld, en gebruikt standaard het eerste abonnement.

Als u meer dan één abonnement hebt, moet u mogelijk het specifieke abonnement opgeven dat is gebruikt voor het maken van uw Azure-sleutel kluis. Als u de abonnementen voor uw account wilt zien, typt u de volgende opdracht:

```powershell
Get-AzSubscription
```

Typ vervolgens de volgende opdracht om het abonnement op te geven dat is gekoppeld aan het Azure Cosmos DB account dat u wilt registreren:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Als u meer dan één abonnement hebt dat aan uw account is gekoppeld, is het belang rijk om het abonnement op te geven dat u wilt gebruiken.
>
>

Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview)voor meer informatie over het configureren van Azure PowerShell.

### <a id="storage"></a>Een nieuw opslagaccount voor uw logboeken maken
Hoewel u een bestaand opslag account voor uw logboeken kunt gebruiken, maken we in deze zelf studie een nieuw opslag account dat is toegewezen aan Azure Cosmos DB Logboeken. Voor het gemak slaan we de details van het opslag account op in een variabele met de naam **sa**.

Voor extra beheer gemak gebruiken we in deze zelf studie dezelfde resource groep als die waarin de Azure Cosmos-data base zich bevindt. Vervang de waarden voor de para meters **ContosoResourceGroup**, **contosocosmosdblogs**en **Noord-Centraal VS** door, indien van toepassing:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Als u besluit om een bestaand opslag account te gebruiken, moet het account hetzelfde abonnement gebruiken als uw Azure Cosmos DB-abonnement. Het account moet ook het Resource Manager-implementatie model gebruiken, in plaats van het klassieke implementatie model.
>
>

### <a id="identify"></a>Het Azure Cosmos DB-account voor uw logboeken identificeren
Stel de naam van het Azure Cosmos DB-account in op een variabele met de naam **account**, waarbij **ResourceName** de naam van het Azure Cosmos DB account is.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Logboekregistratie inschakelen
Als u logboek registratie wilt inschakelen voor Azure Cosmos DB, gebruikt u de `Set-AzDiagnosticSetting` cmdlet met variabelen voor het nieuwe opslag account, Azure Cosmos DB account en de categorie die moet worden ingeschakeld voor logboek registratie. Voer de volgende opdracht uit en stel de vlag **-enabled** in op **$True**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

De uitvoer voor de opdracht moet er ongeveer uitzien als in het volgende voor beeld:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

De uitvoer van de opdracht bevestigt dat logboek registratie nu is ingeschakeld voor uw data base en dat de gegevens worden opgeslagen in uw opslag account.

U kunt eventueel ook het Bewaar beleid voor uw logboeken instellen, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld het Bewaar beleid in met de vlag **-RetentionEnabled** ingesteld op **$True**. Stel de para meter **-RetentionInDays** in op **90** zodat logboeken ouder zijn dan 90 dagen automatisch worden verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Toegang tot uw logboeken
Azure Cosmos DB logboeken voor de categorie **DataPlaneRequests** worden opgeslagen in de container **Insights-logs-DataPlaneRequests** in het opslag account dat u hebt ingevoerd. 

Maak eerst een variabele voor de containernaam. De variabele wordt overal in de loop-through gebruikt.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Als u alle blobs in deze container wilt weer geven, typt u:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

De uitvoer voor de opdracht moet er ongeveer uitzien als in het volgende voor beeld:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Zoals u in deze uitvoer kunt zien, hebben de blobs de volgende naamgevings regels: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

De datum- en tijdwaarden maken gebruik van UTC.

Omdat hetzelfde opslag account kan worden gebruikt voor het verzamelen van Logboeken voor meerdere resources, kunt u de volledig gekwalificeerde Resource-ID in de naam van de BLOB gebruiken om de specifieke blobs die u nodig hebt te openen en te downloaden. Voordat we dit doen, hebben we betrekking op het downloaden van alle blobs.

Maak eerst een map waarin u de blobs wilt downloaden. Bijvoorbeeld:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Vervolgens krijgt u een lijst met alle blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Pipe deze lijst via de `Get-AzStorageBlobContent` opdracht om de blobs in de doelmap te downloaden:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Wanneer u deze tweede opdracht uitvoert, maakt het **/** scheidings teken in de naam van de BLOB een volledige mapstructuur onder de doelmap. Deze mapstructuur wordt gebruikt om de blobs te downloaden en op te slaan als bestanden.

Als u alleen specifieke blobs wilt downloaden, moet u jokertekens gebruiken. Bijvoorbeeld:

* Als u meerdere data bases hebt en logboeken wilt downloaden voor slechts één data base met de naam **CONTOSOCOSMOSDB3**, gebruikt u de opdracht:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Als u meerdere resource groepen hebt en logboeken voor slechts één resource groep wilt downloaden, gebruikt u de opdracht `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Als u alle logboeken voor de maand juli 2017 wilt downloaden, gebruikt u de opdracht `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

U kunt ook de volgende opdrachten uitvoeren:

* Als u de status van diagnostische instellingen voor uw database resource wilt opvragen, gebruikt u de opdracht `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Als u de registratie van de **DataPlaneRequests** -categorie voor uw database account bron wilt uitschakelen, gebruikt u de opdracht `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


De blobs die in elk van deze query's worden geretourneerd, worden als tekst opgeslagen en opgemaakt als een JSON-blob, zoals wordt weer gegeven in de volgende code:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Zie voor meer informatie over de gegevens in elk JSON-blob, [Azure Cosmos DB-logboekgegevens interpreteren](#interpret).

## <a name="manage-your-logs"></a>Uw logboeken beheren

Diagnostische logboeken worden gedurende twee uur beschikbaar gesteld in uw account vanaf het moment dat de Azure Cosmos DB bewerking werd uitgevoerd. Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik de standaard Azure Access Control-methoden om uw logboeken te beveiligen en te beperken wie er toegang toe heeft.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.
* De Bewaar periode voor gegevens vlak-aanvragen die zijn gearchiveerd in een opslag account, wordt geconfigureerd in de Portal wanneer de instelling **logboek DataPlaneRequests** is geselecteerd. Als u deze instelling wilt wijzigen, raadpleegt u [logboek registratie inschakelen in de Azure Portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Weergave van logboeken in Azure Monitor-logboeken

Als u de optie **Send to log Analytics** hebt geselecteerd wanneer u diagnostische logboek registratie hebt ingeschakeld, worden de diagnostische gegevens uit uw container binnen twee uur doorgestuurd naar Azure monitor Logboeken. Wanneer u Azure Monitor logboeken onmiddellijk bekijkt nadat u logboek registratie hebt ingeschakeld, worden er geen gegevens weer gegeven. Wacht gewoon twee uur en probeer het opnieuw. 

Controleer voordat u uw logboeken bekijkt of uw Log Analytics-werk ruimte is bijgewerkt voor het gebruik van de nieuwe Kusto-query taal. Als u wilt controleren, opent u de [Azure Portal](https://portal.azure.com), selecteert u **log Analytics werk ruimten** helemaal links en selecteert u vervolgens de naam van de werk ruimte, zoals wordt weer gegeven in de volgende afbeelding. De pagina **log Analytics werk ruimte** wordt weer gegeven:

![Azure Monitor Logboeken in de Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.  

Als u het volgende bericht ziet op de pagina **log Analytics werk ruimte** , is uw werk ruimte niet bijgewerkt voor gebruik van de nieuwe taal. Zie [uw Azure log Analytics-werk ruimte upgraden naar nieuwe Zoek opdrachten in Logboeken](../log-analytics/log-analytics-log-search-upgrade.md)voor meer informatie over het uitvoeren van een upgrade naar de nieuwe query taal. 

![Upgrade bericht van Azure Monitor-logboeken](./media/logging/upgrade-notification.png)

Als u uw diagnostische gegevens in Azure Monitor logboeken wilt weer geven, opent u de pagina **Zoeken in Logboeken** vanuit het menu links of het gebied **beheer** van de pagina, zoals wordt weer gegeven in de volgende afbeelding:

![Opties voor logboek zoeken in het Azure Portal](./media/logging/log-analytics-open-log-search.png)

Nu u gegevens verzameling hebt ingeschakeld, voert u het volgende voor beeld van zoeken in Logboeken uit met behulp van de nieuwe query taal om de tien meest recente logboeken weer te geven `AzureDiagnostics | take 10`.

![Voorbeeld logboek zoeken naar de tien meest recente logboeken](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="cosmosdb-log-analytics-queries-in-azure-monitor"></a>CosmosDB Log Analytics query's in Azure Monitor

Hier volgen enkele aanvullende query's die u kunt invoeren in het **zoekvak voor logboeken** om u te helpen bij het bewaken van uw Azure Cosmos-containers. Deze query's werken met de [nieuwe taal](../log-analytics/log-analytics-log-search-upgrade.md).  

Zie [uw Azure Cosmos DB-logboeken interpreteren](#interpret)voor meer informatie over de betekenis van de gegevens die door elke logboek zoekactie worden geretourneerd.

* Een query uitvoeren voor alle Diagnostische logboeken van Azure Cosmos DB gedurende een opgegeven tijds periode:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Query's uitvoeren voor de tien meest recent vastgelegde gebeurtenissen:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Om te zoeken naar alle bewerkingen, gegroepeerd op bewerkings type:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Om te zoeken naar alle bewerkingen, gegroepeerd op **resource**:

    ```
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

* Om te zoeken naar alle gebruikers activiteit, gegroepeerd op resource:

    ```
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
    > [!NOTE]
    > Deze opdracht is voor een activiteiten logboek, niet van een diagnostisch logboek.

* Als u alle query's wilt ophalen die groter zijn dan 100 RUs, gekoppeld aan gegevens uit DataPlaneRequests en QueryRunTimeStatistics

    ```
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```
    
      

* Een query uitvoeren voor de bewerkingen die langer duren dan 3 milliseconden:

    ```
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Een query uitvoeren voor de agent die de bewerkingen uitvoert:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Query's uitvoeren wanneer de langlopende bewerkingen zijn uitgevoerd:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | render timechart
    ```
    
* Voor het ophalen van partitie sleutel statistieken voor het evalueren van schuin trekking over de belangrijkste drie partities voor het database account:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    
   
    ```
    

Zie [zoeken naar Logboeken in azure monitor logs](../log-analytics/log-analytics-log-search-new.md)voor meer informatie over het gebruik van de nieuwe Zoek taal voor Logboeken. 

## <a id="interpret"></a>Uw logboeken interpreteren

Diagnostische gegevens die zijn opgeslagen in Azure Storage en Azure Monitor Logboeken gebruiken een vergelijkbaar schema. 

In de volgende tabel wordt de inhoud van elke logboek vermelding beschreven.

| Azure Storage veld of eigenschap | Eigenschap Azure Monitor logboeken | Beschrijving |
| --- | --- | --- |
| **tegelijk** | **TimeGenerated** | De datum en tijd (UTC) waarop de bewerking plaatsvond. |
| **resourceId** | **Resource** | Het Azure Cosmos DB account waarvoor logboeken zijn ingeschakeld.|
| **rubriek** | **Categorie** | Voor Azure Cosmos DB-Logboeken is **DataPlaneRequests** de enige beschik bare waarde. |
| **operationName** | **OperationName** | De naam van de bewerking. Deze waarde kan een van de volgende bewerkingen zijn: maken, bijwerken, lezen, ReadFeed, verwijderen, vervangen, uitvoeren, SqlQuery, query, JSQuery, Head, HeadFeed of Upsert.   |
| **eigenschappen** | N.v.t. | De inhoud van dit veld wordt beschreven in de volgende rijen. |
| **activityId** | **activityId_g** | De unieke GUID voor de geregistreerde bewerking. |
| **User agent** | **userAgent_s** | Een teken reeks die de gebruikers agent van de client opgeeft die de aanvraag uitvoert. De indeling is {naam van de gebruikers agent}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Het type van de resource waartoe toegang wordt verkregen. Deze waarde kan een van de volgende resource typen zijn: data base, container, document, bijlage, gebruiker, machtiging, bestands-, trigger-, UserDefinedFunction-of aanbiedings methode. |
| **Status code** | **statusCode_s** | De antwoord status van de bewerking. |
| **requestResourceId** | **ResourceId** | De resourceId die betrekking heeft op de aanvraag. De waarde kan verwijzen naar databaseRid, collectionRid of documentRid, afhankelijk van de bewerking die is uitgevoerd.|
| **clientIpAddress** | **clientIpAddress_s** | Het IP-adres van de client. |
| **requestCharge** | **requestCharge_s** | Het aantal RUs dat door de bewerking wordt gebruikt |
| **collectionRid** | **collectionId_s** | De unieke ID voor de verzameling.|
| **hebben** | **duration_s** | De duur van de bewerking, in milliseconden. |
| **requestLength** | **requestLength_s** | De lengte van de aanvraag, in bytes. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Deze waarde is niet-leeg wanneer [bron tokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) voor authenticatie worden gebruikt. De waarde verwijst naar de resource-ID van de gebruiker. |

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe u logboek registratie kunt inschakelen en ook de metrische gegevens en logboek categorieën die worden ondersteund door de verschillende Azure-Services, leest u het [overzicht van metrische gegevens in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) en [overzicht van de artikelen van Azure Diagnostic logs](../azure-monitor/platform/resource-logs-overview.md) .
- Lees deze artikelen voor meer informatie over Event hubs:
   - [Wat is Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Lees [metrische gegevens over het downloaden en Diagnostische logboeken van Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Meer [informatie over logboek zoekopdrachten in azure monitor logs](../log-analytics/log-analytics-log-search-new.md).
