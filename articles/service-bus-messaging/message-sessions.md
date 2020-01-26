---
title: Azure Service Bus-bericht sessies | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u met behulp van sessies gezamenlijke en geordende verwerking van gerelateerde berichten kunt maken.
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
ms.openlocfilehash: 0338663046c21adad358e8fddec12a3cc8151c79
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759326"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Bericht sessies: First in, first out (FIFO) 

Microsoft Azure Service Bus-sessies maken gezamenlijke en geordende verwerking van niet-gebonden reeksen van gerelateerde berichten mogelijk. Gebruik sessies om een FIFO-garantie in Service Bus te realiseren. Service Bus is niet vooraf geschreven over de aard van de relatie tussen de berichten en definieert ook niet een bepaald model om te bepalen waar een bericht reeks begint of eindigt.

> [!NOTE]
> De Basic-laag van Service Bus biedt geen ondersteuning voor sessies. De Standard-en Premium-lagen ondersteunen sessies. Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor meer informatie.

Elke afzender kan een sessie maken bij het indienen van berichten in een onderwerp of wachtrij door de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) in te stellen op een door de toepassing gedefinieerde id die uniek is voor de sessie. Op het AMQP 1,0-protocol niveau wordt deze waarde toegewezen aan de eigenschap *Group-ID* .

