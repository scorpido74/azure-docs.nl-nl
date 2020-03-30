---
title: Overzicht van de Azure Event Hubs .NET Framework API's | Microsoft Documenten
description: In dit artikel vindt u een overzicht van enkele van de belangrijkste Gebeurtenishubs .NET Framework client API's (beheer en runtime).
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b14759ed39037bfa172366a2ed8f8ca089786ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137608"
---
# <a name="event-hubs-net-framework-api-overview"></a>Overzicht van Event Hubs .NET Framework API

In dit artikel worden enkele van de belangrijkste Azure Event Hubs [.NET Framework-client-API's](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)samengevat. Er zijn twee categorieën: beheer- en runtime API's. Run-time API's bestaan uit alle bewerkingen die nodig zijn om een bericht te verzenden en te ontvangen. Met beheerbewerkingen u een entiteitsstatus van Gebeurtenishubs beheren door entiteiten te maken, bij te werken en te verwijderen.

[Monitoringscenario's](event-hubs-metrics-azure-monitor.md) omvatten zowel beheer als run-time. Zie de API's .NET Framework , [.NET Standard](/dotnet/api/microsoft.azure.eventhubs)en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) voor gedetailleerde referentiedocumentatie op de API's [.NET.](/dotnet/api/microsoft.servicebus.messaging.eventhubclient)

## <a name="management-apis"></a>Beheer API’s

Als u de volgende beheerbewerkingen wilt uitvoeren, moet u machtigingen **voor beheren** hebben op de naamruimte van gebeurtenishubs:

### <a name="create"></a>Maken

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Update

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Verwijderen

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Runtime API's
### <a name="create-publisher"></a>Uitgever maken

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Bericht publiceren

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Maak de consument

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Bericht gebruiken

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Api's voor hosthost van gebeurtenisprocessor

Deze API's bieden tolerantie voor werkprocessen die mogelijk niet meer beschikbaar zijn, door partities over beschikbare werknemers te verdelen.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

De [Interface iEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) wordt als volgt gedefinieerd:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor meer informatie over Event Hubs-scenario‘s:

* [Wat is Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Programmeerhandleiding voor gebeurtenishubs](event-hubs-programming-guide.md)

De .NET API-verwijzingen zijn hier:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.azure.eventhubs.eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
