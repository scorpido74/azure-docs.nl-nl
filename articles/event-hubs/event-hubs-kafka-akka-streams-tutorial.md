---
title: Akka-streams gebruiken voor Apache Kafka-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het verbinden van Akka-streams met een Apache Kafka ingeschakelde Azure Event Hub.
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
ms.openlocfilehash: ba81ce88bcdf039d020dcd945e45a11cf603c114
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555748"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Akka-streams gebruiken met Event Hubs voor Apache Kafka
In deze zelf studie ziet u hoe u Akka-streams verbindt met gebeurtenis hubs met Kafka-functionaliteit zonder uw protocol-clients of uw eigen clusters te wijzigen. Azure Event Hubs voor de Kafka ondersteunt [Apache Kafka versie 1,0.](https://kafka.apache.org/10/documentation.html)

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Akka-streams uitvoeren 
> * Akka streams Consumer uitvoeren

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, moet u beschikken over de volgende vereisten:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door. 
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Voer op Ubuntu `apt-get install default-jdk` uit om de JDK te installeren.
    * Zorg dat de omgevingsvariabele JAVA_HOME verwijst naar de map waarin de JDK is geïnstalleerd.
* Een binair archief van Maven [downloaden](https://maven.apache.org/download.cgi) en [installeren](https://maven.apache.org/install.html)
    * Op Ubuntu kunt u `apt-get install maven` uitvoeren om Maven te installeren.
* [Git](https://www.git-scm.com/downloads)
    * Op Ubuntu kunt u `sudo apt-get install git` uitvoeren om Git te installeren.

## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken

Een Event Hubs naam ruimte is vereist voor het verzenden of ontvangen van een Event Hubs service. Zie [Kafka ingeschakeld Event hubs maken](event-hubs-create-kafka-enabled.md) voor informatie over het ophalen van een event hubs Kafka-eind punt. Zorg ervoor dat u de Event Hubs-connection string kopieert voor later gebruik.

## <a name="clone-the-example-project"></a>Het voorbeeldproject klonen

Nu u een Event Hubs connection string hebt ingeschakeld, kloont u de Azure Event Hubs voor Kafka-opslag plaats en navigeert u naar de `akka` submap:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Akka-streams uitvoeren

Gebruik het meegeleverde Akka streams producer-voor beeld om berichten naar de Event Hubs-service te verzenden.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="producer-applicationconf"></a>Producer Application. conf

Werk de `bootstrap.servers`-en `sasl.jaas.config` waarden in `producer/src/main/resources/application.conf` bij om de producent om te leiden naar het Event Hubs Kafka-eind punt met de juiste authenticatie.

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

### <a name="run-producer-from-the-command-line"></a>Producer uitvoeren vanaf de opdracht regel

Als u de producent wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of Genereer u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

De producent begint met het verzenden van gebeurtenissen naar het Kafka-Event Hub in het onderwerp `test` en drukt de gebeurtenissen op stdout.

## <a name="run-akka-streams-consumer"></a>Akka streams Consumer uitvoeren

Gebruik het voor beeld van de meegeleverde gebruiker om berichten te ontvangen van de Event hubs met Kafka-functionaliteit.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Een Event Hubs Kafka-eind punt opgeven

#### <a name="consumer-applicationconf"></a>Consumer Application. conf

Werk de `bootstrap.servers`-en `sasl.jaas.config` waarden in `consumer/src/main/resources/application.conf` bij om de consumer naar het Event Hubs Kafka-eind punt met de juiste verificatie te sturen.

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

### <a name="run-consumer-from-the-command-line"></a>Consument uitvoeren vanaf de opdracht regel

Als u de gebruiker wilt uitvoeren vanaf de opdracht regel, genereert u het JAR en voert u uit in maven (of genereert u het JAR met Maven, voert u in Java uit door de benodigde Kafka JAR (s) toe te voegen aan het klassenpad):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Als de met Kafka ingeschakelde Event Hub gebeurtenissen heeft (bijvoorbeeld als uw producent ook wordt uitgevoerd), begint de Consumer gebeurtenissen te ontvangen van het onderwerp `test`. 

Bekijk de [Akka-hand leiding voor Kafka-stromen](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) voor meer gedetailleerde informatie over Akka-streams.

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u Akka-streams verbindt met Event hubs met Kafka-functionaliteit zonder uw protocol-clients of uw eigen clusters te wijzigen. Azure Event Hubs voor de Kafka ondersteunt [Apache Kafka versie 1,0.](https://kafka.apache.org/10/documentation.html). U hebt de volgende acties uitgevoerd als onderdeel van deze zelf studie: 

> [!div class="checklist"]
> * Een Event Hubs-naamruimte maken
> * Het voorbeeldproject klonen
> * Akka-streams uitvoeren 
> * Akka streams Consumer uitvoeren

Zie het volgende onderwerp voor meer informatie over Event Hubs en Event Hubs voor Kafka:  

- [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Event Hubs maken waarvoor Kafka is ingeschakeld](event-hubs-create-kafka-enabled.md)
- [Streamen naar Event Hubs vanaf uw Kafka-toepassingen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Een Kafka-broker spiegelen in een event hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connect Apache Spark to a Kafka-enabled event hub](event-hubs-kafka-spark-tutorial.md) (Apache Spark verbinden met een Event Hub waarvoor Kafka is ingeschakeld)
- [Apache Flink verbinden met een Event Hub waarvoor Kafka is ingeschakeld](event-hubs-kafka-flink-tutorial.md)
- [Integrate Kafka Connect with a Kafka-enabled event hub](event-hubs-kafka-connect-tutorial.md) (Kafka Connect integreren met een Event Hub waarvoor Kafka is ingeschakeld)
- [Explore samples on our GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) (Voorbeelden bekijken op GitHub)
