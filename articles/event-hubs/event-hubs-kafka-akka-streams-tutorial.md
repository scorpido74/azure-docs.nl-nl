---
title: Akkastreams gebruiken voor Apache Kafka - Azure Event Hubs| Microsoft Documenten
description: In dit artikel vindt u informatie over het verbinden van Akka Streams met een Azure-gebeurtenishub.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c53fcc7b4b99e77237ba7ea9219aef4182f2af61
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521822"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Akka Streams gebruiken met Event Hubs voor Apache Kafka
In deze zelfstudie ziet u hoe u Akka Streams verbinden met een gebeurtenishub zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Azure Event Hubs voor de Kafka ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html)

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Run Akka Streams producent 
> * Run Akka Streams consument

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, moet u de volgende vereisten hebben:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een naamruimte voor Gebeurtenishubs is vereist om te verzenden of te ontvangen van een Event Hubs-service. Zie [Een gebeurtenishub maken](event-hubs-create.md) voor gedetailleerde informatie. Zorg ervoor dat u de verbindingstekenreeks Voor gebeurtenishubs kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een verbindingstekenreeks voor gebeurtenishubs hebt, kloont u de `akka` Azure Event Hubs voor kafka-opslagplaats en navigeert u naar de submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Run Akka Streams producent

Met behulp van het meegeleverde voorbeeld van de houder van De Streams van Akka Streams stuurt u berichten naar de service Gebeurtenishubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een eindpunt van gebeurtenishubs Kafka geven

#### <a name="producer-applicationconf"></a>Producent application.conf

Werk `bootstrap.servers` de `sasl.jaas.config` waarden `producer/src/main/resources/application.conf` en waarden in om de producent naar het Kafka-eindpunt van gebeurtenishubs te leiden met de juiste verificatie.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Producent uitvoeren vanaf de opdrachtregel

Als u de producent vanaf de opdrachtregel wilt uitvoeren, genereert u de JAR en loopt u vervolgens vanuit Maven (of genereert u de JAR met Maven en wordt u op Java uitgevoerd door de nodige Kafka JAR(s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

De producent begint met het verzenden `test`van evenementen naar de gebeurtenishub op het onderwerp en drukt de gebeurtenissen af op stdout.

## <a name="run-akka-streams-consumer"></a>Run Akka Streams consument

Ontvang berichten van de gebeurtenishub via het meegeleverde consumentenvoorbeeld.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een eindpunt van gebeurtenishubs Kafka geven

#### <a name="consumer-applicationconf"></a>Consumentenapplicatie.conf

Werk `bootstrap.servers` de `sasl.jaas.config` waarden `consumer/src/main/resources/application.conf` en waarden in om de consument naar het Kafka-eindpunt van gebeurtenishubs te leiden met de juiste verificatie.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Voer de consument uit de opdrachtregel

Als u de consument vanaf de opdrachtregel wilt uitvoeren, genereert u de JAR en loopt u vervolgens vanuit Maven (of genereert u de JAR met Maven en wordt u op Java uitgevoerd door de nodige Kafka JAR(s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Als de gebeurtenishub gebeurtenissen heeft (bijvoorbeeld als uw producent ook actief is), begint de consument gebeurtenissen van onderwerp `test`te ontvangen. 

Bekijk de [Akka Streams Kafka Guide](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) voor meer gedetailleerde informatie over Akka Streams.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heb je geleerd hoe je Akka Streams verbinden met de gebeurtenishub zonder je protocolclients te wijzigen of je eigen clusters uit te voeren. Azure Event Hubs voor de Kafka ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html). Je hebt de volgende acties als onderdeel van deze tutorial: 

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Run Akka Streams producent 
> * Run Akka Streams consument

Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Een gebeurtenishub maken](event-hubs-create.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark aan een Event Hub koppelen](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink aan een Event Hub koppelen](event-hubs-kafka-flink-tutorial.md)
- [Integreer Kafka Connect met een gebeurtenishub](event-hubs-kafka-connect-tutorial.md)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
