---
title: Overzicht van functies - Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over functies en terminologie van Azure Event Hubs.
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
ms.openlocfilehash: 568a21cee5b50a8914c603976f5951d0235dbff7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157173"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Functies en de belangrijkste termen in de Azure Event Hubs

Azure Event Hubs is een schaalbare verwerkingsservice van gebeurtenissen, die worden opgenomen en verwerkt van grote hoeveelheden gebeurtenissen en de gegevens, met een lage latentie en hoge betrouwbaarheid. Zie [Wat is Event hubs?](event-hubs-what-is-event-hubs.md) voor een overzicht op hoog niveau.

Dit artikel is gebaseerd op de informatie in het [overzichts artikel](event-hubs-what-is-event-hubs.md)en biedt technische en implementatie details over Event hubs onderdelen en functies.

## <a name="namespace"></a>Naamruimte
Een Event Hubs naam ruimte biedt een unieke bereik container, waarnaar wordt verwezen door de [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), waarin u een of meer Event hubs of Kafka-onderwerpen maakt. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs voor Apache Kafka

[Deze functie](event-hubs-for-kafka-ecosystem-overview.md) biedt een eind punt waarmee klanten kunnen praten met Event hubs met behulp van het Kafka-protocol. Deze integratie biedt klanten een Kafka-eindpunt. Deze aanbieding kunnen klanten hun bestaande Kafka-toepassingen om te communiceren met Event Hubs, zodat een alternatief voor het uitvoeren van hun eigen Kafka-clusters configureren. Eventhubs voor Apache Kafka biedt ondersteuning voor Kafka-protocol 1.0 en hoger. 

Met deze integratie hoeft u geen Kafka-clusters uit te voeren of ze te beheren met Zookeeper. Hiermee kunt u ook werkt met bepaalde van de meest veeleisende functies van Event Hubs, zoals automatisch vergroten en Geo-noodherstel vastleggen.

Deze integratie kunt ook toepassingen, zoals de Maker van de Mirror of framework, zoals Kafka verbinding maken om te werken clusterless met alleen wijzigingen in de configuratie. 

## <a name="event-publishers"></a>Gebeurtenisuitgevers

Elke entiteit die gegevens naar een Event Hub verzendt, is een gebeurtenis producent of een *Uitgever van gebeurtenissen*. Gebeurtenisuitgevers kunnen gebeurtenissen publiceren met HTTPS of AMQP 1.0 of Kafka 1.0 en hoger. Gebeurtenisuitgevers gebruiken een Shared Access Signature-token (SAS) om zichzelf te identificeren bij een Event Hub en kunnen een unieke identiteit hebben of een algemene SAS-token gebruiken.

### <a name="publishing-an-event"></a>Een gebeurtenis publiceren

