---
title: Apache flink gebruiken voor Apache Kafka-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het verbinden van Apache flink met een Apache Kafka ingeschakelde Azure-Event Hub
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 881546a97b01bef993cc24c6b868ec97ddf5ac36
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555713"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache flink gebruiken met Azure Event Hubs voor Apache Kafka
In deze zelf studie wordt uitgelegd hoe u verbinding kunt maken met Apache flink met Kafka-Event hubs zonder uw protocol-clients of uw eigen clusters te wijzigen. Azure Event Hubs ondersteunt [Apache Kafka versie 1,0.](https://kafka.apache.org/10/documentation.html).

Een van de belangrijkste voor delen van het gebruik van Apache Kafka is het ecosysteem van frameworks waarmee het kan worden verbonden. Kafka ingeschakeld Event Hubs combineert de flexibiliteit van Kafka met de schaal baarheid, consistentie en ondersteuning van het Azure-ecosysteem.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Flink-producer uitvoeren 
> * Flink-consument uitvoeren

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u beschikken over de volgende vereisten:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* Een binair archief van Maven [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een Event Hubs naam ruimte is vereist voor het verzenden of ontvangen van een Event Hubs service. Zie [Kafka ingeschakeld Event hubs maken](event-hubs-create-kafka-enabled.md) voor informatie over het ophalen van een event hubs Kafka-eind punt. Zorg ervoor dat u de Event Hubs-connection string kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een Event Hubs connection string hebt ingeschakeld, kloont u de Azure Event Hubs voor Kafka-opslag plaats en navigeert u naar de `flink` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Flink-producer uitvoeren

Gebruik het meegeleverde flink producer-voor beeld om berichten te verzenden naar de Event Hubs-service.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="producerconfig"></a>producer. config

Werk de `bootstrap.servers`-en `sasl.jaas.config` waarden in `producer/src/main/resources/producer.config` bij om de producent om te leiden naar het Event Hubs Kafka-eind punt met de juiste authenticatie.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Producer uitvoeren vanaf de opdracht regel

Als u de producent wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of Genereer u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

De producent begint nu met het verzenden van gebeurtenissen naar de Kafka ingeschakelde Event hub in het onderwerp `test` en het afdrukken van de gebeurtenissen naar stdout.

## <a name="run-flink-consumer"></a>Flink-consument uitvoeren

Gebruik het voor beeld van de meegeleverde gebruiker om berichten te ontvangen van de Kafka ingeschakeld Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="consumerconfig"></a>Consumer. config

Werk de `bootstrap.servers`-en `sasl.jaas.config` waarden in `consumer/src/main/resources/consumer.config` bij om de consumer naar het Event Hubs Kafka-eind punt met de juiste verificatie te sturen.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Consument uitvoeren vanaf de opdracht regel

Als u de gebruiker wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of genereert u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Als de met Kafka ingeschakelde Event Hub gebeurtenissen bevat (bijvoorbeeld als uw producent ook wordt uitgevoerd), begint de Consumer nu met het ontvangen van gebeurtenissen in het onderwerp `test`.

Bekijk [de Kafka-connector gids van flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) voor meer gedetailleerde informatie over het verbinden van flink met Kafka.

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u Apache flink verbindt met Event hubs met Kafka-functionaliteit zonder uw protocol-clients of uw eigen clusters te wijzigen. U hebt de volgende stappen uitgevoerd als onderdeel van deze zelf studie: 

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Flink-producer uitvoeren 
> * Flink-consument uitvoeren

Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een event hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Kafka Connect integreren met een Event Hub waarvoor Kafka is ingeschakeld)
- [Akka Streams verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
