---
title: Azure Service Bus - uitstel van bericht
description: In dit artikel wordt uitgelegd hoe u de bezorging van Azure Service Bus-berichten uitstellen. Het bericht blijft aanwezig in de wachtrij of het abonnement, maar wordt nog niet verwerkt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538393"
---
# <a name="message-deferral"></a>Berichten uitstellen

Wanneer een wachtrij- of abonnementsclient een bericht ontvangt dat hij bereid is te verwerken, maar waarvoor verwerking momenteel niet mogelijk is vanwege speciale omstandigheden in de toepassing, heeft het de mogelijkheid om het ophalen van het bericht naar een later punt te "uitstellen". Het bericht blijft aanwezig in de wachtrij of het abonnement, maar wordt nog niet verwerkt.

Uitstel is een functie die speciaal is gemaakt voor werkstroomverwerkingsscenario's. Werkstroomkaders kunnen vereisen dat bepaalde bewerkingen in een bepaalde volgorde worden verwerkt en moeten de verwerking van bepaalde ontvangen berichten mogelijk uitstellen totdat voorgeschreven eerder werk dat door andere berichten wordt geïnformeerd, is voltooid.

Een eenvoudig illustratief voorbeeld is een volgorde van orderverwerking waarin een betalingsmelding van een externe betalingsprovider in een systeem wordt weergegeven voordat de overeenkomende inkooporder is gepropageerd van het winkelfront naar het afhandelingssysteem. In dat geval kan het afhandelingssysteem de verwerking van de betalingsmelding uitstellen totdat er een order is waarmee deze kan worden gekoppeld. In rendez-vousscenario's, waarbij berichten uit verschillende bronnen een workflow naar voren leiden, kan de real-time uitvoeringsvolgorde inderdaad correct zijn, maar de berichten die de resultaten weergeven, kunnen buiten de orde komen.

Uiteindelijk helpt uitstel bij het opnieuw ordenen van berichten van de aankomstorder in een volgorde waarin ze kunnen worden verwerkt, terwijl deze berichten veilig in het berichtenarchief blijven waarvoor de verwerking moet worden uitgesteld.

## <a name="message-deferral-apis"></a>Uitstel van het bericht API's

De API is [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) of [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) in de .NET Framework-client, [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) in de .NET Standard-client en [IMessageReceiver.defer](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable) of [IMessageReceiver.deferAsync](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) in de Java-client. 

Uitgestelde berichten blijven in de hoofdwachtrij, samen met alle andere actieve berichten (in tegenstelling tot dode-letter berichten die in een subwachtrij leven), maar ze kunnen niet langer worden ontvangen met behulp van de reguliere Receive/ ReceiveAsync-functies. Uitgestelde berichten kunnen worden ontdekt via [het browsen in berichten](message-browsing.md) als een toepassing ze uit het oog verliest.

Als u een uitgesteld bericht wilt ophalen, is de eigenaar verantwoordelijk voor het onthouden van het [sequencenummer](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) terwijl het wordt uitgesteld. Elke ontvanger die het volgordenummer van een uitgesteld bericht kent, kan het bericht later expliciet ontvangen met `Receive(sequenceNumber)`.

Als een bericht niet kan worden verwerkt omdat een bepaalde bron voor het verwerken van dat bericht tijdelijk niet beschikbaar is, maar de verwerking van berichten niet op een summiere volgorde mag worden opgeschort, is een manier om dat bericht een paar minuten aan de kant te zetten door het **sequencenummer** in een [gepland bericht](message-sequencing.md) te onthouden dat binnen een paar minuten moet worden geplaatst en het uitgestelde bericht opnieuw op te halen wanneer het geplande bericht binnen een paar minuten binnenkomt. Als een berichthandler afhankelijk is van een database voor alle bewerkingen en die database tijdelijk niet beschikbaar is, mag deze geen uitstel gebruiken, maar het ontvangen van berichten helemaal opschorten totdat de database weer beschikbaar is.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
