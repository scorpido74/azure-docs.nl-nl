---
title: Gebeurtenisschema Azure Event Grid Service Bus
description: Beschrijft de eigenschappen die worden geleverd voor ServiceBus-gebeurtenissen met Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561758"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Azure Event Grid-gebeurtenisschema voor servicebus

In dit artikel vindt u de eigenschappen en het schema voor servicebusgebeurtenissen.Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

Zie [Gebeurtenisbron servicebus](event-sources.md#service-bus)voor een lijst met voorbeeldscripts en -zelfstudies .

## <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Service Bus zendt de volgende gebeurtenistypen uit:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Verhoogd wanneer er actieve berichten in een wachtrij of abonnement staan en er geen ontvangers luisteren. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Verhoogd wanneer er actieve berichten in een wachtrij voor dode letters staan en geen actieve listeners. |

## <a name="example-event"></a>Voorbeeldgebeurtenis

In het volgende voorbeeld wordt het schema van actieve berichten weergegeven zonder listenersgebeurtenis:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Het schema voor een wachtrijgebeurtenis met dode letter is vergelijkbaar:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gebeurtenisgegevens voor blobopslag. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| naamruimteNaam | tekenreeks | De naamruimte servicebus waarin de resource bestaat. |
| requestUri | tekenreeks | De URI naar de specifieke wachtrij of het abonnement dat de gebeurtenis uitzendt. |
| entityType | tekenreeks | Het type Service Bus-entiteit dat gebeurtenissen uitzendt (wachtrij of abonnement). |
| queueName | tekenreeks | De wachtrij met actieve berichten als u zich abonneert op een wachtrij. Waarde null als het gebruik van onderwerpen / abonnementen. |
| topicNaam | tekenreeks | Het onderwerp waarhet Service Bus-abonnement met actieve berichten toe behoort. Waarde null als u een wachtrij gebruikt. |
| subscriptionName | tekenreeks | Het Service Bus-abonnement met actieve berichten. Waarde null als u een wachtrij gebruikt. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
* Zie het [overzicht servicebus naar gebeurtenisraster](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)voor meer informatie over het gebruik van Azure Event Grid met servicebus.
* Probeer [Service Bus-gebeurtenissen te ontvangen met functies of Logische Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
