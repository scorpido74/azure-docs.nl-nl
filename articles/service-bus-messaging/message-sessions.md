---
title: Azure Service Bus-berichtensessies | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u sessies gebruiken om gezamenlijke en geordende verwerking van niet-begrensde reeksen gerelateerde berichten mogelijk te maken.
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
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314043"
---
# <a name="message-sessions"></a>Berichtsessies
Microsoft Azure Service Bus-sessies maken gezamenlijke en geordende afhandeling van niet-begrensde reeksen gerelateerde berichten mogelijk. Sessies kunnen worden gebruikt in first in, first out (FIFO) en request-response patronen. In dit artikel ziet u hoe u sessies gebruikt om deze patronen te implementeren bij het gebruik van Service Bus. 

## <a name="first-in-first-out-fifo-pattern"></a>First-in, first out (FIFO) patroon
Gebruik sessies om een FIFO-garantie in Service Bus te realiseren. Service Bus is niet prescriptief over de aard van de relatie tussen de berichten, en definieert ook niet een bepaald model om te bepalen waar een berichtreeks begint of eindigt.

> [!NOTE]
> De basislaag van Service Bus biedt geen ondersteuning voor sessies. De standaard- en premium-ondersteuningssessies. Zie [Servicebus-prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor verschillen tussen deze lagen.

Elke afzender kan een sessie maken wanneer hij berichten in een onderwerp of wachtrij verzendt door de eigenschap [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) in te stellen op een toepassingscode die uniek is voor de sessie. Op het PROTOCOLniveau van AMQP 1.0 wordt deze waarde toegewezen aan de eigenschap *group-id.*

Bij sessiebewuste wachtrijen of abonnementen ontstaan sessies wanneer er ten minste één bericht is met de [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)van de sessie. Zodra een sessie bestaat, is er geen gedefinieerde tijd of API voor wanneer de sessie verloopt of verdwijnt. Theoretisch kan een bericht worden ontvangen voor een sessie vandaag, het volgende bericht in een jaar tijd, en als de **SessionId** overeenkomt, de sessie is hetzelfde vanuit de Service Bus perspectief.

Meestal heeft een toepassing echter een duidelijk idee van waar een reeks gerelateerde berichten begint en eindigt. Service Bus stelt geen specifieke regels in.

Een voorbeeld van het definiëren van een reeks voor het overbrengen van een bestand is het instellen van de eigenschap **Label** voor het eerste bericht dat **wordt gestart,** voor tussenliggende berichten naar **inhoud**en voor het laatste bericht om te **eindigen**. De relatieve positie van de inhoudsberichten kan worden berekend als de delta van het huidige bericht *SequenceNumber* vanaf het **beginbericht** *SequenceNumber*.

De sessiefunctie in Service Bus maakt een specifieke ontvangstbewerking mogelijk, in de vorm van [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) in de C# en Java API's. U schakelt de functie in door de eigenschap [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) in te stellen in de wachtrij of het abonnement via Azure Resource Manager of door de vlag in de portal in te stellen. Dit is vereist voordat u probeert de gerelateerde API-bewerkingen te gebruiken.

Stel in de portal de vlag in met het volgende selectievakje:

![][2]

> [!NOTE]
> Wanneer Sessies zijn ingeschakeld in een wachtrij of een abonnement, kunnen de clienttoepassingen geen gewone berichten ***meer*** verzenden/ontvangen. Alle berichten moeten worden verzonden als onderdeel van een sessie (door het sessie-id in te stellen) en ontvangen door de sessie te ontvangen.

De API's voor sessies bestaan op wachtrij- en abonnementsclients. Er is een noodzakelijk model dat bepaalt wanneer sessies en berichten worden ontvangen, en een handler-gebaseerd model, vergelijkbaar met *OnMessage,* dat de complexiteit van het beheren van de ontvangen lus verbergt.

### <a name="session-features"></a>Sessiefuncties

Sessies bieden gelijktijdige demultiplexing van interleaved berichtstromen met behoud en garantie van de bestelde levering.

![][1]

Een [MessageSession-ontvanger](/dotnet/api/microsoft.servicebus.messaging.messagesession) wordt gemaakt door dat de client een sessie accepteert. De client roept [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) of [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) in C#. In het reactieve callbackmodel registreert het een sessiehandler.

Wanneer het [Object MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) wordt geaccepteerd en terwijl het door een client wordt bewaard, houdt die client een exclusief slot op alle berichten met de [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) van die sessie die in de wachtrij of het abonnement bestaat, en ook op alle berichten met die **SessionId** die nog steeds aankomen terwijl de sessie wordt gehouden.

Het slot wordt vrijgegeven wanneer **Sluiten** of **CloseAsync** wordt aangeroepen of wanneer het slot verloopt in gevallen waarin de toepassing de sluitbewerking niet kan uitvoeren. Het sessieslot moet worden behandeld als een exclusief slot op een bestand, wat betekent dat de toepassing de sessie moet sluiten zodra het niet meer nodig heeft en/of geen verdere berichten verwacht.

Wanneer meerdere gelijktijdige ontvangers uit de wachtrij worden getrokken, worden de berichten die behoren tot een bepaalde sessie verzonden naar de specifieke ontvanger die momenteel het slot voor die sessie vasthoudt. Met die bewerking wordt een interleaved berichtenstroom in één wachtrij of abonnement netjes gedemultiplexed naar verschillende ontvangers en kunnen die ontvangers ook op verschillende clientmachines leven, omdat het vergrendelingsbeheer service-kant gebeurt, binnen Service Bus.

De vorige illustratie toont drie gelijktijdige sessie-ontvangers. Eén sessie `SessionId` met = 4 heeft geen actieve, bezittende client, wat betekent dat er geen berichten worden bezorgd vanuit deze specifieke sessie. Een sessie werkt op vele manieren als een subwachtrij.

Het sessieslot dat door de sessieontvanger wordt vastgehouden, is een paraplu voor de berichtvergrendelingen die worden gebruikt door de peek-lock-vereffeningsmodus. *peek-lock* Een ontvanger kan niet twee berichten tegelijk 'tijdens de vlucht' hebben, maar de berichten moeten in volgorde worden verwerkt. Een nieuw bericht kan alleen worden verkregen als het eerdere bericht is voltooid of doodgeletterd is. Als u een bericht verlaat, wordt hetzelfde bericht opnieuw weergegeven bij de volgende ontvangstbewerking.

### <a name="message-session-state"></a>Status van berichtsessie

Wanneer werkstromen worden verwerkt in cloudsystemen met grote beschikbaarheid, moet de werkstroomhandler die aan een bepaalde sessie is gekoppeld, kunnen herstellen van onverwachte fouten en kan de gedeeltelijk voltooide werkzaamheden op een ander proces of een andere machine worden hervat dan waar het werk is begonnen.

De sessiestatusfaciliteit maakt een door de toepassing gedefinieerde annotatie van een berichtsessie binnen de broker mogelijk, zodat de opgenomen verwerkingsstatus ten opzichte van die sessie direct beschikbaar wordt wanneer de sessie wordt overgenomen door een nieuwe processor.

Vanuit het perspectief servicebus is de status van de berichtsessie een ondoorzichtig binair object dat gegevens van de grootte van één bericht kan bevatten, namelijk 256 KB voor Service Bus Standard, en 1 MB voor Service Bus Premium. De verwerkingsstatus ten opzichte van een sessie kan worden gehouden in de sessiestatus of de sessiestatus kan wijzen op een opslaglocatie of databaserecord met dergelijke informatie.

De API's voor het beheren van de sessiestatus, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) en [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)zijn te vinden op het object [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) in zowel de C#- als java-API's. Een sessie die voorheen geen sessiestatus had ingesteld, retourneert een **null-verwijzing** voor **GetState**. Het wissen van de eerder ingestelde sessiestatus gebeurt met [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

De sessiestatus blijft zolang deze niet is gewist **(null retourneren),** zelfs als alle berichten in een sessie worden verbruikt.

Alle bestaande sessies in een wachtrij of abonnement kunnen worden opgesomd met de **SessionBrowser-methode** in de Java-API en met [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) op de [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) en [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) in de .NET Framework-client.

De sessiestatus die in een wachtrij of in een abonnement wordt gehouden, telt mee voor het opslagquotum van die entiteit. Wanneer de toepassing is voltooid met een sessie, is het daarom raadzaam voor de toepassing om de bewaarde status op te schonen om externe beheerkosten te voorkomen.

### <a name="impact-of-delivery-count"></a>Impact van het aantal levering

De definitie van het aantal bezorging per bericht in de context van sessies wijkt enigszins af van de definitie bij afwezigheid van sessies. Hier is een tabel met een samenvatting wanneer het aantal levering wordt verhoogd.

| Scenario | Is het aantal bezorgingshoeveelheden van het bericht verhoogd |
|----------|---------------------------------------------|
| Sessie wordt geaccepteerd, maar het sessieslot verloopt (vanwege een time-out) | Ja |
| Sessie wordt geaccepteerd, de berichten binnen de sessie zijn niet voltooid (zelfs als ze zijn vergrendeld) en de sessie is gesloten | Nee |
| Sessie wordt geaccepteerd, berichten worden voltooid en vervolgens wordt de sessie expliciet gesloten | N/A (Het is de standaard stroom. Hier worden berichten uit de sessie verwijderd) |

## <a name="request-response-pattern"></a>Patroon voor antwoord op verzoek
Het [patroon van het verzoek-antwoord](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) is een gevestigd integratiepatroon waarmee de afzendertoepassing een verzoek kan verzenden en biedt een manier voor de ontvanger om een antwoord correct terug te sturen naar de afzendertoepassing. Dit patroon heeft meestal een wachtrij of onderwerp van korte duur nodig om de toepassing naar te sturen. In dit scenario bieden sessies een eenvoudige alternatieve oplossing met vergelijkbare semantiek. 

Meerdere toepassingen kunnen hun aanvragen naar één aanvraagwachtrij sturen, waarbij een specifieke kopparameter is ingesteld om de afzendertoepassing op unieke wijze te identificeren. De ontvangertoepassing kan de aanvragen verwerken die in de wachtrij binnenkomen en antwoorden verzenden in een wachtrij met sessies, waarbij de sessie-id wordt ingesteld op de unieke id die de afzender in het verzoekbericht had verzonden. De toepassing die het verzoek heeft verzonden, kan vervolgens berichten ontvangen op een specifieke sessie-id en de antwoorden correct verwerken.

> [!NOTE]
> De toepassing die de eerste verzoeken verzendt, `SessionClient.AcceptMessageSession(SessionID)` moet op de hoogte zijn van de sessie-id en de sessie vergrendelen waarop de sessie wordt verwacht. Het is een goed idee om een GUID te gebruiken die de instantie van de toepassing op een unieke wijze identificeert als een sessie-id. Er mag geen sessie-handler of `AcceptMessageSession(timeout)` in de wachtrij staan om ervoor te zorgen dat antwoorden beschikbaar zijn om te worden vergrendeld en verwerkt door specifieke ontvangers.

## <a name="next-steps"></a>Volgende stappen

- Zie de [voorbeelden van Microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) of [Microsoft.ServiceBus.Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) voor een voorbeeld dat de .NET Framework-client gebruikt om sessiebewuste berichten te verwerken. 

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
