---
title: Gebeurtenissen ontvangen met Eventprocessorhost - Azure Event Hubs | Microsoft Docs
description: Dit artikel beschrijft de Event Processor Host in Azure Event Hubs, dat het beheer van het plaatsen van controlepunten vereenvoudigt, overdracht en bewaartermijn parallel lezen van gebeurtenissen.
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
ms.openlocfilehash: 414179d62970315a7575be0411bf1cb152349fdc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162290"
---
# <a name="event-processor-host"></a>Gebeurtenisprocessorhost
> [!NOTE]
> Dit artikel is van toepassing op de oude versie van Azure Event Hubs SDK. Zie deze migratie handleidingen voor meer informatie over het migreren van uw code naar de nieuwere versie van de SDK. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MIGRATIONGUIDE.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Zie ook [de verdeling van de partitie verdelen over meerdere exemplaren van uw toepassing](event-processor-balance-partition-load.md).

Azure Event Hubs is een krachtige service voor telemetrieopname die kan worden gebruikt voor streaming van miljoenen gebeurtenissen tegen lage kosten. In dit artikel wordt beschreven hoe u opgenomen gebeurtenissen kunt gebruiken met behulp van de *Event processor host* (EPH). een intelligente Consumer-agent die het beheer van de controle punten, leasing en parallelle gebeurtenis lezers vereenvoudigt.  

