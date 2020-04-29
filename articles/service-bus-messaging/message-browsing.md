---
title: Azure Service Bus-bericht bladeren
description: Met Service Bus berichten bladeren en bekijken kunnen een Azure Service Bus-client alle berichten opsommen die zich in een wachtrij of abonnement bevinden.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77539362"
---
# <a name="message-browsing"></a>Berichten doorzoeken

Door een bericht te bladeren of te bekijken, kan een Service Bus-client alle berichten opsommen die zich in een wachtrij of abonnement bevinden, meestal voor diagnose-en fout opsporing.

Met de bewerking Peek worden alle berichten geretourneerd die bestaan in het wachtrij-of abonnements berichten logboek, niet alleen die beschikbaar zijn `Receive()` voor direct `OnMessage()` ophalen met of de lus. De `State` eigenschap van elk bericht vertelt u of het bericht actief is (dat kan worden ontvangen), [uitgesteld](message-deferral.md)of [gepland](message-sequencing.md).

Verbruikte en verlopen berichten worden opgeschoond door een asynchrone ' garbagecollection ' en niet noodzakelijkerwijs precies wanneer berichten verlopen en daarom `Peek` is het mogelijk om berichten te retour neren die al verlopen zijn en worden verwijderd of onbestelbaar wanneer een receive-bewerking vervolgens wordt aangeroepen voor de wachtrij of het abonnement.

Dit is vooral belang rijk bij het herstellen van uitgestelde berichten uit de wachtrij. Een bericht waarvoor de [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) direct is door gegeven, komt niet meer in aanmerking voor regel matige ophaal acties op een andere manier, zelfs wanneer dit wordt geretourneerd door Peek. Het retour neren van deze berichten is opzettelijk, omdat Peek een diagnostisch hulp programma is dat de huidige status van het logboek weergeeft.

PEEK retourneert ook berichten die zijn vergrendeld en die momenteel worden verwerkt door andere ontvangers, maar die nog niet zijn voltooid. Omdat Peek echter een niet-verbonden moment opname retourneert, kan de vergrendelings status van een bericht niet worden waargenomen op getoonde berichten. de eigenschappen [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) en [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) genereren een [InvalidOperationException](/dotnet/api/system.invalidoperationexception) wanneer de toepassing deze probeert te lezen.

## <a name="peek-apis"></a>Api's bekijken

De methoden [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) en [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) bestaan in alle .net-en Java-client bibliotheken en op alle receiver-objecten: **MessageReceiver**, **MessageSession**. Peek werkt op alle wacht rijen en abonnementen en de bijbehorende wacht rijen voor onbestelbare berichten.

Wanneer herhaaldelijk wordt aangeroepen, worden alle berichten die in de wachtrij of het logboek voor het abonnement aanwezig zijn, in volg orde van Volg nummers opgesomd van het laagste beschik bare Volg nummer tot het hoogste niveau. Dit is de volg orde waarin berichten in wachtrij worden gezet en niet de volg orde waarin berichten uiteindelijk kunnen worden opgehaald.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) haalt meerdere berichten op en retourneert deze als een opsomming. Als er geen berichten beschikbaar zijn, is het opsommings object leeg, niet null.

U kunt ook een overbelasting van de methode seeden met een [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) waarop moet worden gestart. Vervolgens roept u de methode-overload zonder para meters aan om verdere inventarisatie uit te voeren. **PeekBatch** werkt gelijk aan, maar haalt een aantal berichten tegelijk op.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
