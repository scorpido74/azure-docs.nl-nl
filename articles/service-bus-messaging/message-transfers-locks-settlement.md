---
title: Overdrachten, vergrendelingen en vereffeningvan Azure Service Bus-berichten
description: In dit artikel vindt u een overzicht van de overdrachten, vergrendelingen en vereffeningsbewerkingen van Azure Service Bus-berichten.
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
ms.date: 01/24/2019
ms.author: aschhab
ms.openlocfilehash: a2c353d612280981a83b32463d34efdc70878495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260993"
---
# <a name="message-transfers-locks-and-settlement"></a>Berichten overdragen, vergrendelen en verwerken

De centrale mogelijkheid van een berichtenmakelaar zoals Service Bus is om berichten in een wachtrij of onderwerp te accepteren en deze beschikbaar te houden voor later ophalen. *Verzenden* is de term die vaak wordt gebruikt voor de overdracht van een bericht naar de berichtenmakelaar. *Ontvangen* is de term die vaak wordt gebruikt voor de overdracht van een bericht naar een ophalende client.

Wanneer een klant een bericht verzendt, wil hij meestal weten of het bericht correct is overgedragen naar en geaccepteerd door de makelaar of dat er een fout is opgetreden. Deze positieve of negatieve erkenning regelt de cliënt en de makelaar begrip over de overdracht staat van het bericht en wordt dus aangeduid als *vereffening*.

Wanneer de makelaar een bericht overdraagt aan een klant, willen de makelaar en de klant ook inzicht krijgen in de vraag of het bericht met succes is verwerkt en dus kan worden verwijderd, of dat de levering of verwerking van het bericht is mislukt, en bericht mogelijk opnieuw moet worden bezorgd.

## <a name="settling-send-operations"></a>Vereffening van verzendbewerkingen

Met behulp van een van de ondersteunde Service Bus API-clients worden bewerkingen altijd expliciet verzonden naar Service Bus, wat betekent dat de API-bewerking wacht op een acceptatieresultaat van Service Bus en vervolgens de verzendbewerking voltooit.

Als het bericht wordt geweigerd door Service Bus, bevat de afwijzing een foutindicator en tekst met een "tracking-id" erin. De afwijzing bevat ook informatie over de vraag of de operatie opnieuw kan worden geprobeerd met enige verwachting van succes. In de client wordt deze informatie omgezet in een uitzondering en verhoogd naar de beller van de verzendbewerking. Als het bericht is geaccepteerd, wordt de bewerking in stilte voltooid.

Bij gebruik van het AMQP-protocol, het exclusieve protocol voor de .NET Standard-client en de Java-client en [dat een optie is voor de .NET Framework-client,](service-bus-amqp-dotnet.md)worden berichtoverdrachten en -vereffeningen in de pijplijn en volledig asynchroon uitgevoerd, en wordt aanbevolen de API-varianten van het asynchrone programmeermodel te gebruiken.

Een afzender kan meerdere berichten snel achter elkaar op de draad zetten zonder te hoeven wachten tot elk bericht wordt erkend, zoals anders het geval zou zijn met het SBMP-protocol of met HTTP 1.1. Deze asynchrone verzendbewerkingen worden voltooid omdat de desbetreffende berichten worden geaccepteerd en opgeslagen, op partitieentiteiten of wanneer bewerking naar verschillende entiteiten overlapt. De voltooiingen kunnen ook plaatsvinden uit de oorspronkelijke verzendorder.

De strategie voor het afhandelen van het resultaat van verzendbewerkingen kan onmiddellijke en significante impact hebben op de prestaties van uw toepassing. De voorbeelden in deze sectie zijn geschreven in C# en zijn gelijkwaardig van toepassing op Java Futures.

Als de toepassing produceert uitbarstingen van berichten, hier geïllustreerd met een duidelijke lus, en waren om de voltooiing van elke verzendbewerking te wachten voordat het verzenden van het volgende bericht, synchrone of asynchrone API vormen gelijk, het verzenden van 10 berichten alleen voltooid na 10 sequentiële volledige rondreizen voor vereffening.