De sleutel worden geschaald voor Event Hubs is het idee van de gepartitioneerde consumenten. In tegens telling tot het patroon van [concurrerende gebruikers](https://msdn.microsoft.com/library/dn568101.aspx) , maakt het gepartitioneerde consument patroon een hoge schaal door de knel punt conflicten te verwijderen en end-to-end-parallellisme te vereenvoudigen.

## <a name="home-security-scenario"></a>Beveiliging voor thuis-scenario

Een voorbeeldscenario, kunt u beter een beveiliging voor thuis-bedrijf dat 100.000 huisadressen bewaakt. Elke minuut, deze gegevens worden opgehaald uit diverse sensoren, zoals een bewegingsherkenning, of venster van de klep open sensor, glas break detector, enz., geïnstalleerd bij elke thuis. Het bedrijf biedt een website voor inwoners van de voor het bewaken van de activiteit van de startpagina in bijna realtime.

Elke sensor pushes gegevens naar een event hub. De event hub is geconfigureerd met 16 partities. Aan het einde in beslag nemen moet u een mechanisme dat u kunt deze gebeurtenissen worden gelezen, kunt u deze (filteren, samen te voegen enzovoort) en de statistische functie naar een blob storage, die vervolgens wordt toegewezen aan een gebruiksvriendelijke webpagina dump.

## <a name="write-the-consumer-application"></a>De consument toepassing schrijven

Bij het ontwerpen van de gebruiker in een gedistribueerde omgeving, moet het scenario ingang in de volgende vereisten:

1. **Schalen:** Maak meerdere consumenten, waarbij elke consument eigenaar is van een aantal Event Hubs partities.
2. **Taak verdeling:** De consumenten dynamisch te verhogen of te verlagen. Bijvoorbeeld, wanneer een nieuw sensortype (bijvoorbeeld een koolmonoxide detector) wordt toegevoegd aan elke start, verhoogt het aantal gebeurtenissen. In dat geval wordt het aantal consumentexemplaren wordt verhoogd in de operator (een mens). Vervolgens, in de groep consumenten herverdelen van het aantal partities ze eigenaar zijn, voor het delen van de belasting met de zojuist toegevoegde consumenten.
3. **Naadloos hervatten bij fouten:** Als een Consumer (**Consumer a**) uitvalt (bijvoorbeeld als de virtuele machine die de consument als host voor de klant heeft gevonden, plotseling vastloopt), moeten andere gebruikers de partities kunnen ophalen die eigendom zijn van de **consument a** en door gaan. Daarnaast moet het voortzettings punt, een *controle punt* of *Offset*, worden opgegeven op het exacte punt waar de **Consumer a** mislukt, of iets voor dat.
4. **Gebeurtenissen gebruiken:** Hoewel de voor gaande drie punten van toepassing zijn op het beheer van de gebruiker, moet er code zijn om de gebeurtenissen te gebruiken en iets nuttig te doen. u kunt deze bijvoorbeeld samen voegen en uploaden naar Blob Storage.

Event Hubs biedt deze functionaliteit in plaats van uw eigen oplossing te maken met behulp van de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -interface en de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -klasse.

## <a name="ieventprocessor-interface"></a>IEventProcessor-interface

Voor de eerste keer gebruiken toepassingen de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -interface, die vier methoden heeft: [openasync, CloseAsync, ProcessErrorAsync en ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Deze interface bevat de werkelijke code voor het gebruiken van de gebeurtenissen die Event Hubs wordt verzonden. De volgende code toont een eenvoudige implementatie:

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

Vervolgens instantiërt u een [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar. Afhankelijk van de overbelasting, worden de volgende para meters gebruikt bij het maken van het [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar in de constructor:

- **hostnaam:** de naam van elke consument instantie. Elk exemplaar van **EventProcessorHost** moet een unieke waarde voor deze variabele hebben binnen een Consumer groep. Zorg er dus voor dat u deze waarde niet vast kunt coderen.
- **eventHubPath:** De naam van de Event Hub.
- **consumerGroupName:** Event Hubs gebruikt **$default** als de naam van de standaard Consumer groep, maar het is een goed idee om een Consumer groep te maken voor uw specifieke verwerkings aspect.
- **eventHubConnectionString:** Het connection string naar de Event Hub, dat kan worden opgehaald uit de Azure Portal. Deze connection string moet **Luister** machtigingen hebben voor de Event hub.
- **storageConnectionString:** Het opslag account dat wordt gebruikt voor intern resource beheer.

Tot slot registreren consumenten het [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar met de Event hubs-service. Registreren van een klasse event processor met een exemplaar van EventProcessorHost start gebeurtenisverwerking. Registratie geeft de Event Hubs-service de opdracht te verwachten dat gebeurtenissen van sommige van de partities door de Consumer-app worden gebruikt en dat de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementatie code moet worden aangeroepen wanneer de gebeurtenissen worden gepusht. 


### <a name="example"></a>Voorbeeld

Een voorbeeld: Stel dat er 5 virtuele machines (VM's) zijn toegewezen aan het verbruiken van gebeurtenissen en een eenvoudige consoletoepassing in elke virtuele machine, die het werkelijke verbruik werkt. Elke console toepassing maakt vervolgens één [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -exemplaar en registreert het met de Event hubs-service.

In dit voorbeeld scenario gaan we ervan uit dat 16 partities worden toegewezen aan de 5 **EventProcessorHost** -instanties. Sommige **EventProcessorHost** -instanties hebben mogelijk een aantal meer partities dan andere. Voor elke partitie waarvan een **EventProcessorHost** -exemplaar eigenaar is, wordt er een instantie van de klasse `SimpleEventProcessor` gemaakt. Daarom zijn er 16 exemplaren van `SimpleEventProcessor` over het algemeen, met één toegewezen aan elke partitie.

De volgende lijst bevat een overzicht van dit voorbeeld:

- 16 event Hubs-partities.
- 5-VM's, 1 consumer-app (bijvoorbeeld Consumer.exe) in elke virtuele machine.
- 5 EPH-exemplaren geregistreerd, 1 in elke virtuele machine door Consumer.exe.
- 16 `SimpleEventProcessor` objecten die zijn gemaakt door de 5 EPH-instanties.
- 1 Consumer. exe-app kan vier `SimpleEventProcessor` objecten bevatten, omdat het 1 EPH-exemplaar een eigenaar van vier partities kan zijn.

## <a name="partition-ownership-tracking"></a>Partitie eigendom bijhouden

Eigendom van een partitie op een EPH-exemplaar (of een gebruiker) wordt bijgehouden via de Azure Storage-account dat is opgegeven voor tracering. U kunt als volgt de tracering als een eenvoudige tabel visualiseren. U kunt de daadwerkelijke implementatie zien door te controleren van de blobs onder het opgegeven opslagaccount:

| **Naam van consumentengroep** | **Partitie-ID** | **Hostnaam (eigenaar)** | **Verwervings tijd (of eigendom) van de lease** | **Offset in partitie (controle punt)** |
| --- | --- | --- | --- | --- |
| Standaar$d | 0 | Consumenten\_VM3 | 2018-04-15T01:23:45 | 156 |
| Standaar$d | 1 | Consumenten\_VM4 | 2018-04-15T01:22:13 | 734 |
| Standaar$d | 2 | Consumenten\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| Standaar$d | 15 | Consumenten\_VM3 | 2018-04-15T01:22:56 | 976 |

Hier, krijgt elke host eigendom van een partitie gedurende een bepaalde periode (de duur van de lease). Als een host mislukt (virtuele machine wordt afgesloten), is de lease is verlopen. Andere hosts proberen op te halen van de eigenaar van de partitie en een van de hosts is geslaagd. Dit proces stelt de lease op de partitie met een nieuwe eigenaar. Op deze manier slechts één lezer tegelijk kunt lezen uit een bepaalde partitie binnen een consumergroep.

## <a name="receive-messages"></a>Berichten ontvangen

Elke aanroep van [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) levert een verzameling gebeurtenissen. Het is uw verantwoordelijkheid om deze gebeurtenissen te verwerken. Als u er zeker van wilt zijn dat de processor host elk bericht ten minste één keer verwerkt, moet u uw eigen code voor het opnieuw proberen van het proces schrijven. Maar wees voorzichtig met verontreinigde berichten.

Het is raadzaam dat u relatief snel handelingen; dat wil zeggen, kunt u doen als weinig verwerking mogelijk. Gebruik in plaats daarvan consumentengroepen. Als u naar opslag moet schrijven en een route ring wilt uitvoeren, is het beter om twee consumenten groepen te gebruiken en twee [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementaties te hebben die afzonderlijk worden uitgevoerd.

Op een bepaald moment tijdens de verwerking, is het raadzaam om wat u hebt gelezen en voltooid bij te houden. Het bijhouden is van essentieel belang als u het lezen, opnieuw opstarten moet, zodat u niet teruggaan naar het begin van de stroom. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) vereenvoudigt dit bijhouden met behulp van *controle punten*. Een controlepunt is een locatie of een offset voor een bepaalde partitie, binnen een bepaalde consumergroep, op het moment waarop u bent ervan overtuigd dat u de berichten zijn verwerkt. Het markeren van een controle punt in **EventProcessorHost** wordt bereikt door het aanroepen van de methode [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) op het [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) -object. Deze bewerking wordt uitgevoerd in de [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) -methode, maar kan ook worden uitgevoerd in [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Controlepunten plaatsen

De methode [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) heeft twee Overloads: de eerste, zonder para meters, de controle punten voor de hoogste gebeurtenis verschuiving in de verzameling die wordt geretourneerd door [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Deze offset is een teken 'high-water'; wordt ervan uitgegaan dat u alle recente gebeurtenissen wanneer u deze aanroepen zijn verwerkt. Als u deze methode op deze manier gebruikt, Let erop dat u wordt naar deze aanroepen nadat de verwerking van de gebeurtenis-code heeft geretourneerd. Met de tweede overbelasting kunt u een [Event Data](/dotnet/api/microsoft.azure.eventhubs.eventdata) -exemplaar opgeven voor het controle punt. Deze methode kunt u met een ander type watermerk controlepunt. Met deze watermerk, kunt u een ' laag ' watermerk implementeren: de laagste gesequentieerde gebeurtenis u er zeker van zijn verwerkt. Deze overbelasting is opgegeven voor het inschakelen van flexibiliteit bij het beheer van offset.

Wanneer het controle punt wordt uitgevoerd, wordt een JSON-bestand met partitie-specifieke informatie (met name de offset) geschreven naar het opslag account dat is opgegeven in de constructor naar [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Dit bestand wordt voortdurend bijgewerkt. Het is essentieel om te overwegen het plaatsen van controlepunten in de context: het is niet verstandig controlepunt elk bericht. Het opslagaccount dat wordt gebruikt voor het plaatsen van controlepunten waarschijnlijk verwerkt niet deze belasting, maar nog belangrijker plaatsen van controlepunten voor elke gebeurtenis is indicatief voor een in de wachtrij abonnementsberichten waarvoor mogelijk een Service Bus-wachtrij een betere optie dan een event hub. Het idee achter Event Hubs, is dat u 'ten minste eenmaal' levering op grote schaal. Door uw downstream-systemen idempotent zijn, het is eenvoudig om fouten te herstellen of opnieuw wordt gestart die resulteren in dezelfde gebeurtenissen meerdere keren worden ontvangen.

## <a name="thread-safety-and-processor-instances"></a>Thread-veiligheid en processor-instanties

[EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) is standaard thread veilig en gedraagt zich op synchrone wijze met betrekking tot het exemplaar van [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Wanneer gebeurtenissen voor een partitie arriveren, wordt [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) aangeroepen voor het **IEventProcessor** -exemplaar voor die partitie en worden verdere aanroepen naar **ProcessEventsAsync** voor de partitie geblokkeerd. Volgende berichten en aanroepen van de **ProcessEventsAsync** -wachtrij worden achter de schermen geplaatst, terwijl de bericht pomp blijft draaien op de achtergrond van andere threads. Deze thread veiligheid hoeven zich niet voor thread-veilige verzamelingen en worden de prestaties aanzienlijk verbeterd.

## <a name="shut-down-gracefully"></a>Zonder problemen worden afgesloten

Ten slotte maakt [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) een schone afsluiting mogelijk van alle partitie lezers en moet deze altijd worden aangeroepen wanneer een instantie van [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)wordt afgesloten. Als u dit niet doet, kan dit leiden tot vertragingen bij het starten van andere exemplaren van **EventProcessorHost** vanwege verval-en epoche conflicten. Het epoche-beheer wordt uitvoerig besproken in de sectie [epoche](#epoch) van het artikel. 

## <a name="lease-management"></a>Lease-management
Registreren van een klasse event processor met een exemplaar van EventProcessorHost start gebeurtenisverwerking. De instantie van de host haalt leases op aantal partities van de Event Hub, mogelijk gegevens uit formulieren sommige vanuit andere exemplaren hosten op een manier die convergeert wel op een gelijkmatige verdeling van partities voor alle instanties van de host ligt. Voor elke partitie geleased de instantie van de host maakt een exemplaar van de klasse opgegeven event processor, klikt u vervolgens ontvangt gebeurtenissen van deze partitie en geeft deze door aan de event processor-instantie. Als u meer instanties zijn toegevoegd en meer leases afkomstig zijn, verdeelt EventProcessorHost uiteindelijk de belasting tussen alle gebruikers.

Zoals eerder is uitgelegd, vereenvoudigt de tracerings tabel het automatisch schalen van [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Als instantie van **EventProcessorHost** wordt gestart, worden zo veel mogelijk leases opgehaald en worden gebeurtenissen gelezen. Wanneer de leases bijna verlopen, probeert **EventProcessorHost** ze te vernieuwen door een reserve ring te plaatsen. Als de lease beschikbaar is voor vernieuwing, gaat de processor door met lezen, maar als dat niet het geval is, wordt de lezer gesloten en wordt [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) aangeroepen. **CloseAsync** is een goede tijd om een definitieve opschoning voor die partitie uit te voeren.

**EventProcessorHost** bevat een eigenschap [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) . Deze eigenschap kunt u bepalen leasebeheer. Stel deze opties in voordat u uw [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -implementatie registreert.

## <a name="control-event-processor-host-options"></a>Opties voor broncodebeheer Event Processor Host

Daarnaast neemt één overbelasting van [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) een [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) -object als para meter. Gebruik deze para meter om het gedrag van [EventProcessorHost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) zelf te beheren. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definieert vier eigenschappen en één gebeurtenis:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): de maximale grootte van de verzameling die u wilt ontvangen in een aanroep van [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Deze grootte is niet het minimum, alleen de maximale grootte. Als er minder berichten moeten worden ontvangen, wordt **ProcessEventsAsync** uitgevoerd met zo veel als beschikbaar.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): een waarde die wordt gebruikt door het onderliggende AMQP-kanaal om de bovengrens te bepalen van het aantal berichten dat de client moet ontvangen. Deze waarde moet groter zijn dan of gelijk zijn aan [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): als deze para meter **True**is, wordt [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) aangeroepen wanneer de onderliggende aanroep voor het ontvangen van gebeurtenissen op een partitie een time-out heeft. Deze methode is handig voor het nemen van op tijd gebaseerde acties tijdens peri Oden van inactiviteit op de partitie.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Hiermee kan een functie-of lambda-expressie worden ingesteld, die wordt aangeroepen om de initiële offset te bieden wanneer een lezer begint met het lezen van een partitie. Zonder deze offset op te geven, begint de lezer bij het oudste evenement, tenzij een JSON-bestand met een offset al is opgeslagen in het opslag account dat is opgegeven aan de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -constructor. Deze methode is handig als u wilt wijzigen van het gedrag van het opstarten van de lezer. Wanneer deze methode wordt aangeroepen, bevat de parameter van het object in de partitie-ID waarvoor de lezer wordt gestart.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Hiermee kunt u meldingen ontvangen over onderliggende uitzonde ringen die optreden in [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Als dingen niet werkt zoals verwacht, is deze gebeurtenis een goede plaats te bekijken.

## <a name="epoch"></a>Epoch

De receive-epoche werkt als volgt:

### <a name="with-epoch"></a>Met epoche
Epoche is een unieke id (epoche waarde) die door de service wordt gebruikt om het eigendom van een partitie/lease af te dwingen. U maakt een op epoche gebaseerde ontvanger met behulp van de methode [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) . Met deze methode wordt een op epoche gebaseerde ontvanger gemaakt. De ontvanger wordt gemaakt voor een specifieke Event Hub partitie van de opgegeven Consumer groep.

De epoche-functie biedt gebruikers de mogelijkheid om ervoor te zorgen dat er op elk moment maar één ontvanger op een Consumer groep is, met de volgende regels:

- Als er geen bestaande ontvanger is voor een Consumer groep, kan de gebruiker een ontvanger maken met elke epoche waarde.
- Als er een ontvanger is met een epoche waarde E1 en er een nieuwe ontvanger wordt gemaakt met een epoche waarde E2 waarbij E1 < = E2, wordt de ontvanger met E1 automatisch losgekoppeld, ontvanger met E2 wordt gemaakt.
- Als er een ontvanger is met een epoche waarde E1 en er een nieuwe ontvanger wordt gemaakt met een epoche waarde E2 waarbij E1 > E2, wordt het maken van E2 met een fout gegenereerd: er bestaat al een ontvanger met epoche E1.

### <a name="no-epoch"></a>Geen epoche
U maakt een ontvanger op basis van een niet-epoche met behulp van de methode [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) . 

Er zijn enkele scenario's in de verwerking van stromen waarbij gebruikers meerdere ontvangers willen maken op één consumer groep. Om dergelijke scenario's te ondersteunen, hebben we de mogelijkheid om een ontvanger zonder epoche te maken. in dit geval kunnen we Maxi maal vijf gelijktijdige ontvangers voor de consumenten groep toestaan.

### <a name="mixed-mode"></a>Gemengde modus
Het gebruik van de toepassing wordt niet aanbevolen, waarbij u een ontvanger maakt met epoche en vervolgens overschakelt naar No-epoche of vice-versa op dezelfde Consumer groep. Als dit probleem zich voordoet, wordt het echter door de service verwerkt met de volgende regels:

- Als er al een ontvanger is gemaakt met epoche E1 en actief gebeurtenissen ontvangt en er een nieuwe ontvanger wordt gemaakt zonder epoche, mislukt het maken van een nieuwe ontvanger. Epoche-ontvangers hebben altijd voor rang op het systeem.
- Als er al een ontvanger is gemaakt met epoche E1 en de verbinding is verbroken, en er een nieuwe ontvanger wordt gemaakt zonder epoche op een nieuwe MessagingFactory, zal het maken van een nieuwe ontvanger slagen. Er wordt hier een voor behoud weer gegeven dat ons systeem na ongeveer 10 minuten de verbinding van de ontvanger moet detecteren.
- Als er een of meer ontvangers zijn gemaakt zonder epoche, en er een nieuwe ontvanger wordt gemaakt met epoche E1, worden alle oude ontvangers losgekoppeld van elkaar.


> [!NOTE]
> We raden u aan verschillende consumenten groepen te gebruiken voor toepassingen die gebruikmaken van epoches en voor gebruikers die geen epoches gebruiken om fouten te voor komen. 


## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de Event Processor Host, Zie de volgende artikelen voor meer informatie over Event Hubs:

- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Programmeerhandleiding voor Event Hubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voor beelden Event Hubs op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
