---
title: Event Hub van Apache Kafka app gebruiken-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over Apache Kafka ondersteuning door Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555787"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Azure Event Hubs van Apache Kafka toepassingen gebruiken
Event Hubs biedt een Kafka-eind punt dat door uw bestaande op Kafka gebaseerde toepassingen kan worden gebruikt als alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs ondersteunt [Apache Kafka protocol 1,0 en hoger](https://kafka.apache.org/documentation/)en werkt met uw bestaande Kafka-toepassingen, waaronder MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Wat biedt Event Hubs voor Kafka?

De functie Event Hubs voor Kafka voorziet in een Protocolsubstatus boven op de Azure-Event Hubs die binair compatibel is met Kafka versie 1,0 en hoger voor zowel lees-als schrijf bewerkingen naar Kafka-onderwerpen. U kunt beginnen met het gebruik van het Kafka-eind punt vanuit uw toepassingen zonder code wijziging, maar een minimale configuratie wijziging. U werkt de connection string in configuraties bij zodat deze verwijzen naar het Kafka-eind punt dat door uw Event Hub wordt weer gegeven in plaats van naar uw Kafka-cluster te wijzen. Vervolgens kunt u gebeurtenissen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in Event Hubs starten. Deze integratie biedt ook ondersteuning voor frameworks zoals [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), dat momenteel als preview-versie beschikbaar is. 

Conceptueel Kafka en Event Hubs bijna identiek zijn: ze zijn beide gepartitioneerde logboeken die zijn gemaakt voor het streamen van gegevens. In de volgende tabel worden concepten tussen Kafka en Event Hubs toegewezen.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Conceptuele toewijzing van Kafka en Event hub

| Kafka-concept | Event Hubs concept|
| --- | --- |
| Cluster | Naamruimte |
| Onderwerp | Event Hub |
| Partitie | Partitie|
| Consumenten groep | Consumenten groep |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>De belangrijkste verschillen tussen Kafka en Event Hubs

Hoewel [Apache Kafka](https://kafka.apache.org/) software is, die u overal kunt uitvoeren, is Event hubs een Cloud service die vergelijkbaar is met Azure Blob Storage. Er zijn geen servers of netwerken om te beheren en er kunnen geen makelaars worden geconfigureerd. U maakt een naam ruimte, een FQDN waarin uw onderwerpen wonen en vervolgens Event Hubs of onderwerpen maken binnen die naam ruimte. Zie [Event hubs-functies](event-hubs-features.md#namespace)voor meer informatie over Event hubs en naam ruimten. Als Cloud service gebruikt Event Hubs één stabiel virtueel IP-adres als het eind punt, zodat clients niet hoeven te weten over de Brokers of computers binnen een cluster. 

De schaal van Event Hubs wordt bepaald door het aantal doorvoer eenheden dat u aanschaft, waarbij elke doorvoer eenheid u een recht geeft op 1 MB per seconde, of 1000 gebeurtenissen per seconde van ingang. Event Hubs worden standaard doorvoer eenheden geschaald wanneer u de limiet bereikt met de functie voor [automatisch verg Roten](event-hubs-auto-inflate.md) . deze functie werkt ook met de Event Hubs voor de functie Kafka. 

### <a name="security-and-authentication"></a>Verificatie en beveiliging

Azure Event Hubs vereist SSL of TLS voor alle communicatie en maakt gebruik van Shared Access signatures (SAS) voor verificatie. Deze vereiste geldt ook voor een Kafka-eind punt binnen Event Hubs. Voor compatibiliteit met Kafka gebruikt Event Hubs SASL PLAIN voor verificatie en SASL SSL voor transport beveiliging. Zie [Event hubs-verificatie en-beveiliging](event-hubs-authentication-and-security-model-overview.md)voor meer informatie over beveiliging in Event hubs.

## <a name="other-event-hubs-features-available-for-kafka"></a>Andere Event Hubs-functies die beschikbaar zijn voor Kafka

Met de functie Event Hubs voor Kafka kunt u met één protocol schrijven en met een andere en lezen, zodat uw huidige Kafka-producenten kunnen blijven publiceren via Kafka, en u kunt lezers toevoegen met Event Hubs, zoals Azure Stream Analytics of Azure Functions. Daarnaast werkt Event Hubs functies, zoals [vastleggen](event-hubs-capture-overview.md) en [geo-herstel na nood gevallen](event-hubs-geo-dr.md) , ook met de Event hubs voor de functie Kafka.

## <a name="features-that-are-not-yet-supported"></a>Functies die nog niet worden ondersteund 

Hier volgt een lijst met Kafka-functies die nog niet worden ondersteund:

*   Idempotent producer
*   trans actie
*   Compressie
*   Bewaar periode op basis van grootte
*   Logboek compressie
*   Partities toevoegen aan een bestaand onderwerp
*   HTTP Kafka API-ondersteuning
*   Kafka stromen

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een inleiding tot Event Hubs voor Kafka beschreven. Ga voor meer informatie naar de volgende koppelingen:

- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een event hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Apache Flink verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-flink-tutorial.md)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Kafka Connect integreren met een Event Hub waarvoor Kafka is ingeschakeld)
- [Akka Streams verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)


