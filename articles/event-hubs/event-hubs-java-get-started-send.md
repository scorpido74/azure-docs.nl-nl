---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van Java (verouderd)
description: Dit artikel bevat een overzicht van het maken van een Java-toepassing die gebeurtenissen naar/van Azure Event Hubs verzendt/ontvangt met behulp van het oude Azure-Event hubs-pakket.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 2c9baa4c0e048419ece09b954cee1af21b1f0cc1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158006"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Java gebruiken om gebeurtenissen te verzenden naar of ontvangen van Azure Event Hubs (Azure-Event hubs)

In deze Quick start ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het **Azure-Event hubs Java-** pakket.

> [!WARNING]
> Deze Snelstartgids maakt gebruik van de oude **Azure-Event hubs** -en **Azure-Event hubs-EPH-** pakketten. Zie [gebeurtenissen verzenden en ontvangen met Azure-Messa ging-Event hubs](get-started-java-send-v2.md)voor een Snelstartgids waarbij gebruik wordt gemaakt van het meest recente **Azure Messa ging-Event hubs** -pakket. Raadpleeg de [hand leiding voor het migreren van Azure-Event hubs naar Azure-Messa ging-Event hubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)als u uw toepassing wilt verplaatsen van het oude pakket naar een nieuwe. 


## <a name="prerequisites"></a>Vereisten

