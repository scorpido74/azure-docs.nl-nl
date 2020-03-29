---
title: Azure Service Bus-berichtenentiteiten automatisch doorsturen
description: In dit artikel wordt beschreven hoe u een Azure Service Bus-wachtrij of abonnement aan een andere wachtrij of onderwerp ketenen.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761046"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Servicebusentiteiten vastketenen met autoforwarding

Met de *functie Autoforwarding servicebus* u een wachtrij of abonnement vastketenen aan een andere wachtrij of een ander onderwerp dat deel uitmaakt van dezelfde naamruimte. Wanneer automatisch doorsturen is ingeschakeld, verwijdert Service Bus automatisch berichten die in de eerste wachtrij of het eerste abonnement zijn geplaatst (bron) en plaatst deze in de tweede wachtrij of onderwerp (bestemming). Het is nog steeds mogelijk om een bericht rechtstreeks naar de entiteit van de bestemming te sturen.

## <a name="using-autoforwarding"></a>Autoforwarding gebruiken

U automatisch doorsturen inschakelen door de eigenschappen [QueueDescription.ForwardTo][QueueDescription.ForwardTo] of [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] in te stellen op de objecten [QueueDescription][QueueDescription] of [SubscriptionDescription][SubscriptionDescription] voor de bron, zoals in het volgende voorbeeld:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

De entiteit van de bestemming moet bestaan op het moment dat de bronentiteit wordt gemaakt. Als de entiteit van de bestemming niet bestaat, retourneert Service Bus een uitzondering wanneer wordt gevraagd de bronentiteit te maken.

U automatisch doorsturen gebruiken om een individueel onderwerp uit te schalen. Service Bus beperkt het [aantal abonnementen op een bepaald onderwerp](service-bus-quotas.md) tot 2.000. U aanvullende abonnementen aanbieden door onderwerpen op het tweede niveau te maken. Zelfs als u niet gebonden bent aan de beperking van de ServiceBus op het aantal abonnementen, kan het toevoegen van een tweede niveau van onderwerpen de algehele doorvoer van uw onderwerp verbeteren.

![Scenario automatisch doorsturen][0]

U ook automatisch doorsturen gebruiken om afzenders van berichten van ontvangers te ontkoppelen. Denk bijvoorbeeld aan een ERP-systeem dat bestaat uit drie modules: orderverwerking, voorraadbeheer en klantrelatiebeheer. Elk van deze modules genereert berichten die zijn in de wachtrij in een overeenkomstig onderwerp. Alice en Bob zijn vertegenwoordigers die geïnteresseerd zijn in alle berichten die betrekking hebben op hun klanten. Om deze berichten te ontvangen, maken Alice en Bob elk een persoonlijke wachtrij en een abonnement op elk van de ERP-onderwerpen die automatisch alle berichten doorsturen naar hun wachtrij.

![Scenario automatisch doorsturen][1]

Als Alice op vakantie gaat, vult haar persoonlijke wachtrij, in plaats van het ERP-onderwerp, zich. In dit scenario bereikt geen van de ERP-onderwerpen ooit een quotum omdat een vertegenwoordiger geen berichten heeft ontvangen.

> [!NOTE]
> Wanneer automatisch doorsturen is ingesteld, wordt de waarde voor AutoDeleteOnIdle op **zowel de bron als de bestemming** automatisch ingesteld op de maximale waarde van het gegevenstype.
> 
>   - Aan de bronzijde fungeert autoforwarding als een ontvangstbewerking. Dus de bron die autoforwarding setup heeft is nooit echt "idle".
>   - Aan de bestemmingszijde wordt dit gedaan om ervoor te zorgen dat er altijd een bestemming is om het bericht naar door te sturen.

## <a name="autoforwarding-considerations"></a>Overwegingen voor autoforwarding

Als de entiteit van de bestemming te veel berichten verzamelt en het quotum overschrijdt of als de entiteit van de bestemming is uitgeschakeld, voegt de bronentiteit de berichten toe aan de wachtrij met [dode letters](service-bus-dead-letter-queues.md) totdat er ruimte in de bestemming is (of de entiteit opnieuw is ingeschakeld). Deze berichten blijven in de wachtrij voor dode letters staan, dus u moet ze expliciet ontvangen en verwerken vanuit de wachtrij voor dode letters.

Bij het aan elkaar ketenen van afzonderlijke onderwerpen om een samengesteld onderwerp met veel abonnementen te verkrijgen, is het raadzaam dat u een gematigd aantal abonnementen hebt op het onderwerp op het eerste niveau en veel abonnementen op de onderwerpen op het tweede niveau. Bijvoorbeeld, een eerste niveau onderwerp met 20 abonnementen, elk van hen geketend aan een tweede niveau onderwerp met 200 abonnementen, zorgt voor een hogere doorvoer dan een eerste niveau onderwerp met 200 abonnementen, elk gekoppeld aan een tweede niveau onderwerp met 20 abonnementen.

ServiceBus factureert één bewerking voor elk doorgestuurd bericht. Het verzenden van een bericht naar een onderwerp met 20 abonnementen, elk geconfigureerd om berichten automatisch door te sturen naar een andere wachtrij of onderwerp, wordt bijvoorbeeld gefactureerd als 21 bewerkingen als alle abonnementen op het eerste niveau een kopie van het bericht ontvangen.

Als u een abonnement wilt maken dat is verbonden aan een andere wachtrij of onderwerp, moet de maker van het abonnement **machtigingen beheren** hebben voor zowel de bron als de bestemmingsentiteit. Voor het verzenden van berichten naar het brononderwerp zijn alleen **machtigingen voor** verzenden vereist.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende referentieonderwerpen voor gedetailleerde informatie over autoforwarding:

* [Doorsturen][QueueDescription.ForwardTo]
* [Beschrijving van wachtrijen][QueueDescription]
* [AbonnementBeschrijving][SubscriptionDescription]

Zie voor meer informatie over prestatieverbeteringen van Service Bus 

* [Best Practices voor prestatieverbeteringen met Service Bus Messaging](service-bus-performance-improvements.md)
* [Verdeelde berichtenentiteiten][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
