---
title: Wachtrijen en onderwerpen voor gepartitioneerde Azure Service Bus maken | Microsoft Documenten
description: Beschrijft hoe u wachtrijen en onderwerpen van servicebussen verdelen met behulp van meerdere berichtmakelaars.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2020
ms.author: aschhab
ms.openlocfilehash: 671368993acb43c0d55eca73119effa934e3cff8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260941"
---
# <a name="partitioned-queues-and-topics"></a>Gepartitioneerde wachtrijen en onderwerpen

Azure Service Bus maakt gebruik van meerdere berichtenmakelaars om berichten en meerdere berichtenwinkels te verwerken om berichten op te slaan. Een conventionele wachtrij of onderwerp wordt behandeld door een enkele bericht makelaar en opgeslagen in een messaging store. Met *servicebuspartities* kunnen wachtrijen en onderwerpen of *berichtenentiteiten*worden verdeeld over meerdere berichtenmakelaars en berichtenwinkels. Partitionering betekent dat de totale doorvoer van een partitieentiteit niet langer wordt beperkt door de prestaties van een enkele berichtenmakelaar of berichtenwinkel. Bovendien is een tijdelijke uitval van een berichtenwinkel niet beschikbaar voor een partitiewachtrij of onderwerp. Partitiewachtrijen en onderwerpen kunnen alle geavanceerde Service Bus-functies bevatten, zoals ondersteuning voor transacties en sessies.

> [!NOTE]
> Partitionering is beschikbaar bij het maken van entiteiten voor alle wachtrijen en onderwerpen in Basic of Standard SKU's. Het is niet beschikbaar voor de Premium messaging SKU, maar alle eerder bestaande partitieentiteiten in Premium-naamruimten blijven werken zoals verwacht.
 
Het is niet mogelijk om de partitieoptie op een bestaande wachtrij of onderwerp te wijzigen; U de optie alleen instellen wanneer u de entiteit maakt.

## <a name="how-it-works"></a>Hoe werkt het?

Elke partitiewachtrij of onderwerp bestaat uit meerdere partities. Elke partitie wordt opgeslagen in een andere berichtenwinkel en behandeld door een andere berichtenmakelaar. Wanneer een bericht naar een partitiewachtrij of onderwerp wordt verzonden, wijst Service Bus het bericht toe aan een van de partities. De selectie gebeurt willekeurig met servicebus of met behulp van een partitiesleutel die de afzender kan opgeven.

Wanneer een client een bericht wil ontvangen vanuit een partitiewachtrij of van een abonnement op een gepartitioneerd onderwerp, stelt Service Bus alle partities voor berichten op en retourneert u het eerste bericht dat is verkregen uit een van de berichtenwinkels naar de ontvanger. Service Bus slaat de andere berichten in de cache en retourneert deze wanneer deze aanvullende ontvangstaanvragen ontvangt. Een ontvangende klant is niet op de hoogte van de partitionering; het clientgerichte gedrag van een partitiewachtrij of onderwerp (bijvoorbeeld lezen, aanvullen, uitstellen, deadletter, prefetching) is identiek aan het gedrag van een gewone entiteit.

Er zijn geen extra kosten verbonden aan het verzenden van een bericht naar of het ontvangen van een bericht van een partitiewachtrij of onderwerp.

## <a name="enable-partitioning"></a>Partitionering inschakelen

Als u partitiewachtrijen en onderwerpen wilt gebruiken met Azure Service Bus, `api-version=2013-10` gebruikt u de Azure SDK-versie 2.2 of hoger of geeft u op of hoger in uw HTTP-aanvragen.

### <a name="standard"></a>Standard

In de standaardberichtenlaag u wachtrijen en onderwerpen van servicebus maken in de grootte van 1, 2, 3, 4 of 5 GB (standaard is 1 GB). Als partitionering is ingeschakeld, maakt Service Bus 16 kopieën (16 partities) van de entiteit, elk van dezelfde opgegeven grootte. Als u dus een wachtrij maakt met een grootte van 5 GB, met 16 partities wordt de maximale wachtrijgrootte (5 \* 16) = 80 GB. U de maximale grootte van uw partitiewachtrij of onderwerp zien door te kijken naar de vermelding ervan op de [Azure-portal][Azure portal]in het **overzichtsblad** voor die entiteit.

### <a name="premium"></a>Premium