Als u niet bekend bent met Azure Event Hubs, raadpleegt u [Event hubs Overview](event-hubs-about.md) voordat u deze Snelstartgids. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure abonnement**. Als u Azure-Services, met inbegrip van Azure Event Hubs, wilt gebruiken, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/free/) of de voor delen van uw MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Een Java-ontwikkelomgeving. Deze Snelstartgids maakt gebruik van [eclips](https://www.eclipse.org/).
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de waarde van de toegangs sleutel voor de Event Hub door de volgende instructies uit het artikel: [get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangs sleutel in de code die u verderop in deze Quick Start schrijft. De naam van de standaard sleutel is: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een Java-toepassing maakt voor het verzenden van gebeurtenissen een Event Hub. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze Snelstartgids volgen om uw eigen te maken.

### <a name="add-reference-to-azure-event-hubs-library"></a>Verwijzing naar de Azure Event Hubs-bibliotheek toevoegen

De Java-client bibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten vanuit de [centrale maven-opslag plaats](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). U kunt naar deze bibliotheek verwijzen met behulp van de volgende afhankelijkheids declaratie in uw Maven-project bestand:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Voor verschillende soorten bouw omgevingen kunt u expliciet de meest recente vrijgegeven JAR-bestanden ophalen uit de [centrale maven-opslag plaats](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Voor een eenvoudige Uitgever van gebeurtenissen importeert u het pakket *com. micro soft. Azure. Event hubs* voor de Event hubs client klassen en het pakket *com. micro soft. Azure. servicebus* voor hulp klassen zoals algemene uitzonde ringen die worden gedeeld met de Azure service bus Messa ging-client. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Voeg een klasse met de naam `SimpleSend`toe en voeg de volgende code toe aan de klasse:

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

### <a name="construct-connection-string"></a>Verbindingsreeks

De klasse ConnectionStringBuilder gebruiken om samen te stellen van een string-waarde van de verbinding moet worden doorgegeven aan het exemplaar van de client Event Hubs. Vervang de tijdelijke aanduidingen door de waarden die u hebt verkregen tijdens het maken van de naamruimte en event hub:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Code schrijven voor het verzenden van gebeurtenissen

Een enkelvoudige gebeurtenis maken door het omzetten van een tekenreeks in de codering UTF-8 bytes. Vervolgens maakt u een nieuw exemplaar van de Event Hubs-client uit de verbindingsreeks en verzenden van het bericht:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
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

Bouwen en voer het programma uit en zorg ervoor dat er geen fouten zijn.

Gefeliciteerd. U hebt nu berichten verzonden naar een Event Hub.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Bijlage: Hoe berichten worden gerouteerd naar Event hub-partities

Voordat u berichten worden opgehaald door de consumenten, die ze moeten worden gepubliceerd naar de partities eerst door de uitgevers. Wanneer berichten worden gepubliceerd naar event hub synchroon met de methode sendSync() op het object com.microsoft.azure.eventhubs.EventHubClient, kan het bericht worden verzonden naar een specifieke partitie of gedistribueerd naar alle beschikbare partities op een wijze round robin afhankelijk van of de partitiesleutel is opgegeven of niet.

Als een tekenreeks voor de partitiesleutel is opgegeven, wordt de sleutel wordt gehasht om te bepalen welke partitie voor het verzenden van de gebeurtenis.

Als de partitiesleutel is niet ingesteld, klikt u vervolgens wordt berichten round-robined op alle beschikbare partities

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
De code in deze zelf studie is gebaseerd op de [EventProcessorSample-code op github](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), die u kunt bekijken om de volledige werk toepassing weer te geven.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Berichten ontvangen met EventProcessorHost in Java

**EventProcessorHost** is een Java-klasse die het ontvangen van gebeurtenissen van Event hubs vereenvoudigt door permanente controle punten en parallelle ontvangst van deze event hubs te beheren. Met EventProcessorHost kunt splitsen u gebeurtenissen over meerdere ontvangers, zelfs als deze worden gehost in verschillende knooppunten. In dit voorbeeld wordt het gebruik van EventProcessorHost gedemonstreerd voor één ontvanger.

### <a name="create-a-storage-account"></a>Create a storage account

Als u EventProcessorHost wilt gebruiken, moet u beschikken over een [Azure Storage account] [Azure Storage account]:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer **een resource maken** aan de linkerkant van het scherm.
2. Selecteer **opslag**en selecteer vervolgens **opslag account**. Typ in het venster **opslag account maken** een naam voor het opslag account. Voltooi de rest van de velden, selecteer de gewenste regio en selecteer vervolgens **maken**.
   
    ![Een opslag account maken in Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Selecteer het zojuist gemaakte opslag account en selecteer vervolgens **toegangs sleutels**:
   
    ![Uw toegangs sleutels in Azure Portal ophalen](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Kopieer de key1-waarde naar een tijdelijke locatie. U gaat deze verderop in de zelfstudie gebruiken.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Maak een Java-project met behulp van EventProcessorHost

De Java-client bibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten van de [centrale opslag plaats voor maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)en er kan naar worden verwezen met de volgende afhankelijkheids declaratie in uw Maven-project bestand: 

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

Voor verschillende soorten bouw omgevingen kunt u expliciet de meest recente vrijgegeven JAR-bestanden ophalen uit de [centrale maven-opslag plaats](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. De klasse wordt `ErrorNotificationHandler`genoemd.     
   
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
2. Gebruik de volgende code om een nieuwe klasse te maken met de naam `EventProcessorSample`. Vervang de tijdelijke aanduidingen door de waarden die worden gebruikt bij het maken van de event hub- en storage-account:
   
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
    ```
3. Maak een klasse met de naam `EventProcessor`, met behulp van de volgende code:
   
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

In deze zelfstudie wordt één exemplaar van EventProcessorHost gebruikt. U wordt aangeraden dat u meerdere exemplaren van EventProcessorHost, bij voorkeur uitgevoerd op afzonderlijke computers voor een betere doorvoer.  Het biedt ook redundantie. In die gevallen werken de verschillende exemplaren automatisch samen om de ontvangen gebeurtenissen gelijkmatig te verdelen. Als u wilt dat meerdere ontvangers *alle* gebeurtenissen verwerken, gebruik dan het concept **ConsumerGroup**. Wanneer er gebeurtenissen van verschillende computers worden ontvangen, kan het nuttig zijn om namen voor EventProcessorHost-exemplaren op te geven op basis van de computers waarop (of rollen waarin) ze zijn geïmplementeerd.

### <a name="publishing-messages-to-eventhub"></a>Publiceren berichten naar EventHub

Voordat u berichten worden opgehaald door de consumenten, die ze moeten worden gepubliceerd naar de partities eerst door de uitgevers. Dit is het vermelden waard dat wanneer berichten worden gepubliceerd naar event hub synchroon met de methode sendSync() op het object com.microsoft.azure.eventhubs.EventHubClient, het bericht kan worden verzonden naar een specifieke partitie of gedistribueerd naar alle beschikbare partities op een wijze round robin, afhankelijk van of u is de partitiesleutel of niet opgegeven.

Als een tekenreeks voor de partitiesleutel is opgegeven, wordt de sleutel wordt gehasht om te bepalen welke partitie voor het verzenden van de gebeurtenis.

Als de partitiesleutel is niet ingesteld, klikt u vervolgens zijn berichten round-robined op alle beschikbare partities

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

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementeren van een aangepaste CheckpointManager voor EventProcessorHost (EPH)

De API biedt een mechanisme voor het implementeren van uw aangepaste controlepuntbeheer voor scenario's waarbij de standaardimplementatie niet compatibel met uw situatie is.

De standaard controlepunt manager maakt gebruik van blob-opslag, maar als u de controlepunt-manager gebruikt door EPH met uw eigen implementatie overschrijft, kunt u geen store die u wilt maken van uw manager-implementatie van het controlepunt.

Maken van een klasse die de interface com.microsoft.azure.eventprocessorhost.ICheckpointManager

Gebruik van uw aangepaste implementatie van de manager van het controlepunt (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Binnen uw implementatie kunt u het standaard mechanisme voor controle punten overschrijven en onze eigen controle punten implementeren op basis van uw eigen gegevens archief (zoals SQL Server, CosmosDB en Azure cache voor redis). U wordt aangeraden dat de store gebruikt voor het back-ups maken uw manager-implementatie van het controlepunt is toegankelijk voor alle EPH-exemplaren die zijn verwerking van gebeurtenissen voor de consumergroep.

U kunt een gegevensopslag die beschikbaar is in uw omgeving.

De klasse com.microsoft.azure.eventprocessorhost.EventProcessorHost beschikt u over twee constructors waarmee u kunt voor de onderdrukking van de manager van het controlepunt voor de EventProcessorHost.


## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)