In sessie-wacht rijen of-abonnementen kunnen sessies aanwezig zijn wanneer er ten minste één bericht is met de sessie- [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Zodra een sessie bestaat, is er geen tijd of API voor het moment waarop de sessie verloopt of verdwijnt. In theorie kan een bericht worden ontvangen voor een sessie, het volgende bericht in de tijd van het jaar en als de sessie- **id** overeenkomt met het komt overeen met het service bus perspectief.

Normaal gesp roken bevat een toepassing echter een duidelijk begrip van waar een reeks gerelateerde berichten begint en eindigt. Service Bus stelt geen specifieke regels in.

Een voor beeld van het afbakenen van een reeks voor het overdragen van een bestand is het instellen van de **Label** eigenschap voor het eerste bericht dat moet worden **gestart**, voor tussenliggende berichten tot **inhoud**en voor het laatste bericht dat moet worden **beëindigd**. De relatieve positie van de inhouds berichten kan worden berekend als het huidige bericht *SequenceNumber* Delta van het **begin** bericht *SequenceNumber*.

Met de sessie functie in Service Bus kan een specifieke ontvangst bewerking worden uitgevoerd, in [](/dotnet/api/microsoft.servicebus.messaging.messagesession) de vorm van C# MessageSession in de en Java-api's. U schakelt het onderdeel in door de eigenschap [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) in te stellen voor de wachtrij of het abonnement via Azure Resource Manager, of door de vlag in te stellen in de portal. Dit is vereist voordat u de gerelateerde API-bewerkingen probeert te gebruiken.

Stel in de Portal de vlag in met het volgende selectie vakje:

![][2]

> [!NOTE]
> Wanneer sessies zijn ingeschakeld voor een wachtrij of een abonnement, kunnen de client toepassingen ***geen*** normale berichten meer verzenden/ontvangen. Alle berichten moeten worden verzonden als onderdeel van een sessie (door de sessie-id in te stellen) en ontvangen door de sessie te ontvangen.

De Api's voor sessies bestaan op de wachtrij-en abonnements-clients. Er is een verplicht model dat bepaalt wanneer er sessies en berichten worden ontvangen en een op een handler gebaseerd model, vergelijkbaar met *OnMessage*, waarmee de complexiteit van het beheer van de receive-lus wordt verborgen.

## <a name="session-features"></a>Sessie functies

Sessies bieden gelijktijdige demultiplexing van Interleaved-berichten stromen tijdens het behoud en het garanderen van de bestelde levering.

![][1]

Er wordt een [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -ontvanger gemaakt door de client die een sessie accepteert. De client roept [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) of [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) aan C#in. In het reactieve call back-model registreert het een sessie-handler.

Wanneer het [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -object wordt geaccepteerd en terwijl het wordt bewaard door een-client, bevat die client een exclusieve vergren deling voor alle berichten met de [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) van die sessie die voor komt in de wachtrij of het abonnement, en ook op alle berichten met die **SessionID** die nog steeds arriveren terwijl de sessie wordt gehouden.

De vergren deling wordt vrijgegeven wanneer **Close** of **CloseAsync** wordt aangeroepen of wanneer de vergren deling verloopt in gevallen waarin de toepassing de sluitings bewerking niet kan uitvoeren. De sessie vergrendeling moet worden behandeld als een exclusieve vergren deling van een bestand, wat inhoudt dat de toepassing de sessie moet sluiten zodra deze niet meer nodig is en/of geen verdere berichten verwacht.

Wanneer meerdere gelijktijdige ontvangers uit de wachtrij halen, worden de berichten die tot een bepaalde sessie behoren, verzonden naar de specifieke ontvanger die momenteel de vergren deling voor die sessie bevat. Met deze bewerking wordt een Interleaved-berichten stroom die zich in één wachtrij of abonnement bevindt, schoon verwijderd naar verschillende ontvangers en die ontvangers kunnen ook live op verschillende client computers worden uitgevoerd, omdat het vergrendelings beheer aan de service zijde plaatsvindt, binnen Service Bus.

In de vorige afbeelding worden drie sessie ontvangers gelijktijdig weer gegeven. Een sessie met `SessionId` = 4 heeft geen actieve, eigenaarste client, wat betekent dat er geen berichten vanuit deze specifieke sessie worden afgeleverd. Een sessie werkt op verschillende manieren als een subwachtrij.

De sessie vergrendeling die wordt gehouden door de sessie ontvanger is een paraplu voor de bericht vergrendelingen die worden gebruikt door de modus voor het weer geven van *vergren delingen* . Een ontvanger kan niet twee berichten gelijktijdig hebben ' in vlucht ', maar de berichten moeten op volg orde worden verwerkt. Een nieuw bericht kan alleen worden verkregen wanneer het vorige bericht is voltooid of onbesteld is. Het afbreken van een bericht zorgt ervoor dat hetzelfde bericht opnieuw wordt weer gegeven met de volgende ontvangst bewerking.

## <a name="message-session-state"></a>Status van de bericht sessie

Wanneer werk stromen worden verwerkt in Cloud systemen met hoge Beschik baarheid, moet de werk stroom-handler die aan een bepaalde sessie is gekoppeld, kunnen worden hersteld van onverwachte storingen en kunnen ook gedeeltelijk voltooide werkzaamheden worden hervat op een andere het proces of de computer van waaruit het werk is begonnen.

De functie sessie status maakt een door de toepassing gedefinieerde aantekening van een bericht sessie binnen de Broker mogelijk, zodat de vastgelegde verwerkings status ten opzichte van die sessie direct beschikbaar wordt wanneer de sessie wordt verkregen door een nieuwe processor.

Vanuit het Service Bus perspectief is de status van de bericht sessie een ondoorzichtig binair object dat gegevens kan bevatten van de grootte van één bericht, 256 KB voor Service Bus standaard en 1 MB voor Service Bus Premium. De verwerkings status ten opzichte van een sessie kan worden vastgehouden binnen de sessie status, of de sessie status kan verwijzen naar een opslag locatie of database record die dergelijke informatie bevat.

De Api's voor het beheren van de sessie status, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) en [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), kunt u vinden in het object [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) in zowel de als de C# Java-api's. Een sessie die eerder geen sessie statusset had, retourneert een **Null** -verwijzing voor **GetState**. Het wissen van de eerder ingestelde sessie status is voltooid met [SetState (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Houd er rekening mee dat de sessie status behouden blijft, omdat deze niet wordt gewist ( **Null**wordt geretourneerd), zelfs als alle berichten in een sessie worden verbruikt.

Alle bestaande sessies in een wachtrij of abonnement kunnen worden geïnventariseerd met de **SessionBrowser** -methode in de Java API en met [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) op de [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) en [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) in de .net-client.

De sessie status die is opgeslagen in een wachtrij of in een abonnement, telt de opslag limiet van die entiteit. Wanneer de toepassing is voltooid met een sessie, wordt het aanbevolen om de status van de toepassing op te schonen om te voor komen dat de kosten voor externe beheer worden bespaard.

## <a name="impact-of-delivery-count"></a>Impact van het aantal levering

De definitie van het aantal leveringen per bericht in de context van sessies verschilt enigszins van de definitie in afwezigheid van sessies. Hier volgt een tabel met een samen vatting van het aantal leverings stappen.

| Scenario | Is het aantal leveringen van het bericht verhoogd |
|----------|---------------------------------------------|
| Sessie wordt geaccepteerd, maar de sessie vergrendeling verloopt (vanwege een time-out) | Ja |
| Sessie wordt geaccepteerd, de berichten in de sessie worden niet voltooid (zelfs als ze zijn vergrendeld) en de sessie wordt gesloten | Nee |
| Sessie wordt geaccepteerd, berichten worden voltooid en de sessie wordt vervolgens expliciet gesloten | N.v.t. (dit is de standaard stroom). Hier worden berichten uit de sessie verwijderd) |

## <a name="next-steps"></a>Volgende stappen

- Zie de voor beelden van [micro soft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) of [micro soft. ServiceBus. Messa ging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) voor een voor beeld waarin de .NET Framework-client wordt gebruikt voor het afhandelen van sessie bewuste berichten. 

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
