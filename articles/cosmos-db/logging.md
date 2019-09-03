---
title: Registratie in diagnoselogboek in Azure Cosmos DB
description: Meer informatie over de verschillende manieren om te leggen en te bewaken gegevens die zijn opgeslagen in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: d8a9963edd689a32ae0642ac6fa4a622c248bc5b
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232379"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Registratie in diagnoselogboek in Azure Cosmos DB 

Nadat u een of meer Azure Cosmos-data bases hebt gebruikt, wilt u wellicht controleren hoe en wanneer uw data bases worden geopend. Dit artikel bevat een overzicht van de logboeken die beschikbaar op het Azure-platform zijn. U leert hoe u diagnostische logboek registratie inschakelt voor bewakings doeleinden om logboeken te verzenden naar [Azure Storage](https://azure.microsoft.com/services/storage/), logboeken te streamen naar [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/)en logboeken te exporteren naar [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Logboeken beschikbaar zijn in Azure

Voordat we praten over het bewaken van uw Azure Cosmos DB-account, laten we een paar dingen voor logboekregistratie en bewaking te verduidelijken. Er zijn verschillende soorten logboeken op de Azure-platform. Er zijn [Azure-activiteitenlogboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [diagnostische logboeken van Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metrische gegevens van Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), gebeurtenissen, heartbeat-bewaking, operations-Logboeken, enzovoort. Er is een breed spectrum van Logboeken. U ziet de volledige lijst met Logboeken in [Azure monitor](https://azure.microsoft.com/services/log-analytics/) -Logboeken in de Azure Portal. 

In de volgende afbeelding ziet u de verschillende soorten Azure-logboeken die beschikbaar zijn:

![Verschillende soorten logboeken in Azure](./media/logging/azurelogging.png)

In de afbeelding, de **Rekenresources** vertegenwoordigen de Azure-resources waarvoor u toegang hebt tot het Gastbesturingssysteem van Microsoft. Bijvoorbeeld: Azure Virtual Machines, virtuele machine schaalsets, Azure Container Service en enzovoort, worden beschouwd als compute-resources. COMPUTE-resources activiteitenlogboeken, diagnostische logboeken en toepassingslogboeken te genereren. Raadpleeg voor meer informatie, de [bronnen van bewakingsgegevens in Azure](../azure-monitor/platform/data-sources.md) artikel.

De **Non-rekenresources** zijn resources in die u kunt geen toegang tot het onderliggende besturingssysteem en werk rechtstreeks met de resource. Bijvoorbeeld, Network Security Groups, Logic Apps, enzovoort. Azure Cosmos DB is een niet-compute-resource. U kunt de logboeken voor niet-compute-resources in het activiteitenlogboek weergeven of schakelt u de optie Logboeken met diagnostische gegevens in de portal. Raadpleeg voor meer informatie, de [bronnen van de gegevens in Azure Monitor](../azure-monitor/platform/data-sources.md) artikel.

Het activiteitenlogboek registreert de bewerkingen op het abonnementsniveau van een voor Azure Cosmos DB. Bewerkingen zoals ListKeys, DatabaseAccounts schrijven en meer worden geregistreerd. Diagnoselogboeken bieden meer gedetailleerde logboekregistratie en kunnen u aan te melden DataPlaneRequests (maken, lezen, Query, enzovoort) en MongoRequests.


In dit artikel hebben we ons richten op de Azure-activiteitenlogboek, Azure diagnostische logboeken en metrische gegevens van Azure. Wat is het verschil tussen deze drie logboeken? 

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek

De Azure-activiteitenlogboek is een abonnementlogboek biedt inzicht in gebeurtenissen op abonnementsniveau die hebben plaatsgevonden in Azure. Het activiteitenlogboek rapporten controlelaag gebeurtenissen voor uw abonnementen onder de beheercategorie. U kunt het activiteitenlogboek gebruiken om te bepalen de ' wat, wie, en wanneer ' voor een schrijfbewerking (PUT, POST, DELETE) op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen. 

Het activiteitenlogboek verschilt van diagnostische logboeken. Het activiteitenlogboek biedt gegevens over de bewerkingen op een resource van buitenaf (de _controlelaag_). Besturingselement vlak bewerkingen zijn onder meer maken in de context van Azure Cosmos DB, container, een lijst met sleutels, verwijderen, lijst database, enzovoort. Logboeken met diagnostische gegevens worden gegenereerd door een resource en geef informatie op over de werking van die resource (de _gegevenslaag_). Enkele voorbeelden van de bewerkingen voor het vlak van gegevens in de diagnostische logboeken zijn Delete, Insert en ReadFeed.

Activiteitenlogboeken (bewerkingen voor de controlelaag) kunnen uitgebreidere van aard en het volledige e-mailadres van de oproepende functie, IP-adres van oproepende functie, Resourcenaam, de naam van bewerking, tenant-id en meer kunnen opnemen. Het activiteitenlogboek bevat verschillende [categorieën](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) van gegevens. Zie voor volledige informatie over de schema's van deze categorieën vallen [gebeurtenisschema in het Azure-activiteitenlogboek](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Logboeken met diagnostische gegevens kan echter zijn beperkingen in de natuur als persoonlijke gegevens vaak wordt verwijderd uit deze logboeken. Mogelijk hebt u het IP-adres van de oproepende functie, maar de laatste octant is verwijderd.

### <a name="azure-metrics"></a>Metrische gegevens van Azure

[Metrische gegevens van Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) zijn de belangrijkste type Azure telemetrische gegevens (ook wel genoemd _prestatiemeteritems_) die wordt verzonden door de meeste Azure-resources. Metrische gegevens kunt u informatie bekijken over de doorvoer, opslag, consistentie, beschikbaarheid en de latentie van uw Azure Cosmos DB-resources. Zie voor meer informatie, [bewaking en foutopsporing met metrische gegevens in Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Diagnostische logboeken in Azure

Diagnostische logboeken in Azure worden gegenereerd door een resource en biedt uitgebreide, regelmatig gegevens over de werking van die resource. Deze logboeken worden per aanvraag vastgelegd. Resourcetype is afhankelijk van de inhoud van deze logboeken. Diagnostische logboeken resourceniveau afwijken van de Gast OS-niveau diagnostische logboeken. Gastbesturingssysteem logboeken met diagnostische gegevens worden verzameld door een agent die wordt uitgevoerd binnen een virtuele machine of andere ondersteund resourcetype. Diagnostische logboeken resourceniveau vereisen geen gegevens van de resource-specifieke agent en vastleggen van de Azure-platform zelf. Gast-OS-niveau logboeken met diagnostische gegevens vastleggen van gegevens uit het besturingssysteem en toepassingen die worden uitgevoerd op een virtuele machine.

![Diagnostische logboek registratie voor opslag, Event Hubs of Azure Monitor-logboeken](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Wat is vastgelegd door diagnoselogboeken van Azure?

* Alle geverifieerde back-end-aanvragen (TCP/REST) voor alle API's worden vastgelegd, met inbegrip van mislukte aanvragen als gevolg van toegangsmachtigingen, systeemfouten of ongeldige aanvragen. Ondersteuning voor de gebruiker geïnitieerde Graph, Cassandra en Table-API-aanvragen zijn niet op dit moment beschikbaar.
* Bewerkingen op de database zelf, waaronder CRUD-bewerkingen op alle documenten, containers en -databases.
* Bewerkingen voor sleutels, waaronder het maken, wijzigen of verwijderen van de sleutels.
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Bijvoorbeeld, aanvragen die geen bearer-token of ongeldige of verlopen zijn, of een ongeldig token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Schakel logboekregistratie in Azure portal

Gebruik de volgende stappen om diagnostische logboek registratie in te scha kelen in de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

1. Navigeer naar uw Azure Cosmos-account. Open het deel venster **Diagnostische instellingen** en selecteer optie **Diagnostische instelling toevoegen** .

    ![Schakel registratie in diagnoselogboek voor Azure Cosmos DB in Azure portal](./media/logging/turn-on-portal-logging.png)

1. Vul op de pagina **Diagnostische instellingen** het formulier in met de volgende details: 

    * **Naam**: Voer een naam voor de logboeken om te maken.

    * U kunt de logboeken opslaan in de volgende services:

      * **Archiveren naar een opslag account**: Als u wilt deze optie gebruikt, moet u een bestaand opslagaccount verbinden. Zie het artikel [een opslag account maken](../storage/common/storage-create-storage-account.md) voor meer informatie over het maken van een nieuw opslag account in de portal. Ga vervolgens terug naar het deel venster Diagnostische instellingen voor Azure Cosmos db in de portal om uw opslag account te selecteren. Het duurt enkele minuten duren voordat de zojuist gemaakte storage-accounts worden weergegeven in de vervolgkeuzelijst.

      * **Streamen naar een event hub**: Als u deze optie wilt gebruiken, hebt u een bestaande Event Hubs naam ruimte en Event Hub om verbinding te maken. Zie voor het maken van een Event Hubs-naamruimte, [een Event Hubs-naamruimte en een event hub maken met behulp van de Azure-portal](../event-hubs/event-hubs-create.md). Ga vervolgens terug naar deze pagina in de portal om de Event hub-naam ruimte en de naam van het beleid te selecteren.

      * **Verzenden naar log Analytics**: Als u deze optie wilt gebruiken, moet u een bestaande werk ruimte gebruiken of een nieuwe Log Analytics-werk ruimte maken door de stappen te volgen om [een nieuwe werk ruimte te maken](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) in de portal. 

   * U kunt de volgende gegevens in een logboek registreren:

      * **DataPlaneRequests**: Selecteer deze optie als u back-end-aanvragen wilt registreren voor alle Api's, waaronder SQL-, Graph-, MongoDB-, Cassandra-en Table-API-accounts in Azure Cosmos DB. Als u naar een opslagaccount archiveren bent, kunt u de bewaarperiode voor de diagnostische logboeken. Logboeken worden automatisch verwijderd nadat de bewaarperiode is verlopen. De volgende JSON-gegevens zijn een voorbeeld uitvoer van gegevens die zijn geregistreerd met DataPlaneRequests. De belangrijkste eigenschappen die u moet weten, zijn: Requestcharge, status code, clientIPaddress en partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Selecteer deze optie als u door de gebruiker geïnitieerde aanvragen van de front-end wilt registreren om aanvragen voor de Azure Cosmos DB-API voor MongoDB te leveren. MongoDB-aanvragen worden weer gegeven in MongoRequests en DataPlaneRequests. Als u naar een opslagaccount archiveren bent, kunt u de bewaarperiode voor de diagnostische logboeken. Logboeken worden automatisch verwijderd nadat de bewaarperiode is verlopen. De volgende JSON-gegevens zijn een voorbeeld uitvoer van gegevens die zijn geregistreerd met MongoRequests. De belangrijkste eigenschappen die u moet weten, zijn: Requestcharge, opcode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Selecteer deze optie om de uitgevoerde query tekst te registreren.  De volgende JSON-gegevens zijn een voorbeeld uitvoer van gegevens die zijn geregistreerd met QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **Metrische aanvragen**: Selecteer deze optie om uitgebreide gegevens op te slaan in metrische data van [Azure](../azure-monitor/platform/metrics-supported.md). Als u naar een opslagaccount archiveren bent, kunt u de bewaarperiode voor de diagnostische logboeken. Logboeken worden automatisch verwijderd nadat de bewaarperiode is verlopen.

3. Selecteer **Opslaan**.

    Als u een foutbericht ontvangt met de tekst ' kan niet bijwerken van diagnostische gegevens voor \<Werkruimtenaam >. Het abonnement \<abonnements-id > is niet geregistreerd voor het gebruik van microsoft.insights, "Voer de [oplossen Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) instructies voor het registreren van het account en voer deze procedure vervolgens opnieuw uit.

    Als u wijzigen hoe uw logboeken met diagnostische gegevens in de toekomst op elk gewenst moment worden opgeslagen wilt, kunt u terugkeren naar deze pagina om de instellingen diagnostische logboeken voor uw account te wijzigen.

## <a name="turn-on-logging-by-using-azure-cli"></a>Logboekregistratie inschakelen met behulp van Azure CLI

Om in te schakelen metrische gegevens en logboekregistratie van diagnostische gegevens met behulp van Azure CLI, gebruikt u de volgende opdrachten:

- Om in te schakelen opslag van logboeken met diagnostische gegevens in een opslagaccount, gebruikt u deze opdracht:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   De `resource` is de naam van het Azure Cosmos DB-account. De resource heeft de notatie '/Subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/< Azure_Cosmos_account_name > ' de `storage-account` is de naam van het opslag account waarnaar u de logboeken wilt verzenden. U kunt andere logboeken registreren door de parameter waarden van de categorie bij te werken naar "MongoRequests" of "DataPlaneRequests". 

- Om in te schakelen van diagnostische logboeken streamen naar een event hub, gebruikt u deze opdracht:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   De `resource` is de naam van het Azure Cosmos DB-account. De `event-hub-rule` is de Event hub regel-id. 

- Als u wilt verzenden, diagnostische logboeken naar Log Analytics-werkruimte inschakelen, gebruikt u deze opdracht:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

U kunt deze parameters voor het inschakelen van meerdere uitvoeropties combineren.

## <a name="turn-on-logging-by-using-powershell"></a>Logboekregistratie inschakelen met behulp van PowerShell

Als u wilt vastleggen van diagnostische gegevens inschakelen met behulp van PowerShell, moet u Azure Powershell met een minimale versie 1.0.1 of hoger.

Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) om Azure PowerShell te installeren en te koppelen aan uw Azure-abonnement.

Als u Azure PowerShell al hebt geïnstalleerd en u niet welke versie van het type van de console PowerShell weet `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Verbinding maken met uw abonnementen
Start een Azure PowerShell-sessie en gebruik de volgende opdracht om u aan te melden bij uw Azure-account:  

```powershell
Connect-AzAccount
```

Voer in het pop-upvenster in de browser uw gebruikersnaam en wachtwoord voor uw Azure-account in. Azure PowerShell haalt alle abonnementen die zijn gekoppeld aan dit account en standaard het eerste certificaat gebruikt.

Als u meer dan één abonnement hebt, hebt u mogelijk om op te geven van het specifieke abonnement dat is gebruikt voor het maken van uw Azure key vault. Als u wilt zien van de abonnementen voor uw account, typ de volgende opdracht:

```powershell
Get-AzSubscription
```

Als u het abonnement dat is gekoppeld aan het Azure Cosmos DB-account dat u zich aanmeldt, typ de volgende opdracht uit:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Als u meer dan één abonnement dat is gekoppeld aan uw account hebt, is het belangrijk om op te geven van het abonnement dat u wilt gebruiken.
>
>

Zie voor meer informatie over het configureren van Azure PowerShell [hoe u Azure PowerShell installeren en configureren](/powershell/azure/overview).

### <a id="storage"></a>Een nieuw opslagaccount voor uw logboeken maken
Hoewel u een bestaand opslagaccount voor uw logboeken in deze zelfstudie gebruiken kunt, maken we een nieuw opslagaccount dat toegewezen aan Azure Cosmos DB-Logboeken. Voor het gemak slaan we details van het opslagaccount in een variabele met de naam **sa**.

Voor extra beheer gemak gebruiken we in deze zelf studie dezelfde resource groep als die waarin de Azure Cosmos-data base zich bevindt. Vervang de waarden voor de **ContosoResourceGroup**, **contosocosmosdblogs**, en **Noord-centraal VS** parameters, zoals van toepassing:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Als u een bestaand opslagaccount gebruiken besluit, moet het account hetzelfde abonnement als uw Azure Cosmos DB-abonnement gebruiken. Het account moet ook gebruiken voor het Resource Manager-implementatiemodel, in plaats van het klassieke implementatiemodel.
>
>

### <a id="identify"></a>De Azure Cosmos DB-account voor uw logboeken identificeren
Stel de naam van het Azure Cosmos DB aan een variabele met de naam **account**, waarbij **ResourceName** is de naam van het Azure Cosmos DB-account.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Logboekregistratie inschakelen
Als u wilt logboekregistratie inschakelen voor Azure Cosmos DB, gebruikt u de `Set-AzDiagnosticSetting` cmdlet met variabelen voor de nieuwe storage-account, Azure Cosmos DB-account en de categorie voor logboekregistratie wilt inschakelen. Voer de volgende opdracht uit en stel de **-ingeschakeld** markering **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

De uitvoer voor de opdracht moet lijken op het volgende voorbeeld:

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

De uitvoer van de opdracht wordt bevestigd dat logboekregistratie nu is ingeschakeld voor uw database en dat informatie wordt opgeslagen naar uw opslagaccount.

U kunt eventueel ook het bewaarbeleid instellen voor uw Logboeken, zodat oudere logboeken automatisch worden verwijderd. Bijvoorbeeld, stel het bewaarbeleid met de **- RetentionEnabled** vlag ingesteld op **$true**. Stel de **- RetentionInDays** parameter **90** zodat logboeken die ouder zijn dan 90 dagen automatisch worden verwijderd.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Toegang tot uw logboeken
Azure Cosmos DB logboeken voor de categorie **DataPlaneRequests** worden opgeslagen in de container **Insights-logs-DataPlaneRequests** in het opslag account dat u hebt ingevoerd. 

Maak eerst een variabele voor de containernaam. De variabele wordt gebruikt tijdens de procedure.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Als alle blobs in deze container, typt u:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

De uitvoer voor de opdracht moet lijken op het volgende voorbeeld:

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

Zoals u in deze uitvoer zien kunt, volgen de blobs een naamconventie: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

De datum- en tijdwaarden maken gebruik van UTC.

Aangezien hetzelfde opslagaccount kan worden gebruikt voor het verzamelen van Logboeken voor meerdere resources, kunt u de volledig gekwalificeerde resource-ID in de blob-naam om te openen en downloaden van de specifieke blobs die u nodig hebt. Voordat we dat gaan doen, behandelen we het downloaden van alle blobs.

Maak eerst een map waarin u de blobs wilt downloaden. Bijvoorbeeld:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Haal vervolgens een lijst met alle van de blobs:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Sluis deze lijst via de `Get-AzStorageBlobContent` opdracht voor het downloaden van de blobs in de doelmap:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Wanneer u deze tweede opdracht uitvoert de **/** scheidingsteken in de blobnamen een volledige mapstructuur onder de doelmap gemaakt. Deze structuur wordt gebruikt om te downloaden en opslaan van de blobs als bestanden.

Als u alleen specifieke blobs wilt downloaden, moet u jokertekens gebruiken. Bijvoorbeeld:

* Als u meerdere databases hebt en wilt downloaden van Logboeken voor slechts één database met de naam **CONTOSOCOSMOSDB3**, gebruikt u de opdracht:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Als u hebt meerdere resourcegroepen en logboeken voor slechts één resourcegroep downloaden die u wilt, gebruikt u de opdracht `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Als u alle logboeken voor de maand van juli 2017 downloaden wilt, gebruikt u de opdracht `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

U kunt ook de volgende opdrachten uitvoeren:

* Als u wilt de status van diagnostische instellingen voor uw database opvragen, gebruikt u de opdracht `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Uitschakelen van logboekregistratie van het **DataPlaneRequests** categorie voor uw account, gebruikt u de opdracht `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


De blobs die worden geretourneerd in elk van deze query's worden opgeslagen als tekst en opgemaakt als een JSON-blob, zoals wordt weergegeven in de volgende code:

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

Logboeken met diagnostische gegevens zijn beschikbaar in uw account gedurende twee uur vanaf het moment dat de Azure Cosmos DB-bewerking is uitgevoerd. Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Standaard Azure access controlemethoden voor het beveiligen van uw logboeken en beperken wie toegang heeft tot deze gebruiken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.
* De bewaarperiode voor gegevens vlak aanvragen die zijn gearchiveerd in een Storage-account is geconfigureerd in de portal wanneer de **Log DataPlaneRequests** instelling is geselecteerd. Als u wilt wijzigen die instelling, Zie [Schakel logboekregistratie in Azure portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Weergave van logboeken in Azure Monitor-logboeken

Als u de optie **Send to log Analytics** hebt geselecteerd wanneer u diagnostische logboek registratie hebt ingeschakeld, worden de diagnostische gegevens uit uw container binnen twee uur doorgestuurd naar Azure monitor Logboeken. Wanneer u Azure Monitor logboeken onmiddellijk bekijkt nadat u logboek registratie hebt ingeschakeld, worden er geen gegevens weer gegeven. Slechts twee uur wacht en probeer het opnieuw. 

Controleer voordat u uw logboeken bekijkt of uw Log Analytics-werk ruimte is bijgewerkt voor het gebruik van de nieuwe Kusto-query taal. Als u wilt controleren, opent u de [Azure Portal](https://portal.azure.com), selecteert u **log Analytics werk ruimten** helemaal links en selecteert u vervolgens de naam van de werk ruimte, zoals wordt weer gegeven in de volgende afbeelding. De **Log Analytics-werkruimte** pagina wordt weergegeven:

![Azure Monitor Logboeken in de Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.  

Als u het volgende bericht wordt weergegeven op de **Log Analytics-werkruimte** pagina uw werkruimte is niet bijgewerkt voor het gebruik van de nieuwe taal. Zie voor meer informatie over het upgraden naar de nieuwe querytaal [uw Azure Log Analytics-werkruimte upgraden voor nieuwe zoekopdrachten](../log-analytics/log-analytics-log-search-upgrade.md). 

![Upgrade bericht van Azure Monitor-logboeken](./media/logging/upgrade-notification.png)

Als u uw diagnostische gegevens in Azure Monitor logboeken wilt weer geven, opent u de pagina **zoeken** in Logboeken vanuit het menu links of het gebied **beheer** van de pagina, zoals wordt weer gegeven in de volgende afbeelding:

![Opties voor het doorzoeken van logboekbestanden in de Azure-portal](./media/logging/log-analytics-open-log-search.png)

Nu dat u gegevensverzameling hebt ingeschakeld, het volgende voorbeeld van de zoekopdracht in Logboeken uitvoeren met behulp van de nieuwe querytaal om te zien van de 10 meest recente logboeken `AzureDiagnostics | take 10`.

![Voorbeeld van zoeken in Logboeken voor de 10 meest recente Logboeken](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Query's

Hier volgen enkele aanvullende query's die u kunt invoeren in het **zoekvak** voor Logboeken om u te helpen bij het bewaken van uw Azure Cosmos-containers. Deze query's werken met de [nieuwe taal](../log-analytics/log-analytics-log-search-upgrade.md). 

Zie voor meer informatie over de betekenis van de gegevens die wordt geretourneerd door elke zoeken in Logboeken, [Azure Cosmos DB-logboekgegevens interpreteren](#interpret).

* Zoeken naar alle diagnostische logboeken van Azure Cosmos DB voor een opgegeven periode:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Als u wilt zoeken naar de 10 meest recent geregistreerde gebeurtenissen:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Query uitvoeren voor alle bewerkingen, gegroepeerd op bewerkingstype:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Query voor alle bewerkingen, gegroepeerd op **Resource**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Zoeken naar alle gebruikersactiviteiten, gegroepeerd op resource:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Met deze opdracht is voor een activiteitenlogboek, niet een diagnostisch logboek.

* Query uitvoeren op waarvoor operations duurt langer dan 3 milliseconden:

    ```
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Query uitvoeren voor welke agent de bewerkingen wordt uitgevoerd:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Zoeken naar wanneer de langlopende bewerkingen zijn uitgevoerd:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```

Zie [zoeken naar Logboeken in azure monitor logs](../log-analytics/log-analytics-log-search-new.md)voor meer informatie over het gebruik van de nieuwe Zoek taal voor Logboeken. 

## <a id="interpret"></a>-Logboekgegevens interpreteren

Diagnostische gegevens die zijn opgeslagen in Azure Storage en Azure Monitor Logboeken gebruiken een vergelijkbaar schema. 

De volgende tabel beschrijft de inhoud van elke logboekvermelding.

| Azure Storage-veld of eigenschap | Eigenschap Azure Monitor logboeken | Description |
| --- | --- | --- |
| **tijd** | **TimeGenerated** | De datum en tijd (UTC) wanneer de bewerking opgetreden. |
| **ResourceId** | **Resource** | Het Azure Cosmos DB-account waarvoor de logboeken zijn ingeschakeld.|
| **Categorie** | **Categorie** | Voor Azure Cosmos DB-Logboeken is **DataPlaneRequests** is de enige beschikbare waarde. |
| **OperationName** | **OperationName** | Naam van de bewerking. Deze waarde kan een van de volgende bewerkingen zijn: Maken, bijwerken, lezen, ReadFeed, verwijderen, vervangen, uitvoeren, SqlQuery, query, JSQuery, Head, HeadFeed of Upsert.   |
| **Eigenschappen** | N.v.t. | De inhoud van dit veld worden beschreven in de rijen die volgen. |
| **ActivityId** | **activityId_g** | De unieke GUID voor de geregistreerde bewerking. |
| **UserAgent** | **userAgent_s** | Een tekenreeks waarmee de clientagent van de gebruiker die de aanvraag uitvoert. De indeling is {gebruiker agent name} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Het type van de toegang tot bronnen. Deze waarde kan een van de volgende resource typen zijn: Data Base, container, document, bijlage, gebruiker, machtiging, opgeslagen procedure, trigger, UserDefinedFunction of aanbieding. |
| **statusCode** | **statusCode_s** | De reactiestatus van de bewerking. |
| **requestResourceId** | **ResourceId** | De resourceId die betrekking hebben op de aanvraag. De waarde kan verwijzen naar databaseRid, collectionRid of documentRid, afhankelijk van de bewerking die wordt uitgevoerd.|
| **clientIpAddress** | **clientIpAddress_s** | IP-adres van de client. |
| **requestCharge** | **requestCharge_s** | Het aantal ru's die worden gebruikt door de bewerking |
| **collectionRid** | **collectionId_s** | De unieke ID voor de verzameling.|
| **Duur** | **duration_s** | De duur van de bewerking, in milliseconden. |
| **requestLength** | **requestLength_s** | De lengte van de aanvraag, in bytes. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Deze waarde is niet leeg zijn wanneer [brontokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) worden gebruikt voor verificatie. De waarde verwijst naar de resource-ID van de gebruiker. |

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie over het inschakelen van logboekregistratie en metrische gegevens en logboekbestanden categorieën die worden ondersteund door de verschillende Azure-services, zowel de [overzicht van metrische gegevens in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) en [overzicht van Azure diagnostische logboeken ](../azure-monitor/platform/diagnostic-logs-overview.md) artikelen.
- Lees deze artikelen voor meer informatie over eventhubs:
   - [Wat is Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Aan de slag met Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Lezen [metrische gegevens en logboeken met diagnostische gegevens downloaden uit Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Meer [informatie over logboek zoekopdrachten in azure monitor logs](../log-analytics/log-analytics-log-search-new.md).
