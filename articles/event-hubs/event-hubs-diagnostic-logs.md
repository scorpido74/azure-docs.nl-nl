---
title: Diagnostische logboeken instellen - Azure Event Hub | Microsoft Documenten
description: Meer informatie over het instellen van activiteitslogboeken en diagnostische logboeken voor gebeurtenishubs in Azure.
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
ms.openlocfilehash: 68aa62ad34f8db531d439a581ef024862da0f90c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162307"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnostische logboeken instellen voor een Azure Event Hub

U twee typen logboeken voor Azure Event Hubs weergeven:

* **[Activiteitslogboeken:](../azure-monitor/platform/platform-logs-overview.md)** met deze logboeken vindt u informatie over bewerkingen die op een taak worden uitgevoerd. De logboeken zijn altijd ingeschakeld.
* **[Diagnostische logboeken:](../azure-monitor/platform/platform-logs-overview.md)** U diagnostische logboeken configureren voor een rijkere weergave van alles wat er met een taak gebeurt. Diagnostische logboeken hebben betrekking op activiteiten vanaf het moment dat de taak wordt gemaakt totdat de taak is verwijderd, inclusief updates en activiteiten die plaatsvinden terwijl de taak wordt uitgevoerd.

## <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Voer de volgende stappen uit om diagnostische logboeken in te schakelen:

1.  Klik in de [Azure-portal](https://portal.azure.com)onder **Monitoring + Beheer**op Diagnostische **logboeken**.

    ![Navigatie van deelvenster naar diagnostische logboeken](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klik op de resource die u wilt controleren.

3.  Klik op **Diagnostische gegevens inschakelen**.

    ![Diagnostische logboeken inschakelen](./media/event-hubs-diagnostic-logs/image2.png)

4.  Klik voor **status**op **Aan**.

    ![De status van diagnostische logboeken wijzigen](./media/event-hubs-diagnostic-logs/image3.png)

5.  Stel het gewenste archiefdoel in. bijvoorbeeld een opslagaccount, een gebeurtenishub of Azure Monitor-logboeken.

6.  Sla de nieuwe diagnostische instellingen op.

Nieuwe instellingen worden van kracht in ongeveer 10 minuten. Daarna worden logboeken weergegeven in het geconfigureerde archiefdoel in het **deelvenster Logboeken diagnostische gegevens.**

Zie het overzicht van Diagnostische [logboeken van Azure](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostische gegevens.

## <a name="diagnostic-logs-categories"></a>Categorieën diagnostische logboeken

Event Hubs legt diagnostische logboeken vast voor twee categorieën:

* **Archieflogboeken**: logboeken met betrekking tot Event Hubs-archieven, in het bijzonder, logboeken met betrekking tot archieffouten.
* **Operationele logboeken:** informatie over wat er gebeurt tijdens gebeurtenishubsbewerkingen, met name het bewerkingstype, inclusief het maken van gebeurtenishubs, gebruikte resources en de status van de bewerking.

## <a name="diagnostic-logs-schema"></a>Schema diagnostische logboeken

Alle logboeken worden opgeslagen in de JSON-indeling (JavaScript Object Notation). Elk item heeft tekenreeksvelden die de notatie gebruiken die in de volgende secties is beschreven.

### <a name="archive-logs-schema"></a>Schema voor archieflogboeken

Json-tekenreeksen voor archieflogboeken bevatten elementen in de volgende tabel:

Name | Beschrijving
------- | -------
Taaknaam | Beschrijving van de taak die is mislukt.
ActivityId (ActivityId) | Interne ID, gebruikt voor tracking.
trackingId | Interne ID, gebruikt voor tracking.
resourceId | Azure Resource Manager-bron-id.
eventHub | Volledige naam van gebeurtenishub (inclusief naamruimtenaam).
partitionId | Gebeurtenishubpartitie waarnaar wordt geschreven.
archiefStap | ArchiveFlushWriter
startTime | Begintijd van het mislukken.
Mislukkingen | Het aantal keren dat er is gefaald.
duurInSeconden | Duur van de mislukking.
message | Foutbericht.
category | Archieflogboeken

De volgende code is een voorbeeld van een JSON-tekenreeks voor archieflogboeken:

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

### <a name="operational-logs-schema"></a>Schema voor operationele logboeken

Json-tekenreeksen voor operationele logboeken bevatten elementen in de volgende tabel:

Name | Beschrijving
------- | -------
ActivityId (ActivityId) | Interne ID, gebruikt om het doel bij te houden.
Gebeurtenisnaam | naam van bewerking.  
resourceId | Azure Resource Manager-bron-id.
SubscriptionId | Abonnement-ID.
EventTimeString | Operatietijd.
EventProperties | Bedrijfseigenschappen.
Status | De status van de bewerking.
Caller | Beller van de bewerking (Azure-portal of beheerclient).
category | OperationalLogs (OperationalLogs)

De volgende code is een voorbeeld van een operationele JSON-tekenreeks voor logboeken:

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
- [Inleiding tot eventhubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs-API-overzicht](event-hubs-api-overview.md)
- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
