---
title: Aanbevolen procedures voor het verbeteren van de prestaties met Azure Service Bus
description: Beschrijft hoe u Service Bus gebruiken om de prestaties te optimaliseren bij het uitwisselen van brokered berichten.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: b864f433c67d47b4b92a1d4b98693ebd42806dd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259459"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Best Practices voor prestatieverbeteringen met Service Bus Messaging

In dit artikel wordt beschreven hoe u Azure Service Bus gebruiken om de prestaties te optimaliseren bij het uitwisselen van brokered-berichten. Het eerste deel van dit artikel beschrijft de verschillende mechanismen die worden aangeboden om de prestaties te verbeteren. Het tweede deel geeft richtlijnen voor het gebruik van Service Bus op een manier die de beste prestaties in een bepaald scenario kan bieden.

In dit artikel verwijst de term "client" naar elke entiteit die toegang heeft tot Service Bus. Een klant kan de rol van afzender of ontvanger op zich nemen. De term 'afzender' wordt gebruikt voor een wachtrij of onderwerpclient van servicebus die berichten verzendt naar een wachtrij of onderwerpabonnement van servicebus. De term 'ontvanger' verwijst naar een servicebuswachtrij of abonnementsclient die berichten ontvangt van een wachtrij of abonnement van servicebus.

Deze secties introduceren verschillende concepten die Service Bus gebruikt om de prestaties te verbeteren.

## <a name="protocols"></a>Protocollen

Service Bus stelt klanten in staat om berichten te verzenden en te ontvangen via een van de drie protocollen:

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. Hypertext Transfer Protocol (HTTP)

AMQP is het meest efficiënt, omdat het de verbinding met Service Bus onderhoudt. Het implementeert ook batching en prefetching. Tenzij expliciet vermeld, alle inhoud in dit artikel gaat uit van het gebruik van AMQP of SBMP.

> [!IMPORTANT]
> De SBMP is alleen beschikbaar voor .NET Framework. AMQP is de standaardinstelling voor .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>De juiste Service Bus .NET SDK kiezen

Er zijn twee ondersteunde Azure Service Bus .NET SDKs. Hun API's zijn zeer vergelijkbaar, en het kan verwarrend zijn welke te kiezen. Raadpleeg de volgende tabel om uw beslissing te begeleiden. We stellen de Microsoft.Azure.ServiceBus SDK voor omdat deze moderner, performant is en cross-platform compatibel is. Daarnaast ondersteunt het AMQP via WebSockets en maakt het deel uit van de Azure .NET SDK-verzameling van open-sourceprojecten.

| NuGet-pakket | Primaire naamruimte(en) | Minimumplatform(en) | Protocol(len) |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>Universeel Windows-platform 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP (SBMP)<br>HTTP |

Zie [.NET-implementatieondersteuning](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)voor meer informatie over minimale ondersteuning voor het .NET-standaardplatform .

## <a name="reusing-factories-and-clients"></a>Hergebruik van fabrieken en klanten

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Service Bus-clientobjecten, zoals [`IQueueClient`][QueueClient] implementaties van of [`IMessageSender`][MessageSender], moeten worden geregistreerd voor afhankelijkheidsinjectie als singletons (of eenmaal geanieerd en gedeeld). Het wordt aanbevolen dat u berichtenfabrieken of wachtrij-, onderwerp- en abonnementsklanten niet sluit nadat u een bericht hebt verzonden en deze vervolgens opnieuw maakt wanneer u het volgende bericht verzendt. Als u een berichtenfabriek sluit, wordt de verbinding met de Service Bus-service verwijderd en wordt er een nieuwe verbinding tot stand gebracht bij het opnieuw maken van de fabriek. Het tot stand brengen van een verbinding is een dure bewerking die u vermijden door dezelfde fabrieks- en clientobjecten opnieuw te gebruiken voor meerdere bewerkingen. U deze clientobjecten veilig gebruiken voor gelijktijdige asynchrone bewerkingen en van meerdere threads.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Service Bus-clientobjecten, `QueueClient` `MessageSender`zoals of , worden gemaakt via een [MessagingFactory-object,][MessagingFactory] dat ook intern beheer van verbindingen biedt. Het wordt aanbevolen dat u berichtenfabrieken of wachtrij-, onderwerp- en abonnementsklanten niet sluit nadat u een bericht hebt verzonden en deze vervolgens opnieuw maakt wanneer u het volgende bericht verzendt. Als u een berichtenfabriek sluit, wordt de verbinding met de Service Bus-service verwijderd en wordt er een nieuwe verbinding tot stand gebracht bij het opnieuw maken van de fabriek. Het tot stand brengen van een verbinding is een dure bewerking die u vermijden door dezelfde fabrieks- en clientobjecten opnieuw te gebruiken voor meerdere bewerkingen. U deze clientobjecten veilig gebruiken voor gelijktijdige asynchrone bewerkingen en van meerdere threads.

