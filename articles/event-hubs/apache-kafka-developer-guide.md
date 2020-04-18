---
title: Apache Kafka-ontwikkelaarshandleiding voor Event Hubs
description: In dit artikel vindt u koppelingen naar artikelen waarin wordt beschreven hoe u uw Kafka-toepassingen integreren met Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: d90bf8efab28624672dcedb5bf53d45052dd9123
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605128"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka-ontwikkelaarshandleiding voor Azure Event Hubs
In dit artikel vindt u koppelingen naar artikelen waarin wordt beschreven hoe u uw Apache Kafka-toepassingen integreren met Azure Event Hubs. 

## <a name="overview"></a>Overzicht
Event Hubs biedt een Kafka-eindpunt dat door uw bestaande Kafka-gebaseerde toepassingen kan worden gebruikt als alternatief voor het uitvoeren van uw eigen Kafka-cluster. Event Hubs ondersteunt Apache Kafka protocol 1.0 en hoger en werkt met uw bestaande Kafka-toepassingen, waaronder MirrorMaker. Zie [Gebeurtenishubs voor Apache Kafka voor](event-hubs-for-kafka-ecosystem-overview.md) meer informatie

## <a name="quickstarts"></a>Snelstartgidsen
Je snel beginnen in GitHub en in deze contentset waarmee je snel opvoeren op Event Hubs voor Kafka.

### <a name="quickstarts-in-github"></a>Snel starten in GitHub
Bekijk de volgende quickstarts in de **azure-event-hubs-for-kafka** repo: 

