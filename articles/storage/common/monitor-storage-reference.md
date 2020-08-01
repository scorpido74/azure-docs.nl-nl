---
title: Informatie over Azure Storage bewakings gegevens | Microsoft Docs
description: Naslag informatie over Logboeken en metrieken voor het bewaken van gegevens van Azure Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 28a127b4debeacd2562867008bc594897558d50d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446847"
---
# <a name="azure-storage-monitoring-data-reference"></a>Naslag informatie over Azure Storage bewakings gegevens

Zie [bewaking Azure Storage](monitor-storage.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Storage.

## <a name="metrics"></a>Metrische gegevens

De volgende tabellen geven een lijst van de platform gegevens die zijn verzameld voor Azure Storage. 

### <a name="capacity-metrics"></a>Metrische gegevens over capaciteit

Waarden voor capaciteits metrieken worden elk uur verzonden naar Azure Monitor. De waarden worden dagelijks vernieuwd. De tijdgranulariteit definieert het tijds interval waarvoor metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens over capaciteit zijn één uur (PT1H).

Azure Storage biedt de volgende metrische gegevens over capaciteit in Azure Monitor.

#### <a name="account-level"></a>Account niveau

In deze tabel worden [metrische gegevens op account niveau](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts)weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| UsedCapacity | De hoeveelheid opslag die wordt gebruikt door het opslag account. Voor standaardopslagaccounts is dit de som van de capaciteit die wordt gebruikt door blob, table, file en queue. Voor premium-opslagaccounts en blob-opslagaccounts is dit hetzelfde als BlobCapacity. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

#### <a name="blob-storage"></a>Blob Storage

In deze tabel worden [metrische gegevens voor Blob-opslag](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsblobservices)weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| BlobCapacity | Het totaal van de Blob-opslag die in het opslag account wordt gebruikt. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 <br/> Dimensies: **BlobType**en **BlobTier** ([definitie](#metrics-dimensions)) |
| BlobCount    | Het aantal BLOB-objecten dat is opgeslagen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 <br/> Dimensies: **BlobType**en **BlobTier** ([definitie](#metrics-dimensions)) |
| BlobProvisionedSize | De hoeveelheid opslag die is ingericht in het opslag account. Deze metriek geldt alleen voor Premium Storage-accounts. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld |
| ContainerCount    | Het aantal containers in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| IndexCapacity     | De hoeveelheid opslag die wordt gebruikt door ADLS Gen2 hiërarchische index <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

#### <a name="table-storage"></a>Table Storage

In deze tabel worden [metrische gegevens van tabel opslag](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices)weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| TableCapacity | De hoeveelheid tabel opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| TableCount   | Het aantal tabellen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| TableEntityCount | Het aantal tabel entiteiten in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

#### <a name="queue-storage"></a>Queue Storage

In deze tabel worden [metrische opslag gegevens](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)voor de wachtrij weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| QueueCapacity | De hoeveelheid wachtrij opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| QueueCount   | Het aantal wacht rijen in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| QueueMessageCount | Het geschatte aantal wachtrij berichten in de Queue-service van het opslag account. <br/><br/>Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |

#### <a name="file-storage"></a>File Storage

In deze tabel worden [metrische gegevens over bestands opslag](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices)weer gegeven.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| FileCapacity | De hoeveelheid bestands opslag die door het opslag account wordt gebruikt. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| FileCount   | Het aantal bestanden in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| FileShareCount | Het aantal bestands shares in het opslag account. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Waarde-voor beeld: 1024 |
| FileShareProvisionedIOPS | Het aantal ingerichte IOPS op een bestands share. Deze metriek is alleen van toepassing op Premium file storage. <br/><br/> Eenheid: bytes <br/> Aggregatie type: gemiddeld |

### <a name="transaction-metrics"></a>Metrische gegevens voor transacties

Metrische gegevens over trans acties worden verzonden voor elke aanvraag naar een opslag account van Azure Storage naar Azure Monitor. In het geval van geen activiteit in uw opslag account, worden er geen gegevens in de periode weer gegeven. Alle metrische gegevens over trans acties zijn beschikbaar op zowel account-als service niveau (Blob-opslag, tabel opslag, Azure Files en wachtrij opslag). De tijd korrel definieert het tijds interval dat metrische waarden worden weer gegeven. De ondersteunde tijd korrels voor alle metrische gegevens van de trans actie zijn PT1H en PT1M.

Azure Storage levert de volgende metrische gegevens over trans acties in Azure Monitor.

| Gegevens | Beschrijving |
| ------------------- | ----------------- |
| Transacties | Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. <br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Toepasselijke dimensies: ResponseType, geotype, ApiName en Authentication ([definitie](#metrics-dimensions))<br/> Waarde-voor beeld: 1024 |
| Inkomend verkeer | De hoeveelheid inkomende gegevens. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure. <br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| Uitgaand verkeer | De hoeveelheid uitgaande gegevens. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer. <br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| SuccessServerLatency | De gemiddelde tijd die nodig is om een aanvraag door Azure Storage te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency. <br/><br/> Eenheid: milliseconden <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| SuccessE2ELatency | De gemiddelde end-to-end-latentie van geslaagde aanvragen aan een opslagservice of de opgegeven API-bewerking. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen. <br/><br/> Eenheid: milliseconden <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 1024 |
| Beschikbaarheid | Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde voor het totale aantal factureerbare aanvragen te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. <br/><br/> Eenheid: percentage <br/> Aggregatie type: gemiddeld <br/> Toepasselijke dimensies: geotype, ApiName en verificatie ([definitie](#metrics-dimensions)) <br/> Waarde-voor beeld: 99,99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrische dimensies

Azure Storage ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor.

| Dimensie naam | Beschrijving |
| ------------------- | ----------------- |
| **BlobType** | Het type BLOB voor alleen metrische gegevens van blobs. De ondersteunde waarden zijn **BlockBlob**, **PageBlob**en **Azure data Lake Storage**. Toevoeg-blobs zijn opgenomen in **BlockBlob**. |
| **BlobTier** | Azure Storage biedt verschillende toegangs lagen, waarmee u gegevens van blob-objecten op de meest rendabele manier kunt opslaan. Meer weer geven in [Azure Storage BLOB-laag](../blobs/storage-blob-storage-tiers.md). De ondersteunde waarden zijn onder andere: <br/> <li>**Warm**: warme laag</li> <li>**Cool**: cool-laag</li> <li>**Archief**: laag van archief</li> <li>**Premium**: Premium-laag voor blok-BLOB</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: laag typen voor Premium-pagina-BLOB</li> <li>**Standaard**: laag type voor de standaard pagina-BLOB</li> <li>**Ongetierd**: laag type voor algemeen gebruik v1-opslag account</li> |
| **Geotype** | Trans actie van het primaire of secundaire cluster. De beschik bare waarden zijn onder andere **primaire** en **secundaire**. Dit is van toepassing op lees toegang geografisch redundante opslag (RA-GRS) bij het lezen van objecten van een secundaire Tenant. |
| **ResponseType** | Antwoord type voor de trans actie. De beschikbare waarden zijn onder meer: <br/><br/> <li>**ServerOtherError**: alle overige serverfouten, behalve diegene die zijn beschreven </li> <li>**ServerBusyError**: geverifieerde aanvraag waardoor een HTTP 503-statuscode is geretourneerd. </li> <li>**ServerTimeoutError**: geverifieerde aanvraag met time-out waardoor een HTTP 500-statuscode is geretourneerd. De time-out is opgetreden vanwege een serverfout. </li> <li>**AuthorizationError**: geverifieerde aanvraag die is mislukt vanwege niet-geautoriseerde toegang tot gegevens of een autorisatiefout. </li> <li>**NetworkError**: geverifieerde aanvraag die is mislukt vanwege netwerkfouten. Treedt meestal op als dooreen client voortijdig een verbinding wordt verbroken voordat de time-out voorbij is. </li><li>**ClientAccountBandwidthThrottlingError**: de aanvraag wordt beperkt over de band breedte voor het overschrijden van de [schaalbaarheids limieten van het opslag account](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: de aanvraag wordt beperkt op aanvraag snelheid voor overschrijding van de [schaalbaarheids limieten van het opslag account](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: andere fout met beperking aan client zijde. ClientAccountBandwidthThrottlingError en ClientAccountRequestThrottlingError zijn uitgesloten.</li> <li>**ClientTimeoutError**: geverifieerde aanvraag met time-out waardoor een HTTP 500-statuscode is geretourneerd. Als de time-out van het clientnetwerk of van de aanvraag is ingesteld op een lagere waarde dan door de opslagservice wordt verwacht, is er sprake van een verwachte time-out. Anders wordt deze als ServerTimeoutError gerapporteerd. </li> <li>**ClientOtherError**: alle overige fouten aan de clientzijde, behalve diegene die zijn beschreven. </li> <li>**Geslaagd**: succesvolle aanvraag</li> <li> **SuccessWithThrottling**: de aanvraag is voltooid wanneer een SMB-client wordt beperkt tijdens de eerste poging (en), maar na nieuwe pogingen slaagt.</li> |
| **ApiName** | De naam van de bewerking. Bijvoorbeeld: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Zie [document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor alle bewerkings namen. |
| **Verificatie** | Het verificatie type dat wordt gebruikt in trans acties. De beschikbare waarden zijn onder meer: <br/> <li>**AccountKey**: de trans actie is geverifieerd met de sleutel van het opslag account.</li> <li>**SAS**: de trans actie wordt geverifieerd met hand tekeningen voor gedeelde toegang.</li> <li>**OAuth**: de trans actie wordt geverifieerd met OAuth-toegangs tokens.</li> <li>**Anoniem**: de trans actie is anoniem aangevraagd. Het bevat geen Preflight-aanvragen.</li> <li>**AnonymousPreflight**: de trans actie is een Preflight-aanvraag.</li> |

Voor de metrische gegevens die maat staven ondersteunen, moet u de dimensie waarde opgeven om de bijbehorende metrische waarden weer te geven. Als u bijvoorbeeld de **transactie** waarde voor geslaagde reacties bekijkt, moet u de **ResponseType** -dimensie filteren op **geslaagd**. Of als u de **BlobCount** -waarde voor blok-BLOB bekijkt, moet u de dimensie **BlobType** filteren met **BlockBlob**.

## <a name="resource-logs-preview"></a>Resource Logboeken (preview-versie)

> [!NOTE]
> Azure Storage-Logboeken in Azure Monitor bevinden zich in de open bare preview-versie en is beschikbaar voor preview-tests in alle open bare Cloud regio's. Zie [Deze pagina](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)als u zich wilt inschrijven voor de preview-versie.  Met deze preview-versie kunt u logboeken maken voor blobs (inclusief Azure Data Lake Storage Gen2), bestanden, wacht rijen, tabellen, Premium-opslag accounts in versie 1 voor algemeen gebruik en voor algemeen gebruik v2-opslag accounts. Klassieke opslag accounts worden niet ondersteund.

De volgende tabel geeft een lijst van de eigenschappen voor Azure Storage bron Logboeken wanneer ze worden verzameld in Azure Monitor Logboeken of Azure Storage. De eigenschappen beschrijven de bewerking, de service en het type autorisatie dat is gebruikt om de bewerking uit te voeren.

### <a name="fields-that-describe-the-operation"></a>Velden waarin de bewerking wordt beschreven

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| Eigenschap | Beschrijving |
|:--- |:---|
|**time** | De UTC-tijd (Universal Time Coordinated) wanneer de aanvraag is ontvangen door de opslag. Bijvoorbeeld: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | De resource-ID van het opslag account. Bijvoorbeeld: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | De categorie van de aangevraagde bewerking. Bijvoorbeeld: `StorageRead` , `StorageWrite` of `StorageDelete` .|
|**operationName** | Het type REST bewerking dat is uitgevoerd. <br> Zie [Opslaganalyse logboeken en status berichten](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor een volledige lijst met bewerkingen. |
|**operationVersion** | De versie van de opslag service die tijdens het maken van de aanvraag is opgegeven. Dit is gelijk aan de waarde van de **x-MS-version-** header. Bijvoorbeeld: `2017-04-17`.|
|**schemaVersion** | De schema versie van het logboek. Bijvoorbeeld: `1.0`.|
|**statuscode** | De HTTP-status code voor de aanvraag. Als de aanvraag wordt onderbroken, kan deze waarde worden ingesteld op `Unknown` . <br> Bijvoorbeeld: `206` |
|**statusText** | De status van de aangevraagde bewerking.  Zie [Opslaganalyse het onderwerp geregistreerde bewerkingen en status berichten](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor een volledige lijst met status berichten. In versie 2017-04-17 en hoger wordt het status bericht `ClientOtherError` niet gebruikt. In plaats daarvan bevat dit veld een fout code. Bijvoorbeeld: `SASSuccess`  |
|**durationMs** | De totale tijd, uitgedrukt in milliseconden, om de aangevraagde bewerking uit te voeren. Dit omvat de tijd voor het lezen van de inkomende aanvraag en het verzenden van het antwoord naar de aanvrager. Bijvoorbeeld: `12`.|
|**callerIpAddress** | Het IP-adres van de aanvrager, inclusief het poort nummer. Bijvoorbeeld: `192.100.0.102:4362`. |
|**correlationId** | De ID die wordt gebruikt voor het correleren van Logboeken tussen resources. Bijvoorbeeld: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**locatie** | De locatie van het opslag account. Bijvoorbeeld: `North Europe`. |
|**Protocolsubstatus**|Het protocol dat wordt gebruikt voor de bewerking. Bijvoorbeeld:, `HTTP` , `HTTPS` `SMB` of`NFS`|
| **URI** | Een uniform resource-id die wordt aangevraagd. Bijvoorbeeld: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Velden die beschrijven hoe de bewerking is geverifieerd

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| Eigenschap | Beschrijving |
|:--- |:---|
|**identiteit/type** | Het type verificatie dat is gebruikt om de aanvraag uit te voeren. Bijvoorbeeld:, `OAuth` , `SAS Key` `Account Key` of`Anonymous` |
|**identiteits-tokenHash**|Dit veld is alleen gereserveerd voor intern gebruik. |
|**autorisatie/actie** | De actie die is toegewezen aan de aanvraag. Bijvoorbeeld: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**autorisatie-roleAssignmentId** | De roltoewijzings-ID. Bijvoorbeeld: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorisatie-Roledefinitionid hebben** | De roldefinitie-ID. Bijvoorbeeld: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals/id** | De ID van de beveiligingsprincipal. Bijvoorbeeld: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals/type** | Het type van de beveiligingsprincipal. Bijvoorbeeld: `ServicePrincipal`. |
|**aanvrager/appID** | De Open Authorization (OAuth)-toepassings-ID die wordt gebruikt als aanvrager. <br> Bijvoorbeeld: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**aanvrager/doel groep** | De OAuth-doel groep van de aanvraag. Bijvoorbeeld: `https://storage.azure.com`. |
|**aanvrager/objectId** | De OAuth-object-ID van de aanvrager. In het geval van Kerberos-verificatie vertegenwoordigt de object-id van door Kerberos geverifieerde gebruiker. Bijvoorbeeld: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**aanvrager/tenantId** | De OAuth-Tenant-ID van de identiteit. Bijvoorbeeld: `72f988bf-86f1-41af-91ab-222222222222`.|
|**aanvrager/tokenIssuer** | De certificaat verlener van het OAuth-token. Bijvoorbeeld: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**aanvrager/UPN** | De UPN (User Principal Name) van de aanvrager. Bijvoorbeeld: `someone@contoso.com`. |
|**aanvrager/gebruikers naam** | Dit veld is alleen gereserveerd voor intern gebruik.|

### <a name="fields-that-describe-the-service"></a>Velden waarin de service wordt beschreven

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| Eigenschap | Beschrijving |
|:--- |:---|
|**accountName** | De naam van het opslag account. Bijvoorbeeld: `mystorageaccount`.  |
|**requestUrl** | De aangevraagde URL. Bijvoorbeeld: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | De waarde van de **User-Agent-header** , in aanhalings tekens. Bijvoorbeeld: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | De waarde van de **verwijzende** header. Bijvoorbeeld: `http://contoso.com/about.html`.|
|**clientRequestId** | De waarde voor de **x-MS-Client-Request-id** van de aanvraag. Bijvoorbeeld: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**ETAG** | De ETag-id voor het geretourneerde object, in aanhalings tekens. Bijvoorbeeld: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | De totale tijd in milliseconden voor het uitvoeren van de aangevraagde bewerking. Deze waarde omvat geen netwerk latentie (de tijd voor het lezen van de inkomende aanvraag en het verzenden van het antwoord naar de aanvrager). Bijvoorbeeld: `22`. |
|**Service** | De service die aan deze aanvraag is gekoppeld. Bijvoorbeeld: `blob` ,, `table` `files` of `queue` . |
|**operationCount** | Het aantal geregistreerde bewerkingen dat is betrokken bij de aanvraag. Dit aantal begint met een index van `0` . Voor sommige aanvragen is meer dan één bewerking vereist, zoals een aanvraag voor het kopiëren van een blob. De meeste aanvragen voeren slechts één bewerking uit. Bijvoorbeeld: `1`. |
|**requestHeaderSize** | De grootte van de aanvraag header, uitgedrukt in bytes. Bijvoorbeeld: `578`. <br>Als een aanvraag mislukt, kan deze waarde leeg zijn. |
|**requestBodySize** | De grootte van de aanvraag pakketten, uitgedrukt in bytes, die door de opslag service worden gelezen. <br> Bijvoorbeeld: `0`. <br>Als een aanvraag mislukt, kan deze waarde leeg zijn.  |
|**responseHeaderSize** | De grootte van de reactie header, uitgedrukt in bytes. Bijvoorbeeld: `216`. <br>Als een aanvraag mislukt, kan deze waarde leeg zijn.  |
|**responseBodySize** | De grootte van de antwoord pakketten, geschreven door de opslag service, in bytes. Als een aanvraag mislukt, kan deze waarde leeg zijn. Bijvoorbeeld: `216`.  |
|**requestMd5** | De waarde van de kop **Content-MD5** of de header **x-MS-content-MD5** in de aanvraag. De MD5-hash-waarde die is opgegeven in dit veld vertegenwoordigt de inhoud in de aanvraag. Bijvoorbeeld: `788815fd0198be0d275ad329cafd1830`. <br>Dit veld mag leeg zijn.  |
|**serverMd5** | De waarde van de MD5-hash die wordt berekend door de opslag service. Bijvoorbeeld: `3228b3cf1069a5489b298446321f8521`. <br>Dit veld mag leeg zijn.  |
|**lastModifiedTime** | De tijd van de laatste wijziging (LMT) van het geretourneerde object.  Bijvoorbeeld: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Dit veld is leeg voor bewerkingen die meerdere objecten kunnen retour neren. |
|**conditionsUsed** | Een door punt komma's gescheiden lijst met sleutel-waardeparen die een voor waarde vertegenwoordigen. De voor waarden kunnen een van de volgende oorzaken hebben: <li> If-Modified-sinds <li> Als-ongewijzigd-sinds <li> If-match <li> If-None-Match  <br> Bijvoorbeeld: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | De waarde van de content-length-header voor de aanvraag die naar de opslag service wordt verzonden. Als de aanvraag is voltooid, is deze waarde gelijk aan requestBodySize. Als een aanvraag mislukt, is deze waarde mogelijk niet gelijk aan requestBodySize of kan deze leeg zijn. |
|**tlsVersion** | De TLS-versie die wordt gebruikt in de verbinding van de aanvraag. Bijvoorbeeld: `TLS 1.2`. |
|**smbTreeConnectID** | De SMB- **treeConnectId** (Server Message Block) is ingesteld tijdens de structuur verbindings tijd. Bijvoorbeeld: `0x3` |
|**smbPersistentHandleID** | De permanente ingangs-ID van een SMB2-aanvraag maken die de netwerk verbinding verbreekt.  Ernaar wordt verwezen in [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 als **SMB2_FILEID. Permanent**. Bijvoorbeeld: `0x6003f` |
|**smbVolatileHandleID** | Vluchtige ingangs-ID van een SMB2 maken-aanvraag die wordt gerecycled bij het opnieuw verbinden met het netwerk.  Ernaar wordt verwezen in [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 als **SMB2_FILEID. Vluchtig**. Bijvoorbeeld: `0xFFFFFFFF00000065` |
|**smbMessageID** | De verbinding met de relatieve **MessageId**. Bijvoorbeeld: `0x3b165` |
|**smbCreditsConsumed** | De ingang of uitgave die door de aanvraag wordt verbruikt in eenheden van 64 kB. Bijvoorbeeld: `0x3` |
|**smbCommandDetail** | Meer informatie over deze specifieke aanvraag in plaats van het algemene type aanvraag. Bijvoorbeeld: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | De **Bestands identificatie** die aan het bestand of de map is gekoppeld.  Ongeveer hetzelfde als een NTFS-bestands-id. Bijvoorbeeld: `0x9223442405598953` |
|**smbSessionID** | De SMB2- **sessie-id** is ingesteld tijdens de installatie van de sessie. Bijvoorbeeld: `0x8530280128000049` |
|**smbCommandMajor uint32** | Waarde in de **SMB2_HEADER. opdracht**. Dit is momenteel een getal tussen 0 en 18, inclusief. Bijvoorbeeld: `0x6` |
|**smbCommandMinor** | , Indien van toepassing, de subklasse van **SmbCommandMajor**. Bijvoorbeeld: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Zie tevens

- Zie [bewaking Azure Storage](monitor-storage.md) voor een beschrijving van de bewakings Azure Storage.
- Zie [Azure-resources bewaken met Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.