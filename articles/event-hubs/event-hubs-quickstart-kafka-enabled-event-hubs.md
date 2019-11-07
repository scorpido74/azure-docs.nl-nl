---
title: 'Quick Start: gegevensstreamen met Azure Event Hubs met behulp van het Kafka-Protocol'
description: "Snelstartgids: in dit artikel vindt u informatie over het streamen naar Azure Event Hubs met het Kafka-protocol en Api's."
services: event-hubs
author: ShubhaVijayasarathy
ms.author: shvija
ms.service: event-hubs
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.openlocfilehash: 2222345054982799f9f9e0b84961271a3cc04ddf
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717820"
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
* [Een Event Hubs-naamruimte waarvoor Kafka is ingeschakeld](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken waarvoor Kafka is ingeschakeld

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com) en klik op **Een resource maken** linksboven in het scherm.

2. Zoek naar Event Hubs en selecteer de hier getoonde opties:
    
    ![Zoeken naar Event Hubs in de portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Geef een unieke naam op en schakel Kafka in op de naamruimte. Klik op **Maken**. Opmerking: Event Hubs voor Kafka wordt alleen ondersteund door de standaard-en toegewezen laag Event Hubs. Door Event Hubs van de categorie Basic wordt een onderwerpautorisatiefout geretourneerd als reactie op Kafka-bewerkingen.
    
    ![Een naamruimte maken](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. Als de naamruimte is gemaakt, klikt u op het tabblad **Instellingen** op **Beleid voor gedeelde toegang** om de verbindingsreeks op te halen.

    ![Klikken op Beleid voor gedeelde toegang](./media/event-hubs-create/create-event-hub7.png)

5. U kunt de standaardwaarde **RootManageSharedAccessKey** kiezen of een nieuwe beleid toevoegen. Klik op de beleidsnaam en kopieer de verbindingsreeks. 
    
    ![Beleid selecteren](./media/event-hubs-create/create-event-hub8.png)
 
6. Voeg deze verbindingsreeks toe aan de Kafka-toepassingsconfiguratie.

U kunt nu gebeurtenissen vanaf uw toepassing, waarbij gebruikgemaakt wordt van het Kafka-protocol, naar Event Hubs streamen.

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Berichten verzenden en ontvangen met Kafka in Event Hubs

1. Kloon de [Azure Event Hubs for Kafka-opslagplaats](https://github.com/Azure/azure-event-hubs-for-kafka).

2. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer`.

3. Werk de configuratiegegevens voor de producent in `src/main/resources/producer.config` als volgt bij:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
    
4. Voer de producentcode uit en stream deze naar Event Hubs waarvoor Kafka is ingeschakeld.
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
    
5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/consumer`.

6. Werk de configuratiegegevens voor de consument in `src/main/resources/consumer.config` als volgt bij:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Voer de consumentcode en het proces uit vanuit Event Hubs waarvoor Kafka is ingeschakeld met behulp van uw Kafka-clients:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Als uw Event Hubs Kafka-cluster gebeurtenissen heeft, moet u deze nu ontvangen van de consument.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd u hoe u kunt streamen naar Event Hubs waarvoor Kafka is ingeschakeld zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Ga verder met de volgende zelfstudie voor meer informatie:

* [Meer informatie over Event Hubs](event-hubs-what-is-event-hubs.md)
* [Meer informatie over Event Hubs for Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Meer voorbeelden van de Event Hubs for Kafka-GitHub verkennen](https://github.com/Azure/azure-event-hubs-for-kafka)
* Gebruik [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) voor het [streamen van gebeurtenissen van Kafka on premises naar Kafka ingeschakeld Event hubs in de Cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* Meer informatie over streamen naar Kafka waarvoor Event Hubs is ingeschakeld, met behulp van [Apache Flink](event-hubs-kafka-flink-tutorial.md) of [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