| Klanttaal/framework | Beschrijving | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Deze quickstart laat zien hoe u een Endpoint van Gebeurtenishubs Kafka maken en verbinden met behulp van een voorbeeldproducent en consument die in C# is geschreven met behulp van .NET Core 2.0.</p><p>Dit voorbeeld is gebaseerd op [confluent's Apache Kafka .NET client](https://github.com/confluentinc/confluent-kafka-dotnet), aangepast voor gebruik met Event Hubs voor Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Deze quickstart laat zien hoe u een Endpoint van Gebeurtenishubs Kafka maken en er verbinding maken met een voorbeeldproducent en consument die op Java is geschreven. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Deze quickstart laat zien hoe u een endpoint van Gebeurtenishubs Kafka maken en er verbinding maken met een voorbeeldproducent en consument die in Node is geschreven.</p><p>In dit voorbeeld wordt de [bibliotheek node-rdkafka](https://github.com/Blizzard/node-rdkafka) gebruikt. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Deze quickstart laat zien hoe u een Endpoint van Gebeurtenishubs Kafka maken en er verbinding maken met een voorbeeldproducent en consument die in python is geschreven.</p><p>Dit voorbeeld is gebaseerd op [confluent's Apache Kafka Python client](https://github.com/confluentinc/confluent-kafka-python), aangepast voor gebruik met Event Hubs voor Kafka.</p>|
| [OK](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Deze quickstart laat zien hoe u een Endpoint van Gebeurtenishubs Kafka maken en er verbinding maken met een voorbeeldproducent en consument die in Go is geschreven.</p><p>Dit voorbeeld is gebaseerd op [confluent's Apache Kafka Golang client](https://github.com/confluentinc/confluent-kafka-go), aangepast voor gebruik met Event Hubs voor Kafka.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Deze quickstart laat zien hoe u een Endpoint van Event Hubs Kafka maken en verbinden met behulp van een voorbeeldproducent en consument die in Go is geschreven met behulp van de [Sarama Kafka-clientbibliotheek.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Deze quickstart laat zien hoe u een Kafka-eindpunt voor gebeurtenishubs maken en er verbinding maken met de CLI die wordt geleverd met de Apache Kafka-distributie.| 
| [Kafkacat Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat is een niet-JVM command-line consument en producent op basis van librdkafka, populair vanwege zijn snelheid en kleine voetafdruk. Deze quickstart bevat een voorbeeldconfiguratie en een aantal eenvoudige voorbeeldopdrachten voor kafkacat. | 
 
### <a name="quickstarts-in-docs"></a>Snel starten in DOCUMENTEN
Bekijk de quickstart: [Gegevensstreamen met Gebeurtenishubs met behulp van het Kafka-protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md) in deze inhoudsset, die stapsgewijze instructies biedt over het streamen naar gebeurtenishubs. U leert hoe u uw producenten en consumenten gebruiken om met Event Hubs te praten met slechts een configuratiewijziging in uw toepassingen. 


## <a name="tutorials"></a>Zelfstudies 

### <a name="tutorials-in-github"></a>Zelfstudies in GitHub
Bekijk de volgende tutorials op GitHub:

| Zelfstudie | Beschrijving | 
| ------------------------- | ----------- | 
| [Akka Akka (Akka)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | In deze zelfstudie ziet u hoe u Akka Streams verbinden met evenementenhubs met Kafka zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Er zijn twee afzonderlijke tutorials met **java-** en scala-programmeertalen. **Scala** | 
| [Verbinden](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Dit document begeleidt u bij de integratie van Kafka Connect met Azure Event Hubs en het implementeren van basisConnectors voor FileStreamSource en FileStreamSink. Hoewel deze connectors niet bedoeld zijn voor productiegebruik, tonen ze een end-to-end Kafka Connect-scenario aan waarin Azure Event Hubs zich voordoen als een Kafka-broker.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Dit document begeleidt u door de integratie van Filebeat en Event Hubs via Filebeat's Kafka-uitvoer. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | In deze zelfstudie wordt uitgelegd hoe u Apache Flink verbinden met evenementenhubs met Kafka zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. | 
| [Vloeiend](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Dit document zal u door het integreren van `out_kafka` Fluentd en Event Hubs met behulp van de output plugin voor Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | In deze zelfstudie ziet u hoe u gebeurtenissen uitwisselen tussen consumenten en producenten met behulp van verschillende protocollen. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Deze zelfstudie zal u door het integreren van Logstash met Kafka-enabled Event Hubs met behulp van Logstash Kafka input / output plugins. | 
| [Spiegelmaker (Spiegelmaker)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | In deze zelfstudie ziet u hoe een gebeurtenishub en Kafka MirrorMaker een bestaande Kafka-pijplijn in Azure kunnen integreren door de Kafka-invoerstream in de gebeurtenishubs-service te spiegelen. |
| [NiFi (NiFi)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | In deze zelfstudie wordt uitgelegd hoe u Apache NiFi verbinden met een naamruimte voor gebeurtenishubs. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Quickstarts laten u zien hoe u een Kafka-eindpunt voor gebeurtenishubs maakt en verbinding maakt met behulp van een voorbeeldproducent en consument die is geschreven in de programmeertalen Go en Java. |
| [Confluent's Schema Register](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Deze zelfstudie leert je door het integreren van Schema Registry en Event Hubs voor Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | In deze zelfstudie wordt uitgelegd hoe u uw Spark-toepassing verbinden met een gebeurtenishub zonder uw protocolclients te wijzigen of uw eigen Kafka-clusters uit te voeren. | 

### <a name="tutorials-in-docs"></a>Zelfstudies in DOCUMENTEN
Zie ook de zelfstudie: [Apache Kafka verwerken voor gebeurtenissen in eventhubs met Stream-analyses](event-hubs-kafka-stream-analytics.md) in deze inhoudsset, waarmee wordt weergegeven hoe u gegevens streamen naar gebeurtenishubs en deze verwerken met Azure Stream Analytics.

## <a name="how-to-guides"></a>Instructiegidsen
Zie de volgende Handleidingen in onze documentatie:

| Artikel | Beschrijving | 
| ------- | ----------- | 
| [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md) | Laat zien hoe je een Kafka-makelaar spiegelt in een gebeurtenishub met Kafka MirrorMaker. |
| [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md) | Begeleidt u door het aansluiten van uw Spark-applicatie met Event Hubs voor real-time streaming. |
| [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md) | Laat u zien hoe u Apache Flink verbinden met een gebeurtenishub zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. |
| [Apache Kafka Connect integreren met een gebeurtenishub (Voorbeeld)](event-hubs-kafka-connect-tutorial.md) | Begeleidt u door de integratie van Kafka Connect met een gebeurtenishub en het implementeren van basisConnectors voor FileStreamSource en FileStreamSink. |
| [Akka-streams verbinden met een Event Hub](event-hubs-kafka-akka-streams-tutorial.md) | Laat u zien hoe u Akka Streams verbinden met een gebeurtenishub zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. |
| [De springbootstarter voor Apache Kafka gebruiken met Azure Event Hubs](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Laat zien hoe u een op Java gebaseerde Spring Cloud Stream Binder configureert die is gemaakt met de Initializer voor springboot om Apache Kafka te gebruiken met Azure Event Hubs. |

## <a name="next-steps"></a>Volgende stappen
Bekijk voorbeelden in de GitHub repo [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) onder quickstart- en zelfstudies.

Zie ook de volgende artikelen:

- [Handleiding voor het oplossen van problemen met Apache Kafka voor gebeurtenishubs](apache-kafka-troubleshooting-guide.md)
- [Veelgestelde vragen - Event Hubs voor Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Apache Kafka migratiegids voor Event Hubs](apache-kafka-migration-guide.md)



