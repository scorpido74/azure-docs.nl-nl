---
title: Overzicht van transactieverwerking in Azure Service Bus
description: In dit artikel vindt u een overzicht van transactieverwerking en de functie verzenden via in Azure Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 22744ecbced40b3195f4d047227b1e2a37228102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260902"
---
# <a name="overview-of-service-bus-transaction-processing"></a>Overzicht van de verwerking van servicebustransacties

In dit artikel worden de transactiemogelijkheden van Microsoft Azure Service Bus besproken. Een groot deel van de discussie wordt geïllustreerd door de [AMQP-transacties met servicebusvoorbeeld](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia). Dit artikel is beperkt tot een overzicht van transactieverwerking en de *functie verzenden via* servicebus, terwijl de steekproef voor atoomtransacties breder en complexer van omvang is.

## <a name="transactions-in-service-bus"></a>Transacties in servicebus

Een *transactie* groepeert twee of meer bewerkingen samen in een *uitvoeringsbereik*. Een dergelijke transactie moet er van nature voor zorgen dat alle transacties die tot een bepaalde groep transacties behoren, gezamenlijk slagen of mislukken. In dit opzicht fungeren transacties als één eenheid, die vaak *atomiciteit*wordt genoemd .

Service Bus is een transactionele berichtenmakelaar en zorgt voor transactionele integriteit voor alle interne bewerkingen ten opzichte van zijn berichtenwinkels. Alle overdrachten van berichten in de Service Bus, zoals het verplaatsen van berichten naar een [wachtrij met dode letter](service-bus-dead-letter-queues.md) of het automatisch [doorsturen](service-bus-auto-forwarding.md) van berichten tussen entiteiten, zijn transactioneel. Als Service Bus een bericht accepteert, is het daarom al opgeslagen en gelabeld met een volgnummer. Vanaf dat moment zijn berichtoverdrachten binnen Service Bus gecoördineerde bewerkingen tussen entiteiten en leiden ze niet tot verlies (bron slaagt en doel mislukt) of tot duplicatie (bron mislukt en doel slaagt) van het bericht.

Service Bus biedt ondersteuning voor het groeperen van bewerkingen voor één berichtentiteit (wachtrij, onderwerp of abonnement) binnen het bereik van een transactie. U bijvoorbeeld meerdere berichten naar één wachtrij sturen vanuit een transactiebereik en de berichten worden alleen vastgelegd in het logboek van de wachtrij wanneer de transactie is voltooid.

## <a name="operations-within-a-transaction-scope"></a>Transacties binnen een transactiebereik

De bewerkingen die binnen een transactiebereik kunnen worden uitgevoerd, zijn als volgt:

* ** [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient), [MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender), [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)**: Verzenden, VerzendenAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

Ontvangstbewerkingen zijn niet inbegrepen, omdat wordt aangenomen dat de toepassing berichten verkrijgt met de [ReceiveMode.PeekLock-modus,](/dotnet/api/microsoft.azure.servicebus.receivemode) in sommige ontvangstlus of met een [OnMessage-terugroepen,](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) en pas daarna een transactiebereik opent voor het verwerken van het bericht.

De dispositie van het bericht (volledig, verlaten, dode letter, uitstellen) vindt dan plaats binnen het bereik van en afhankelijk van het algemene resultaat van de transactie.

## <a name="transfers-and-send-via"></a>Transfers en "verzenden via"

Als u transactionele overdracht van gegevens van een wachtrij naar een processor wilt inschakelen en vervolgens naar een andere wachtrij, ondersteunt Service Bus *overdrachten*. In een overdrachtsbewerking stuurt een afzender eerst een bericht naar een *transferwachtrij*en verplaatst de transferwachtrij het bericht onmiddellijk naar de beoogde doelwachtrij met dezelfde robuuste overdrachtsimplementatie waarop de automatische doorstuurmogelijkheid is gebaseerd. Het bericht is nooit vastgelegd in het logboek van de overdrachtswachtrij op een manier die zichtbaar wordt voor de consumenten van de transferwachtrij.

De kracht van deze transactionele mogelijkheid wordt duidelijk wanneer de overdrachtswachtrij zelf de bron is van de invoerberichten van de afzender. Met andere woorden, Service Bus kan het bericht overbrengen naar de doelwachtrij "via" de transferwachtrij, terwijl u een volledige (of uitstellen, of dode-letter) bewerking uitvoert op het invoerbericht, allemaal in één atoombewerking. 

### <a name="see-it-in-code"></a>Bekijk het in code

Als u dergelijke overdrachten wilt instellen, maakt u een afzender van het bericht die zich richt op de doelwachtrij via de transferwachtrij. Je hebt ook een ontvanger die berichten uit dezelfde wachtrij haalt. Bijvoorbeeld:

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

Een eenvoudige transactie gebruikt deze elementen vervolgens, zoals in het volgende voorbeeld. Als u het volledige voorbeeld wilt verwijzen, raadpleegt u de [broncode op GitHub:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over wachtrijen voor servicebussen:

* [Wachtrijen voor servicebussen gebruiken](service-bus-dotnet-get-started-with-queues.md)
* [Servicebusentiteiten vastketenen met automatisch doorsturen](service-bus-auto-forwarding.md)
* [Voorbeeld van automatisch doorsturen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Voorbeeld van atoomtransacties met servicebus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Azure-wachtrijen en wachtrijen voor servicebussen vergeleken](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