---

## <a name="concurrent-operations"></a>Gelijktijdige bewerkingen

Het uitvoeren van een bewerking (verzenden, ontvangen, verwijderen, enz.) kost enige tijd. Deze tijd omvat de verwerking van de bewerking door de Service Bus-service naast de latentie van de aanvraag en het antwoord. Om het aantal bewerkingen per tijd te verhogen, moeten bewerkingen gelijktijdig worden uitgevoerd.

De client plant gelijktijdige bewerkingen door asynchrone bewerkingen uit te voeren. De volgende aanvraag wordt gestart voordat de vorige aanvraag is voltooid. Het volgende codefragment is een voorbeeld van een asynchrone verzendbewerking:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

De volgende code is een voorbeeld van een asynchrone ontvangstbewerking.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Zie de GitHub repository voor volledige <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">broncode voorbeelden: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

Het `MessageReceiver` object wordt geinstantieerd met de verbindingstekenreeks, de naam van de wachtrij en de ontvangstmodus voor peek-look. Vervolgens wordt `receiver` de instantie gebruikt om de berichthandler te registreren.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Zie de GitHub repository voor volledige <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">broncode voorbeelden: <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

Hiermee `MessagingFactory` maakt `factory` u een object uit de verbindingstekenreeks. Met `factory` de instantie, een `MessageReceiver` is instantiated. Vervolgens wordt `receiver` de instantie gebruikt om de on-message handler te registreren.

---

## <a name="receive-mode"></a>Ontvangstmodus

Wanneer u een wachtrij- of abonnementsclient maakt, u een ontvangstmodus opgeven: *Peek-lock* of *Ontvangen en verwijderen*. De standaard ontvangstmodus is `PeekLock`. Wanneer de client in de standaardmodus werkt, stuurt hij een verzoek om een bericht van Service Bus te ontvangen. Nadat de client het bericht heeft ontvangen, stuurt deze een verzoek om het bericht te voltooien.

Wanneer u de `ReceiveAndDelete`ontvangstmodus instelt op , worden beide stappen in één aanvraag gecombineerd. Deze stappen verminderen het totale aantal bewerkingen en kunnen de algehele berichtdoorvoer verbeteren. Deze prestatiewinst loopt het risico berichten te verliezen.

Service Bus ondersteunt geen transacties voor ontvangen- en verwijderenbewerkingen. Daarnaast zijn peek-lock semantiek vereist voor alle scenario's waarin de klant een bericht wil uitstellen of [dood-letteren.](service-bus-dead-letter-queues.md)

## <a name="client-side-batching"></a>Batching aan clientzijde

