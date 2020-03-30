---
title: .NET-programmeerhandleiding - Azure Event Hubs (legacy) | Microsoft Documenten
description: In dit artikel vindt u informatie over het schrijven van code voor Azure Event Hubs met de Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: d958c2d32c16874676f46bb216067fe2d7bbe784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280974"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET-programmeerhandleiding voor Azure Event Hubs (legacy Microsoft.Azure.EventHubs-pakket)
In dit artikel worden enkele veelvoorkomende scenario's besproken bij het schrijven van code met Azure Event Hubs. Er wordt uitgegaan van een basisbegrip van Event Hubs. Zie het [Overzicht van Event Hubs](event-hubs-what-is-event-hubs.md) voor een conceptueel overzicht van Event Hubs.

> [!WARNING]
> Deze handleiding is voor het oude **Microsoft.Azure.EventHubs-pakket.** We raden u aan uw code te [migreren](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) om het nieuwste [Azure.Messaging.EventHubs-pakket](get-started-dotnet-standard-send-v2.md) te gebruiken.  


## <a name="event-publishers"></a>Gebeurtenisuitgevers

U verzendt gebeurtenissen naar een gebeurtenishub via HTTP POST of via een AMQP 1.0-verbinding. De keuze van welke te gebruiken en wanneer hangt af van het specifieke scenario wordt aangepakt. AMQP 1.0-verbindingen zijn brokered verbindingen in Service Bus. Ze zijn met name geschikt voor scenario‘s met vaak voorkomende hogere berichtvolumes en lagere latentievereisten, omdat ze een permanent berichtenkanaal bieden.

Wanneer de beheerde .NET-API‘s worden gebruikt, zijn de klassen [EventHubClient][] en [EventData][] de primaire constructs voor het publiceren van gegevens naar Event Hubs. [EventHubClient][] biedt het AMQP-communicatiekanaal waarover gebeurtenissen naar de gebeurtenishub worden verzonden. De klasse [EventData][] vertegenwoordigt een gebeurtenis en wordt gebruikt om berichten naar een gebeurtenishub te publiceren. Deze klasse bevat de hoofdtekst, sommige metagegevens (Eigenschappen) en header-informatie (SystemProperties) over de gebeurtenis. Andere eigenschappen worden toegevoegd aan het object [EventData][] terwijl het door een gebeurtenishub loopt.

