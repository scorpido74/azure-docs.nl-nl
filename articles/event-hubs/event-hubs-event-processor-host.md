---
title: Gebeurtenissen ontvangen met Event Processor Host - Azure Event Hubs | Microsoft Documenten
description: In dit artikel wordt de Host eventprocessor in Azure Event Hubs beschreven, wat het beheer van checkpointing-, leasing- en leesgebeurtenissen ion parallel vereenvoudigt.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372233"
---
# <a name="event-processor-host"></a>Gebeurtenisprocessorhost
> [!NOTE]
> Dit artikel is van toepassing op de oude versie van Azure Event Hubs SDK. Zie deze migratiehulplijnen voor meer informatie over het migreren van uw code naar de nieuwere versie van de SDK. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java-script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Zie [Ook De verdelingsbelasting van saldo in meerdere exemplaren van uw toepassing.](event-processor-balance-partition-load.md)

Azure Event Hubs is een krachtige telemetrie-opnameservice die kan worden gebruikt om miljoenen gebeurtenissen tegen lage kosten te streamen. In dit artikel wordt beschreven hoe u ingenomen gebeurtenissen consumeren met behulp van de *Event Processor Host* (EPH); een intelligente consumentenagent die het beheer van checkpointing, leasing en parallelle evenementlezers vereenvoudigt.  

