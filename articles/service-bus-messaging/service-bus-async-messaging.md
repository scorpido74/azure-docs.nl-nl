---
title: Service Bus asynchrone berichten | Microsoft Documenten
description: Ontdek hoe Azure Service Bus asynchronisme ondersteunt via een winkel- en doorstuurmechanisme met wachtrijen, onderwerpen en abonnementen.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761029"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchrone berichtenpatronen en hoge beschikbaarheid

Asynchrone berichten kunnen op verschillende manieren worden geïmplementeerd. Met wachtrijen, onderwerpen en abonnementen ondersteunt Azure Service Bus asynchronisme via een winkel- en doorloopmechanisme. Bij normale (synchrone) bewerking verzendt u berichten naar wachtrijen en onderwerpen en ontvangt u berichten uit wachtrijen en abonnementen. Toepassingen die u schrijft, zijn afhankelijk van het feit dat deze entiteiten altijd beschikbaar zijn. Wanneer de status van de entiteit verandert, als gevolg van verschillende omstandigheden, hebt u een manier nodig om een entiteit met beperkte capaciteit te bieden die aan de meeste behoeften kan voldoen.

Toepassingen gebruiken meestal asynchrone berichtenpatronen om een aantal communicatiescenario's in te schakelen. U toepassingen bouwen waarin clients berichten naar services kunnen verzenden, zelfs als de service niet wordt uitgevoerd. Voor toepassingen die communicatieuitbarstingen ervaren, kan een wachtrij helpen de belasting te nivelleren door een plaats te bieden voor het bufferen van communicatie. Ten slotte u een eenvoudige maar effectieve load balancer krijgen om berichten over meerdere machines te distribueren.

Om de beschikbaarheid van een van deze entiteiten te behouden, moet u rekening houden met een aantal verschillende manieren waarop deze entiteiten niet beschikbaar kunnen lijken voor een duurzaam berichtensysteem. Over het algemeen zien we dat de entiteit niet meer beschikbaar is voor toepassingen die we op de volgende verschillende manieren schrijven:

* Kan geen berichten verzenden.
* Kan geen berichten ontvangen.
* Kan entiteiten niet beheren (entiteiten maken, ophalen, bijwerken of verwijderen).
* Kan geen contact opnemen met de service.

Voor elk van deze fouten bestaan verschillende foutmodi waarmee een toepassing op een bepaald niveau van beperkte capaciteit kan blijven werken. Een systeem dat bijvoorbeeld berichten kan verzenden maar deze niet kan ontvangen, kan nog steeds bestellingen van klanten ontvangen, maar kan deze bestellingen niet verwerken. In dit onderwerp worden mogelijke problemen besproken die zich kunnen voordoen en hoe deze problemen worden beperkt. Service Bus heeft een aantal oplossingen geïntroduceerd waar u op moet ingaan, en dit onderwerp bespreekt ook de regels voor het gebruik van deze opt-in-mitigaties.

## <a name="reliability-in-service-bus"></a>Betrouwbaarheid in servicebus
Er zijn verschillende manieren om problemen met berichten en entiteiten af te handelen en er zijn richtlijnen voor het juiste gebruik van deze oplossingen. Als u de richtlijnen wilt begrijpen, moet u eerst begrijpen wat er kan mislukken in Service Bus. Als gevolg van het ontwerp van Azure-systemen, al deze problemen hebben de neiging om van korte duur. Op een hoog niveau verschijnen de verschillende oorzaken van onbeschikbaarheid als volgt:

* Beperking van een extern systeem waarvan Service Bus afhankelijk is. Beperking vindt plaats vanuit interacties met opslag- en rekenbronnen.
* Probleem voor een systeem waarvan servicebus afhankelijk is. Een bepaald deel van de opslag kan bijvoorbeeld problemen ondervinden.
* Storing van servicebus op één subsysteem. In deze situatie kan een compute node in een inconsistente status komen en moet het zichzelf opnieuw opstarten, waardoor alle entiteiten die het dient om het saldo naar andere knooppunten te laden, worden geladen. Dit kan op zijn beurt leiden tot een korte periode van trage verwerking van berichten.
* Fout van servicebus in een Azure-datacenter. Dit is een "catastrofale storing" waarbij het systeem vele minuten of enkele uren onbereikbaar is.