Met een veronderstelde 70 milliseconde TCP retourlatentie afstand van een on-premises site naar Service Bus en het geven van slechts 10 ms voor Service Bus te accepteren en op te slaan elk bericht, de volgende lus duurt ten minste 8 seconden, niet meegerekend payload transfer tijd of potentiële congestieeffecten van de route:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Als de toepassing de 10 asynchrone verzendbewerkingen onmiddellijk achter elkaar start en afzonderlijk wacht op de voltooiing ervan, overlapt de retourtijd voor die 10 verzendbewerkingen. De 10 berichten worden onmiddellijk achter elkaar overgedragen, mogelijk zelfs het delen van TCP-frames, en de totale overdrachtsduur hangt grotendeels af van de netwerkgerelateerde tijd die nodig is om de berichten naar de broker te laten overbrengen.

Het maken van dezelfde veronderstellingen als voor de voorafgaande lus, de totale overlappende uitvoeringstijd voor de volgende lus kan blijven ruim onder een seconde:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Het is belangrijk op te merken dat alle asynchrone programmeermodellen een of andere vorm van geheugengebaseerde, verborgen werkwachtrij gebruiken die lopende bewerkingen bevat. Wanneer [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) of **Send** (Java) retour, de verzendtaak in de wachtrij in de wachtrij staat, maar het protocolgebaar begint pas als de taak is om uit te voeren. Voor code die de neiging om te duwen uitbarstingen van berichten en waar betrouwbaarheid is een punt van zorg, zorg moet worden genomen dat niet te veel berichten worden "in flight" in een keer, omdat alle verzonden berichten nemen geheugen totdat ze feitelijk zijn gezet op de draad.

Semaforen, zoals weergegeven in het volgende codefragment in C#, zijn synchronisatieobjecten die dergelijke throttling op toepassingsniveau inschakelen wanneer dat nodig is. Dit gebruik van een semafoor zorgt voor maximaal 10 berichten te worden in de vlucht in een keer. Een van de 10 beschikbare semafoor sloten wordt genomen voor het verzenden en het wordt vrijgegeven als het verzenden voltooit. De 11e pass door de lus wacht tot ten minste een van de voorafgaande stuurt is voltooid, en maakt vervolgens het slot beschikbaar:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Toepassingen mogen **nooit** een asynchrone verzendbewerking op een "fire and forget"-manier starten zonder het resultaat van de bewerking op te halen. Hierdoor kan de interne en onzichtbare taakwachtrij tot geheugenuitputting worden geladen en kan worden voorkomen dat de toepassing verzendfouten detecteert:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Met een low-level AMQP-client accepteert Service Bus ook "vooraf geregelde" overdrachten. Een vooraf afgewikkelde overdracht is een fire-and-forget-bewerking waarvoor de uitkomst hoe dan ook niet wordt gerapporteerd aan de klant en het bericht wordt beschouwd als afgehandeld wanneer het wordt verzonden. Het gebrek aan feedback aan de client betekent ook dat er geen bruikbare gegevens beschikbaar zijn voor diagnostiek, wat betekent dat deze modus niet in aanmerking komt voor hulp via Azure-ondersteuning.

## <a name="settling-receive-operations"></a>Afhandeling ontvangen operaties

Voor ontvangstbewerkingen schakelen de API-clients van Service Bus twee verschillende expliciete modi in: *Receive-and-Delete* en *Peek-Lock*.

### <a name="receiveanddelete"></a>Ontvangen anddelete

De [modus Ontvangen en verwijderen](/dotnet/api/microsoft.servicebus.messaging.receivemode) vertelt de broker om alle berichten die hij naar de ontvangende client verzendt, als afgehandeld te beschouwen wanneer deze wordt verzonden. Dat betekent dat het bericht wordt beschouwd als verbruikt zodra de makelaar heeft het op de draad. Als de overdracht van berichten mislukt, gaat het bericht verloren.

Het voordeel van deze modus is dat de ontvanger geen verdere actie hoeft te ondernemen op het bericht en ook niet wordt vertraagd door te wachten op de uitkomst van de schikking. Als de gegevens in de afzonderlijke berichten een lage waarde hebben en/of slechts voor een zeer korte tijd zinvol zijn, is deze modus een redelijke keuze.

### <a name="peeklock"></a>PeekLock (PeekLock)

