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
ms.openlocfilehash: 67ee882acab22d977f08124591289e9cfc7cded1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81261819"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Quick Start: gegevensstreaming met Event Hubs met behulp van het Kafka-Protocol
In deze Quick start ziet u hoe u kunt streamen naar Event Hubs zonder dat u uw protocol-clients of uw eigen clusters hoeft te wijzigen. U leert hoe u uw producenten en consumenten kunt gebruiken om te praten met Event Hubs met alleen een configuratie wijziging in uw toepassingen. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Dit voor beeld is beschikbaar op [github](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door.
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks).
* [Down load](https://maven.apache.org/download.cgi) en [Installeer](https://maven.apache.org/install.html) een binaire maven-archief.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Wanneer u een **standaardlaag Event hubs** naam ruimte maakt, wordt het Kafka-eind punt voor de naam ruimte automatisch ingeschakeld. U kunt gebeurtenissen streamen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in de standaardlaag Event Hubs. Volg de stapsgewijze instructies in het **artikel** [een event hub maken met behulp van Azure Portal](event-hubs-create.md) om een standaardlaag Event hubs naam ruimte te maken. 

> [!NOTE]
> Event Hubs voor Kafka is alleen beschikbaar in de lagen **Standard** en **dedicated** . De laag **basis** biedt geen ondersteuning voor Kafka op Event hubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Berichten verzenden en ontvangen met Kafka in Event Hubs

1. Kloon de [Azure Event Hubs for Kafka-opslagplaats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Werk de configuratiegegevens voor de producent in `src/main/resources/producer.config` als volgt bij:

    **TLS/SSL:**

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
4. Voer de code van de producent en stream uit in Event Hubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Werk de configuratiegegevens voor de consument in `src/main/resources/consumer.config` als volgt bij:
   
    **TLS/SSL:**

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
7. Voer de code en proces gebeurtenissen van de consument uit Event Hub met behulp van uw Kafka-clients:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Als uw Event Hubs Kafka-cluster gebeurtenissen heeft, moet u deze nu ontvangen van de consument.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u kunt streamen naar Event Hubs zonder uw protocol-clients te wijzigen of uw eigen clusters uit te voeren. Zie [Apache Kafka ontwikkelaars handleiding voor Azure Event hubs](apache-kafka-developer-guide.md)voor meer informatie. 
