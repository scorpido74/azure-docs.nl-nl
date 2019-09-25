---
title: Diagnostische logboeken Azure Service Bus | Microsoft Docs
description: Meer informatie over het instellen van Diagnostische logboeken voor Service Bus in Azure.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 6443cb727573645792a4e6c929b80c3406d72025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261809"
---
# <a name="service-bus-diagnostic-logs"></a>Diagnostische logboeken Service Bus

U kunt twee typen logboeken voor Azure Service Bus weer geven:
* **[Activiteiten logboeken](../azure-monitor/platform/activity-logs-overview.md)** . Deze logboeken bevatten informatie over de bewerkingen die worden uitgevoerd op een taak. De logboeken zijn altijd ingeschakeld.
* **[Diagnostische logboeken](../azure-monitor/platform/resource-logs-overview.md)** . U kunt Diagnostische logboeken configureren voor uitgebreide informatie over alles wat er in een taak gebeurt. Diagnostische logboeken voor activiteiten vanaf het moment dat de taak is gemaakt totdat de taak wordt verwijderd, met inbegrip van updates en activiteiten die plaatsvinden terwijl de taak wordt uitgevoerd.

## <a name="turn-on-diagnostic-logs"></a>Logboeken met diagnostische gegevens inschakelen

Diagnostische logboeken zijn standaard uitgeschakeld. Als u Diagnostische logboeken wilt inschakelen, voert u de volgende stappen uit:

1.  In de [Azure-portal](https://portal.azure.com)onder **bewaking en beheer**, klikt u op **diagnoselogboeken**.

    ![Blade navigatie naar Diagnostische logboeken](./media/service-bus-diagnostic-logs/image1.png)

2. Klik op de resource die u wilt bewaken.  

3.  Klik op **Diagnostische gegevens inschakelen**.

    ![Diagnostische logboeken inschakelen](./media/service-bus-diagnostic-logs/image2.png)

4.  Voor **Status**, klikt u op **op**.

    ![Diagnostische logboeken voor status wijzigen](./media/service-bus-diagnostic-logs/image3.png)

5.  Stel het gewenste archief doel in; bijvoorbeeld een opslag account, een Event Hub of Azure Monitor Logboeken.

6.  De nieuwe instellingen voor diagnostische gegevens opslaan.

Nieuwe instellingen van kracht in ongeveer 10 minuten. Daarna worden logboeken weer gegeven in het geconfigureerde archiverings doel op de Blade **Diagnostische logboeken** .

Zie voor meer informatie over het configureren van diagnostische gegevens over de [overzicht van diagnostische logboeken in Azure](../azure-monitor/platform/resource-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Diagnostische logboeken schema

Alle logboeken worden opgeslagen in JavaScript Object Notation (JSON)-indeling. Elke vermelding bevat teken reeks velden die gebruikmaken van de indeling die wordt beschreven in de volgende sectie.

## <a name="operational-logs-schema"></a>Operationele logboeken schema

Logboeken in de **OperationalLogs** -categorie vastleggen wat er gebeurt tijdens service bus bewerkingen. In deze logboeken wordt het bewerkings type vastgelegd, inclusief het maken van de wachtrij, de gebruikte resources en de status van de bewerking.

Operationeel logboek van JSON-tekenreeksen zijn onder andere elementen die worden vermeld in de volgende tabel:

Name | Description
------- | -------
ActivityId | Interne ID, gebruikt voor bijhouden
EventName | Naam van bewerking           
resourceId | Resource-ID Azure Resource Manager
SubscriptionId | Abonnements-id
EventTimeString | Bewerkings tijd
EventProperties | Bewerkings eigenschappen
State | Bewerkingsstatus
Caller | Aanroeper van de bewerking (Azure Portal of Management-client)
category | OperationalLogs

Hier volgt een voor beeld van een JSON-teken reeks voor een operationeel logboek:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende koppelingen voor meer informatie over Service Bus:

* [Inleiding tot Service Bus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus](service-bus-dotnet-get-started-with-queues.md)
