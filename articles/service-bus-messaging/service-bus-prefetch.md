---
title: Azure Service Bus prefetch-berichten | Microsoft Documenten
description: Verbeter de prestaties door Azure Service Bus-berichten vooraf op te halen. Berichten zijn direct beschikbaar voor lokaal ophalen voordat de toepassingsaanvragen voor hen worden ingediend.
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
ms.openlocfilehash: 80717ab940d27e9bf108b3740309bcd7d71668fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760654"
---
# <a name="prefetch-azure-service-bus-messages"></a>Azure Service Bus-berichten vooraf ophalen

Wanneer *Prefetch* is ingeschakeld in een van de officiële Service Bus-clients, verwerft de ontvanger stilletjes meer berichten, tot de [PrefetchCount-limiet,](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) verder dan wat de toepassing in eerste instantie vroeg.

Een enkele eerste [receive of](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) [receiveAsync-oproep](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) krijgt daarom een bericht voor directe consumptie dat zo snel mogelijk wordt geretourneerd. De client verwerft vervolgens verdere berichten op de achtergrond, om de prefetch-buffer te vullen.

## <a name="enable-prefetch"></a>Prefetch inschakelen

Met .NET schakelt u de functie Prefetch in door de eigenschap [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) van een **MessageReceiver,** **QueueClient**of **SubscriptionClient** in te stellen op een getal groter dan nul. Als u de waarde op nul instelt, schakelt u prefetch uit.

U deze instelling eenvoudig toevoegen aan de instellingen van de [queuesGettingStarted-](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) of [ReceiveLoop-voorbeelden](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) om het effect in die contexten te zien.

Hoewel berichten beschikbaar zijn in de prefetch-buffer, worden eventuele volgende **Receive**/**ReceiveAsync-oproepen** onmiddellijk uitgevoerd vanuit de buffer en wordt de buffer op de achtergrond aangevuld zodra er ruimte beschikbaar komt. Als er geen berichten beschikbaar zijn voor levering, leegt de ontvangstbewerking de buffer en wacht of blokkeert, zoals verwacht.

Prefetch werkt ook op dezelfde manier met de [OnMessage-](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) en [OnMessageAsync-API's.](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync)

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Als het sneller is, waarom is Prefetch dan niet de standaardoptie?

Prefetch versnelt de berichtenstroom door een bericht direct beschikbaar te hebben voor lokaal ophalen wanneer en voordat de toepassing er om vraagt. Deze doorvoerwinst is het resultaat van een afweging die de auteur van de toepassing expliciet moet maken:

Met de [receiveanddelete-ontvangstmodus](/dotnet/api/microsoft.servicebus.messaging.receivemode) zijn alle berichten die zijn/verkregen in de prefetch-buffer niet langer beschikbaar in de wachtrij en bevinden ze zich alleen in de prefetch-buffer in het geheugen totdat ze in de toepassing zijn ontvangen via de ApI's **ontvangen ontvangen**/**async** of **OnMessage****OnMessageAsync.** Als de toepassing wordt beëindigd voordat de berichten in de toepassing worden ontvangen, gaan deze berichten onherstelbaar verloren.

In de [peeklock-ontvangstmodus](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) worden berichten die in de Prefetch-buffer worden opgehaald, in de buffer in een vergrendelde status opgehaald en wordt de time-outklok voor het vergrendelen getikt. Als de prefetch-buffer groot is en de verwerking zo lang duurt dat berichtvergrendelingen verlopen terwijl ze in de prefetch-buffer verblijven of zelfs terwijl de toepassing het bericht verwerkt, kunnen er enkele verwarrende gebeurtenissen zijn die de toepassing moet verwerken.

De toepassing kan een bericht verkrijgen met een verlopen of binnenkort verlopen slot. Als dat het geval is, kan de toepassing het bericht verwerken, maar vervolgens vinden dat het niet kan voltooien als gevolg van een vergrendeling sloopt. De toepassing kan de eigenschap [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) controleren (die onderhevig is aan klokscheefheid tussen de makelaar en de lokale machineklok). Als het berichtslot is verlopen, moet de toepassing het bericht negeren. er mag geen API-aanroep of met het bericht worden gemaakt. Als het bericht niet is verlopen, maar de vervaldatum op handen is, kan het slot worden verlengd met een andere standaardvergrendelingsperiode door [een oproepbericht. RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Als het slot stilletjes verloopt in de prefetch-buffer, wordt het bericht als verlaten behandeld en opnieuw beschikbaar gesteld voor ophalen uit de wachtrij. Dat kan ertoe leiden dat het wordt opgehaald in de prefetch-buffer; geplaatst aan het einde. Als de prefetch-buffer meestal niet kan worden uitgevoerd tijdens het verlopen van het bericht, zorgt dit ervoor dat berichten herhaaldelijk vooraf worden opgehaald, maar nooit effectief worden geleverd in een bruikbare (geldig vergrendelde) status, en uiteindelijk worden verplaatst naar de wachtrij met dode letters zodra de het maximale aantal levering wordt overschreden.

Als u een hoge mate van betrouwbaarheid nodig hebt voor de verwerking van berichten en verwerking veel werk en tijd vergt, wordt aanbevolen dat u de prefetch-functie conservatief of helemaal niet gebruikt.

Als u een hoge doorvoer nodig hebt en de verwerking van berichten vaak goedkoop is, levert prefetch aanzienlijke doorvoervoordelen op.

Het maximale aantal voorhalen en de vergrendelingsduur die zijn geconfigureerd in de wachtrij of het abonnement, moeten zodanig worden afgewogen dat de vergrendelingstime-out ten minste de cumulatieve verwachte tijd voor de verwerking van berichten voor de maximale grootte van de prefetch-buffer, plus één bericht, overschrijdt. Tegelijkertijd moet de lock time-out niet zo lang duren dat berichten hun maximale [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) kunnen overschrijden wanneer ze per ongeluk worden gedropt, waardoor hun vergrendeling moet verlopen voordat ze opnieuw worden afgeleverd.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
