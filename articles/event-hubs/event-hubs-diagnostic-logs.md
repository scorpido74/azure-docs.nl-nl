---
title: Diagnostische logboeken - Azure Event Hub instellen | Microsoft Docs
description: Meer informatie over het instellen van activiteitenlogboeken en diagnostische logboeken voor eventhubs in Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 12a20abb4014712f26e5827bcd1d3c822e8b25a1
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750724"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnostische logboeken voor een Azure event hub instellen

U kunt twee typen logboeken voor Azure Event Hubs bekijken:

* **[Activiteitenlogboeken](../azure-monitor/platform/platform-logs-overview.md)** : deze logboeken zijn informatie over de bewerkingen die worden uitgevoerd op een andere taak. De logboeken zijn altijd ingeschakeld.
* **[Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md)** : U kunt diagnostische logboeken voor een uitgebreidere weergave van alles wat er gebeurt met een taak configureren. Diagnostische logboeken voor activiteiten vanaf het moment dat de taak is gemaakt totdat de taak wordt verwijderd, met inbegrip van updates en activiteiten die plaatsvinden terwijl de taak wordt uitgevoerd.

## <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Volg deze stappen zodat logboeken met diagnostische gegevens:

1.  In de [Azure-portal](https://portal.azure.com)onder **bewaking en beheer**, klikt u op **diagnoselogboeken**.

    ![Deelvenster navigatie naar Logboeken met diagnostische gegevens](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klik op de resource die u wilt bewaken.

3.  Klik op **Diagnostische gegevens inschakelen**.

    ![Logboeken met diagnostische gegevens inschakelen](./media/event-hubs-diagnostic-logs/image2.png)

4.  Voor **Status**, klikt u op **op**.

    ![Wijzig de status van diagnostische logboeken](./media/event-hubs-diagnostic-logs/image3.png)

5.  Stel het gewenste archief doel in; bijvoorbeeld een opslag account, een Event Hub of Azure Monitor Logboeken.

6.  De nieuwe instellingen voor diagnostische gegevens opslaan.

Nieuwe instellingen van kracht in ongeveer 10 minuten. Hierna logboeken worden weergegeven in de geconfigureerde archivering doel, in de **diagnoselogboeken** deelvenster.

Zie voor meer informatie over het configureren van diagnostische gegevens over de [overzicht van diagnostische logboeken in Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorieën van diagnostische logboeken

Eventhubs vastleggen diagnostische logboeken voor twee categorieën:

* **Logboeken archiveren**: logboeken die specifiek betrekking hebben op Event Hubs-archieven, logboeken met betrekking tot het archiveren van fouten.
* **Operationele logboeken**: informatie over wat er gebeurt tijdens Event Hubs-bewerkingen, met name het bewerkingstype, met inbegrip van de event hub maken, resources die worden gebruikt en de status van de bewerking.

## <a name="diagnostic-logs-schema"></a>Diagnostische logboeken schema

Alle logboeken worden opgeslagen in JavaScript Object Notation (JSON)-indeling. Elk item heeft tekenreeksvelden die gebruikmaken van de indeling die wordt beschreven in de volgende secties.

### <a name="archive-logs-schema"></a>Logboeken archiefschema

Archief log JSON-tekenreeksen zijn onder andere elementen die worden vermeld in de volgende tabel:

Name | Beschrijving
------- | -------
Taaknaam | Beschrijving van de taak die is mislukt.
ActivityId | Interne ID, die wordt gebruikt voor het bijhouden.
trackingId | Interne ID, die wordt gebruikt voor het bijhouden.
resourceId | Azure Resource Manager resource-ID.
eventHub | Event hub volledige naam (inclusief de naam van naamruimte).
PartitionId | Event Hub-partitie wordt geschreven.
archiveStep | ArchiveFlushWriter
startTime | Begintijd van de fout.
fouten | Aantal keren dat is een fout opgetreden.
durationInSeconds | De duur van de fout.
message | Foutbericht.
category | ArchiveLogs

De volgende code is een voorbeeld van een logboek archiveren JSON-tekenreeks:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>Operationele logboeken schema

Operationeel logboek van JSON-tekenreeksen zijn onder andere elementen die worden vermeld in de volgende tabel:

Name | Beschrijving
------- | -------
ActivityId | Interne ID gebruikt voor het bijhouden van gebruik.
EventName | Naam van de bewerking.  
resourceId | Azure Resource Manager resource-ID.
SubscriptionId | Abonnements-ID.
EventTimeString | Bewerkingstijd.
EventProperties | Eigenschappen van de bewerking.
Status | Status van de bewerking.
Caller | De aanroeper van bewerking (Azure portal of de beheer-client).
category | OperationalLogs

De volgende code is een voorbeeld van een operationeel logboek van JSON-tekenreeks:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Eventhubs](event-hubs-what-is-event-hubs.md)
* [Event Hubs-API-overzicht](event-hubs-api-overview.md)
* [Aan de slag met Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
