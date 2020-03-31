---
title: Azure Service Bus - bladeren door berichten
description: Met Browse- en peek Service Bus-berichten kan een Azure Service Bus-client alle berichten opsommen die zich in een wachtrij of abonnement bevinden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539362"
---
# <a name="message-browsing"></a>Berichten doorzoeken

Met bladeren in berichten of gluren kan een Service Bus-client alle berichten opsommen die zich in een wachtrij of abonnement bevinden, meestal voor diagnostische en foutopsporingsdoeleinden.

De peek-bewerkingen retourneren alle berichten die in het wachtrij- of `Receive()` abonnementsberichtlogboek staan, niet alleen berichten die beschikbaar zijn voor onmiddellijke acquisitie met of de `OnMessage()` lus. De `State` eigenschap van elk bericht geeft aan of het bericht actief is (beschikbaar om te worden ontvangen), [uitgesteld](message-deferral.md)of [gepland](message-sequencing.md).

Verbruikte en verlopen berichten worden opgeschoond door een asynchrone "garbage collection" run `Peek` en niet noodzakelijkerwijs precies wanneer berichten verlopen, en daarom kan inderdaad berichten die al zijn verlopen en zal worden verwijderd of dood-lettered wanneer een ontvangst bewerking wordt volgende ingeroepen op de wachtrij of abonnement.

Dit is vooral belangrijk om in gedachten te houden bij een poging om uitgestelde berichten uit de wachtrij te herstellen. Een bericht waarvoor het [moment van ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) is verstreken, komt niet langer in aanmerking voor regelmatig ophalen op een andere manier, zelfs niet wanneer het wordt geretourneerd door Peek. Het retourneren van deze berichten is opzettelijk, omdat Peek een diagnosetool is die de huidige status van het logboek weergeeft.

Peek retourneert ook berichten die zijn vergrendeld en momenteel worden verwerkt door andere ontvangers, maar nog niet zijn voltooid. Omdat Peek echter een losgekoppelde momentopname retourneert, kan de vergrendelingsstatus van een bericht niet worden waargenomen op gegluurde berichten en gooien de eigenschappen [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) en [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) een [InvalidOperationException](/dotnet/api/system.invalidoperationexception) wanneer de toepassing deze probeert te lezen.

## <a name="peek-apis"></a>Peek-API's

De methoden [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) en [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) bestaan in alle .NET- en Java-clientbibliotheken en op alle ontvangerobjecten: **MessageReceiver**, **MessageSession**. Peek werkt op alle wachtrijen en abonnementen en hun respectievelijke dode-letter wachtrijen.

Wanneer u herhaaldelijk wordt aangeroepen, somt de methode Peek alle berichten op die in het wachtrij- of abonnementslogboek aanwezig zijn, in volgorde van volgordenummer, van het laagste beschikbare volgnummer tot het hoogste. Dit is de volgorde waarin berichten in de wachtrij zijn geplaatst en is niet de volgorde waarin berichten uiteindelijk kunnen worden opgehaald.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) haalt meerdere berichten op en retourneert deze als opsomming. Als er geen berichten beschikbaar zijn, is het opsommingsobject leeg en niet null.

U ook een overbelasting van de methode met een [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) waar u moet beginnen, en vervolgens de parameterloze methodeoverbelasting aanroepen om verder op te sommen. **PeekBatch** functioneert gelijkwaardig, maar haalt een set berichten in één keer op.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