De sleutel tot schaal voor Event Hubs is het idee van verdeelde consumenten. In tegenstelling tot het patroon van de [concurrerende consumenten](https://msdn.microsoft.com/library/dn568101.aspx) maakt het verdeelde consumentenpatroon een grote schaal mogelijk door het geschilknelpunt weg te nemen en het parallellisme van de end-to-end te vergemakkelijken.

## <a name="home-security-scenario"></a>Home security scenario

Als voorbeeld scenario, overweeg dan een home security bedrijf dat 100.000 woningen controleert. Elke minuut, het krijgt gegevens van verschillende sensoren, zoals een bewegingsdetector, deur / raam open sensor, glas breken detector, enz., geïnstalleerd in elk huis. Het bedrijf biedt een website voor bewoners om de activiteit van hun huis te controleren in de buurt van real-time.

Elke sensor duwt gegevens naar een gebeurtenishub. De gebeurtenishub is geconfigureerd met 16 partities. Aan het verbruikende uiteinde hebt u een mechanisme nodig dat deze gebeurtenissen kan lezen, consolideren (filter, aggregaat, enz.) en het aggregaat dumpen naar een opslagblob, die vervolgens wordt geprojecteerd op een gebruiksvriendelijke webpagina.

## <a name="write-the-consumer-application"></a>Schrijf de consumententoepassing

Bij het ontwerpen van de consument in een gedistribueerde omgeving moet het scenario aan de volgende vereisten voldoen:

1. **Schaal:** Maak meerdere consumenten, waarbij elke consument eigenaar wordt van het lezen van een paar Event Hubs-partities.
2. **Load balans:** Verhoog of reduceer de consument dynamisch. Wanneer bijvoorbeeld een nieuw sensortype (bijvoorbeeld een koolmonoxidemelder) aan elk huis wordt toegevoegd, neemt het aantal gebeurtenissen toe. In dat geval verhoogt de operator (een mens) het aantal consumenteninstanties. Vervolgens kan de pool van consumenten het aantal partities dat ze bezitten opnieuw in evenwicht brengen, om de belasting te delen met de nieuw toegevoegde consumenten.
3. **Naadloos hervatten bij storingen:** Als een consument **(consument A)** faalt (bijvoorbeeld de virtuele machine die de consument host plotseling crasht), dan moeten andere consumenten in staat zijn om de partities van **consument A** op te halen en door te gaan. Ook moet het vervolgpunt, een *checkpoint* of *offset*genoemd, precies op het punt zijn waarop **consument A** faalde, of iets daarvoor.
4. **Gebeurtenissen consumeren:** Terwijl de vorige drie punten betrekking hebben op het beheer van de consument, moet er code zijn om de gebeurtenissen te consumeren en er iets nuttigs mee te doen; Aggreisseer het bijvoorbeeld en upload het naar blobopslag.

In plaats van hiervoor een eigen oplossing te bouwen, biedt Event Hubs deze functionaliteit via de [IEventProcessor-interface](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) en de [klasse EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)

## <a name="ieventprocessor-interface"></a>IEventProcessor-interface

Ten eerste implementeren verbruikende toepassingen de [IEventProcessor-interface,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) die vier methoden heeft: [OpenAsync, CloseAsync, ProcessErrorAsync en ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Deze interface bevat de werkelijke code om de gebeurtenissen te gebruiken die gebeurtenishubs verzenden. De volgende code toont een eenvoudige implementatie:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Vervolgens u een [EventProcessorHost-exemplaar](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instantiëren. Afhankelijk van de overbelasting worden bij het maken van de [instantie EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) in de constructor de volgende parameters gebruikt:

- **hostName:** de naam van elk consumentenexemplaar. Elk exemplaar van **EventProcessorHost** moet een unieke waarde hebben voor deze variabele binnen een consumentengroep, dus codeer deze waarde niet hard.
- **eventHubPath:** De naam van de gebeurtenishub.
- **consumerGroupName:** Event Hubs gebruikt **$Default** als de naam van de standaardconsumentengroep, maar het is een goede gewoonte om een consumentengroep te maken voor uw specifieke aspect van verwerking.
- **eventHubConnectionString:** De verbindingstekenreeks met de gebeurtenishub, die kan worden opgehaald uit de Azure-portal. Deze verbindingstekenreeks moet **machtigingen voor luisteren** op de gebeurtenishub hebben.
- **storageConnectionString:** Het opslagaccount dat wordt gebruikt voor intern beheer van resources.

Ten slotte registreren consumenten de [EventProcessorHost-instantie](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) bij de Event Hubs-service. Als u een gebeurtenisprocessorklasse registreert met een exemplaar van EventProcessorHost, wordt de verwerking van gebeurtenissen gestart. Registreren instrueert de Event Hubs-service om te verwachten dat de consumentenapp gebeurtenissen van sommige partities verbruikt en de [implementatiecode van IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) aan te roepen wanneer gebeurtenissen worden verbruikt om te consumeren. 


### <a name="example"></a>Voorbeeld

Stel je bijvoorbeeld voor dat er 5 virtuele machines (VM's) zijn die zijn gewijd aan het consumeren van gebeurtenissen en een eenvoudige consoletoepassing in elke VM, die het werkelijke verbruik doet. Elke consoletoepassing maakt vervolgens één [EventProcessorHost-exemplaar](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) en registreert deze met de Event Hubs-service.

Stel dat in dit voorbeeldscenario 16 partities worden toegewezen aan de 5 **EventProcessorHost-exemplaren.** Sommige **EventProcessorHost-exemplaren** bezitten mogelijk een paar partities meer dan andere. Voor elke partitie waarvan een **instantie EventProcessorHost** eigenaar `SimpleEventProcessor` is, wordt een instantie van de klasse gemaakt. Daarom zijn er 16 `SimpleEventProcessor` exemplaren van de totale, met een toegewezen aan elke partitie.

In de volgende lijst wordt dit voorbeeld samengevat:

- 16 Gebeurtenishubspartities.
- 5 VM's, 1 consumentenapp (bijvoorbeeld Consumer.exe) in elke VM.
- 5 EPH-exemplaren geregistreerd, 1 in elke VM door Consumer.exe.
- 16 `SimpleEventProcessor` objecten gemaakt door de 5 EPH-exemplaren.
- 1 Consumer.exe-app `SimpleEventProcessor` kan 4 objecten bevatten, aangezien de 1 EPH-instantie 4 partities kan bezitten.

## <a name="partition-ownership-tracking"></a>Het bijhouden van partitieeigendom

Het eigendom van een partitie aan een EPH-instantie (of een consument) wordt bijgehouden via het Azure Storage-account dat wordt bijgehouden. U de tracking als een eenvoudige tabel visualiseren, als volgt. U de werkelijke implementatie bekijken door de blobs te onderzoeken onder het opgegeven opslagaccount:

| **Naam van consumentengroep** | **Partitie-id** | **Hostnaam (eigenaar)** | **Lease (of eigendom) verworven tijd** | **Verschuiving in partitie (controlepunt)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Consument\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Consument\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Consument\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Consument\_VM3 | 2018-04-15T01:22:56 | 976 |

Hier verwerft elke host het eigendom van een partitie voor een bepaalde duur (de leaseduur). Als een host mislukt (VM wordt afgesloten), vervalt de lease. Andere hosts proberen eigenaar te worden van de partitie en een van de hosts slaagt. Dit proces reset de lease op de partitie met een nieuwe eigenaar. Op deze manier kan slechts één lezer tegelijk lezen van een bepaalde partitie binnen een consumentengroep.

## <a name="receive-messages"></a>Berichten ontvangen

Elke oproep aan [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) levert een verzameling evenementen. Het is uw verantwoordelijkheid om deze gebeurtenissen te behandelen. Als u ervoor wilt zorgen dat de processorhost elk bericht minstens één keer verwerkt, moet u uw eigen code blijven opnieuw proberen. Maar wees voorzichtig met vergiftigde berichten.

Het wordt aanbevolen dat je dingen relatief snel doet; dat wil zeggen, doe zo weinig mogelijk verwerking. Gebruik in plaats daarvan consumentengroepen. Als u naar opslag moet schrijven en een aantal routering en routeringswerk moet doen, is het beter om twee consumentengroepen te gebruiken en twee [IEventProcessor-implementaties](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) te hebben die afzonderlijk worden uitgevoerd.

Op een bepaald moment tijdens de verwerking wilt u misschien bijhouden wat u hebt gelezen en voltooid. Het bijhouden is essentieel als u het lezen opnieuw moet starten, zodat u niet terugkeert naar het begin van de stream. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) vereenvoudigt deze tracking met behulp van *checkpoints.* Een controlepunt is een locatie, of compensatie, voor een bepaalde partitie, binnen een bepaalde consumentengroep, op welk punt u ervan overtuigd bent dat u de berichten hebt verwerkt. Het markeren van een controlepunt in **EventProcessorHost** wordt bereikt door de [CheckpointAsync-methode](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) aan te roepen op het object [PartitionContext.](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) Deze bewerking gebeurt binnen de [ProcessEventsAsync-methode,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) maar kan ook in [CloseAsync.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)

## <a name="checkpointing"></a>Controlepunten plaatsen

De [CheckpointAsync-methode](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) heeft twee overbelastingen: de eerste, zonder parameters, controlepunten naar de hoogste gebeurtenisverschuiving binnen de verzameling die wordt geretourneerd door [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Deze offset is een "hoog water" merk; het gaat ervan uit dat u alle recente gebeurtenissen hebt verwerkt wanneer u het noemt. Als u deze methode op deze manier gebruikt, moet u ervan op letten dat u deze gebruikt nadat uw andere gebeurtenisverwerkingscode is geretourneerd. Met de tweede overbelasting u een [EventData-exemplaar](/dotnet/api/microsoft.azure.eventhubs.eventdata) opgeven aan controlepunt. Met deze methode u een ander type watermerk gebruiken om controlepunt. Met dit watermerk u een laag water merk implementeren: de laagste volgorde gebeurtenis die u zeker bent is verwerkt. Deze overbelasting is aanwezig om flexibiliteit in offsetbeheer mogelijk te maken.

Wanneer het controlepunt wordt uitgevoerd, wordt een JSON-bestand met partitiespecifieke informatie (met name de offset) naar de opslagrekening geschreven die in de constructor aan [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)wordt geleverd. Dit bestand wordt voortdurend bijgewerkt. Het is van cruciaal belang om checkpointing in context te overwegen - het zou onverstandig zijn om elk bericht te controleren. Het opslagaccount dat wordt gebruikt voor controlepunten zou deze belasting waarschijnlijk niet verwerken, maar nog belangrijker is dat het controleren van elke gebeurtenis een indicatie is van een berichtenpatroon in de wachtrij waarvoor een wachtrij voor servicebussen een betere optie is dan een gebeurtenishub. Het idee achter Event Hubs is dat je "ten minste eenmaal" levering op grote schaal krijgt. Door uw downstreamsystemen idempotent te maken, is het gemakkelijk om te herstellen van storingen of opnieuw opstarten die resulteren in dezelfde gebeurtenissen die meerdere keren worden ontvangen.

## <a name="thread-safety-and-processor-instances"></a>Thread-beveiliging en processorinstanties

[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) is standaard draadveilig en gedraagt zich synchroon ten opzichte van de instantie van [IEventProcessor.](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) Wanneer gebeurtenissen aankomen voor een partitie, wordt [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) ingeschakeld op de **iEventProcessor-instantie** voor die partitie en worden verdere oproepen naar **ProcessEventsAsync** voor de partitie geblokkeerd. Volgende berichten en oproepen naar **ProcessEventsAsync** wachtrij achter de schermen als het bericht pomp blijft draaien op de achtergrond op andere threads. Deze draadveiligheid elimineert de noodzaak van draadveilige verzamelingen en verhoogt de prestaties aanzienlijk.

## <a name="shut-down-gracefully"></a>Sierlijk afsluiten

Tot slot maakt [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) een schone afsluiting van alle partitielezers mogelijk en moet altijd worden gebeld bij het afsluiten van een exemplaar van [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Als u dit niet doet, kan dit leiden tot vertragingen bij het starten van andere exemplaren van **EventProcessorHost** vanwege lease-vervaldatum en Epoch-conflicten. Het beheer van het tijdvak wordt in detail behandeld in de sectie [Van het Tijdvak](#epoch) van het artikel. 

## <a name="lease-management"></a>Leasebeheer
Als u een gebeurtenisprocessorklasse registreert met een exemplaar van EventProcessorHost, wordt de verwerking van gebeurtenissen gestart. De hostinstantie verkrijgt leases op sommige partities van de Gebeurtenishub, waarbij mogelijk enkele van andere host-exemplaren worden gegrepen, op een manier die convergeert op een gelijkmatige verdeling van partities over alle hostexemplaren. Voor elke gehuurde partitie maakt de hostinstantie een instantie van de opgegeven gebeurtenisprocessorklasse, ontvangt vervolgens gebeurtenissen van die partitie en geeft deze door aan de instantie gebeurtenisprocessor. Naarmate er meer exemplaren worden toegevoegd en meer leases worden buitgemaakt, balanceert EventProcessorHost uiteindelijk de belasting onder alle consumenten.

Zoals eerder uitgelegd, vereenvoudigt de trackingtabel het automatisch schalen de karakter van [EventProcessorHost.UnregisterEventProcessorAsync.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) Als een exemplaar van **EventProcessorHost** begint, verwerft het zo veel leases mogelijk, en begint het lezen van evenementen. Aangezien de leases bijna verlopen, probeert **EventProcessorHost** deze te verlengen door een reservering te plaatsen. Als de lease beschikbaar is voor verlenging, blijft de processor lezen, maar als dit niet het is, wordt de lezer gesloten en wordt [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) aangeroepen. **CloseAsync** is een goed moment om een definitieve opschoning voor die partitie uit te voeren.

**EventProcessorHost** bevat een eigenschap [PartitionManagerOptions.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) Deze eigenschap maakt controle over leasemanagement mogelijk. Stel deze opties in voordat u uw [IEventProcessor-implementatie](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) registreert.

## <a name="control-event-processor-host-options"></a>Opties voor gebeurtenisprocessorhost beheren

Daarnaast neemt één overbelasting van [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) een [Object EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) als parameter. Gebruik deze parameter om het gedrag van [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) zelf te beheren. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definieert vier eigenschappen en één gebeurtenis:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): De maximale grootte van de collectie die u wilt ontvangen in een aanroep van [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Deze grootte is niet het minimum, alleen de maximale grootte. Als er minder berichten moeten worden ontvangen, voert **ProcessEventsAsync** uit met zoveel als beschikbaar was.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Een waarde die wordt gebruikt door het onderliggende AMQP-kanaal om de bovengrens te bepalen van het aantal berichten dat de client moet ontvangen. Deze waarde moet groter zijn dan of gelijk zijn aan [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Als deze parameter **waar**is, wordt [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) aangeroepen wanneer de onderliggende aanroep om gebeurtenissen te ontvangen op een partitietime-out. Deze methode is handig voor het uitvoeren van op tijd gebaseerde acties tijdens perioden van inactiviteit op de partitie.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Hiermee kan een functieaanwijzer of lambda-expressie worden ingesteld, die wordt aangeroepen om de eerste verschuiving te bieden wanneer een lezer een partitie begint te lezen. Zonder deze verschuiving op te geven, begint de lezer bij de oudste gebeurtenis, tenzij een JSON-bestand met een verschuiving al is opgeslagen in de opslagrekening die aan de [EventProcessorHost-constructor](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) is geleverd. Deze methode is handig wanneer u het gedrag van het opstarten van de lezer wilt wijzigen. Wanneer deze methode wordt aangeroepen, bevat de objectparameter de partitie-id waarvoor de lezer wordt gestart.
- [ExceptionReceivedEventArgs:](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs)Hiermee u een melding ontvangen van eventuele onderliggende uitzonderingen die zich voordoen in [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Als dingen niet werken zoals je verwacht, is dit evenement een goede plek om te beginnen met zoeken.

## <a name="epoch"></a>Epoch

Hier is hoe de ontvangen tijdperk werkt:

### <a name="with-epoch"></a>Met Epoch
Epoch is een unieke id (tijdvakwaarde) die de service gebruikt om partitie/leaseeigendom af te dwingen. U maakt een ontvanger op basis van Epoch met behulp van de [CreateEpochReceiver-methode.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) Deze methode creëert een ontvanger op basis van Epoch. De ontvanger wordt gemaakt voor een specifieke gebeurtenishubpartitie van de opgegeven consumentengroep.

De functie voor het tijdperk biedt gebruikers de mogelijkheid om ervoor te zorgen dat er op elk moment slechts één ontvanger op een consumentengroep is, met de volgende regels:

- Als er geen bestaande ontvanger op een consumentengroep is, kan de gebruiker een ontvanger maken met een tijdvakwaarde.
- Als er een ontvanger is met een tijdvakwaarde e1 en een nieuwe ontvanger wordt gemaakt met een tijdvakwaarde e2 waar e1 <= e2, wordt de ontvanger met e1 automatisch losgekoppeld, wordt ontvanger met e2 met succes gemaakt.
- Als er een ontvanger met een tijdvak waarde e1 en een nieuwe ontvanger wordt gemaakt met een tijdperk waarde e2 waar e1 > e2, dan is de oprichting van e2 met mislukken met de fout: Een ontvanger met tijdperk e1 bestaat al.

### <a name="no-epoch"></a>Geen tijdperk
U maakt een ontvanger op basis van een niet-Epoch met behulp van de [CreateReceiver-methode.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) 

Er zijn enkele scenario's in streamprocessing waarbij gebruikers meerdere ontvangers op één consumentengroep willen maken. Om dergelijke scenario's te ondersteunen, hebben we de mogelijkheid om een ontvanger te maken zonder tijdperk en in dit geval staan we maximaal 5 gelijktijdige ontvangers toe op de consumentengroep.

### <a name="mixed-mode"></a>Gemengde modus
We raden toepassingsgebruik niet aan wanneer u een ontvanger met tijdvak maakt en vervolgens overschakelt naar geen-tijdperk of omgekeerd op dezelfde consumentengroep. Wanneer dit gedrag optreedt, verwerkt de service het echter met behulp van de volgende regels:

- Als er al een ontvanger is gemaakt met tijdperk e1 en actief gebeurtenissen ontvangt en een nieuwe ontvanger wordt gemaakt zonder tijdperk, zal de creatie van een nieuwe ontvanger mislukken. Epoch ontvangers hebben altijd voorrang in het systeem.
- Als er een ontvanger al gemaakt met tijdperk e1 en kreeg losgekoppeld, en een nieuwe ontvanger is gemaakt zonder tijdperk op een nieuwe MessagingFactory, de oprichting van nieuwe ontvanger zal slagen. Er is een voorbehoud hier dat ons systeem zal de "ontvanger ontkoppeling" te detecteren na ~ 10 minuten.
- Als er een of meer ontvangers zijn gemaakt zonder tijdperk, en een nieuwe ontvanger is gemaakt met tijdperk e1, worden alle oude ontvangers losgekoppeld.


> [!NOTE]
> We raden u aan verschillende consumentengroepen te gebruiken voor toepassingen die tijdperken gebruiken en voor toepassingen die geen tijdvakken gebruiken om fouten te voorkomen. 


## <a name="next-steps"></a>Volgende stappen

Nu je bekend bent met de Event Processor Host, raadpleeg je de volgende artikelen voor meer informatie over Event Hubs:

- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Programmeerhandleiding voor gebeurtenishubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeelden van gebeurtenishubs op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