> [!NOTE]
> De term **opslag** kan zowel Azure Storage als SQL Azure betekenen.
> 
> 

Service Bus bevat een aantal oplossingen voor deze problemen. In de volgende secties wordt elk probleem en hun respectieve oplossingen besproken.

### <a name="throttling"></a>Beperking
Met Service Bus maakt throttling coöperatieve message rate management mogelijk. Elke individuele Service Bus knooppunt herbergt vele entiteiten. Elk van deze entiteiten stelt eisen aan het systeem in termen van CPU, geheugen, opslag en andere facetten. Wanneer een van deze facetten gebruik detecteert dat bepaalde drempelwaarden overschrijdt, kan Service Bus een bepaalde aanvraag weigeren. De beller ontvangt een [ServerBusyException][ServerBusyException] en probeert na 10 seconden opnieuw.

Als beperking moet de code de fout lezen en eventuele pogingen van het bericht gedurende ten minste 10 seconden stoppen. Aangezien de fout kan optreden in stukken van de klanttoepassing, wordt verwacht dat elk stuk onafhankelijk de logica voor het opnieuw proberen uitvoert. De code kan de kans op een verkorting verminderen door partitionering in te schakelen in een wachtrij of onderwerp.

### <a name="issue-for-an-azure-dependency"></a>Probleem voor een Azure-afhankelijkheid
Andere onderdelen binnen Azure kunnen af en toe serviceproblemen hebben. Wanneer een systeem dat servicebus gebruikt bijvoorbeeld wordt bijgewerkt, kan dat systeem tijdelijk minder mogelijkheden ervaren. Om dit soort problemen op te lossen, onderzoekt en implementeert Service Bus regelmatig oplossingen. Bijwerkingen van deze mitigaties verschijnen. Service Bus implementeert bijvoorbeeld een systeem waarmee berichtbewerkingen consistent kunnen werken om tijdelijke problemen met opslag op te lossen. Vanwege de aard van de beperking kan het tot 15 minuten duren voordat een verzonden bericht in de betreffende wachtrij of het betreffende abonnement wordt weergegeven en klaar is voor een ontvangstbewerking. Over het algemeen zullen de meeste entiteiten dit probleem niet ervaren. Gezien het aantal entiteiten in Service Bus binnen Azure is deze beperking echter soms nodig voor een kleine subset van Service Bus-klanten.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Servicebusstoring op één subsysteem
Bij elke toepassing kunnen omstandigheden ertoe leiden dat een intern onderdeel van Service Bus inconsistent wordt. Wanneer Service Bus dit detecteert, verzamelt het gegevens van de toepassing om te helpen bij het diagnosticeren van wat er is gebeurd. Zodra de gegevens zijn verzameld, wordt de toepassing opnieuw gestart in een poging om deze naar een consistente status terug te brengen. Dit proces gebeurt vrij snel en resulteert in een entiteit die tot een paar minuten niet beschikbaar lijkt te zijn, hoewel de typische downtijden veel korter zijn.

In deze gevallen genereert de clienttoepassing een [uitzondering voor System.TimeoutException][System.TimeoutException] of [MessagingException.][MessagingException] Service Bus bevat een beperking voor dit probleem in de vorm van geautomatiseerde client opnieuw proberen logica. Zodra de periode voor het opnieuw proberen is uitgeput en het bericht niet wordt bezorgd, u verkennen met behulp van andere genoemd in het artikel over de [behandeling van storingen en rampen.][handling outages and disasters]

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van asynchrone berichten in Service Bus hebt geleerd, leest u meer details over [het afhandelen van storingen en rampen.][handling outages and disasters]

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
