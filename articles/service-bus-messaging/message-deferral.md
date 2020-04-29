---
title: Azure Service Bus-uitstel van berichten
description: In dit artikel wordt uitgelegd hoe u de bezorging van Azure Service Bus berichten uitstelt. Het bericht blijft aanwezig in de wachtrij of het abonnement, maar wordt nog niet verwerkt.
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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77538393"
---
# <a name="message-deferral"></a>Berichten uitstellen

Wanneer een wachtrij of abonnements-client een bericht ontvangt dat het wil verwerken, maar waarvoor de verwerking momenteel niet mogelijk is vanwege speciale omstandigheden in de toepassing, heeft het de optie ' het uitstellen van het bericht ' het ophalen van berichten naar een later tijdstip. Het bericht blijft aanwezig in de wachtrij of het abonnement, maar wordt nog niet verwerkt.

Uitstel is een functie die specifiek is gemaakt voor werk stroom verwerkings scenario's. Werk stroom raamwerken kunnen vereisen dat bepaalde bewerkingen in een bepaalde volg orde worden verwerkt en moeten mogelijk de verwerking van enkele ontvangen berichten uitstellen totdat het vereiste voorafgaande werk dat wordt geïnformeerd door andere berichten is voltooid.

Een eenvoudig voor beeld is een verwerkings volgorde voor orders waarbij een betalings melding van een externe betalings provider wordt weer gegeven in een systeem voordat de overeenkomende aankoop order is door gegeven van de winkel naar het fulfillment-systeem. In dat geval kan het fulfillment-systeem de verwerking van de betalings melding uitstellen totdat er een order is waarmee deze kan worden gekoppeld. In de scenario's van rendez, waar berichten van verschillende bronnen een werk stroom sturen, is het mogelijk dat de volg orde voor het uitvoeren van realtime juist is, maar de berichten die de resultaten weer spie gelen, kunnen in de juiste volg orde arriveren.

Uiteindelijk worden hulp middelen voor uitstel bij het opnieuw ordenen van berichten van de aankomst order omgezet in een volg orde waarin ze kunnen worden verwerkt, terwijl deze berichten veilig in het berichten archief worden bewaard waarvoor de verwerking moet worden uitgesteld.

## <a name="message-deferral-apis"></a>Api's voor uitstel van berichten

De API is [BrokeredMessage. defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) of [BrokeredMessage. DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) in de .NET Framework-client, [MessageReceiver. DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) in de .NET Standard-client en [IMessageReceiver. defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) of [IMessageReceiver. DeferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) in de Java-client. 

Uitgestelde berichten blijven in de hoofd wachtrij, samen met alle andere actieve berichten (in tegens telling tot onbestelbare berichten in een subwachtrij), maar ze kunnen niet langer worden ontvangen met de normale receive/ReceiveAsync-functies. Uitgestelde berichten kunnen worden gedetecteerd via het [Bladeren door berichten](message-browsing.md) als een toepassing deze niet kan bijhouden.

Als u een uitgesteld bericht wilt ophalen, is de eigenaar verantwoordelijk voor het onthouden van de [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) . Elke ontvanger die het Volg nummer van een uitgesteld bericht kent, kan het bericht later expliciet ontvangen `Receive(sequenceNumber)`met.

Als een bericht niet kan worden verwerkt omdat een bepaalde resource voor de verwerking van dat bericht tijdelijk niet beschikbaar is, maar de verwerking van berichten niet samen vatting mag worden onderbroken, is het niet meer mogelijk om de **SequenceNumber** in een [gepland bericht](message-sequencing.md) te onthouden en het uitgestelde bericht opnieuw op te halen wanneer het geplande bericht binnenkomt. Als een Message Handler afhankelijk is van een Data Base voor alle bewerkingen en die data base tijdelijk niet beschikbaar is, moet deze geen uitstel gebruiken, maar in plaats daarvan de ontvangst van berichten samen onderbreken totdat de Data Base weer beschikbaar is.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
