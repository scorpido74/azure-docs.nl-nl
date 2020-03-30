---
title: Service Bus dode-letter wachtrijen | Microsoft Documenten
description: Beschrijft wachtrijen voor dode letters in Azure Service Bus. ServiceBuswachtrijen en onderwerpabonnementen bieden een secundaire subwachtrij, een dode-letterwachtrij genoemd.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: 9c1a0cb92fbaf98d25799ffb5a85e666e7c05f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158895"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Overzicht van servicebus dode-letter wachtrijen

Azure Service Bus-wachtrijen en onderwerpabonnementen bieden een secundaire subwachtrij, een *dode-letterwachtrij* (DLQ). De wachtrij voor dode letters hoeft niet expliciet te worden gemaakt en kan niet worden verwijderd of anderszins onafhankelijk van de hoofdentiteit worden beheerd.

In dit artikel worden wachtrijen voor dode letters in Service Bus beschreven. Een groot deel van de discussie wordt geïllustreerd door de [Dead-Letter wachtrijen monster](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) op GitHub.
 
## <a name="the-dead-letter-queue"></a>De dode-letter wachtrij

Het doel van de wachtrij met dode letters is om berichten vast te houden die niet aan een ontvanger kunnen worden bezorgd, of berichten die niet kunnen worden verwerkt. Berichten kunnen vervolgens worden verwijderd uit de DLQ en geïnspecteerd. Een toepassing kan, met behulp van een operator, problemen corrigeren en het bericht opnieuw indienen, het feit dat er een fout is opgetreden registreren en corrigerende maatregelen nemen. 

Vanuit API- en protocolperspectief is de DLQ meestal vergelijkbaar met elke andere wachtrij, behalve dat berichten alleen kunnen worden ingediend via de dode-letterbewerking van de bovenliggende entiteit. Bovendien wordt time-to-live niet waargenomen en kun je een bericht van een DLQ niet dood-letteren. De wachtrij voor dode letters ondersteunt volledig de levering van peek-lock en transactionele bewerkingen.

Er is geen automatische opruiming van de DLQ. Berichten blijven in de DLQ totdat u ze expliciet ophalen uit de DLQ en [bel Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) op de dode-letter bericht.

## <a name="dlq-message-count"></a>Aantal DLQ-berichten
Het is niet mogelijk om het aantal berichten in de wachtrij voor dode letters op het onderwerpniveau te verkrijgen. Dat komt omdat berichten niet op onderwerpniveau zitten, tenzij Service Bus een interne fout genereert. In plaats daarvan, wanneer een afzender een bericht naar een onderwerp stuurt, wordt het bericht binnen milliseconden doorgestuurd naar abonnementen voor het onderwerp en bevindt het zich dus niet meer op het onderwerpniveau. U dus berichten zien in de DLQ die zijn gekoppeld aan het abonnement voor het onderwerp. In het volgende voorbeeld laat **Service Bus Explorer** zien dat er momenteel 62 berichten in de DLQ staan voor het abonnement "test1". 

