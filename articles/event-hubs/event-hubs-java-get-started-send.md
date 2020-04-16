---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs met Java (legacy)
description: In dit artikel vindt u een walkthrough van het maken van een Java-toepassing die gebeurtenissen verzendt/ontvangt van/naar Azure Event Hubs met behulp van het oude azure-eventhubs-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 379739533e15e60bc47bfc883a67037d4a58d0e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417627"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Java gebruiken om gebeurtenissen naar Azure Event Hubs te verzenden of te ontvangen (azure-eventhubs)

Deze quickstart laat zien hoe u gebeurtenissen verzendt naar en ontvangt van een gebeurtenishub met behulp van het **Java-pakket azure-eventhubs.**

> [!WARNING]
> Deze quickstart maakt gebruik van de oude **azure-eventhubs** en **azure-eventhubs-eph-pakketten.** Zie [Gebeurtenissen verzenden en ontvangen met azure-messaging-eventhubs](get-started-java-send-v2.md)voor een snelle start waarbij gebruik wordt gemaakt van het nieuwste pakket **azure-messaging-eventhubs.** Als u uw toepassing wilt verplaatsen van het gebruik van het oude pakket naar een nieuw pakket, raadpleegt u de [handleiding om te migreren van azure-eventhubs naar azure-messaging-eventhubs.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md) 


