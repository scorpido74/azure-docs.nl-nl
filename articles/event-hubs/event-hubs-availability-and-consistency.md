---
title: Beschikbaarheid en consistentie - Azure Event Hubs | Microsoft Documenten
description: Zo u de maximale hoeveelheid beschikbaarheid en consistentie met Azure Event Hubs bieden met behulp van partities.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904478"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Beschikbaarheid en consistentie in Event Hubs

## <a name="overview"></a>Overzicht
Azure Event Hubs gebruikt een [partitiemodel](event-hubs-scalability.md#partitions) om de beschikbaarheid en parallelisatie binnen één gebeurtenishub te verbeteren. Als een gebeurtenishub bijvoorbeeld vier partities heeft en een van deze partities wordt verplaatst van de ene server naar de andere in een taakverdelingsbewerking, u nog steeds verzenden en ontvangen van drie andere partities. Bovendien u met meer partities meer gelijktijdige lezers hebben die uw gegevens verwerken, waardoor uw totale doorvoer wordt verbeterd. Inzicht in de implicaties van partitionering en bestellen in een gedistribueerd systeem is een essentieel aspect van het ontwerp van oplossingen.

Om de afweging tussen bestellen en beschikbaarheid te verklaren, zie de [stelling van het GLB](https://en.wikipedia.org/wiki/CAP_theorem), ook wel bekend als de stelling van brewer. Deze stelling bespreekt de keuze tussen consistentie, beschikbaarheid en partitietolerantie. Het stelt dat voor de systemen verdeeld door het netwerk is er altijd afweging tussen consistentie en beschikbaarheid.

De stelling van de brouwer definieert consistentie en beschikbaarheid als volgt:
* Partitietolerantie: de mogelijkheid van een gegevensverwerkingssysteem om gegevens te blijven verwerken, zelfs als er een partitiefout optreedt.
* Beschikbaarheid: een niet-falend knooppunt retourneert een redelijk antwoord binnen een redelijke tijd (zonder fouten of time-outs).
* Consistentie: een read is gegarandeerd de meest recente schrijven voor een bepaalde client terug te keren.

## <a name="partition-tolerance"></a>Partitietolerantie
Event Hubs is gebouwd bovenop een verdeeld gegevensmodel. U het aantal partities in uw gebeurtenishub configureren tijdens de installatie, maar u deze waarde later niet wijzigen. Aangezien u partities moet gebruiken met Event Hubs, moet u een beslissing nemen over beschikbaarheid en consistentie voor uw toepassing.

## <a name="availability"></a>Beschikbaarheid
De eenvoudigste manier om aan de slag te gaan met Gebeurtenishubs is door het standaardgedrag te gebruiken. Als u een nieuw **[EventHubClient-object](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** maakt en de methode **[Verzenden](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** gebruikt, worden uw gebeurtenissen automatisch verdeeld over partities in uw gebeurtenishub. Dit gedrag zorgt voor de grootste hoeveelheid up-time.

Voor use cases waarvoor de maximale uptime nodig is, heeft dit model de voorkeur.

## <a name="consistency"></a>Consistentie
In sommige scenario's kan het ordenen van gebeurtenissen belangrijk zijn. U wilt bijvoorbeeld dat uw back-endsysteem een updateopdracht verwerkt voordat een opdracht verwijderen wordt gedaan. In dit geval u de partitiesleutel instellen op `PartitionSender` een gebeurtenis of een object gebruiken om alleen gebeurtenissen naar een bepaalde partitie te verzenden. Als u dit doet, zorgt u ervoor dat wanneer deze gebeurtenissen uit de partitie worden gelezen, ze in volgorde worden gelezen.

Houd er bij deze configuratie rekening mee dat als de specifieke partitie waarnaar u verzendt niet beschikbaar is, u een foutmelding ontvangt. Als vergelijkingspunt stuurt de gebeurtenisservice uw gebeurtenis naar de eerstvolgende beschikbare partitie als u geen affiniteit hebt met één partitie.

Een mogelijke oplossing om te zorgen voor het bestellen, terwijl ook het maximaliseren van de tijd, zou zijn om gebeurtenissen te verzamelen als onderdeel van uw gebeurtenisverwerking toepassing. De eenvoudigste manier om dit te bereiken is door uw evenement te stempelen met een aangepaste eigenschap van het volgnummer. In de volgende code ziet u een voorbeeld:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 of hoger)](#tab/latest)

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

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 of eerder)](#tab/old)
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

In dit voorbeeld wordt uw gebeurtenis naar een van de beschikbare partities in uw gebeurtenishub geplaatst en wordt het bijbehorende volgnummer van uw toepassing ingesteld. Deze oplossing vereist dat de status door uw verwerkingstoepassing wordt bewaard, maar geeft uw afzenders een eindpunt dat waarschijnlijk beschikbaar is.

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Overzicht van de service event hubs](event-hubs-what-is-event-hubs.md)
* [Een gebeurtenishub maken](event-hubs-create.md)