![Aantal DLQ-berichten](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

U ook het aantal DLQ-berichten ontvangen [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription?view=azure-cli-latest#az-servicebus-topic-subscription-show)met de opdracht Azure CLI:. 

## <a name="moving-messages-to-the-dlq"></a>Berichten verplaatsen naar de DLQ

Er zijn verschillende activiteiten in Service Bus die ervoor zorgen dat berichten naar de DLQ worden geduwd vanuit de berichtenengine zelf. Een toepassing kan ook expliciet berichten verplaatsen naar de DLQ. 

Als het bericht wordt verplaatst door de makelaar, twee eigenschappen worden toegevoegd aan het bericht als `DeadLetterReason` `DeadLetterErrorDescription`de makelaar noemt de interne versie van de [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) methode op het bericht: en .

Toepassingen kunnen hun eigen `DeadLetterReason` codes voor de eigenschap definiëren, maar het systeem stelt de volgende waarden in.

| Voorwaarde | DeadLetterReason DeadLetterReason DeadLetterReason DeadLetter | DeadLetterErrorDescription DeadLetterErrorBeschrijving |
| --- | --- | --- |
| Altijd |HeaderSizeOverschreed |Het quotum voor de grootte voor deze stream is overschreden. |
| ! TopicBeschrijving.<br />FilteringMessagesBeforePublishing en SubscriptionDescription inschakelen.<br />InschakelenDeadLetteringOnFilterEvaluatieUitzonderingen |Uitzondering. GetType(). Naam |Uitzondering. Bericht |
| Inschakelen DeadLetteringOnMessageExpiratie |TTLExpiredException |Het bericht is verlopen en naar de wachtrij voor onbestelbare berichten verplaatst. |
| Beschrijving van abonnementen.VereistSessie |Sessie-ID is null. |Door sessie ingeschakelde entiteit staat geen berichten toe waarvan de sessie-id null is. |
| !dode letter wachtrij | MaxTransferHopCountoverschreden | Het maximum aantal toegestane hops bij het doorsturen tussen wachtrijen. De waarde is ingesteld op 4. |
| Toepassing expliciete dode letters |Opgegeven door toepassing |Opgegeven door toepassing |

## <a name="exceeding-maxdeliverycount"></a>Overschrijding van het aantal maxdeliverys

Wachtrijen en abonnementen hebben elk een [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) en [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) eigenschap; de standaardwaarde is 10. Wanneer een bericht onder een slot is bezorgd[(ReceiveMode.PeekLock),](/dotnet/api/microsoft.azure.servicebus.receivemode)maar expliciet is verlaten of het slot is verlopen, wordt het bericht [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) verhoogd. Wanneer [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) [maxdeliverycount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount)overschrijdt, wordt het bericht verplaatst `MaxDeliveryCountExceeded` naar de DLQ, waarbij de redencode wordt opgegeven.

Dit gedrag kan niet worden uitgeschakeld, maar u [het aantal MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) instellen op een groot aantal.

## <a name="exceeding-timetolive"></a>Meer dan TimeToLive

Wanneer de eigenschap [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) of [SubscriptionDescription.EnableDeadLetteringOnMessageExpir](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) is ingesteld op **true** (de standaard is **false),** worden `TTLExpiredException` alle verlopende berichten verplaatst naar de DLQ, waarbij de redencode wordt opgegeven.

Verlopen berichten worden alleen verwijderd en verplaatst naar de DLQ wanneer er ten minste één actieve ontvanger uit de hoofdwachtrij of het abonnement wordt getrokken; dat gedrag is door het ontwerp.

## <a name="errors-while-processing-subscription-rules"></a>Fouten tijdens het verwerken van abonnementsregels

Wanneer de eigenschap [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) is ingeschakeld voor een abonnement, worden eventuele fouten die optreden tijdens de SQL-filterregel van een abonnement, vastgelegd in de DLQ, samen met het gewraakte bericht.

## <a name="application-level-dead-lettering"></a>Toepassingsniveau dead-lettering

Naast de door het systeem geleverde dead-lettering-functies, kunnen toepassingen de DLQ gebruiken om onaanvaardbare berichten expliciet af te wijzen. Ze kunnen berichten bevatten die niet goed kunnen worden verwerkt vanwege elk systeemprobleem, berichten met misvormde payloads of berichten die niet worden gesnapt bij verificatie wanneer een beveiligingssysteem op berichtniveau wordt gebruikt.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Dead-lettering in ForwardTo of SendVia scenario's

Berichten worden onder de volgende voorwaarden naar de wachtrij voor de overdracht van de dode letter verzonden:

- Een bericht gaat door meer dan vier wachtrijen of onderwerpen die aan elkaar zijn [geketend.](service-bus-auto-forwarding.md)
- De doelwachtrij of het onderwerp is uitgeschakeld of verwijderd.
- De doelwachtrij of het onderwerp overschrijdt de maximale entiteitsgrootte.

Als u deze berichten met een dode letter wilt ophalen, u een ontvanger maken met behulp van de [hulpprogramma-hulpprogramma FormatTransferDeadletterPath.](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath)

## <a name="example"></a>Voorbeeld

In het volgende codefragment wordt een berichtontvanger gemaakt. In de ontvangstlus voor de hoofdwachtrij wordt het bericht opgehaald met [Receive (TimeSpan.Zero),](/dotnet/api/microsoft.servicebus.messaging.messagereceiver)waarin de makelaar wordt gevraagd om direct een bericht terug te sturen dat direct beschikbaar is, of om zonder resultaat terug te keren. Als de code een bericht ontvangt, wordt deze `DeliveryCount`onmiddellijk verlaten, waardoor de . Zodra het systeem het bericht naar de DLQ verplaatst, is de hoofdwachtrij leeg en wordt de lus afgesloten, omdat [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) **null retourneert.**

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="path-to-the-dead-letter-queue"></a>Pad naar de wachtrij voor dode letters
U de wachtrij met dode letters openen met behulp van de volgende syntaxis:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Als u de .NET SDK gebruikt, u het pad naar de wachtrij voor dode letters krijgen met de methode SubscriptionClient.FormatDeadLetterPath(). Deze methode neemt de naam/het abonnement van het onderwerp met **/$DeadLetterQueue**.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over wachtrijen voor servicebussen:

* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Azure-wachtrijen en wachtrijen voor servicebussen vergeleken](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