In een naamruimte van de Premium-laag worden partitioneringsentiteiten niet ondersteund. U echter nog steeds wachtrijen en onderwerpen van ServiceBus maken in de grootte van 1, 2, 3, 4, 5, 10, 20, 40 of 80 GB (de standaardis 1 GB). U de grootte van uw wachtrij of onderwerp zien door te kijken naar de vermelding ervan op de [Azure-portal][Azure portal]in het **overzichtsblad** voor die entiteit.

### <a name="create-a-partitioned-entity"></a>Een partitieentiteit maken

Er zijn verschillende manieren om een partitiewachtrij of onderwerp te maken. Wanneer u de wachtrij of het onderwerp vanuit uw toepassing maakt, u partitionering voor de wachtrij of het onderwerp inschakelen door respectievelijk de [queuedescription.EnablePartitioning][QueueDescription.EnablePartitioning] of [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] eigenschap in te **stellen op true**. Deze eigenschappen moeten worden ingesteld op het moment dat de wachtrij of het onderwerp wordt gemaakt en zijn alleen beschikbaar in de oudere [WindowsAzure.ServiceBus-bibliotheek.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Zoals eerder vermeld, is het niet mogelijk om deze eigenschappen te wijzigen in een bestaande wachtrij of onderwerp. Bijvoorbeeld:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

U ook een wachtrij of onderwerp met partities maken in de [Azure-portal.][Azure portal] Wanneer u een wachtrij of onderwerp in de portal maakt, wordt de optie **Partitionering** inschakelen in het dialoogvenster wachtrij of onderwerp **Maken** standaard ingeschakeld. U deze optie alleen uitschakelen in een entiteit met een standaardlaag. in de Premium-laagpartitie wordt niet ondersteund en het selectievakje heeft geen effect. 

## <a name="use-of-partition-keys"></a>Gebruik van partitiesleutels

Wanneer een bericht in een partitiewachtrij of onderwerp in de wachtrij staat, controleert Service Bus op de aanwezigheid van een partitiesleutel. Als er een wordt gevonden, selecteert deze de partitie op basis van die sleutel. Als er geen partitiesleutel wordt gevonden, selecteert deze de partitie op basis van een intern algoritme.

### <a name="using-a-partition-key"></a>Een partitiesleutel gebruiken

Sommige scenario's, zoals sessies of transacties, vereisen dat berichten worden opgeslagen in een specifieke partitie. Al deze scenario's vereisen het gebruik van een partitiesleutel. Alle berichten die dezelfde partitiesleutel gebruiken, worden aan dezelfde partitie toegewezen. Als de partitie tijdelijk niet beschikbaar is, retourneert Service Bus een fout.

Afhankelijk van het scenario worden verschillende berichteigenschappen gebruikt als partitiesleutel:

**SessionId**: Als een bericht de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) heeft ingesteld, gebruikt Service Bus **SessionID** als partitiesleutel. Op deze manier worden alle berichten die tot dezelfde sessie behoren, behandeld door dezelfde berichtenmakelaar. Met Sessies kan Service Bus het bestellen van berichten en de consistentie van sessiestatussen garanderen.

**PartitionKey:** Als een bericht de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) heeft, maar niet de [eigenschapset SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) gebruikt Service Bus de [eigenschapswaarde PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) als partitiesleutel. Als het bericht zowel de [eigenschappen SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) als [partitionkey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) heeft ingesteld, moeten beide eigenschappen identiek zijn. Als de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) is ingesteld op een andere waarde dan de eigenschap [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) retourneert Service Bus een ongeldige bewerkingsuitzondering. De eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) moet worden gebruikt als een afzender niet-sessiebewuste transactionele berichten verzendt. De partitiesleutel zorgt ervoor dat alle berichten die binnen een transactie worden verzonden, worden verwerkt door dezelfde berichtenmakelaar.

**MessageId:** Als de eigenschap Queue of topic de eigenschap [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) heeft ingesteld op **true** en de eigenschappen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) of [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) niet zijn ingesteld, dient de [eigenschapswaarde MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) als partitiesleutel. (De Microsoft .NET- en AMQP-bibliotheken wijzen automatisch een bericht-id toe als de verzendende toepassing dat niet doet.) In dit geval worden alle kopieën van hetzelfde bericht verwerkt door dezelfde berichtenmakelaar. Met deze ID kan Service Bus dubbele berichten detecteren en elimineren. Als de eigenschap [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) niet is ingesteld op **true,** beschouwt Service Bus de eigenschap [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) niet als een partitiesleutel.

### <a name="not-using-a-partition-key"></a>Geen partitiesleutel gebruiken

