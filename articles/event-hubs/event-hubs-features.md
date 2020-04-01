---
title: Overzicht van functies - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u informatie over functies en terminologie van Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 76ab92285cace284c187109ca48c6634777ebbc0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398323"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Functies en terminologie in Azure Event Hubs

Azure Event Hubs is een schaalbare gebeurtenisverwerkingsservice die grote hoeveelheden gebeurtenissen en gegevens inneemt en verwerkt, met een lage latentie en hoge betrouwbaarheid. Zie [Wat is Event Hubs?](event-hubs-what-is-event-hubs.md) voor een overzicht op hoog niveau.

Dit artikel bouwt voort op de informatie in het [overzichtsartikel](event-hubs-what-is-event-hubs.md)en bevat technische en implementatiedetails over onderdelen en functies van Event Hubs.

## <a name="namespace"></a>Naamruimte
Een naamruimte voor gebeurtenishubs biedt een unieke scopingcontainer, waarnaar wordt verwezen door de [volledig gekwalificeerde domeinnaam,](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)waarin u een of meer gebeurtenishubs of Kafka-onderwerpen maakt. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs voor Apache Kafka

[Deze functie](event-hubs-for-kafka-ecosystem-overview.md) biedt een eindpunt waarmee klanten met Gebeurtenishubs kunnen praten met het Kafka-protocol. Deze integratie biedt klanten een Kafka-eindpunt. Dit stelt klanten in staat om hun bestaande Kafka-toepassingen te configureren om met Event Hubs te praten, wat een alternatief biedt voor het runnen van hun eigen Kafka-clusters. Event Hubs voor Apache Kafka ondersteunt Kafka protocol 1.0 en hoger. 

Met deze integratie hoeft u geen Kafka-clusters uit te voeren of ze te beheren met Zookeeper. Hiermee u ook werken met enkele van de meest veeleisende functies van Event Hubs zoals Capture, Auto-inflate en Geo-disaster Recovery.

Met deze integratie kunnen toepassingen zoals Mirror Maker of framework zoals Kafka Connect clusterloos werken met alleen configuratiewijzigingen. 

## <a name="event-publishers"></a>Gebeurtenisuitgevers

Elke entiteit die gegevens naar een gebeurtenishub verzendt, is een gebeurtenisproducent of *gebeurtenisuitgever.* Uitgevers van gebeurtenissen kunnen gebeurtenissen publiceren via HTTPS of AMQP 1.0 of Kafka 1.0 en hoger. Gebeurtenisuitgevers gebruiken een Shared Access Signature-token (SAS) om zichzelf te identificeren bij een Event Hub en kunnen een unieke identiteit hebben of een algemene SAS-token gebruiken.

### <a name="publishing-an-event"></a>Een gebeurtenis publiceren

