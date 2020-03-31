---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs via Java (laatste)
description: Dit artikel biedt een walkthrough van het maken van een Java-toepassing die gebeurtenissen verzendt/ontvangt van/van Azure Event Hubs met behulp van het nieuwste azure-messaging-eventhubs-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 44f57f52be512924e228d6488a786d117c6444e7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370590"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java gebruiken om gebeurtenissen naar Azure Event Hubs te verzenden of te ontvangen (azure-messaging-eventhubs)
Deze quickstart laat zien hoe u gebeurtenissen verzendt naar en ontvangt vanuit een gebeurtenishub met behulp van het **Java-pakket azure-messaging-eventhubs.**

> [!IMPORTANT]
> Deze quickstart maakt gebruik van het nieuwe **azure-messaging-eventhubs-pakket.** Zie [Gebeurtenissen verzenden en ontvangen met azure-eventhubs-eph](event-hubs-java-get-started-send.md)voor een snelle start waarbij gebruik wordt gemaakt van de oude **azure-eventhubs** en **azure-eventhubs-eph.** 


## <a name="prerequisites"></a>Vereisten
Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- Een Java-ontwikkelomgeving. Deze quickstart maakt gebruik van [Eclipse](https://www.eclipse.org/). Java Development Kit (JDK) met versie 8 of hoger is vereist. 
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de **verbindingstekenreeks voor de naamruimte van gebeurtenishubs** door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingstekenreeks later in deze snelstart.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie ziet u hoe u een Java-toepassing maakt om gebeurtenissen een gebeurtenishub te sturen. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Verwijzing toevoegen aan de Azure Event Hubs-bibliotheek

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten in de [Maven Central Repository.](https://search.maven.org/search?q=a:azure-messaging-eventhubs) U naar deze bibliotheek verwijzen met de volgende afhankelijkheidsverklaring in uw Maven-projectbestand:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Voeg een `SimpleSend`klasse met de naam toe en voeg de volgende code toe aan de klasse:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Verbindingstekenreeks en gebeurtenishub
Deze code gebruikt de verbindingstekenreeks met de naamruimte van gebeurtenishubs en de naam van de gebeurtenishub om een gebeurtenishubsclient te bouwen. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Een client Event Hubs-producenten maken 
Met deze code wordt een clientobject voor producenten gemaakt dat wordt gebruikt voor het produceren/verzenden van gebeurtenissen naar de gebeurtenishub. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Een batch gebeurtenissen voorbereiden
Deze code bereidt een reeks gebeurtenissen voor. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>De batch gebeurtenissen naar de gebeurtenishub verzenden
Deze code stuurt de batch gebeurtenissen die u in de vorige stap hebt voorbereid naar de gebeurtenishub. De volgende code blokken op de bewerking verzenden. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Sluiten en opruimen
Deze code sluit de producent. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Volledige code om gebeurtenissen te verzenden
Hier is de volledige code om gebeurtenissen naar de gebeurtenishub te verzenden. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

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

Bouw het programma en zorg ervoor dat er geen fouten zijn. U voert dit programma uit nadat u het ontvangerprogramma hebt uitgevoerd. 

## <a name="receive-events"></a>Gebeurtenissen ontvangen
De code in deze zelfstudie is gebaseerd op het [Voorbeeld van EventProcessorClient op GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), dat u onderzoeken om de volledige werkende toepassing te zien.

### <a name="create-a-java-project"></a>Een Java-project maken

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten in de [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)en kan worden verwezen met behulp van de volgende afhankelijkheidsverklaring in uw Maven-projectbestand: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Gebruik de volgende code om een nieuwe klasse te maken met de naam `Receiver`. Vervang de tijdelijke aanduidingen door de waarden die worden gebruikt bij het maken van de gebeurtenishub en het opslagaccount:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
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
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new InMemoryCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Download het **InMemoryCheckpointStore.java-bestand** van [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/InMemoryCheckpointStore.java)en voeg het toe aan uw project. 
3. Bouw het programma en zorg ervoor dat er geen fouten zijn. 

## <a name="run-the-applications"></a>De toepassingen uitvoeren
1. Voer eerst de **ontvangertoepassing** uit.
1. Voer vervolgens de **afzendertoepassing** uit. 
1. Controleer in het venster **voor de toepassing** van de ontvanger of u de gebeurtenissen ziet die zijn gepubliceerd door de afzendertoepassing.
1. Druk **op ENTER** in het venster van de ontvangertoepassing om de toepassing te stoppen. 

## <a name="next-steps"></a>Volgende stappen
Bekijk [Java SDK-voorbeelden op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