U kunt een gebeurtenis publiceren met AMQP 1.0, Kafka 1.0 (en hoger) of HTTPS. Event Hubs biedt [client bibliotheken en-klassen](event-hubs-dotnet-framework-api-overview.md) voor het publiceren van gebeurtenissen naar een event hub van .net-clients. Voor andere runtimes en platforms kunt u een AMQP 1.0-client gebruiken, zoals [Apache Qpid](https://qpid.apache.org/). U kunt gebeurtenissen afzonderlijk of batchgewijs publiceren. Eén publicatie (exemplaar met gebeurtenisgegevens) heeft een limiet van 1 MB, ongeacht of het één gebeurtenis of om een batch. Het publiceren van gebeurtenissen die groter zijn dan deze drempelwaarde resulteert in een fout. Het is voor uitgevers een best practice om niets te weten over de partities binnen Event Hub en alleen een *partitiesleutel* (zie volgende sectie) of hun identiteit via de SAS-token op te geven.

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

Met [Event hubs Capture](event-hubs-capture-overview.md) kunt u automatisch de streaminggegevens in Event hubs vastleggen en opslaan in een Blob Storage-account of een Azure data Lake-service account. U kunt Capture inschakelen via de Azure-portal en geef een minimale grootte en het tijdvenster om uit te voeren van het vastleggen. Met Event Hubs Capture kunt opgeven u uw eigen Azure Blob Storage-account en een container of een Azure Data Lake-Service-account, een van die wordt gebruikt voor het opslaan van de vastgelegde gegevens. Vastgelegde gegevens worden geschreven in de Apache Avro-indeling.

## <a name="partitions"></a>Partities
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-tokens

Event Hubs gebruikt *Shared Access Signatures* die beschikbaar zijn op het niveau van de naamruimte en Event Hub. Een SAS-token wordt gegenereerd uit een SAS-sleutel en is een SHA-hash of URL. gecodeerd in een specifieke indeling. Event Hubs kan de hash opnieuw genereren met de naam van de sleutel (het beleid) en de token, en op deze manier de afzender verifiëren. Normaal gesproken worden SAS-tokens voor gebeurtenisuitgevers alleen gemaakt met bevoegdheden voor **verzenden** voor een specifieke Event Hub. Dit URL-mechanisme met SAS-token vormt de basis voor de uitgeversidentificatie die in het uitgeversbeleid wordt geïntroduceerd. Zie [Shared Access Signature-verificatie met Service Bus](../service-bus-messaging/service-bus-sas.md) voor meer informatie over werken met SAS.

## <a name="event-consumers"></a>Gebeurtenisconsumers

Elke entiteit die gebeurtenisgegevens van een Event Hub leest, is een *gebeurtenisconsumer*. Alle Event Hubs-consumers maken verbinding via de AMQP 1.0-sessie, waarin gebeurtenissen worden geleverd zodra deze beschikbaar komen. De client hoeft de beschikbaarheid van de gegevens niet te peilen.

### <a name="consumer-groups"></a>Consumergroepen

Het Event Hubs-mechanisme voor publiceren/abonneren wordt geactiveerd via *consumergroepen*. Een consumergroep is een weergave (status, positie of offset) van een volledige Event Hub. Consumergroepen maken het mogelijk dat meerdere consumers beschikken over een afzonderlijke weergave van de gebeurtenisstroom. De toepassingen kunnen de stroom onafhankelijk, in hun eigen tempo en met hun eigen offsets lezen.

In een architectuur waarin de stroom wordt verwerkt, is elke downstream-toepassing gelijk aan een consumergroep. Als u gebeurtenisgegevens naar de langetermijnopslag wilt schrijven, is de schrijftoepassing die hiervoor wordt gebruikt, een consumergroep. De complexe verwerking van gebeurtenissen kan vervolgens worden uitgevoerd door een andere, afzonderlijke consumergroep. U hebt alleen toegang tot partities via een consumergroep. Een Event Hub bevat altijd een standaardconsumergroep. Voor een Event Hub van het type Standaard kunt u maximaal 20 consumergroepen maken.

Er kunnen Maxi maal vijf gelijktijdige lezers op een partitie per consumenten groep zijn. **het is echter raadzaam dat er slechts één actieve ontvanger is op een partitie per Consumer groep**. Binnen een enkele partitie ontvangt elke lezer alle berichten. Als u meerdere lezers op dezelfde partitie hebt, klikt verwerken u dubbele berichten. U moet dit in uw code, die mogelijk niet eenvoudig te verwerken. Het is echter een geldig benadering in sommige scenario's.


Dit zijn voorbeelden van de URI-conventie voor consumergroepen:

```http
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #1]
//[my namespace].servicebus.windows.net/[event hub name]/[Consumer Group #2]
```

In de volgende afbeelding ziet u de architectuur voor de verwerking van stromen van Event Hubs:

![Event Hubs](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Stroom-offsets

Een *offset* is de positie van een gebeurtenis binnen een partitie. U kunt een offset beschouwen als een clientcursor. De offset is een bytenummering van de gebeurtenis. Met deze offset kan een gebeurtenisconsumer (lezer) een punt in de gebeurtenisstroom opgeven vanwaaruit begonnen moet worden met het lezen van gebeurtenissen. U kunt de offset opgeven als een tijdstempel of als een offsetwaarde. Consumers zijn zelf verantwoordelijk voor het opslaan van hun eigen offsetwaarden buiten de Event Hubs-service. Binnen een partitie bevat elke gebeurtenis een offset.

![Event Hubs](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Controlepunten plaatsen

*Het plaatsen van controlepunten* is een proces waarbij lezers hun positie binnen de gebeurtenisvolgorde van een partitie markeren of vastleggen. Het plaatsen van controlepunten is de verantwoordelijkheid van de consumer en vindt plaats per partitie binnen een consumergroep. Deze verantwoordelijkheid houdt in dat elke partitielezer voor elke consumergroep de huidige positie in de gebeurtenisstroom moet bijhouden en de service kan informeren wanneer de gegevensstroom is voltooid.

Als een lezer van een partitie is losgekoppeld en er vervolgens weer verbinding wordt gemaakt, begint het lezen bij het controlepunt dat eerder is verzonden door de laatste lezer van de betreffende partitie in de consumergroep. Wanneer de lezer verbinding maakt, wordt de verschuiving van de doorgegeven aan de event hub om op te geven van de locatie waarop u wilt beginnen met lezen. Op deze manier kunt u het plaatsen van controlepunten gebruiken om gebeurtenissen te markeren als 'voltooid' door downstream-toepassingen. Bovendien beschikt u met controlepunten over tolerantie bij een failover tussen lezers die op verschillende apparaten worden uitgevoerd. Het is mogelijk om terug te keren naar de oudere gegevens door een lagere offset van dit controlepuntproces op te geven. Via dit mechanisme zorgt het plaatsen van controlepunten voor failover-tolerantie en voor herhaling van gebeurtenisstromen.

### <a name="common-consumer-tasks"></a>Algemene taken voor consumers

Alle Event Hubs-consumers maken verbinding via een AMQP 1.0-sessie, een statusbewust bidirectioneel communicatiekanaal. Elke partitie heeft een AMQP 1.0-sessie die het mogelijk maakt partitiespecifieke gebeurtenissen te transporteren.

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
    - [JavaScript](get-started-java-send-v2.md)
* [Programmeerhandleiding voor Event Hubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Event Hubs-voor beelden][]

[Event Hubs-voor beelden]: https://github.com/Azure/azure-event-hubs/tree/master/samples
