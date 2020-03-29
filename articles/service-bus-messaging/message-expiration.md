---
title: Azure Service Bus - bericht verlopen
description: In dit artikel wordt uitgelegd over het verstrijken en de tijd om te leven met Azure Service Bus-berichten. Na zo'n deadline wordt het bericht niet meer bezorgd.
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
ms.openlocfilehash: e86c92fa1cfb13929d5617502224f479709efdd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756331"
---
# <a name="message-expiration-time-to-live"></a>Verlopen van berichten (Time to Live)

De payload in een bericht, of een opdracht of onderzoek dat een bericht overbrengt aan een ontvanger, is bijna altijd onderworpen aan een vorm van deadline op toepassingsniveau. Na een dergelijke deadline wordt de inhoud niet meer geleverd of wordt de gevraagde bewerking niet meer uitgevoerd.

Voor ontwikkel- en testomgevingen waarin wachtrijen en onderwerpen vaak worden gebruikt in de context van gedeeltelijke uitvoeringen van toepassingen of toepassingsonderdelen, is het ook wenselijk dat gestrande testberichten automatisch worden verzameld, zodat de volgende testrun kan worden verzameld beginnen schoon te maken.

De vervaldatum voor een afzonderlijk bericht kan worden gecontroleerd door de eigenschap [TimeToLive-systeem](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) in te stellen, die een relatieve duur opgeeft. De vervaldatum wordt een absoluut moment wanneer het bericht in de entiteit is geplaatst. Op dat moment neemt de eigenschap [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) de waarde op [zich (**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). De time-to-live (TTL) instelling op een brokered bericht wordt niet afgedwongen wanneer er geen klanten actief luisteren.

Voorbij het **moment van ExpiresAtUtc** komen berichten niet meer in aanmerking voor ophalen. De vervaldatum heeft geen invloed op berichten die momenteel zijn vergrendeld voor levering; deze berichten worden nog steeds normaal behandeld. Als het slot verloopt of het bericht wordt verlaten, wordt de vervaldatum onmiddellijk van kracht.

Terwijl het bericht is vergrendeld, kan de toepassing in het bezit zijn van een bericht dat is verlopen. Of de toepassing bereid is om door te gaan met de verwerking of ervoor kiest om het bericht te verlaten is aan de uitvoerder.

## <a name="entity-level-expiration"></a>Vervaldatum op entiteitsniveau

Alle berichten die in een wachtrij of onderwerp worden verzonden, zijn onderhevig aan een standaardvervaldatum die op entiteitsniveau is ingesteld met de [eigenschap defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) en die ook tijdens het maken in de portal kan worden ingesteld en later kan worden aangepast. De standaardvervaldatum wordt gebruikt voor alle berichten die worden verzonden naar de entiteit waar [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) niet expliciet is ingesteld. De standaardvervaldatum fungeert ook als plafond voor de **TimeToLive-waarde.** Berichten met een langere **TimeToLive-vervaldatum** dan de standaardwaarde worden in stilte aangepast aan de **standaardWaardeMessageTimeToLive** voordat ze in de wachtrij staan.

> [!NOTE]
> De standaard [TimeToLive-waarde](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) voor een brokered bericht is [TimeSpan.Max,](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) indien dit niet anders is opgegeven.
>
> Voor berichtenentiteiten (wachtrijen en onderwerpen) is de standaardvervaldatum ook [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) voor servicebusstandaard en premiumlagen.  Voor de basislaag is de standaardverlooptijd 14 dagen.

Verlopen berichten kunnen optioneel worden verplaatst naar een [wachtrij met dode letters](service-bus-dead-letter-queues.md) door de eigenschap [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) in te stellen of het desbetreffende vakje in de portal in te schakelen. Als de optie is uitgeschakeld, worden verlopen berichten verwijderd. Verlopen berichten verplaatst naar de dode-letter wachtrij kan worden onderscheiden van andere dode-lettered berichten door de evaluatie van de [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) eigenschap die de makelaar opslaat in de user properties sectie; de waarde is [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) in dit geval.

In het bovengenoemde geval waarin het bericht is beschermd tegen vervaldatum terwijl het onder slot is en als de vlag is ingesteld op de entiteit, wordt het bericht verplaatst naar de wachtrij voor dode letters wanneer het slot wordt verlaten of verloopt. Het wordt echter niet verplaatst als het bericht met succes is afgehandeld, wat er vervolgens van uitgaat dat de toepassing het met succes heeft afgehandeld, ondanks de nominale vervaldatum.

De combinatie van [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) en automatische (en transactionele) dode letters bij het verstrijken zijn een waardevol hulpmiddel voor het vaststellen van vertrouwen in de vraag of een taak gegeven aan een handler of een groep handlers onder een deadline wordt opgehaald voor verwerking als de deadline is bereikt.

Denk bijvoorbeeld aan een website die op betrouwbare wijze taken moet uitvoeren op een backend met beperkte beperking op schaal en die af en toe verkeerspieken ervaart of wil worden geïsoleerd tegen beschikbaarheidsafleveringen van die backend. In het reguliere geval duwt de server-side handler voor de ingediende gebruikersgegevens de informatie in een wachtrij en ontvangt vervolgens een antwoord dat bevestigt dat de transactie succesvol is verwerkt in een wachtrij voor antwoorden. Als er een verkeerspiek is en de backendhandler zijn achterstandsitems niet op tijd kan verwerken, worden de verlopen taken geretourneerd in de wachtrij met dode letters. De interactieve gebruiker kan worden gewaarschuwd dat de gevraagde bewerking iets langer zal duren dan normaal, en de aanvraag kan vervolgens op een andere wachtrij worden geplaatst voor een verwerkingspad waar het uiteindelijke verwerkingsresultaat per e-mail naar de gebruiker wordt verzonden. 


## <a name="temporary-entities"></a>Tijdelijke entiteiten

Wachtrijen voor servicebus, onderwerpen en abonnementen kunnen worden gemaakt als tijdelijke entiteiten, die automatisch worden verwijderd wanneer ze gedurende een bepaalde periode niet zijn gebruikt.
 
Automatische opruiming is handig bij het ontwikkelen en testen van scenario's waarin entiteiten dynamisch worden gemaakt en niet worden opgeschoond na gebruik, als gevolg van enige onderbreking van de test- of foutopsporingsrun. Het is ook handig wanneer een toepassing dynamische entiteiten maakt, zoals een antwoordwachtrij, voor het ontvangen van reacties terug in een webserverproces of in een ander relatief kortstondig object waar het moeilijk is om deze entiteiten betrouwbaar op te schonen wanneer het object instantie verdwijnt.

De functie is ingeschakeld met de eigenschap [autoDeleteOnIdle.](/azure/templates/microsoft.servicebus/namespaces/queues) Deze eigenschap is ingesteld op de duur waarvoor een entiteit moet worden inactief (ongebruikt) voordat deze automatisch wordt verwijderd. De minimumwaarde voor deze eigenschap is 5.
 
De eigenschap **autoDeleteOnIdle** moet worden ingesteld via een Azure Resource Manager-bewerking of via de .NET Framework-client [NamespaceManager-API's.](/dotnet/api/microsoft.servicebus.namespacemanager) Je het niet in het portaal zetten.

## <a name="idleness"></a>Nietsdoen

Dit is wat beschouwd als nietsnutte van entiteiten (wachtrijen, onderwerpen en abonnementen):

- Wachtrijen
    - Geen verzendingen  
    - Geen ontvangt  
    - Geen updates voor de wachtrij  
    - Geen geplande berichten  
    - Geen bladeren/gluren 
- Onderwerpen  
    - Geen verzendingen  
    - Geen updates voor het onderwerp  
    - Geen geplande berichten 
- Abonnementen
    - Geen ontvangt  
    - Geen updates voor het abonnement  
    - Geen nieuwe regels toegevoegd aan het abonnement  
    - Geen bladeren/gluren  
 


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