Bij afwezigheid van een partitiesleutel distribueert Service Bus berichten op een round-robin-manier naar alle partities van de partities van de partities of het onderwerp. Als de gekozen partitie niet beschikbaar is, wijst Service Bus het bericht toe aan een andere partitie. Op deze manier slaagt de verzendbewerking ondanks de tijdelijke onbeschikbaarheid van een berichtenwinkel. U zult echter niet de gegarandeerde volgorde bereiken die een partitiesleutel biedt.

Zie [dit artikel](../event-hubs/event-hubs-availability-and-consistency.md)voor een meer diepgaande bespreking van de afweging tussen beschikbaarheid (geen partitiesleutel) en consistentie (met behulp van een partitiesleutel). Deze informatie is ook van toepassing op partitieservicebusentiteiten.

Als u Service Bus voldoende tijd wilt geven om het bericht in een andere partitie te plaatsen, moet de waarde [Van OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) die is opgegeven door de client die het bericht verzendt, langer zijn dan 15 seconden. Het wordt aanbevolen de eigenschap [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) in te stellen op de standaardwaarde van 60 seconden.

Een partitiesleutel "speldt" een bericht op een specifieke partitie. Als de berichtenwinkel met deze partitie niet beschikbaar is, retourneert Service Bus een fout. Bij afwezigheid van een partitiesleutel kan Service Bus een andere partitie kiezen en slaagt de bewerking. Daarom wordt aanbevolen dat u geen partitiesleutel levert, tenzij dit vereist is.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Geavanceerde onderwerpen: transacties gebruiken met partitieentiteiten

