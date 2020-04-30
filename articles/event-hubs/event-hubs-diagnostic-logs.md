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
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509763"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Diagnostische logboeken instellen voor een Azure Event Hub

U kunt twee typen logboeken voor Azure Event Hubs bekijken:

* **[Activiteiten logboeken](../azure-monitor/platform/platform-logs-overview.md)**: deze logboeken bevatten informatie over bewerkingen die zijn uitgevoerd voor een taak. De logboeken zijn altijd ingeschakeld. U kunt de vermeldingen in het activiteiten logboek bekijken door **activiteiten logboek** te selecteren in het linkerdeel venster voor uw event hub naam ruimte in de Azure Portal. Bijvoorbeeld: ' naam ruimte maken of bijwerken ', ' event hub maken of bijwerken '.

    ![Activiteiten logboek voor een Event Hubs naam ruimte](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md)**: u kunt Diagnostische logboeken configureren voor een uitgebreidere weer gave van alles wat er gebeurt met een taak. Diagnostische logboeken behandelen activiteiten vanaf het moment dat de taak wordt gemaakt, totdat de taak wordt verwijderd, inclusief updates en activiteiten die optreden terwijl de taak wordt uitgevoerd.

    In de volgende sectie wordt beschreven hoe u Diagnostische logboeken inschakelt voor een Event Hubs naam ruimte.

## <a name="enable-diagnostic-logs"></a>Diagnostische logboeken inschakelen
Diagnostische logboeken zijn standaard uitgeschakeld. Voer de volgende stappen uit om Diagnostische logboeken in te scha kelen:

