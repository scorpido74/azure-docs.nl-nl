---
title: Azure Service Bus-verlopen van berichten
description: In dit artikel wordt uitgelegd over de verval tijd en de duur van Azure Service Bus berichten. Na een dergelijke deadline wordt het bericht niet meer bezorgd.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ca789be91e835576ec06a422bdbbbf25eb775dac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341204"
---
# <a name="message-expiration-time-to-live"></a>Verlopen van berichten (Time to Live)

De payload in een bericht of een opdracht of query die een bericht overbrengt naar een ontvanger, is bijna altijd onderworpen aan een bepaalde vorm van verval deadline op toepassings niveau. Na deze deadline wordt de inhoud niet meer bezorgd of wordt de aangevraagde bewerking niet meer uitgevoerd.

Voor ontwikkel-en test omgevingen waarin wacht rijen en onderwerpen vaak worden gebruikt in de context van gedeeltelijke uitvoeringen van toepassingen of toepassings onderdelen, is het ook wenselijk dat geringe test berichten automatisch worden opgeruimd, zodat de volgende test uitvoering schoon kan worden gestart.

De verval datum van elk afzonderlijk bericht kan worden bepaald door de systeem eigenschap [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) in te stellen, waarmee een relatieve duur wordt opgegeven. De verval datum wordt een absolute directe melding wanneer het bericht in de entiteit wordt geplaatst. Op dat moment gebruikt de eigenschap [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) de waarde [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc)  +  [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). De TTL-instelling (time-to-Live) voor een brokered bericht wordt niet afgedwongen wanneer er geen clients actief Luis teren.

Na de **ExpiresAtUtc** -chat worden berichten niet meer in aanmerking komen voor ophalen. De verval datum heeft geen invloed op berichten die momenteel zijn vergrendeld voor levering; deze berichten worden nog steeds normaal verwerkt. Als de vergren deling is verlopen of het bericht is afgebroken, wordt de verval datum direct van kracht.

Wanneer het bericht is vergrendeld, is de toepassing mogelijk in bezit van een bericht dat is verlopen. Of de toepassing kan door gaan met het verwerken of ervoor kiest om het bericht af te breken, is de uitvoerder.

## <a name="entity-level-expiration"></a>Verval datum entiteit-niveau

Alle berichten die worden verzonden naar een wachtrij of onderwerp, zijn onderhevig aan een standaard verval datum die is ingesteld op het entiteits niveau met de eigenschap [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) en die later kan worden ingesteld in de portal tijdens het maken en aangepast. De standaard vervaldatum wordt gebruikt voor alle berichten die worden verzonden naar de entiteit waarbij [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) niet expliciet is ingesteld. De standaard vervaldatum fungeert ook als een plafond voor de waarde **TimeToLive** . Berichten die een langere **TimeToLive** -verloop hebben dan de standaard waarde, worden op de achtergrond aangepast aan de **defaultMessageTimeToLive** -waarde voordat deze in de wachtrij wordt gezet.

> [!NOTE]
> De standaard waarde voor [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) voor een brokered-bericht is [time span. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) als niet anderszins is opgegeven.
>
> Voor Messa ging-entiteiten (wacht rijen en onderwerpen) is de standaard verloop tijd ook [time span. Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) voor Service Bus standaard-en Premium-lagen.  Voor de laag basis is de standaard verval tijd 14 dagen.

Verlopen berichten kunnen eventueel worden verplaatst naar een [wachtrij met onbestelbare](service-bus-dead-letter-queues.md) meldingen door de eigenschap [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) in te stellen of het respectieve vak in de portal te controleren. Als de optie is uitgeschakeld, worden verlopen berichten verwijderd. Verlopen berichten die zijn verplaatst naar de wachtrij met onbestelbare meldingen kunnen worden onderscheiden van andere onbestelbare berichten door de [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) -eigenschap te evalueren die de Broker opslaat in de sectie gebruikers eigenschappen. in dit geval is de waarde [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) .