Berichten die worden verzonden als onderdeel van een transactie moeten een partitiesleutel opgeven. De sleutel kan een van de volgende eigenschappen zijn: [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)of [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Alle berichten die als onderdeel van dezelfde transactie worden verzonden, moeten dezelfde partitiesleutel opgeven. Als u probeert een bericht te verzenden zonder partitiesleutel binnen een transactie, retourneert Service Bus een ongeldige bewerkingsuitzondering. Als u probeert meerdere berichten binnen dezelfde transactie te verzenden met verschillende partitiesleutels, retourneert Service Bus een ongeldige bewerkingsuitzondering. Bijvoorbeeld:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Als een van de eigenschappen die als partitiesleutel dienen, is ingesteld, speldt Service Bus het bericht in op een specifieke partitie. Dit gedrag treedt op of een transactie al dan niet wordt gebruikt. Het wordt aanbevolen dat u geen partitiesleutel opgeeft als dit niet nodig is.

## <a name="using-sessions-with-partitioned-entities"></a>Sessies gebruiken met verdeelde entiteiten

Als u een transactioneel bericht naar een sessiebewust onderwerp of wachtrij wilt verzenden, moet het bericht de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) hebben ingesteld. Als de eigenschap [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ook is opgegeven, moet deze identiek zijn aan de eigenschap [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Als deze verschillen, retourneert Service Bus een ongeldige bewerkingsuitzondering.

In tegenstelling tot reguliere (niet-gepartitioneerde) wachtrijen of onderwerpen, is het niet mogelijk om één transactie te gebruiken om meerdere berichten naar verschillende sessies te verzenden. Als er een poging wordt ondernomen, retourneert Service Bus een ongeldige bewerkingsuitzondering. Bijvoorbeeld:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisch doorsturen van berichten met partitieentiteiten

ServiceBus ondersteunt automatisch doorsturen van berichten van, naar of tussen verdeelde entiteiten. Als u automatisch doorsturen van berichten wilt inschakelen, stelt u de eigenschap [QueueDescription.ForwardTo][QueueDescription.ForwardTo] in op de bronwachtrij of het abonnement. Als het bericht een partitiesleutel opgeeft[(SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)of [MessageId),](/dotnet/api/microsoft.azure.servicebus.message.messageid)wordt die partitiesleutel gebruikt voor de doelentiteit.

## <a name="considerations-and-guidelines"></a>Overwegingen en richtlijnen
* **Functies met hoge consistentie:** Als een entiteit functies zoals sessies, dubbele detectie of expliciete controle van de partitioneringssleutel gebruikt, worden de berichtenbewerkingen altijd doorgestuurd naar specifieke partitie. Als een van de partities veel verkeer ondervindt of als de onderliggende winkel niet in orde is, mislukken deze bewerkingen en wordt de beschikbaarheid verminderd. Over het algemeen is de consistentie nog steeds veel hoger dan niet-gepartitioneerde entiteiten; slechts een subset van het verkeer ondervindt problemen, in tegenstelling tot al het verkeer. Zie voor meer informatie deze [discussie over beschikbaarheid en consistentie.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Beheer:** bewerkingen zoals Maken, Bijwerken en Verwijderen moeten worden uitgevoerd op alle partities van de entiteit. Als een partitie niet in orde is, kan dit leiden tot fouten voor deze bewerkingen. Voor de bewerking Ophalen moet informatie zoals berichttellingen worden samengevoegd uit alle partities. Als een partitie niet in orde is, wordt de beschikbaarheidsstatus van de entiteit gerapporteerd als beperkt.
* **Scenario's voor berichten met een laag volume:** voor dergelijke scenario's, vooral bij het gebruik van het HTTP-protocol, moet u mogelijk meerdere ontvangstbewerkingen uitvoeren om alle berichten te verkrijgen. Voor ontvangen van aanvragen voert de front-end een ontvangst uit op alle partities en caches alle ontvangen antwoorden. Een volgende ontvangstaanvraag op dezelfde verbinding zou profiteren van deze caching en de latencies ontvangen zullen lager zijn. Als u echter meerdere verbindingen hebt of HTTP gebruikt, wordt voor elk verzoek een nieuwe verbinding gemaakt. Als zodanig is er geen garantie dat het zou landen op hetzelfde knooppunt. Als alle bestaande berichten zijn vergrendeld en in de cache in een andere front-end zijn opgeslagen, wordt de bewerking **ontvangen null geretourneerd.** Berichten verlopen uiteindelijk en u ze opnieuw ontvangen. HTTP keep-alive wordt aanbevolen. Wanneer u partitionering gebruikt in scenario's met een laag volume, kan het langer duren voordat ontvangstbewerkingen zijn uitgevoerd dan verwacht. Daarom raden we u aan partitionering niet te gebruiken in deze scenario's. Verwijder bestaande partitieentiteiten en maak ze opnieuw samen met partitionering uitgeschakeld om de prestaties te verbeteren.
* **Browse/Peek-berichten:** alleen beschikbaar in de oudere [WindowsAzure.ServiceBus-bibliotheek.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) retourneert niet altijd het aantal berichten dat is opgegeven in de eigenschap [MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Er zijn twee veel voorkomende redenen voor dit gedrag. Een van de redenen is dat de geaggregeerde grootte van de verzameling van berichten groter is dan de maximale grootte van 256 KB. Een andere reden is dat als de wachtrij of het onderwerp de [eigenschap EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) op **true**heeft ingesteld, een partitie mogelijk niet genoeg berichten heeft om het gevraagde aantal berichten te voltooien. In het algemeen, als een toepassing een specifiek aantal berichten wil ontvangen, moet het [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) herhaaldelijk bellen totdat het dat aantal berichten krijgt, of er zijn geen berichten meer om te gluren. Zie de [API-documentatie QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) of [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) voor meer informatie, inclusief codevoorbeelden.

## <a name="latest-added-features"></a>Nieuwste toegevoegde functies

* Regel toevoegen of verwijderen wordt nu ondersteund met partitieentiteiten. Anders dan niet-gepartitioneerde entiteiten, worden deze bewerkingen niet ondersteund onder transacties. 
* AMQP wordt nu ondersteund voor het verzenden en ontvangen van berichten van en naar een partitieentiteit.
* AMQP wordt nu ondersteund voor de volgende bewerkingen: [Batch Send](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batch Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), Receive by Sequence [Number](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Peek](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Renew Lock](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Schedule Message](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), Cancel Scheduled [Message](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Add Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Remove Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription), Session Renew [Lock](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), Set [Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), Get Session [State](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), and [Enumerate Sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Beperkingen van verdeelde entiteiten

Momenteel legt Service Bus de volgende beperkingen op voor partitiewachtrijen en onderwerpen:

* Partitiewachtrijen en onderwerpen worden niet ondersteund in de premium-berichtenlaag. Sessies worden ondersteund in de eerste laag met behulp van SessionId. 
* Partitiewachtrijen en onderwerpen bieden geen ondersteuning voor het verzenden van berichten die tot verschillende sessies in één transactie behoren.
* Service Bus staat momenteel maximaal 100 gepartitioneerde wachtrijen en onderwerpen per naamruimte toe. Elke partitiewachtrij of onderwerp telt mee voor het quotum van 10.000 entiteiten per naamruimte (is niet van toepassing op de premiumlaag).

## <a name="next-steps"></a>Volgende stappen

Lees meer over de kernconcepten van de AMQP 1.0-berichtenspecificatie in de [AMQP 1.0-protocolhandleiding.](service-bus-amqp-protocol-guide.md)

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