## <a name="get-started"></a>Aan de slag
De .NET-klassen die gebeurtenishubs ondersteunen, worden aangeboden in het [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet-pakket. U installeren met de Visual Studio Solution Explorer of de [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio. Hiervoor voert u de volgende opdracht uit in het venster voor de [Pakketbeheerconsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Een Event Hub maken

U de Azure-portal, Azure PowerShell of Azure CLI gebruiken om gebeurtenishubs te maken. Zie [Naamruimte voor gebeurtenishubs maken en een gebeurtenishub met de Azure-portal](event-hubs-create.md)voor meer informatie.

## <a name="create-an-event-hubs-client"></a>Een Event Hubs-client maken

De primaire klasse voor interactie met gebeurtenishubs is [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. U deze klasse instantiëren met de methode [CreateFromConnectionString,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) zoals in het volgende voorbeeld wordt weergegeven:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Gebeurtenissen naar een gebeurtenishub verzenden

U verzendt gebeurtenissen naar een gebeurtenishub door een [EventHubClient-exemplaar][] te maken en deze asynchroon te verzenden via de [methode SendAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) Met deze methode wordt één [gebeurtenisparameter EventData][] nodig en wordt deze asynchroon naar een gebeurtenishub verzendt.

## <a name="event-serialization"></a>Gebeurtenisserialisatie

De klasse [EventData][] heeft [twee overbelaste constructors](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) die verschillende parameters, bytes of een bytearray nemen, die de payload van gebeurtenisgegevens vertegenwoordigen. Als u JSON gebruikt met [EventData][], kunt u **Encoding.UTF8.GetBytes()** gebruiken om de bytematrix op te halen voor een met JSON gecodeerde tekenreeks. Bijvoorbeeld:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partitiesleutel

> [!NOTE]
> Als u niet bekend bent met partities, [raadpleegt](event-hubs-features.md#partitions)u dit artikel . 

Bij het verzenden van gebeurtenisgegevens u een waarde opgeven die is gehasht om een partitietoewijzing te produceren. U geeft de partitie op met de eigenschap [PartitionSender.PartitionID.](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) De beslissing om partities te gebruiken impliceert echter een keuze tussen beschikbaarheid en consistentie. 

### <a name="availability-considerations"></a>Beschikbaarheidsoverwegingen

Het gebruik van een partitiesleutel is optioneel en u moet goed overwegen om er al dan niet een te gebruiken. Als u bij het publiceren van een gebeurtenis geen partitiesleutel opgeeft, wordt er gebruikgemaakt van round robin-toewijzing. In veel gevallen is het gebruik van een partitiesleutel een goede keuze als het bestellen van gebeurtenissen belangrijk is. Wanneer u een partitiesleutel gebruikt, vereisen deze partities beschikbaarheid op één knooppunt en kunnen er na verloop van tijd storingen optreden. bijvoorbeeld wanneer compute nodes opnieuw opstarten en patchen. Als u dus een partitie-id instelt en die partitie om de een of andere reden niet meer beschikbaar is, mislukt een poging om toegang te krijgen tot de gegevens in die partitie. Als hoge beschikbaarheid het belangrijkst is, geeft u geen partitiesleutel op. in dat geval worden gebeurtenissen verzonden naar partities met behulp van het eerder beschreven round-robin-model. In dit scenario maakt u een expliciete keuze tussen beschikbaarheid (geen partitie-id) en consistentie (afspraken vastmaken aan een partitie-id).

Een andere overweging is het afhandelen van vertragingen bij het verwerken van gebeurtenissen. In sommige gevallen is het misschien beter om gegevens te laten vallen en opnieuw te proberen dan om te proberen de verwerking bij te houden, wat mogelijk verdere downstream verwerkingsvertragingen kan veroorzaken. Met een voorraadticker kun je bijvoorbeeld beter wachten op volledige up-to-date gegevens, maar in een live chat- of VOIP-scenario heb je de gegevens liever snel, zelfs als deze niet volledig zijn.

Gezien deze beschikbaarheidsoverwegingen u in deze scenario's een van de volgende foutafhandelingsstrategieën kiezen:

- Stoppen (stop met lezen van Event Hubs totdat alles is opgelost)
- Drop (berichten zijn niet belangrijk, laat ze vallen)
- Opnieuw proberen (de berichten opnieuw proberen zoals u dat wilt)

Zie [Beschikbaarheid en consistentie in gebeurtenishubs](event-hubs-availability-and-consistency.md)voor meer informatie en een discussie over de afwegingen tussen beschikbaarheid en consistentie. 

## <a name="batch-event-send-operations"></a>Batchbewerkingen voor het verzenden van gebeurtenissen

Het verzenden van gebeurtenissen in batches kan helpen de doorvoer te verhogen. U de [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API gebruiken om een batch te maken waaraan later gegevensobjecten kunnen worden toegevoegd voor een [SendAsync-aanroep.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)

Eén batch mag de limiet van 1 MB van een gebeurtenis niet overschrijden. Daarnaast maakt elk bericht in de batch gebruik van dezelfde uitgever-id. Het is de verantwoordelijkheid van de afzender om ervoor te zorgen dat de batch de maximale gebeurtenisgrootte niet overschrijdt. Als dit wel gebeurt, wordt aan clientzijde een **Verzendfout** gegenereerd. U de helpermethode [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) gebruiken om ervoor te zorgen dat de batch niet meer dan 1 MB bedraagt. U krijgt een lege [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) uit de [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API en gebruikt [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) om gebeurtenissen toe te voegen om de batch te construeren. 

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchroon verzenden en op schaal verzenden

U verzendt gebeurtenissen asynchroon naar een gebeurtenishub. Als u asynchroon verzendt, wordt de snelheid waarmee een client gebeurtenissen kan verzenden, verhoogd. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) retourneert een [object Taak.](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) U de klasse [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) op de client gebruiken om opties voor het opnieuw proberen van de client te beheren.

## <a name="event-consumers"></a>Gebeurtenisconsumers
Met de klasse [EventProcessorHost][] worden gegevens uit Event Hubs verwerkt. Gebruik deze implementatie bij het bouwen van gebeurtenislezers op het .NET-platform. [EventProcessorHost][] biedt een thread-veilige, beveiligde runtimeomgeving met meerdere processen voor implementaties van gebeurtenisprocessors die ook beheer biedt van controlepunten en partitielease.

Als u de klasse [EventProcessorHost][] wilt gebruiken, kunt u [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementeren. Deze interface bevat vier methoden:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Als u de verwerking van gebeurtenissen wilt starten, u [EventProcessorHost][]instantiate, met de juiste parameters voor uw gebeurtenishub. Bijvoorbeeld:

> [!NOTE]
> EventProcessorHost en de bijbehorende klassen worden aangeboden in het **Microsoft.Azure.EventHubs.Processor-pakket.** Voeg het pakket toe aan uw Visual Studio-project door de instructies in dit`Install-Package Microsoft.Azure.EventHubs.Processor` [artikel](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) te volgen of door de volgende opdracht uit te geven in het venster Package Manager [Console:](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Bel vervolgens [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) om uw [IEventProcessor-implementatie](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) te registreren met de runtime:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Op dit punt probeert de host een lease op elke partitie in de gebeurtenishub te verkrijgen met behulp van een "gulzig" algoritme. Deze huurcontracten duren voor een bepaalde termijn en moeten dan worden verlengd. Zodra nieuwe knooppunten, in dit geval werkrolexemplaren, online komen, worden er reserveringen voor leases geplaatst. Later verschuift de werklast tussen de knooppunten wanneer elk knooppunt probeert om meer leases te verkrijgen.

![Gebeurtenisprocessorhost](./media/event-hubs-programming-guide/IC759863.png)

In de loop van de tijd komt er een evenwicht tot stand. Dankzij deze dynamische mogelijkheid kan er op consumers automatisch schalen op basis van CPU worden toegepast, zowel voor omhoog als voor omlaag schalen. Omdat Event Hubs geen direct concept van berichttellingen heeft, is gemiddeld CPU-gebruik vaak het beste mechanisme om back-end of consumentenschaal te meten. Als uitgevers meer gebeurtenissen publiceren dan consumers kunnen verwerken, kan de toename van het CPU-gebruik voor consumers worden gebruikt om het aantal werkrolexemplaren automatisch te schalen.

Met de klasse [EventProcessorHost][] wordt ook een op Azure Storage gebaseerd mechanisme van controlepunten geïmplementeerd. Hiermee wordt de offset opgeslagen op basis van partitie, zodat elke consumer kan bepalen wat het laatste controlepunt van de vorige consumer was. Terwijl partities via leases schakelen tussen knooppunten, maakt dit synchronisatiemechanisme de verschuiving van werklasten mogelijk.

## <a name="publisher-revocation"></a>Uitgever intrekken

Naast de geavanceerde runtime-functies van Event Processor Host, maakt de Event Hubs-service [publisher seining](/rest/api/eventhub/revoke-publisher) mogelijk om specifieke uitgevers te blokkeren om een evenement naar een gebeurtenishub te verzenden. Deze functies zijn handig als een publisher-token is gecompromitteerd of als een software-update ervoor zorgt dat ze zich ongepast gedragen. In deze gevallen kan de uitgever-id, die onderdeel is van het bijbehorende SAS-token, worden geblokkeerd voor het uitgeven van gebeurtenissen.

> [!NOTE]
> Momenteel ondersteunt alleen REST API deze functie[(intrekking van uitgevers).](/rest/api/eventhub/revoke-publisher)

Zie het voorbeeld [Op grote schaal veilig publiceren met Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) voor meer informatie over het intrekken van uitgevers en over het als uitgever verzenden naar Event Hubs.

## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor meer informatie over Event Hubs-scenario‘s:

* [Event Hubs-API-overzicht](event-hubs-api-overview.md)
* [Wat is Event Hubs](event-hubs-what-is-event-hubs.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [API-verwijzing voor de gebeurtenisprocessorhost](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData (EventData)]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
