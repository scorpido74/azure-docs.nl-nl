---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van Java (nieuwste)
description: Dit artikel bevat een overzicht van het maken van een Java-toepassing voor het verzenden/ontvangen van gebeurtenissen naar/van Azure Event Hubs met behulp van het meest recente Azure Messa ging-Event hubs-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: d9d22374868f3befd659918c532f339d49ba1642
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032130"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java gebruiken om gebeurtenissen te verzenden naar of ontvangen van Azure Event Hubs (Azure-Messa ging-Event hubs)
In deze Quick start ziet u hoe u Java-toepassingen kunt maken voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van Azure Event Hubs. 

Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van het nieuwe **Azure-Messa ging-Event hubs-** pakket. Raadpleeg [dit artikel](event-hubs-java-get-started-send.md)voor een Snelstartgids die gebruikmaakt van de oude **Azure-Event hubs** en **Azure-Event hubs-EPH-** pakketten. 


## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Een Java-ontwikkelomgeving. In deze zelf studie wordt gebruikgemaakt van [eclips](https://www.eclipse.org/). Java Development Kit (JDK) met versie 8 of hoger is vereist. 
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de waarde van de toegangs sleutel voor de Event Hub door de volgende instructies uit het artikel: [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u verderop in deze zelfstudie schrijft. De naam van de standaard sleutel is: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een Java-toepassing maakt voor het verzenden van gebeurtenissen een Event Hub. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Verwijzing naar de Azure Event Hubs-bibliotheek toevoegen

De Java-client bibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten vanuit de [centrale maven-opslag plaats](https://search.maven.org/search?q=a:azure-messaging-eventhubs). U kunt naar deze bibliotheek verwijzen met behulp van de volgende afhankelijkheids declaratie in uw Maven-project bestand:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Voeg een klasse met de naam `SimpleSend`toe en voeg de volgende code toe aan de klasse:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
    public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Verbindings reeks en Event Hub
Deze code gebruikt de connection string voor de Event Hubs naam ruimte en de naam van de Event Hub om een Event Hubs-client samen te stellen. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Een Event Hubs producer-client maken 
Met deze code wordt een producer-client object gemaakt dat wordt gebruikt voor het produceren/verzenden van gebeurtenissen naar de Event Hub. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducer();
```

### <a name="prepare-a-batch-of-events"></a>Een batch gebeurtenissen voorbereiden
Met deze code wordt een batch gebeurtenissen voor bereid. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>De batch met gebeurtenissen naar de Event Hub verzenden
Met deze code wordt de batch gebeurtenissen die u in de vorige stap hebt voor bereid, verzonden naar de Event Hub. De volgende code blokken op de verzend bewerking. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Sluiten en opschonen
Met deze code wordt de producent afgesloten. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Volledige code voor het verzenden van gebeurtenissen
Dit is de volledige code voor het verzenden van gebeurtenissen naar de Event Hub. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducer();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Bouw het programma en zorg ervoor dat er geen fouten zijn. U voert dit programma uit nadat u het receiver-programma hebt uitgevoerd. 

## <a name="receive-events"></a>Gebeurtenissen ontvangen
De code in deze zelf studie is gebaseerd op het [EventProcessorClient-voor beeld op github](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), dat u kunt bekijken om de volledige werk toepassing weer te geven.

### <a name="create-a-java-project"></a>Een Java-project maken

De Java-client bibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten van de [centrale opslag plaats voor maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)en er kan naar worden verwezen met de volgende afhankelijkheids declaratie in uw Maven-project bestand: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Gebruik de volgende code om een nieuwe klasse te maken met de naam `Receiver`. Vervang de tijdelijke aanduidingen door de waarden die worden gebruikt bij het maken van de event hub- en storage-account:
   
   ```java
    import com.azure.messaging.eventhubs.*;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;

    public class Receiver {

        private static final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static final String eventHubName = "EVENT HUB NAME";
    
        public static void main(String[] args) throws Exception {

            // function to process events
            Consumer<EventContext> processEvent = eventContext  -> {
                System.out.print("Received event: ");
                // print the body of the event
                System.out.println(eventContext.getEventData().getBodyAsString());
                eventContext.updateCheckpoint();
            };

            // function to process errors
            Consumer<ErrorContext> processError = errorContext -> {
                // print the error message
                System.out.println(errorContext.getThrowable().getMessage());
            };

            EventProcessorBuilder eventProcessorBuilder = new EventProcessorBuilder()
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .connectionString(connectionString, eventHubName)
                .processEvent(processEvent)
                .processError(processError)
                .checkpointStore(new InMemoryCheckpointStore());
        
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();
            System.out.println("Starting event processor");
            eventProcessorClient.start();

            System.out.println("Press enter to stop.");
            System.in.read();

            System.out.println("Stopping event processor");
            eventProcessor.stop();
            System.out.println("Event processor stopped.");
    
            System.out.println("Exiting process");
        }
    }
    ```
    
2. Down load het **InMemoryCheckpointStore. java** -bestand van [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)en voeg dit toe aan uw project. 
3. Bouw het programma en zorg ervoor dat er geen fouten zijn. 

## <a name="run-the-applications"></a>De toepassingen uitvoeren
1. Voer eerst de **receiver** -toepassing uit.
1. Voer vervolgens de toepassing **Sender** uit. 
1. Controleer in het toepassings venster van de **ontvanger** of de gebeurtenissen worden weer geven die zijn gepubliceerd door de toepassing van de afzender.
1. Druk op **Enter** in het toepassings venster van de ontvanger om de toepassing te stoppen. 

## <a name="next-steps"></a>Volgende stappen
Bekijk de [Java SDK-voor beelden op github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