In het voor beeld waarin het bericht is beveiligd tegen verloop tijd en als de vlag is ingesteld voor de entiteit, wordt het bericht verplaatst naar de wachtrij voor onbestelbare berichten, omdat de vergren deling wordt afgebroken of verloopt. Het wordt echter niet verplaatst als het bericht is afgewikkeld, waarna wordt aangenomen dat de toepassing het heeft verwerkt, ondanks de nominale verval datum.

De combi natie van [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) en automatische (en transactionele) onbestelbare berichten op de verval datum is een waardevol hulp middel voor het tot stand brengen van een vertrouwens functie, of een taak die wordt gegeven aan een handler of een groep handlers onder een deadline wordt opgehaald om te worden verwerkt, omdat de deadline is bereikt.

Denk bijvoorbeeld aan een website die op betrouw bare wijze taken moet uitvoeren op een op een schaal beperkte back-end en die af en toe kan leiden tot pieken in de beschik baarheid van de back-end. In het normale geval stuurt de handler aan de server zijde voor de verzonden gebruikers gegevens de gegevens naar een wachtrij en ontvangt vervolgens een antwoord dat de verwerking van de trans actie in een antwoord wachtrij heeft bevestigd. Als er sprake is van een piek van het verkeer en de back-end-handler de items in de tijd niet kan verwerken, worden de verlopen taken in de wachtrij voor onbestelbare berichten geretourneerd. De interactieve gebruiker kan worden gewaarschuwd dat de aangevraagde bewerking wat langer duurt dan normaal. de aanvraag kan vervolgens worden geplaatst in een andere wachtrij voor een verwerkings traject waarbij het uiteindelijke verwerkings resultaat per e-mail naar de gebruiker wordt verzonden. 


## <a name="temporary-entities"></a>Tijdelijke entiteiten

Service Bus-wacht rijen,-onderwerpen en-abonnementen kunnen worden gemaakt als tijdelijke entiteiten die automatisch worden verwijderd wanneer ze gedurende een opgegeven periode niet zijn gebruikt.
 
Automatisch opschonen is handig in ontwikkelings-en test scenario's waarin entiteiten dynamisch worden gemaakt en niet na gebruik worden opgeschoond, vanwege een onderbreking van de test of het uitvoeren van de fout opsporing. Het is ook handig wanneer een toepassing dynamische entiteiten maakt, zoals een antwoord wachtrij, voor het ontvangen van antwoorden op een webserver proces, of in een ander relatief versteld object, waar het lastig is om die entiteiten op betrouw bare wijze op te schonen wanneer het object exemplaar verdwijnt.

De functie is ingeschakeld met behulp van de eigenschap [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) . Deze eigenschap wordt ingesteld op de duur waarvoor een entiteit inactief moet zijn voordat deze automatisch wordt verwijderd. De minimum waarde voor deze eigenschap is 5.
 
De eigenschap **autoDeleteOnIdle** moet worden ingesteld via een Azure Resource Manager bewerking of via de [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) -api's van de .NET Framework-client. U kunt deze niet instellen in de portal.

## <a name="idleness"></a>Inactiviteit

Hier ziet u wat er als niet-actief van entiteiten (wacht rijen, onderwerpen en abonnementen) geldt:

- Wachtrijen
    - Geen verzen dingen  
    - Geen ontvangen  
    - Geen updates voor de wachtrij  
    - Geen geplande berichten  
    - Geen bladeren/bekijken 
- Onderwerpen  
    - Geen verzen dingen  
    - Het onderwerp is niet bijgewerkt  
    - Geen geplande berichten 
- Abonnementen
    - Geen ontvangen  
    - Het abonnement is niet bijgewerkt  
    - Er zijn geen nieuwe regels toegevoegd aan het abonnement  
    - Geen bladeren/bekijken  
 


## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
