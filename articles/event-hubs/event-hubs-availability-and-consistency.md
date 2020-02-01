---
title: Beschikbaarheid en consistentie - Azure Event Hubs | Microsoft Docs
description: Het leveren van de maximale hoeveelheid beschikbaarheid en consistentie met Azure Event Hubs met behulp van partities.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904478"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Beschikbaarheid en consistentie in Event Hubs

## <a name="overview"></a>Overzicht
Azure Event Hubs gebruikt een [partitioneren model](event-hubs-scalability.md#partitions) voor betere beschikbaarheid en parallellisering binnen één event hub. Bijvoorbeeld, als een event hub vier partities heeft, en een van deze partities van de ene server naar een andere in een bewerking voor de taakverdeling wordt verplaatst, kunt u nog steeds verzenden en ontvangen van drie andere partities. Bovendien kunt u meer gelijktijdige lezers verwerken van uw gegevens, de geaggregeerde doorvoer verbeteren hebben meer partities. Informatie over de gevolgen van partitionering en bestellen in een gedistribueerd systeem is een essentieel onderdeel van het ontwerp van de oplossing.

Om te helpen de verhouding tussen de volgorde en de beschikbaarheid wordt uitgelegd, Zie de [CAP-theorema](https://en.wikipedia.org/wiki/CAP_theorem), ook wel aangeduid als de Brewer theorema. Deze theorema bespreekt de keuze tussen consistentie, beschikbaarheid en tolerantie van de partitie. Deze geeft aan dat voor de systemen die is gepartitioneerd op basis van netwerk er altijd een afweging tussen de consistentie en beschikbaarheid.

De Brewer theorema consistentie en beschikbaarheid wordt als volgt gedefinieerd:
* Tolerantie partitioneren: het vermogen van een systeem gegevens verwerken om door te gaan verwerken van gegevens, zelfs als er een partitie-fout optreedt.
* Beschikbaarheid: een knooppunt niet mislukt retourneert een redelijke antwoord binnen een redelijk tijdsbestek (met geen fouten of time-outs).
* Consistentie: een leesbewerking is gegarandeerd om terug te keren van de meest recente schrijven voor een bepaalde client.

## <a name="partition-tolerance"></a>Partitie tolerantie
Eventhubs is gebaseerd op een model gepartitioneerde gegevens. U kunt het aantal partities in uw event hub configureren tijdens de installatie, maar u kunt deze waarde later niet wijzigen. Omdat partities u met Event Hubs gebruiken moet, moet u een beslissing nemen over de beschikbaarheid en consistentie voor uw toepassing.

## <a name="availability"></a>Beschikbaarheid
De eenvoudigste manier om aan de slag met Event Hubs is het standaardgedrag te gebruiken. Als u een nieuwe maakt **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** object en gebruik de **[verzenden](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** methode en de gebeurtenissen worden automatisch verdeeld tussen partities in de event hub. Dit gedrag kunt u de grootste mate van beschikbaarheid.

Voor use-cases die de maximale tijd nodig hebben, heeft dit model de voorkeur.

## <a name="consistency"></a>Consistentie
In sommige scenario's, kan de volgorde van gebeurtenissen belangrijk zijn. Bijvoorbeeld, kunt u uw back-end-systeem voor het verwerken van een bijwerkopdracht voordat u een opdracht tot verwijderen. In dit geval, u kunt de partitiesleutel instellen op een gebeurtenis, of gebruik een `PartitionSender` object alleen gebeurtenissen verzenden naar een bepaalde partitie. Hiermee zorgt u ervoor dat wanneer deze gebeurtenissen worden gelezen uit de partitie, worden gelezen in volgorde.

Houd er rekening mee dat als de bepaalde partitie die u verzendt zijn niet beschikbaar is, u een foutmelding ontvangt met deze configuratie. Als een punt van de vergelijking, als u nog geen een affiniteit met één partitie, verzendt de Event Hubs-service uw gebeurtenis naar het volgende beschikbare partitie.

Een mogelijke oplossing om ervoor te zorgen volgorde, en ook maximaliseert beschikbaarheid, zou worden statistische gebeurtenissen als onderdeel van uw toepassing voor gebeurtenisverwerking. De eenvoudigste manier om dit te doen is om uw evenement met een aangepaste reeks numerieke eigenschap. In de volgende code ziet u een voorbeeld:

#### <a name="azuremessagingeventhubs-500-or-latertablatest"></a>[Azure. Messa ging. Event hubs (5.0.0 of hoger)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earliertabold"></a>[Micro soft. Azure. Event hubs (4.1.0 of eerder)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

In dit voorbeeld verzendt de gebeurtenis naar een van de beschikbare partities in uw event hub en wordt het bijbehorende volgnummer van uw toepassing ingesteld. Deze oplossing status om te worden gehouden door uw toepassing vereist, maar uw afzenders biedt een eindpunt die waarschijnlijk zal beschikbaar zijn.

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van Event Hubs-service](event-hubs-what-is-event-hubs.md)
* [Een Event Hub maken](event-hubs-create.md)
