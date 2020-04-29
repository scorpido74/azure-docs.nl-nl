---
title: Diagnostische logboeken instellen-Azure Event hub | Microsoft Docs
description: Meer informatie over het instellen van activiteiten logboeken en Diagnostische logboeken voor Event hubs in Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162307"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnostische logboeken instellen voor een Azure Event Hub

U kunt twee typen logboeken voor Azure Event Hubs bekijken:

* **[Activiteiten logboeken](../azure-monitor/platform/platform-logs-overview.md)**: deze logboeken bevatten informatie over de bewerkingen die worden uitgevoerd op een taak. De logboeken zijn altijd ingeschakeld.
* **[Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md)**: u kunt Diagnostische logboeken configureren voor een uitgebreidere weer gave van alles wat er gebeurt met een taak. Diagnostische logboeken behandelen activiteiten vanaf het moment dat de taak wordt gemaakt, totdat de taak wordt verwijderd, inclusief updates en activiteiten die optreden terwijl de taak wordt uitgevoerd.

## <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Voer de volgende stappen uit om Diagnostische logboeken in te scha kelen:

1.  Klik in de [Azure Portal](https://portal.azure.com)onder **controle en beheer**op **Diagnostische logboeken**.

    ![Deel venster navigatie naar Diagnostische logboeken](./media/event-hubs-diagnostic-logs/image1.png)

2.  Klik op de resource die u wilt bewaken.

3.  Klik op **Diagnostische gegevens inschakelen**.

    ![Diagnostische logboeken inschakelen](./media/event-hubs-diagnostic-logs/image2.png)

4.  Voor **status** **, klikt u op.**

    ![De status van Diagnostische logboeken wijzigen](./media/event-hubs-diagnostic-logs/image3.png)

5.  Stel het gewenste archief doel in; bijvoorbeeld een opslag account, een Event Hub of Azure Monitor Logboeken.

6.  Sla de nieuwe instellingen voor diagnostische gegevens op.

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in het geconfigureerde archief doel in het deel venster **Diagnostische logboeken** .

Zie voor meer informatie over het configureren van diagnostische gegevens het [overzicht van Azure Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorieën met Diagnostische logboeken

Event Hubs Diagnostische logboeken voor twee categorieën worden vastgelegd:

* **Archief logboeken**: Logboeken gerelateerd aan Event hubs archieven, met name logboeken die betrekking hebben op archief fouten.
* **Operationele logboeken**: informatie over wat er gebeurt tijdens Event hubs bewerkingen, met name het bewerkings type, inclusief Event hub maken, gebruikte resources en de status van de bewerking.

## <a name="diagnostic-logs-schema"></a>Schema voor Diagnostische logboeken

Alle logboeken worden opgeslagen in de indeling van de JavaScript Object Notation (JSON). Elke vermelding bevat teken reeks velden die gebruikmaken van de indeling die wordt beschreven in de volgende secties.

### <a name="archive-logs-schema"></a>Schema voor archief logboeken

De JSON-teken reeksen van het archief logboek bevatten elementen die in de volgende tabel worden weer gegeven:

Naam | Beschrijving
------- | -------
TaskName | Beschrijving van de taak die is mislukt.
ActivityId | Interne ID, gebruikt voor tracering.
trackingId | Interne ID, gebruikt voor tracering.
resourceId | Azure Resource Manager Resource-ID.
eventHub | Volledige naam van Event hub (bevat naam van naam ruimte).
partitionId | De Event hub-partitie waarnaar wordt geschreven.
archiveStep | ArchiveFlushWriter
startTime | Begin tijd van de fout.
Masters | Aantal keren dat de fout is opgetreden.
durationInSeconds | Duur van de fout.
message | Fout bericht.
category | ArchiveLogs

De volgende code is een voor beeld van een JSON-teken reeks in het archief logboek:

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

### <a name="operational-logs-schema"></a>Schema van operationele logboeken

De JSON-teken reeksen van het operationele logboek bevatten elementen die in de volgende tabel worden weer gegeven:

Naam | Beschrijving
------- | -------
ActivityId | Interne ID, gebruikt voor het bijhouden van het doel.
Gebeurtenisnaam | naam van bewerking.  
resourceId | Azure Resource Manager Resource-ID.
SubscriptionId | Abonnements-ID.
EventTimeString | Bewerkings tijd.
EventProperties | Eigenschappen van de bewerking.
Status | Bewerkings status.
Caller | De aanroeper van de bewerking (Azure Portal-of Management-client).
category | OperationalLogs

De volgende code is een voor beeld van een JSON-teken reeks voor een operationeel logboek:

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
- [Inleiding tot Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs-API-overzicht](event-hubs-api-overview.md)
- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