U een evenement publiceren via AMQP 1.0, Kafka 1.0 (en hoger) of HTTPS. Event Hubs biedt [clientbibliotheken en -klassen](event-hubs-dotnet-framework-api-overview.md) voor het publiceren van gebeurtenissen naar een gebeurtenishub van .NET-clients. Voor andere runtimes en platforms kunt u een AMQP 1.0-client gebruiken, zoals [Apache Qpid](https://qpid.apache.org/). U kunt gebeurtenissen afzonderlijk of batchgewijs publiceren. Eén publicatie (instantie gebeurtenisgegevens) heeft een limiet van 1 MB, ongeacht of het een enkele gebeurtenis of een batch is. Het publiceren van gebeurtenissen die groter zijn dan deze drempelwaarde, resulteert in een fout. Het is een aanbevolen praktijk voor uitgevers om zich niet bewust te zijn van partities binnen de gebeurtenishub en alleen een *partitiesleutel* op te geven (geïntroduceerd in de volgende sectie), of hun identiteit via hun SAS-token.

De keuze om AMQP of HTTPS te gebruiken, geldt specifiek voor het gebruiksscenario. AMQP vereist de inrichting van een permanente bidirectionele socket naast Transport Layer Security (TLS) of SSL/TLS. AMQP gaat gepaard met hogere netwerkkosten tijdens de initialisatie van de sessie. Voor HTTPS is echter extra SSL-overhead vereist voor elke aanvraag. AMQP biedt betere prestaties voor regelmatige uitgevers.

![Event Hubs](./media/event-hubs-features/partition_keys.png)

Event Hubs zorgt ervoor dat alle gebeurtenissen met een partitiesleutelwaarde op volgorde en aan dezelfde partitie worden geleverd. De identiteit van de uitgever en de waarde van de partitiesleutel moeten overeenkomen als er partitiesleutels met uitgeversbeleid worden gebruikt. Als deze niet overeenkomen, treedt er een fout op.

### <a name="publisher-policy"></a>Uitgeversbeleid

In Event Hubs kunt u gebeurtenisuitgevers nauwkeurig beheren met behulp van *uitgeversbeleid*. Uitgeversbeleid bestaat uit runtimefuncties die zijn ontworpen om grote aantallen onafhankelijke gebeurtenisuitgevers mogelijk te maken. Als u uitgeversbeleid implementeert, gebruikt elke uitgever zijn eigen unieke id bij het publiceren van gebeurtenissen naar een Event Hub. Hierbij wordt het volgende mechanisme gebruikt:

```http
//[my namespace].servicebus.windows.net/[event hub name]/publishers/[my publisher name]
```

Het is niet nodig om van tevoren uitgeversnamen te maken. De namen moeten echter wel overeenkomen met het SAS-token dat wordt gebruikt wanneer een gebeurtenis wordt gepubliceerd. Hiermee wordt voor onafhankelijke uitgeversidentiteiten gezorgd. Als u uitgeversbeleid gebruikt, is de waarde **Partitiesleutel** ingesteld op de naam van de uitgever. Voor een goede werking moeten deze waarden overeenkomen.

## <a name="capture"></a>Capture

[Met Event Hubs Capture](event-hubs-capture-overview.md) u de streaminggegevens automatisch vastleggen in gebeurtenishubs en deze opslaan in uw keuze van een Blob-opslagaccount of een Azure Data Lake Service-account. U Vastleggen inschakelen vanuit de Azure-portal en een minimumgrootte en tijdvenster opgeven om de opname uit te voeren. Met Event Hubs Capture geeft u uw eigen Azure Blob Storage-account en container op, of Azure Data Lake Service-account, waarvan er één wordt gebruikt om de vastgelegde gegevens op te slaan. Vastgelegde gegevens zijn geschreven in het Apache Avro-formaat.

## <a name="partitions"></a>Partities
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-tokens

Event Hubs maakt gebruik van *Gedeelde toegangshandtekeningen,* die beschikbaar zijn op het niveau van de naamruimte en gebeurtenishub. Een SAS-token wordt gegenereerd uit een SAS-sleutel en is een SHA-hash of URL. gecodeerd in een specifieke indeling. Event Hubs kan de hash opnieuw genereren met de naam van de sleutel (het beleid) en de token, en op deze manier de afzender verifiëren. Normaal gesproken worden SAS-tokens voor gebeurtenis-uitgevers gemaakt met alleen **verzendrechten** op een specifieke gebeurtenishub. Dit URL-mechanisme met SAS-token vormt de basis voor de uitgeversidentificatie die in het uitgeversbeleid wordt geïntroduceerd. Zie [Shared Access Signature-verificatie met Service Bus](../service-bus-messaging/service-bus-sas.md) voor meer informatie over werken met SAS.

## <a name="event-consumers"></a>Gebeurtenisconsumers

Elke entiteit die gebeurtenisgegevens van een gebeurtenishub leest, is een *gebeurtenisconsument.* Alle Event Hubs-consumers maken verbinding via de AMQP 1.0-sessie, waarin gebeurtenissen worden geleverd zodra deze beschikbaar komen. De client hoeft de beschikbaarheid van de gegevens niet te peilen.

### <a name="consumer-groups"></a>Consumergroepen

Het publicatie-/abonneermechanisme van Event Hubs is ingeschakeld via *consumentengroepen.* Een consumergroep is een weergave (status, positie of offset) van een volledige Event Hub. Consumergroepen maken het mogelijk dat meerdere consumerende toepassingen beschikken over een afzonderlijke weergave van de gebeurtenisstroom. De toepassingen kunnen de stroom onafhankelijk, in hun eigen tempo en met hun eigen offsets lezen.

In een architectuur waarin de stroom wordt verwerkt, is elke downstream-toepassing gelijk aan een consumergroep. Als u gebeurtenisgegevens naar de langetermijnopslag wilt schrijven, is de schrijftoepassing die hiervoor wordt gebruikt, een consumergroep. De complexe verwerking van gebeurtenissen kan vervolgens worden uitgevoerd door een andere, afzonderlijke consumergroep. U hebt alleen toegang tot partities via een consumergroep. Een Event Hub bevat altijd een standaardconsumergroep. Voor een Event Hub van het type Standaard kunt u maximaal 20 consumergroepen maken.

Er kunnen maximaal 5 gelijktijdige lezers op een partitie per consumentengroep; het wordt echter **aanbevolen dat er slechts één actieve ontvanger op een partitie per consumentengroep is.** Binnen één partitie ontvangt elke lezer alle berichten. Als u meerdere lezers op dezelfde partitie hebt, verwerkt u dubbele berichten. Je moet dit te behandelen in uw code, die misschien niet triviaal. Echter, het is een geldige aanpak in sommige scenario's.


Dit zijn voorbeelden van de URI-conventie voor consumergroepen:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

In de volgende afbeelding ziet u de architectuur voor de verwerking van stromen van Event Hubs:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Stroom-offsets

Een *verschuiving* is de positie van een gebeurtenis binnen een partitie. U kunt een offset beschouwen als een clientcursor. De offset is een bytenummering van de gebeurtenis. Met deze offset kan een gebeurtenisconsumer (lezer) een punt in de gebeurtenisstroom opgeven vanwaaruit begonnen moet worden met het lezen van gebeurtenissen. U kunt de offset opgeven als een tijdstempel of als een offsetwaarde. Consumers zijn zelf verantwoordelijk voor het opslaan van hun eigen offsetwaarden buiten de Event Hubs-service. Binnen een partitie bevat elke gebeurtenis een offset.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Controlepunten plaatsen

*Het plaatsen van controlepunten* is een proces waarbij lezers hun positie binnen de gebeurtenisvolgorde van een partitie markeren of vastleggen. Het plaatsen van controlepunten is de verantwoordelijkheid van de consumer en vindt plaats per partitie binnen een consumergroep. Deze verantwoordelijkheid houdt in dat elke partitielezer voor elke consumergroep de huidige positie in de gebeurtenisstroom moet bijhouden en de service kan informeren wanneer de gegevensstroom is voltooid.

Als een lezer van een partitie is losgekoppeld en er vervolgens weer verbinding wordt gemaakt, begint het lezen bij het controlepunt dat eerder is verzonden door de laatste lezer van de betreffende partitie in de consumergroep. Wanneer de lezer verbinding maakt, passeert deze de verschuiving naar de gebeurtenishub om de locatie op te geven waar u moet beginnen met lezen. Op deze manier kunt u het plaatsen van controlepunten gebruiken om gebeurtenissen te markeren als 'voltooid' door downstream-toepassingen. Bovendien beschikt u met controlepunten over tolerantie bij een failover tussen lezers die op verschillende apparaten worden uitgevoerd. Het is mogelijk om terug te keren naar de oudere gegevens door een lagere offset van dit controlepuntproces op te geven. Via dit mechanisme zorgt het plaatsen van controlepunten voor failover-tolerantie en voor herhaling van gebeurtenisstromen.

> [!NOTE]
> Als u Azure Blob Storage gebruikt als controlepuntopslag in een omgeving die een andere versie van Storage Blob SDK ondersteunt dan die welke doorgaans beschikbaar is op Azure, moet u code gebruiken om de API-versie van de opslagservice te wijzigen in de specifieke versie die wordt ondersteund door die omgeving. Als u bijvoorbeeld Gebeurtenishubs uitvoert [op een Azure Stack Hub-versie 2002,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)is versie 2017-11-09 de hoogst beschikbare versie voor de opslagservice. In dit geval moet u code gebruiken om de API-versie van de opslagservice te targeten op 2017-11-09. Zie deze voorbeelden op GitHub voor een voorbeeld over het targeten van een specifieke Storage API-versie: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) of [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

### <a name="common-consumer-tasks"></a>Algemene taken voor consumers

Alle Event Hubs-consumenten maken verbinding via een AMQP 1.0-sessie, een staatsbewust bidirectioneel communicatiekanaal. Elke partitie heeft een AMQP 1.0-sessie die het mogelijk maakt partitiespecifieke gebeurtenissen te transporteren.

#### <a name="connect-to-a-partition"></a>Verbinding maken met een partitie

Het is gebruikelijk om een leasemechanisme te gebruiken wanneer u verbinding maakt met partities, zodat u het verbinden van lezers aan specifieke partities kunt coördineren. Op deze manier maakt u het mogelijk dat elke partitie in een consumergroep slechts één actieve lezer heeft. Het plaatsen van controlepunten voor en het leasen en beheren van lezers wordt vereenvoudigd door toepassing van de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)-klasse voor .NET-clients. De Event Processor Host is een intelligente consumeragent.

#### <a name="read-events"></a>Gebeurtenissen lezen

Nadat er voor een specifieke partitie een AMQP 1.0-sessie en -koppeling zijn geopend, worden de gebeurtenissen door de Event Hubs-service aan de AMQP 1.0-client geleverd. Dit leveringsmechanisme maakt hogere doorvoer en lagere latentie mogelijk dan pull-mechanismen zoals HTTP GET. Tijdens het verzenden van gebeurtenissen naar de client wordt elk gebeurtenisgegeven voorzien van belangrijke metagegevens, zoals de offset en het volgnummer. Deze worden gebruikt om het plaatsen van controlepunten in de gebeurtenisvolgorde mogelijk te maken.

Gebeurtenisgegevens:
* Offset
* Volgnummer
* Hoofdtekst
* Gebruikerseigenschappen
* Systeemeigenschappen

U bent verantwoordelijk voor het beheer van de offset.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Programmeerhandleiding voor gebeurtenishubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Voorbeelden van gebeurtenishubs][]

[Voorbeelden van gebeurtenishubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
