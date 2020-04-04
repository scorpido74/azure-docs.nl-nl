---
title: 'Snelstart: streaming van gegevens met Azure Event Hubs met het Kafka-protocol'
description: "Snelstart: in dit artikel vindt u informatie over het streamen naar Azure Event Hubs met behulp van het Kafka-protocol en API's."
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 89d2c16e3f54d3170b4f0d23590294be13dd8caa
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632754"
---
# <a name="quickstart-data-streaming-with-event-hubs-using-the-kafka-protocol"></a>Snelstart: streaming van gegevens met gebeurtenishubs met behulp van het Kafka-protocol
Deze quickstart laat zien hoe je streamen naar Event Hubs zonder je protocolclients te wijzigen of je eigen clusters uit te voeren. U leert hoe u uw producenten en consumenten gebruiken om met Event Hubs te praten met slechts een configuratiewijziging in uw toepassingen. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Dit voorbeeld is beschikbaar op [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java)

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Lees het artikel [Event Hubs voor Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) door.
* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een Maven binair archief.
* [Git](https://www.git-scm.com/)


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Wanneer u een naamruimte voor **standaardgebeurtenishubs** maakt, wordt het Kafka-eindpunt voor de naamruimte automatisch ingeschakeld. U gebeurtenissen streamen vanuit uw toepassingen die het Kafka-protocol gebruiken naar gebeurtenishubs met standaardniveau. Volg stapsgewijze instructies in de [gebeurtenishub maken met Azure-portal](event-hubs-create.md) om een naamruimte voor **standaardniveaugebeurtenishubs** te maken. 

> [!NOTE]
> Event Hubs voor Kafka is alleen beschikbaar op **standaard** en **dedicated** lagen. De **basislaag** biedt geen ondersteuning voor Kafka op gebeurtenishubs.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Berichten verzenden en ontvangen met Kafka in Event Hubs

1. Kloon de [Azure Event Hubs for Kafka-opslagplaats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Werk de configuratiegegevens voor de producent in `src/main/resources/producer.config` als volgt bij:

    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ```    

    U vindt de broncode voor de voorbeeldhandlerklasse CustomAuthenticateCallbackHandler op GitHub [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/producer/src/main/java).
4. Voer de producentencode uit en stream gebeurtenissen naar gebeurtenishubs:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Werk de configuratiegegevens voor de consument in `src/main/resources/consumer.config` als volgt bij:
   
    **Ssl:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

    **Oauth:**

    ```xml
    bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=OAUTHBEARER
    sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
    sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
    ``` 

    U vindt de broncode voor de voorbeeldhandlerklasse CustomAuthenticateCallbackHandler op GitHub [hier](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/appsecret/consumer/src/main/java).

    U vindt alle OAuth monsters voor Event Hubs voor Kafka [hier.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)
7. Voer de consumentencode uit en verwerk gebeurtenissen vanaf de gebeurtenishub met uw Kafka-klanten:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Als uw Event Hubs Kafka-cluster gebeurtenissen heeft, moet u deze nu ontvangen van de consument.

## <a name="next-steps"></a>Volgende stappen
In dit artikel heb je geleerd hoe je streamen naar Event Hubs zonder je protocolclients te wijzigen of je eigen clusters uit te voeren. Zie [de ontwikkelaarshandleiding van Apache Kafka voor Azure Event Hubs voor](apache-kafka-developer-guide.md)meer informatie. 
