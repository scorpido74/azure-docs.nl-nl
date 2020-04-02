---
title: Apache Flink gebruiken voor Apache Kafka - Azure Event Hubs | Microsoft Documenten
description: In dit artikel vindt u informatie over het verbinden van Apache Flink met een Azure-gebeurtenishub
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6ab542e1328bb986f53d31e2eca75007cf1e0c75
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521790"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Apache Flink gebruiken met Azure Event Hubs voor Apache Kafka
In deze zelfstudie ziet u hoe u Apache Flink verbinden met een gebeurtenishub zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html).

Een van de belangrijkste voordelen van het gebruik van Apache Kafka is het ecosysteem van frameworks waarmee het verbinding kan maken. Event Hubs combineert de flexibiliteit van Kafka met de schaalbaarheid, consistentie en ondersteuning van het Azure-ecosysteem.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Run Flink producent 
> * Run Flink consument

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, moet u de volgende vereisten hebben:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een naamruimte voor Gebeurtenishubs is vereist om te verzenden of te ontvangen van een Event Hubs-service. Zie [Een gebeurtenishub maken](event-hubs-create.md) voor instructies voor het maken van een naamruimte en een gebeurtenishub. Zorg ervoor dat u de verbindingstekenreeks Voor gebeurtenishubs kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u de verbindingstekenreeks Event Hubs hebt, kloont u de `flink` Azure Event Hubs voor Kafka-opslagplaats en navigeert u naar de submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Run Flink producent

Met behulp van het meegeleverde voorbeeld van Flink-producent stuur je berichten naar de Event Hubs-service.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een eindpunt van gebeurtenishubs Kafka geven

#### <a name="producerconfig"></a>producent.config

Werk `bootstrap.servers` de `sasl.jaas.config` waarden `producer/src/main/resources/producer.config` en waarden in om de producent naar het Kafka-eindpunt van gebeurtenishubs te leiden met de juiste verificatie.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Producent uitvoeren vanaf de opdrachtregel

Als u de producent vanaf de opdrachtregel wilt uitvoeren, genereert u de JAR en loopt u vervolgens vanuit Maven (of genereert u de JAR met Maven en wordt u op Java uitgevoerd door de nodige Kafka JAR(s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

De producent zal nu beginnen met het `test` verzenden van evenementen naar de gebeurtenis hub op het onderwerp en het afdrukken van de gebeurtenissen naar stdout.

## <a name="run-flink-consumer"></a>Run Flink consument

Ontvang berichten van de gebeurtenishub via het meegeleverde consumentenvoorbeeld. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een eindpunt van gebeurtenishubs Kafka geven

#### <a name="consumerconfig"></a>consumer.config

Werk `bootstrap.servers` de `sasl.jaas.config` waarden `consumer/src/main/resources/consumer.config` en waarden in om de consument naar het Kafka-eindpunt van gebeurtenishubs te leiden met de juiste verificatie.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Voer de consument uit de opdrachtregel

Als u de consument vanaf de opdrachtregel wilt uitvoeren, genereert u de JAR en loopt u vervolgens vanuit Maven (of genereert u de JAR met Maven en wordt u op Java uitgevoerd door de nodige Kafka JAR(s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Als de gebeurtenishub gebeurtenissen heeft (bijvoorbeeld als uw producent ook actief is), `test`begint de consument nu gebeurtenissen van het onderwerp te ontvangen.

Bekijk [flink's Kafka Connector Guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) voor meer gedetailleerde informatie over het verbinden van Flink met Kafka.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heb je geleerd hoe je Apache Flink verbinden met Event Hubs zonder je protocolclients te wijzigen of je eigen clusters uit te voeren. U hebt de volgende stappen uitgevoerd als onderdeel van deze zelfstudie: 

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Run Flink producent 
> * Run Flink consument

Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Een Event Hub maken](event-hubs-create.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Integreer Kafka Connect met een gebeurtenishub](event-hubs-kafka-connect-tutorial.md)
- [Akka Streams verbinden met een gebeurtenishub](event-hubs-kafka-akka-streams-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