## <a name="prerequisites"></a>Vereisten

Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- Een Java-ontwikkelomgeving. Deze quickstart maakt gebruik van [Eclipse](https://www.eclipse.org/).
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de waarde van de toegangssleutel voor de gebeurtenishub door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u later in deze quickstart schrijft. De standaardsleutelnaam is: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie ziet u hoe u een Java-toepassing maakt om gebeurtenissen een gebeurtenishub te sturen. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U ook de stappen in deze quickstart volgen om uw eigen stappen te maken.

### <a name="add-reference-to-azure-event-hubs-library"></a>Verwijzing toevoegen aan de Azure Event Hubs-bibliotheek

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten in de [Maven Central Repository.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) U naar deze bibliotheek verwijzen met de volgende afhankelijkheidsverklaring in uw Maven-projectbestand:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Voor verschillende typen buildomgevingen u expliciet de nieuwste uitgebrachte JAR-bestanden verkrijgen van de [Maven Central Repository.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)  

Voor een eenvoudige gebeurtenisuitgever importeert u het *com.microsoft.azure.eventhubs-pakket* voor de clientklassen Event Hubs en het *com.microsoft.azure.servicebus-pakket* voor hulpprogrammaklassen, zoals algemene uitzonderingen die worden gedeeld met de Azure Service Bus-berichtenclient. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Voeg een `SimpleSend`klasse met de naam toe en voeg de volgende code toe aan de klasse:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Verbindingstekenreeks construeren

Gebruik de klasse ConnectionStringBuilder om een verbindingstekenreekswaarde te maken die u wilt doorgeven aan de clientinstantie Event Hubs. Vervang de tijdelijke aanduidingen door de waarden die u hebt verkregen toen u de naamruimte en gebeurtenishub maakte:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Code schrijven om gebeurtenissen te verzenden

Maak een enkelvoudige gebeurtenis door een tekenreeks om te zetten in de utf-8-bytecodering. Maak vervolgens een nieuw clientexemplaar van Event Hubs vanuit de verbindingstekenreeks en verstuur het bericht:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Bouw en voer het programma uit en zorg ervoor dat er geen fouten zijn.

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Bijlage: Hoe berichten worden doorgestuurd naar EventHub-partities

Voordat berichten door consumenten worden opgehaald, moeten ze eerst door de uitgevers naar de partities worden gepubliceerd. Wanneer berichten synchroon worden gepubliceerd naar gebeurtenishub met de methode sendSync() op het object com.microsoft.azure.eventhubs.EventHubClient, kan het bericht naar een specifieke partitie worden verzonden of op een round-robin-manier naar alle beschikbare partities worden gedistribueerd, afhankelijk van of de partitiesleutel is opgegeven of niet.

Wanneer een tekenreeks die de partitiesleutel vertegenwoordigt is opgegeven, wordt de sleutel gehasht om te bepalen naar welke partitie de gebeurtenis moet worden verzonden.

Wanneer de partitiesleutel niet is ingesteld, worden berichten geround-robined naar alle beschikbare partities

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Gebeurtenissen ontvangen
De code in deze zelfstudie is gebaseerd op de [EventProcessorSample-code op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), die u onderzoeken om de volledige werkende toepassing te zien.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Berichten ontvangen met EventProcessorHost in Java

**EventProcessorHost** is een Java-klasse die het ontvangen van gebeurtenissen van eventhubs vereenvoudigt door permanente controlepunten en parallelle ontvangsts van deze Gebeurtenishubs te beheren. Met EventProcessorHost kunt u gebeurtenissen splitsen over meerdere ontvangers, zelfs als deze worden gehost in verschillende knooppunten. In dit voorbeeld wordt het gebruik van EventProcessorHost gedemonstreerd voor één ontvanger.

### <a name="create-a-storage-account"></a>Create a storage account

Als u EventProcessorHost wilt gebruiken, moet u een [Azure Storage-account][Azure Storage-account]:

1. Meld u aan voor de [Azure-portal](https://portal.azure.com)en selecteer **Een resource maken** aan de linkerkant van het scherm.
2. Selecteer **Opslag**en selecteer **vervolgens Opslagaccount**. Typ in het venster **Opslagaccount maken** een naam voor het opslagaccount. Vul de rest van de velden in, selecteer het gewenste gebied en selecteer **Vervolgens Maken**.
   
    ![Een opslagaccount maken in Azure-portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Selecteer het nieuw gemaakte opslagaccount en selecteer **Vervolgens Toegangssleutels:**
   
    ![Uw toegangssleutels ophalen in Azure-portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Kopieer de waarde key1 naar een tijdelijke locatie. U gaat deze verderop in de zelfstudie gebruiken.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Maak een Java-project met behulp van EventProcessorHost

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten in de [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)en kan worden verwezen met behulp van de volgende afhankelijkheidsverklaring in uw Maven-projectbestand: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Voor verschillende typen buildomgevingen u expliciet de nieuwste uitgebrachte JAR-bestanden verkrijgen van de [Maven Central Repository.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)

1. Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. De klasse `ErrorNotificationHandler`heet .     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Gebruik de volgende code om een nieuwe klasse te maken met de naam `EventProcessorSample`. Vervang de tijdelijke aanduidingen door de waarden die worden gebruikt bij het maken van de gebeurtenishub en het opslagaccount:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
   }
   ```
3. Maak nog een `EventProcessor`klasse met de volgende code:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

In deze zelfstudie wordt één exemplaar van EventProcessorHost gebruikt. Om de doorvoer te verhogen, raden we u aan meerdere exemplaren van EventProcessorHost uit te voeren, bij voorkeur op afzonderlijke machines.  Het biedt redundantie ook. In die gevallen werken de verschillende exemplaren automatisch samen om de ontvangen gebeurtenissen gelijkmatig te verdelen. Als u wilt dat meerdere ontvangers *alle* gebeurtenissen verwerken, gebruik dan het concept **ConsumerGroup**. Wanneer er gebeurtenissen van verschillende computers worden ontvangen, kan het nuttig zijn om namen voor EventProcessorHost-exemplaren op te geven op basis van de computers waarop (of rollen waarin) ze zijn geïmplementeerd.

### <a name="publishing-messages-to-eventhub"></a>Berichten publiceren naar EventHub

Voordat berichten door consumenten worden opgehaald, moeten ze eerst door de uitgevers naar de partities worden gepubliceerd. Het is vermeldenswaard dat wanneer berichten synchroon worden gepubliceerd naar gebeurtenishub met behulp van de methode sendSync() op het object com.microsoft.azure.eventhubs.EventHubClient, het bericht naar een specifieke partitie kan worden verzonden of op een round-robin-manier naar alle beschikbare partities kunnen worden gedistribueerd, afhankelijk van of de partitiesleutel is opgegeven of niet.

Wanneer een tekenreeks die de partitiesleutel vertegenwoordigt is opgegeven, wordt de sleutel gehasht om te bepalen naar welke partitie de gebeurtenis moet worden verzonden.

Wanneer de partitiesleutel niet is ingesteld, worden berichten rondgetoetst aan alle beschikbare partities

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Een aangepaste CheckpointManager implementeren voor EventProcessorHost (EPH)

De API biedt een mechanisme om uw aangepaste controlepuntbeheerder te implementeren voor scenario's waarin de standaardimplementatie niet compatibel is met uw use case.

De standaardcontrolepuntbeheer gebruikt blob-opslag, maar als u de controlepuntmanager die door EPH wordt gebruikt, overschrijft met uw eigen implementatie, u elke winkel gebruiken die u wilt gebruiken om de implementatie van uw controlepuntmanager te back-upen.

Een klasse maken die de interface com.microsoft.azure.eventprocessorhost.ICheckpointManager implementeert

Uw aangepaste implementatie van de controlepuntmanager gebruiken (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Binnen uw implementatie u het standaard controlemechanisme overschrijven en onze eigen controlepunten implementeren op basis van uw eigen gegevensarchief (zoals SQL Server, CosmosDB en Azure Cache voor Redis). We raden u aan dat de winkel die wordt gebruikt om de implementatie van uw controlepuntmanager te onderschreven, toegankelijk is voor alle EPH-exemplaren die gebeurtenissen voor de consumentengroep verwerken.

U elk datastore gebruiken dat beschikbaar is in uw omgeving.

De klasse com.microsoft.azure.eventprocessorhost.EventProcessorHost biedt u twee constructors waarmee u de controlepuntmanager voor uw EventProcessorHost overschrijven.


## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