1.  Navigeer in het [Azure Portal](https://portal.azure.com)naar uw event hubs naam ruimte. 
2. Selecteer **Diagnostische instellingen** onder **bewaking** in het linkerdeel venster en selecteer **+ Diagnostische instelling toevoegen**. 

    ![Pagina Diagnostische instellingen-diagnostische instelling toevoegen](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Selecteer in de sectie **categorie Details** de **typen Diagnostische logboeken** die u wilt inschakelen. Verderop in dit artikel vindt u meer informatie over deze categorieën. 
5. Stel in de sectie **doel Details** het archief doel (doel) in dat u wilt. bijvoorbeeld een opslag account, een Event Hub of een Log Analytics-werk ruimte.

    ![Pagina Diagnostische instellingen toevoegen](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Selecteer **Opslaan** op de werk balk om de diagnostische instellingen op te slaan.

    Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in het geconfigureerde archief doel in het deel venster **Diagnostische logboeken** .

    Zie voor meer informatie over het configureren van diagnostische gegevens het [overzicht van Azure Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorieën met Diagnostische logboeken

Event Hubs Diagnostische logboeken vastleggen voor de volgende categorieën:

- **Archief logboeken**: Logboeken gerelateerd aan Event hubs archieven, met name logboeken die betrekking hebben op archief fouten.
- **Operationele logboeken**: informatie over wat er gebeurt tijdens Event hubs bewerkingen, met name het bewerkings type, inclusief Event hub maken, gebruikte resources en de status van de bewerking.
- **Logboeken voor automatisch schalen**: informatie over bewerkingen met automatische schaling die worden uitgevoerd op een event hubs naam ruimte. 
- **Kafka Coordinator-logboeken** : informatie over de Kafka-coördinator bewerkingen met betrekking tot Event hubs. 
- **Kafka-gebruikers Logboeken**: informatie over Kafka-gebruikers bewerkingen met betrekking tot Event hubs. 
- **Event hubs verbinding met het virtuele netwerk (VNet)**: informatie over de verbindings gebeurtenissen van een virtueel netwerk event hubs. 
- Door de **klant beheerde sleutel gebruikers Logboeken**: informatie over bewerkingen met betrekking tot door de klant beheerde sleutel. 


    Alle logboeken worden opgeslagen in de indeling van de JavaScript Object Notation (JSON). Elke vermelding bevat teken reeks velden die gebruikmaken van de indeling die wordt beschreven in de volgende secties.

## <a name="archive-logs-schema"></a>Schema voor archief logboeken

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

## <a name="operational-logs-schema"></a>Schema van operationele logboeken

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

## <a name="autoscale-logs-schema"></a>Schema voor automatisch schalen van Logboeken
JSON van het logboek voor automatisch schalen bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- | 
| trackingId | Interne ID, die wordt gebruikt voor tracerings doeleinden |
| resourceId | Interne ID, die de ID van het Azure-abonnement en de naam van de naam ruimte bevat |
| message | Informatief bericht, dat details bevat over het automatisch verg Roten van de actie. Het bericht bevat de vorige en huidige waarde van de doorvoer eenheid voor een opgegeven naam ruimte en wat de verg Roten van de TU heeft veroorzaakt. |

## <a name="kafka-coordinator-logs-schema"></a>Kafka Coordinator-logboeken schema
Kafka Coordinator-logboek JSON bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- | 
| requestId | aanvraag-ID, die wordt gebruikt voor tracerings doeleinden |
| resourceId | Interne ID, die de ID van het Azure-abonnement en de naam van de naam ruimte bevat |
| operationName | De naam van de bewerking die wordt uitgevoerd tijdens de groeps coördinatie |
| clientId | Client-id |
| namespaceName | Naam van naamruimte | 
| subscriptionId | Azure-abonnements-ID |
| message | Informatief bericht, dat details bevat over acties die zijn uitgevoerd tijdens de coördinatie van de consumenten groep. |

## <a name="kafka-user-error-logs-schema"></a>Schema voor Kafka-gebruikers fout logboeken
Kafka voor fout logboek van gebruikers bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- |
| trackingId | tracerings-ID, die wordt gebruikt voor tracerings doeleinden. |
| namespaceName | Naam van naamruimte |
| eventhub | Naam van Event Hub |
| partitionId | Partitie-id |
| groupId | Groeps-id |
| ClientId | Client-id |
| resourceId | Interne ID, die de ID van het Azure-abonnement en de naam van de naam ruimte bevat |
| message | Informatief bericht, dat details bevat over een fout |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Verbindings gebeurtenis schema voor virtuele netwerk Event Hubs

De JSON-verbindings gebeurtenis van het Event Hubs virtuele netwerk (VNet) bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---  | ----------- | 
| subscriptionId | Azure-abonnements-ID |
| namespaceName | Naam van naamruimte |
| IPAdres | IP-adres van een client die verbinding maakt met de Event Hubs-service |
| action | De actie die door de Event Hubs-service wordt uitgevoerd bij de evaluatie van verbindings aanvragen. Ondersteunde acties zijn **AcceptConnection** en **RejectConnection**. |
| reason | Geeft een reden waarom de actie is uitgevoerd |
| count | Aantal exemplaren voor de opgegeven actie |
| resourceId | De interne Resource-ID, die de abonnements-ID en naam van de naam ruimte bevat. |

## <a name="customer-managed-key-user-logs"></a>Door de klant beheerde sleutel gebruikers Logboeken
Door de klant beheerde Key gebruikers logboek JSON bevat elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ---- | ----------- | 
| category | Type categorie voor een bericht. Dit is een van de volgende waarden: **fout** en **info** |
| resourceId | Interne Resource-ID, waaronder de Azure-abonnements-ID en naam van de naam ruimte |
| keyVault | De naam van de Key Vault resource |
| sleutel | De naam van de Key Vault sleutel. |
| versie | Versie van de Key Vault sleutel |
| schijf | De naam van een bewerking die is uitgevoerd om aanvragen te behandelen |
| code | Statuscode |
| message | Bericht, dat details over een fout of informatief bericht bevat |



## <a name="next-steps"></a>Volgende stappen
- [Inleiding tot Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs-API-overzicht](event-hubs-api-overview.md)
- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