De [Peek-Lock-modus](/dotnet/api/microsoft.servicebus.messaging.receivemode) vertelt de makelaar dat de ontvangende klant ontvangen berichten expliciet wil vereffenen. Het bericht wordt beschikbaar gesteld voor de ontvanger te verwerken, terwijl gehouden onder een exclusief slot in de dienst, zodat andere, concurrerende ontvangers niet kunnen zien. De duur van het slot wordt in eerste instantie gedefinieerd op wachtrij- of abonnementsniveau en kan worden verlengd door de client die eigenaar is van het slot, via de [RenewLock-bewerking.](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Wanneer een bericht is vergrendeld, kunnen andere clients die uit dezelfde wachtrij of een abonnement worden ontvangen, vergrendelingen overnemen en de volgende beschikbare berichten ophalen die niet onder actieve vergrendeling staan. Wanneer het slot op een bericht expliciet wordt vrijgegeven of wanneer het slot verloopt, verschijnt het bericht weer op of in de buurt van de voorkant van de ophaalorder voor herlevering.

Wanneer het bericht herhaaldelijk wordt vrijgegeven door ontvangers of wanneer het slot voor een bepaald aantal keren vervalt[(maxDeliveryCount),](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)wordt het bericht automatisch uit de wachtrij of het abonnement verwijderd en in de bijbehorende dode-letterwachtrij geplaatst.

De ontvangende client initieert de afwikkeling van een ontvangen bericht met een positieve bevestiging wanneer het [voltooid](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) op API-niveau aanroept. Dit geeft aan de broker aan dat het bericht is verwerkt en dat het bericht uit de wachtrij of het abonnement wordt verwijderd. De makelaar reageert op de schikkingsintentie van de ontvanger met een antwoord dat aangeeft of de schikking kan worden uitgevoerd.

Wanneer de ontvangende client een bericht niet verwerkt, maar wil dat het bericht opnieuw wordt afgeleverd, kan het expliciet vragen om het bericht direct vrij te geven en te ontgrendelen door [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) te bellen of het kan niets doen en het slot laten verlopen.

Als een ontvangende client een bericht niet verwerkt en weet dat het opnieuw verzenden van het bericht en het opnieuw proberen van de bewerking niet zal helpen, kan het bericht weigeren, waardoor het in de wachtrij met dode letters wordt geplaatst door [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)te bellen , waardoor ook een aangepaste eigenschap kan worden ingesteld, inclusief een redencode die kan worden opgehaald met het bericht uit de wachtrij met dode letters.

Een speciaal geval van vestiging is uitstel, dat in een afzonderlijk artikel wordt besproken.

De **volledige** of **deadletter-bewerkingen** en de **RenewLock-bewerkingen** kunnen mislukken als gevolg van netwerkproblemen, als de held lock is verlopen of er andere service-side voorwaarden zijn die vereffening voorkomen. In een van de laatste gevallen stuurt de service een negatieve bevestiging dat de api-clients als uitzondering worden geplaatst. Als de reden een verbroken netwerkverbinding is, wordt de vergrendeling verwijderd omdat Service Bus geen ondersteuning biedt voor het herstel van bestaande AMQP-koppelingen op een andere verbinding.

Als **Voltooid** mislukt, wat meestal gebeurt aan het einde van de afhandeling van berichten en in sommige gevallen na minuten van verwerking, kan de ontvangende toepassing beslissen of het de status van het werk behoudt en hetzelfde bericht negeert wanneer het een tweede keer wordt bezorgd, of dat het het werkresultaat en de nieuwe pogingen wordt weggestoof als het bericht opnieuw wordt afgeleverd.

Het typische mechanisme voor het identificeren van dubbele berichtleveringen is door de message-id te controleren, die door de afzender kan en moet worden ingesteld op een unieke waarde, mogelijk uitgelijnd met een id van het oorspronkelijke proces. Een taakplanner zou de bericht-id waarschijnlijk instellen op de id van de taak die hij met de opgegeven werknemer aan een werknemer probeert toe te wijzen, en de werknemer negeert het tweede optreden van de taaktoewijzing als die taak al is uitgevoerd.

> [!IMPORTANT]
> Het is belangrijk op te merken dat het slot dat PeekLock verwerft op het bericht vluchtig is en verloren kan gaan in de volgende omstandigheden
>   * Service-update
>   * OS-update
>   * Eigenschappen op de entiteit wijzigen (wachtrij, onderwerp, abonnement) terwijl u het slot vasthoudt.
>
> Wanneer het slot verloren gaat, genereert Azure Service Bus een LockLostException die wordt weergegeven op de code van de clienttoepassing. In dit geval moet de standaardlogica voor het opnieuw proberen van de client automatisch worden gestart en de bewerking opnieuw proberen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over berichten over Service Bus:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
