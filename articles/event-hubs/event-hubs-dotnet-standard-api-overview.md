---
title: Overzicht van de Azure Event Hubs .NET Standard API's | Microsoft Documenten
description: .NET Standard API-overzicht
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60821910"
---
# <a name="event-hubs-net-standard-api-overview"></a>Overzicht van Gebeurtenishubs .NET Standard API

In dit artikel worden enkele van de belangrijkste Azure Event Hubs [.NET Standard client API's](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)samengevat. Er zijn momenteel twee .NET Standard-clientbibliotheken voor gebeurtenishubs:

* [Microsoft.Azure.EventHubs:](/dotnet/api/microsoft.azure.eventhubs)biedt alle basisruntimebewerkingen.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Voegt extra functionaliteit toe waarmee verwerkte gebeurtenissen kunnen worden bijgehouden en is de eenvoudigste manier om te lezen vanaf een gebeurtenishub.

## <a name="event-hubs-client"></a>Gebeurtenishubs-client

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) is het primaire object dat u gebruikt om gebeurtenissen te verzenden, ontvangers te maken en tijdsinformatie op te halen. Deze client is gekoppeld aan een bepaalde gebeurtenishub en maakt een nieuwe verbinding met het eindpunt van gebeurtenishubs.

### <a name="create-an-event-hubs-client"></a>Een Event Hubs-client maken

Een [EventHubClient-object](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) wordt gemaakt op basis van een verbindingstekenreeks. De eenvoudigste manier om een nieuwe client te instantiëren wordt weergegeven in het volgende voorbeeld:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Als u de verbindingstekenreeks programmatisch wilt bewerken, u de klasse [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) gebruiken en de verbindingstekenreeks als parameter doorgeven aan [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Gebeurtenissen verzenden

Als u gebeurtenissen naar een gebeurtenishub wilt verzenden, gebruikt u de klasse [EventData.](/dotnet/api/microsoft.azure.eventhubs.eventdata) Het lichaam moet `byte` een array `byte` of een arraysegment zijn.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Gebeurtenissen ontvangen

De aanbevolen manier om gebeurtenissen te ontvangen van Gebeurtenishubs is het gebruik van de [GebeurtenisprocessorHost](#event-processor-host-apis), die functionaliteit biedt om automatisch de gebeurtenishubverschuivingen en partitiegegevens bij te houden. Er zijn echter bepaalde situaties waarin u de flexibiliteit van de kernbibliotheek van Event Hubs gebruiken om evenementen te ontvangen.

#### <a name="create-a-receiver"></a>Een ontvanger maken

Ontvangers zijn gekoppeld aan specifieke partities, dus om alle gebeurtenissen in een gebeurtenishub te ontvangen, moet u meerdere exemplaren maken. Het is een goede gewoonte om de partitie informatie programmatisch te krijgen, in plaats van hard-codering van de partitie-id's. Hiervoor u de [GetRuntimeInformationAsync-methode](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) gebruiken.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Omdat gebeurtenissen nooit worden verwijderd uit een gebeurtenishub (en alleen verlopen), moet u het juiste beginpunt opgeven. In het volgende voorbeeld worden mogelijke combinaties weergegeven:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Een gebeurtenis consumeren

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>Api's voor hosthost van gebeurtenisprocessor

Deze API's bieden tolerantie voor werkprocessen die mogelijk niet meer beschikbaar zijn, door partities over beschikbare werknemers te verdelen:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Het volgende is een voorbeeld implementatie van de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interface:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor meer informatie over Event Hubs-scenario‘s:

* [Wat is Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Beschikbare gebeurtenishubs-api's](event-hubs-api-overview.md)

De .NET API-verwijzingen zijn hier:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
