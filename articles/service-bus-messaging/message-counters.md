---
title: Azure Service Bus - aantal berichten
description: Haal het aantal berichten op dat in wachtrijen en abonnementen wordt bewaard met Azure Resource Manager en de Azure Service Bus NamespaceManager API's.
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
ms.date: 04/08/2020
ms.author: aschhab
ms.openlocfilehash: 8020b12ca892fbf7dec6fed6259526d958fb110f
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891761"
---
# <a name="message-counters"></a>Berichtentellers

U het aantal berichten dat in wachtrijen en abonnementen wordt vastgehouden, ophalen met Azure Resource Manager en de API's voor [ServiceBusNamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) in de .NET Framework SDK.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met PowerShell u de telling als volgt verkrijgen:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Details van het aantal berichten

Het kennen van het aantal actieve berichten is handig om te bepalen of een wachtrij een achterstand opbouwt waarvoor meer resources moeten worden verwerkt dan wat momenteel is geïmplementeerd. De volgende details van de teller zijn beschikbaar in de klasse [MessageCountDetails:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails)

-   [ActiveMessageCount:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount)berichten in de wachtrij of het abonnement die in de actieve status zijn en klaar zijn voor levering.
-   [DeadLetterMessageCount:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount)Berichten in de wachtrij voor dode letters.
-   [ScheduledMessageCount:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount)Berichten in de geplande status.
-   [TransferDeadLetterMessageCount:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount)Berichten die niet zijn overgezet naar een andere wachtrij of onderwerp en zijn verplaatst naar de wachtrij voor de overdracht van dode letter.
-   [TransferMessageCount:](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount)Berichten in afwachting van overdracht naar een andere wachtrij of onderwerp.

Als een toepassing resources wil schalen op basis van de lengte van de wachtrij, moet deze worden uitgevoerd met een gemeten tempo. De aankoop van de berichtentellers is een dure bewerking binnen de berichtenmakelaar en het uitvoeren ervan heeft vaak direct en negatief effect op de prestaties van de entiteit.

> [!NOTE]
> De berichten die naar een servicebusonderwerp worden verzonden, worden doorgestuurd naar abonnementen voor dat onderwerp. Het actieve berichtdat op het onderwerp zelf wordt geteld, is dus 0, omdat deze berichten met succes zijn doorgestuurd naar het abonnement. Haal het aantal berichten op bij het abonnement en controleer of het groter is dan 0. Hoewel u berichten bij het abonnement ziet, worden ze daadwerkelijk opgeslagen in een opslag die eigendom is van het onderwerp. 

Als je kijkt naar de abonnementen, dan zouden ze niet-nul aantal berichten (die tot 323 MB ruimte voor deze hele entiteit) toevoegen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