Clientbatching stelt een wachtrij- of onderwerpclient in staat om het verzenden van een bericht voor een bepaalde periode uit te stellen. Als de client aanvullende berichten verstuurt tijdens deze periode, worden de berichten in één batch verstuurd. Batching aan clientzijde zorgt er ook voor dat een wachtrij- of abonnementsclient meerdere **Complete-aanvragen** in één aanvraag batcht. Batching is alleen beschikbaar voor asynchrone **verzend-** en **completebewerkingen.** Synchrone bewerkingen worden onmiddellijk naar de servicebusservice verzonden. Batching vindt niet plaats voor peek- of receive-bewerkingen en batching vindt ook niet plaats bij clients.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Batching functionaliteit voor de .NET Standard SDK, nog niet bloot een eigenschap te manipuleren.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Standaard gebruikt een client een batch-interval van 20 ms. U het batch-interval wijzigen door de eigenschap [BatchFlushInterval][BatchFlushInterval] in te stellen voordat u de berichtenfabriek maakt. Deze instelling is van invloed op alle clients die door deze fabriek zijn gemaakt.

Als u batching wilt uitschakelen, stelt u de eigenschap [BatchFlushInterval][BatchFlushInterval] in op **TimeSpan.Zero**. Bijvoorbeeld:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Batching heeft geen invloed op het aantal factureerbare berichtenbewerkingen en is alleen beschikbaar voor het servicebusclientprotocol via de [Microsoft.ServiceBus.Messaging-bibliotheek.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Het HTTP-protocol biedt geen ondersteuning voor batching.

> [!NOTE]
> Instelling `BatchFlushInterval` zorgt ervoor dat de batching impliciet is vanuit het perspectief van de toepassing. d.w.z. de toepassing `SendAsync` `CompleteAsync` maakt en roept en maakt geen specifieke Batch-aanroepen.
>
> Expliciete clientside batching kan worden geïmplementeerd door gebruik te maken van de onderstaande methode aanroep:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Hier moet de gecombineerde grootte van de berichten kleiner zijn dan de maximale grootte die wordt ondersteund door de prijscategorie.

---

## <a name="batching-store-access"></a>Toegang tot batchopslag

Als u de doorvoer van een wachtrij, onderwerp of abonnement wilt verhogen, plaatst Service Bus meerdere berichten wanneer deze naar het interne archief wordt geschreven. Als deze is ingeschakeld in een wachtrij of onderwerp, worden het schrijven van berichten in de winkel batched. Als deze is ingeschakeld in een wachtrij of abonnement, worden berichten uit de winkel verwijderd. Als batched store-toegang is ingeschakeld voor een entiteit, stelt Service Bus een winkelschrijfbewerking met maximaal 20 ms uit.

> [!NOTE]
> Er is geen risico op het verliezen van berichten met batching, zelfs als er een Service Bus storing aan het einde van een 20ms batching interval.

Extra winkelbewerkingen die tijdens dit interval plaatsvinden, worden aan de batch toegevoegd. Toegang tot batched store is alleen van invloed op **verzend-** en **completebewerkingen;** ontvangen bewerkingen worden niet beïnvloed. Toegang tot batched-winkels is een eigenschap van een entiteit. Batching vindt plaats in alle entiteiten die batched store access inschakelen.

Bij het maken van een nieuwe wachtrij, onderwerp of abonnement is batched store-toegang standaard ingeschakeld.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Als u batched store-toegang wilt uitschakelen, `ManagementClient`hebt u een exemplaar van een . Maak een wachtrij op basis `EnableBatchedOperations` van `false`een wachtrijbeschrijving die de eigenschap instelt op .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Raadpleeg de volgende artikelen voor meer informatie:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Als u batched store-toegang wilt uitschakelen, `NamespaceManager`hebt u een exemplaar van een . Maak een wachtrij op basis `EnableBatchedOperations` van `false`een wachtrijbeschrijving die de eigenschap instelt op .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Raadpleeg de volgende artikelen voor meer informatie:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Toegang tot batched store heeft geen invloed op het aantal factureerbare berichtenbewerkingen en is een eigenschap van een wachtrij, onderwerp of abonnement. Het is onafhankelijk van de ontvangstmodus en het protocol dat wordt gebruikt tussen een client en de Service Bus-service.

## <a name="prefetching"></a>Prefetching

[Met Prefetching](service-bus-prefetch.md) kan de wachtrij- of abonnementsclient extra berichten van de service laden wanneer deze een ontvangstbewerking uitvoert. De client slaat deze berichten op in een lokale cache. De grootte van de cache `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` wordt bepaald door de of eigenschappen. Elke client die prefetching mogelijk maakt, behoudt zijn eigen cache. Een cache wordt niet gedeeld tussen clients. Als de client een ontvangstbewerking initieert en de cache leeg is, verzendt de service een batch berichten. De grootte van de batch is gelijk aan de grootte van de cache of 256 KB, welke kleiner is. Als de client een ontvangstbewerking initieert en de cache een bericht bevat, wordt het bericht uit de cache gehaald.

Wanneer een bericht vooraf is opgehaald, vergrendelt de service het vooraf opgehaalde bericht. Met het slot kan het vooraf opgehaalde bericht niet door een andere ontvanger worden ontvangen. Als de ontvanger het bericht niet kan voltooien voordat het slot verloopt, wordt het bericht beschikbaar voor andere ontvangers. De vooraf opgehaalde kopie van het bericht blijft in de cache. De ontvanger die de verlopen kopie in de cache verbruikt, ontvangt een uitzondering wanneer deze dit bericht probeert in te vullen. Standaard verloopt het berichtslot na 60 seconden. Deze waarde kan worden verlengd tot 5 minuten. Om het verbruik van verlopen berichten te voorkomen, moet de cachegrootte altijd kleiner zijn dan het aantal berichten dat door een client binnen het tijdsinterval voor vergrendeling kan worden verbruikt.

Bij het gebruik van de standaard vergrendeling verloop `PrefetchCount` van 60 seconden, een goede waarde voor is 20 keer de maximale verwerkingssnelheden van alle ontvangers van de fabriek. Een fabriek maakt bijvoorbeeld drie ontvangers en elke ontvanger kan tot 10 berichten per seconde verwerken. Het aantal voorhalen mag niet hoger zijn dan 20 X 3 X 10 = 600. Standaard `PrefetchCount` is ingesteld op 0, wat betekent dat er geen extra berichten worden opgehaald van de service.

Als u berichten vooraf ophaalt, wordt de algehele doorvoer voor een wachtrij of abonnement verhoogd omdat het totale aantal berichtbewerkingen of retourvluchten wordt verminderd. Het ophalen van het eerste bericht duurt echter langer (vanwege de grotere berichtgrootte). Het ontvangen van vooraf opgehaalde berichten zal sneller zijn omdat deze berichten al zijn gedownload door de client.

De eigenschap time-to-live (TTL) van een bericht wordt gecontroleerd door de server op het moment dat de server het bericht naar de client verzendt. De client controleert niet de TTL-eigenschap van het bericht wanneer het bericht wordt ontvangen. In plaats daarvan kan het bericht worden ontvangen, zelfs als de TTL van het bericht is verstreken terwijl het bericht door de client in de cache is opgeslagen.

Prefetching heeft geen invloed op het aantal factureerbare berichtenbewerkingen en is alleen beschikbaar voor het servicebusclientprotocol. Het HTTP-protocol biedt geen ondersteuning voor vooraf ophalen. Prefetching is beschikbaar voor zowel synchrone als asynchrone ontvangstbewerkingen.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Zie de volgende eigenschappen `PrefetchCount` voor meer informatie:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Zie de volgende eigenschappen `PrefetchCount` voor meer informatie:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Batch vooraf ophalen en ontvangen

> [!NOTE]
> Deze sectie is alleen van toepassing op de WindowsAzure.ServiceBus SDK, omdat de Microsoft.Azure.ServiceBus SDK batchfuncties niet blootlegt.

Terwijl de concepten van het vooraf halen van meerdere berichten samen hebben`ReceiveBatch`vergelijkbare semantiek aan het verwerken van berichten in een batch ( ), zijn er enkele kleine verschillen die in gedachten moeten worden gehouden bij het gebruik van deze samen.

Prefetch is een configuratie (of modus) `SubscriptionClient`op `ReceiveBatch` de client (en)`QueueClient` en is een bewerking (met semantiek voor het aanvragen van aanvragen).

Houd bij het gebruik van deze samen rekening met de volgende gevallen -

* Prefetch moet groter zijn dan of gelijk zijn aan het `ReceiveBatch`aantal berichten dat u verwacht te ontvangen van .
* Prefetch kan tot n/3 keer het aantal berichten per seconde zijn verwerkt, waarbij n de standaardvergrendelingsduur is.

Er zijn een aantal uitdagingen met het hebben van een hebzuchtige aanpak (dat wil zeggen het houden van de prefetch tellen zeer hoog), omdat het impliceert dat het bericht is vergrendeld op een bepaalde ontvanger. De aanbeveling is om prefetch waarden uit te proberen tussen de hierboven genoemde drempels en empirisch te identificeren wat past.

## <a name="multiple-queues"></a>Meerdere wachtrijen

Als de verwachte belasting niet door één wachtrij of onderwerp kan worden afgehandeld, moet u meerdere berichtenentiteiten gebruiken. Maak bij het gebruik van meerdere entiteiten een specifieke client voor elke entiteit, in plaats van dezelfde client te gebruiken voor alle entiteiten.

## <a name="development-and-testing-features"></a>Ontwikkelings- en testfuncties

> [!NOTE]
> Deze sectie is alleen van toepassing op de WindowsAzure.ServiceBus SDK, omdat de Microsoft.Azure.ServiceBus SDK deze functionaliteit niet blootlegt.

Service Bus heeft een functie, die specifiek wordt gebruikt voor ontwikkeling, die **nooit mag worden gebruikt in productieconfiguraties:** [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering].

Wanneer er nieuwe regels of filters aan [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] het onderwerp worden toegevoegd, u controleren of de nieuwe filterexpressie werkt zoals verwacht.

## <a name="scenarios"></a>Scenario's

In de volgende secties worden typische berichtenscenario's beschreven en worden de instellingen van de gewenste ServiceBus beschreven. Doorvoersnelheden worden geclassificeerd als klein (minder dan 1 bericht per seconde), matig (1 bericht per seconde of groter, maar minder dan 100 berichten per seconde) en hoog (100 berichten per seconde of hoger). Het aantal cliënten wordt geclassificeerd als klein (5 of minder), matig (meer dan 5 maar minder dan of gelijk aan 20), en groot (meer dan 20).

### <a name="high-throughput-queue"></a>Wachtrij met hoge doorvoer

Doel: Maximaliseer de doorvoer van één wachtrij. Het aantal afzenders en ontvangers is klein.

* Als u de totale verzendsnelheid in de wachtrij wilt verhogen, gebruikt u meerdere berichtenfabrieken om afzenders te maken. Gebruik voor elke afzender asynchrone bewerkingen of meerdere threads.
* Als u het totale ontvangstpercentage van de wachtrij wilt verhogen, gebruikt u meerdere berichtenfabrieken om ontvangers te maken.
* Gebruik asynchrone bewerkingen om te profiteren van batching aan de clientzijde.
* Stel het batchinterval in op 50 ms om het aantal servicebusclientprotocol-transmissies te verminderen. Als er meerdere afzenders worden gebruikt, verhoogt u het batchinterval tot 100 ms.
* Toegang tot batched store ingeschakeld. Deze toegang verhoogt de algehele snelheid waarmee berichten in de wachtrij kunnen worden geschreven.
* Stel het aantal voorhalen in op 20 keer de maximale verwerkingssnelheden van alle ontvangers van een fabriek. Dit aantal vermindert het aantal servicebusclientprotocol-transmissies.

### <a name="multiple-high-throughput-queues"></a>Meerdere wachtrijen met hoge doorvoer

Doel: Maximaliseer de totale doorvoer van meerdere wachtrijen. De doorvoer van een afzonderlijke wachtrij is matig of hoog.

Als u een maximale doorvoer over meerdere wachtrijen wilt verkrijgen, gebruikt u de beschreven instellingen om de doorvoer van één wachtrij te maximaliseren. Gebruik bovendien verschillende fabrieken om klanten te maken die verzenden of ontvangen vanuit verschillende wachtrijen.

### <a name="low-latency-queue"></a>Wachtrij met lage latentie

Doel: Minimaliseer de end-to-end latentie van een wachtrij of onderwerp. Het aantal afzenders en ontvangers is klein. De doorvoer van de wachtrij is klein of matig.

* Batching aan clientzijde uitschakelen. De klant stuurt onmiddellijk een bericht.
* Toegang tot batched store uitschakelen. De dienst schrijft het bericht onmiddellijk naar de winkel.
* Als u één client gebruikt, stelt u het aantal voorhalen in op 20 keer de verwerkingssnelheid van de ontvanger. Als meerdere berichten tegelijkertijd in de wachtrij binnenkomen, verzendt het servicebusclientprotocol ze allemaal tegelijk. Wanneer de client het volgende bericht ontvangt, bevindt dat bericht zich al in de lokale cache. De cache moet klein zijn.
* Als u meerdere clients gebruikt, stelt u het aantal voorophalen in op 0. Door het aantal in te stellen, kan de tweede client het tweede bericht ontvangen terwijl de eerste client nog bezig is met het eerste bericht.

### <a name="queue-with-a-large-number-of-senders"></a>Wachtrij met een groot aantal afzenders

Doel: Maximaliseer de doorvoer van een wachtrij of onderwerp met een groot aantal afzenders. Elke afzender stuurt berichten met een gematigd tarief. Het aantal ontvangers is klein.

Service Bus maakt tot 1000 gelijktijdige verbindingen met een berichtenentiteit (of 5000 via AMQP). Deze limiet wordt afgedwongen op naamruimteniveau en wachtrijen/onderwerpen/abonnementen worden beperkt door de limiet van gelijktijdige verbindingen per naamruimte. Voor wachtrijen wordt dit nummer gedeeld tussen afzenders en ontvangers. Als alle 1000 verbindingen nodig zijn voor afzenders, vervangt u de wachtrij door een onderwerp en één abonnement. Een onderwerp accepteert maximaal 1000 gelijktijdige verbindingen van afzenders, terwijl het abonnement nog eens 1000 gelijktijdige verbindingen van ontvangers accepteert. Als er meer dan 1000 gelijktijdige afzenders nodig zijn, moeten de afzenders via HTTP berichten naar het Service Bus-protocol sturen.

Voer de volgende stappen uit om de doorvoer te maximaliseren:

* Als elke afzender zich in een ander proces bevindt, gebruikt u slechts één fabriek per proces.
* Gebruik asynchrone bewerkingen om te profiteren van batching aan de clientzijde.
* Gebruik het standaard batchinterval van 20 ms om het aantal servicebusclientprotocol-transmissies te verminderen.
* Toegang tot batched store ingeschakeld. Deze toegang verhoogt de algemene snelheid waarmee berichten in de wachtrij of het onderwerp kunnen worden geschreven.
* Stel het aantal voorhalen in op 20 keer de maximale verwerkingssnelheden van alle ontvangers van een fabriek. Dit aantal vermindert het aantal servicebusclientprotocol-transmissies.

### <a name="queue-with-a-large-number-of-receivers"></a>Wachtrij met een groot aantal ontvangers

Doel: Maximaliseer het ontvangstpercentage van een wachtrij of abonnement bij een groot aantal ontvangers. Elke ontvanger ontvangt berichten in een gematigd tempo. Het aantal afzenders is klein.

Service Bus maakt maximaal 1000 gelijktijdige verbindingen met een entiteit mogelijk. Als een wachtrij meer dan 1000 ontvangers vereist, vervangt u de wachtrij door een onderwerp en meerdere abonnementen. Elk abonnement kan tot 1000 gelijktijdige verbindingen ondersteunen. Als alternatief hebben ontvangers toegang tot de wachtrij via het HTTP-protocol.

Ga als volgt te werk om de doorvoer te maximaliseren:

* Als elke ontvanger zich in een ander proces bevindt, gebruikt u slechts één fabriek per proces.
* Ontvangers kunnen synchrone of asynchrone bewerkingen gebruiken. Gezien de matige ontvangstsnelheid van een individuele ontvanger heeft client-side batching van een Complete aanvraag geen invloed op de ontvangerdoorvoer.
* Toegang tot batched store ingeschakeld. Deze toegang vermindert de totale belasting van de entiteit. Het vermindert ook de totale snelheid waarmee berichten kunnen worden geschreven in de wachtrij of onderwerp.
* Stel het aantal voorhalen in op een kleine waarde (bijvoorbeeld PrefetchCount = 10). Dit aantal voorkomt dat ontvangers niet actief zijn, terwijl andere ontvangers grote aantallen berichten in de cache hebben opgeslagen.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Onderwerp met een klein aantal abonnementen

Doel: Maximaliseer de doorvoer van een onderwerp met een klein aantal abonnementen. Een bericht wordt ontvangen door veel abonnementen, wat betekent dat het gecombineerde ontvangsttarief voor alle abonnementen groter is dan het verzendtarief. Het aantal afzenders is klein. Het aantal ontvangers per abonnement is klein.

Ga als volgt te werk om de doorvoer te maximaliseren:

* Als u de totale verzendsnelheid in het onderwerp wilt verhogen, gebruikt u meerdere berichtenfabrieken om afzenders te maken. Gebruik voor elke afzender asynchrone bewerkingen of meerdere threads.
* Als u het totale ontvangstpercentage van een abonnement wilt verhogen, gebruikt u meerdere berichtenfabrieken om ontvangers te maken. Gebruik voor elke ontvanger asynchrone bewerkingen of meerdere threads.
* Gebruik asynchrone bewerkingen om te profiteren van batching aan de clientzijde.
* Gebruik het standaard batchinterval van 20 ms om het aantal servicebusclientprotocol-transmissies te verminderen.
* Toegang tot batched store ingeschakeld. Deze toegang verhoogt de algemene snelheid waarmee berichten in het onderwerp kunnen worden geschreven.
* Stel het aantal voorhalen in op 20 keer de maximale verwerkingssnelheden van alle ontvangers van een fabriek. Dit aantal vermindert het aantal servicebusclientprotocol-transmissies.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Onderwerp met een groot aantal abonnementen

Doel: Maximaliseer de doorvoer van een onderwerp met een groot aantal abonnementen. Een bericht wordt ontvangen door veel abonnementen, wat betekent dat het gecombineerde ontvangsttarief voor alle abonnementen veel groter is dan het verzendtarief. Het aantal afzenders is klein. Het aantal ontvangers per abonnement is klein.

Onderwerpen met een groot aantal abonnementen geven doorgaans een lage totale doorvoer bloot als alle berichten naar alle abonnementen worden doorgestuurd. Deze lage doorvoer wordt veroorzaakt door het feit dat elk bericht vele malen wordt ontvangen en alle berichten die zijn opgenomen in een onderwerp en alle abonnementen worden opgeslagen in hetzelfde archief. Aangenomen wordt dat het aantal afzenders en het aantal ontvangers per abonnement klein is. Service Bus ondersteunt maximaal 2.000 abonnementen per onderwerp.

Als u de doorvoer wilt maximaliseren, probeert u de volgende stappen:

* Gebruik asynchrone bewerkingen om te profiteren van batching aan de clientzijde.
* Gebruik het standaard batchinterval van 20 ms om het aantal servicebusclientprotocol-transmissies te verminderen.
* Toegang tot batched store ingeschakeld. Deze toegang verhoogt de algemene snelheid waarmee berichten in het onderwerp kunnen worden geschreven.
* Stel het aantal voorhalen in op 20 keer de verwachte ontvangstsnelheid in seconden. Dit aantal vermindert het aantal servicebusclientprotocol-transmissies.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md