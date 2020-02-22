---
title: 'Quick Start: gegevensstreamen met Azure Event Hubs met behulp van het Kafka-Protocol'
description: "Snelstartgids: in dit artikel vindt u informatie over het streamen naar Azure Event Hubs met het Kafka-protocol en Api's."
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: dd3c7a55d1b3d39f8a16b1730d861edf0de84690
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539566"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Quick Start: gegevensstreaming met Event Hubs met behulp van het Kafka-Protocol
Deze snelstart laat zien hoe u kunt streamen naar Event Hubs waarvoor Kafka is ingeschakeld zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. U leert hoe u uw producenten kunt gebruiken en consumenten met Event Hubs waarvoor Kafka is ingeschakeld, kunnen praten met slechts een configuratiewijziging in uw toepassingen. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door.
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief.
* [Git](https://www.git-scm.com/)


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken waarvoor Kafka is ingeschakeld
Wanneer u een standaardlaag Event Hubs naam ruimte maakt, wordt het Kafka-eind punt voor de naam ruimte automatisch ingeschakeld. U kunt gebeurtenissen streamen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in de standaardlaag Event Hubs. Het is niet ingeschakeld voor de laag basis Event Hubs naam ruimte. Volg de stapsgewijze instructies in het **artikel** [een event hub maken met behulp van Azure Portal](event-hubs-create.md) om een standaardlaag Event hubs naam ruimte te maken. 

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Berichten verzenden en ontvangen met Kafka in Event Hubs

1. Kloon de [Azure Event Hubs for Kafka-opslagplaats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Werk de configuratiegegevens voor de producent in `src/main/resources/producer.config` als volgt bij:

    **-**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    U kunt [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java)de bron code voor de voor beeld-handler klasse CustomAuthenticateCallbackHandler in github vinden.
4. Voer de code van de producent en de stream-gebeurtenissen uit in Kafka ingeschakeld Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Werk de configuratiegegevens voor de consument in `src/main/resources/consumer.config` als volgt bij:
   
    **-**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **OAuth**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    U kunt [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java)de bron code voor de voor beeld-handler klasse CustomAuthenticateCallbackHandler in github vinden.

    U vindt [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)alle OAuth-voor beelden voor Event hubs voor Kafka.
7. Voer de consumentcode en het proces uit vanuit Event Hubs waarvoor Kafka is ingeschakeld met behulp van uw Kafka-clients:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Als uw Event Hubs Kafka-cluster gebeurtenissen heeft, moet u deze nu ontvangen van de consument.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd u hoe u kunt streamen naar Event Hubs waarvoor Kafka is ingeschakeld zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Raadpleeg de volgende artikelen en voor beelden voor meer informatie:

- [Meer informatie over Event Hubs for Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Quick starts voor Event Hubs voor Kafka op GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart)
- [Zelf studies voor Event Hubs voor Kafka op GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials)
- Gebruik [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) voor het [streamen van gebeurtenissen van Kafka on premises naar Kafka ingeschakeld Event hubs in de Cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
- Meer informatie over streamen naar Kafka waarvoor Event Hubs is ingeschakeld, met behulp van [Apache Flink](event-hubs-kafka-flink-tutorial.md) of [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
