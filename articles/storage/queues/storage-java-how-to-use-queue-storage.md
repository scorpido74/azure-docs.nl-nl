---
title: Queue Storage gebruiken vanuit Java-Azure Storage
description: Meer informatie over het gebruik van wachtrij opslag voor het maken en verwijderen van wacht rijen en het invoegen, ophalen en verwijderen van berichten met de Azure Storage-client bibliotheek voor Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 17e5a542bc951df5b40144490f05e41aec1a09e8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319419"
---
# <a name="how-to-use-queue-storage-from-java"></a>Queue Storage gebruiken met Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

In deze hand leiding wordt uitgelegd hoe u algemene scenario's uitvoert met behulp van de Azure Queue Storage-service. De voorbeelden zijn geschreven in Java en maken gebruik van de [Azure Storage SDK voor Java][Azure Storage SDK for Java]. De gedekte scenario's zijn het **Invoegen**, **inspecteren**, **ophalen**en **verwijderen** van wachtrij berichten, en het **maken** en **verwijderen** van wacht rijen. Zie de sectie [volgende stappen](#next-steps) voor meer informatie over wacht rijen.

> [!IMPORTANT]
> In dit artikel wordt verwezen naar de oudere versie van de Azure Storage-client bibliotheek voor Java. Als u aan de slag wilt gaan met de nieuwste versie, raadpleegt u [Quick Start: Azure Queue Storage-client bibliotheek voor Java](storage-quickstart-queues-java.md)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Een Java-toepassing maken

In deze hand leiding gebruikt u opslag functies die lokaal kunnen worden uitgevoerd in een Java-toepassing of in code die wordt uitgevoerd in een webtoepassing in Azure.

Hiervoor moet u de Java Development Kit (JDK) installeren en een Azure-opslag account maken in uw Azure-abonnement. Zodra u dit hebt gedaan, moet u controleren of uw ontwikkel systeem voldoet aan de minimale vereisten en afhankelijkheden die worden vermeld in de [Azure Storage SDK voor Java][Azure Storage SDK for Java] -opslag plaatsen op github. Als uw systeem aan deze vereisten voldoet, kunt u de instructies voor het downloaden en installeren van de Azure Storage bibliotheken voor Java op uw systeem volgen vanuit die opslag plaats. Nadat u deze taken hebt voltooid, kunt u een Java-toepassing maken die gebruikmaakt van de voor beelden in dit artikel.

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren voor toegang tot de wachtrij opslag

Voeg de volgende import instructies toe aan de bovenkant van het Java-bestand waar u Azure Storage-Api's wilt gebruiken voor toegang tot wacht rijen:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Een Azure Storage-connection string instellen

Een Azure-opslagclient gebruikt een opslagverbindingstekenreeks om eindpunten en referenties voor toegang tot gegevensbeheerservices op te slaan. Bij uitvoering in een clienttoepassing moet u de opslagverbindingstekenreeks opgeven in de volgende indeling, met de naam van uw opslagaccount en de primaire toegangssleutel voor het opslagaccount dat in [Azure Portal](https://portal.azure.com) wordt vermeld voor de waarden *AccountName* en *AccountKey*. In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

In een toepassing die wordt uitgevoerd binnen een rol in Microsoft Azure, kan deze teken reeks worden opgeslagen in het service configuratie bestand *ServiceConfiguration. cscfg*en kan worden geopend met een aanroep van de methode **RoleEnvironment. getConfigurationSettings** . Hier volgt een voorbeeld van het ophalen van de verbindingstekenreeks van een **Setting**-element met de naam *StorageConnectionString* in het serviceconfiguratiebestand:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

In de volgende voorbeelden wordt ervan uitgegaan dat u een van deze twee methoden hebt gebruikt om de opslagverbindingstekenreeks op te halen.

## <a name="how-to-create-a-queue"></a>Procedure: een wachtrij maken
Met een **CloudQueueClient** -object kunt u referentie objecten voor wacht rijen ophalen. Met de volgende code wordt een **CloudQueueClient** -object gemaakt. (Opmerking: er zijn meer manieren om **Cloud Storage account** -objecten te maken. Zie **Cloud Storage account** in de naslag informatie voor de [Azure Storage-client SDK]voor meer gegevens.

Gebruik het object **CloudQueueClient** om een verwijzing naar de wachtrij die u wilt gebruiken op te halen. U kunt de wachtrij maken als deze niet bestaat.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-a-message-to-a-queue"></a>Procedure: een bericht aan een wachtrij toevoegen
Voor het invoegen van een bericht in een bestaande wachtrij maakt u eerst een nieuwe **CloudQueueMessage**. Vervolgens roept u de methode **addMessage** aan. Een **CloudQueueMessage** kan worden gemaakt vanuit een tekenreeks (in UTF-8-indeling) of een bytematrix. Hier is code waarmee een wachtrij wordt gemaakt (als deze niet bestaat) en het bericht ' Hello, World ' wordt ingevoegd.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-peek-at-the-next-message"></a>Procedure: het volgende bericht bekijken
U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door **peekMessage**aan te roepen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedure: de inhoud van een bericht in de wachtrij wijzigen
U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zichtbaarheid met 60 seconden verlengd. Als u de time-out voor de zicht baarheid uitbreidt, wordt de status van het werk dat is gekoppeld aan het bericht opgeslagen en wordt de client nog een minuut om aan het bericht te blijven werken U kunt deze techniek gebruiken om uit meerdere stappen bestaande werkstromen in berichten in de wachtrij te volgen zonder dat u helemaal opnieuw hoeft te beginnen als een verwerkingsstap vanwege een hardware- of softwarefout is mislukt. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

Met het volgende code voorbeeld wordt gezocht in de wachtrij met berichten en wordt het eerste bericht gezocht dat overeenkomt met ' Hallo, wereld ' voor de inhoud. vervolgens wordt de inhoud van het bericht gewijzigd en wordt het programma afgesloten.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Als alternatief wordt met het volgende code voorbeeld alleen het eerste zicht bare bericht in de wachtrij bijgewerkt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-get-the-queue-length"></a>Procedure: de lengte van de wachtrij ophalen
U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode **downloadAttributes** vraagt de Queue-service voor verschillende huidige waarden, met inbegrip van het aantal berichten in een wachtrij. De telling is alleen geschatte omdat berichten kunnen worden toegevoegd of verwijderd nadat de Queue-service op uw aanvraag heeft gereageerd. De methode **getApproximateMessageCount** retourneert de laatste waarde die is opgehaald door de aanroep van **downloadAttributes**, zonder de Queue-service aan te roepen.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-dequeue-the-next-message"></a>Procedure: het volgende bericht uit de wachtrij verwijderen
Met uw code wordt een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderd. Wanneer u **retrieveMessage**aanroept, wordt het volgende bericht in een wachtrij weer gegeven. Een bericht dat wordt geretourneerd door **retrieveMessage** , wordt onzichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook **deleteMessage**aanroepen. Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code aanroepen **deleteMessage** direct nadat het bericht is verwerkt.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Aanvullende opties voor het dequeuing van berichten
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht.

In het volgende code voorbeeld wordt de methode **retrieveMessages** gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp **van een for** -lus. Ook wordt de time-out voor onzichtbaar ingesteld op vijf minuten (300 seconden) voor elk bericht. De vijf minuten begin voor alle berichten tegelijk, dus wanneer vijf minuten zijn verstreken sinds de aanroep van **retrieveMessages**, worden alle berichten die niet zijn verwijderd, weer zichtbaar.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-queues"></a>Procedure: de wacht rijen weer geven
Voor het verkrijgen van een lijst met de huidige wacht rijen roept u de methode **CloudQueueClient. list Queues ()** aan, die een verzameling **CloudQueue** -objecten retourneert.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-queue"></a>Procedure: een wachtrij verwijderen
Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de methode **deleteIfExists** aan voor het object **CloudQueue** .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

> [!NOTE]
> Er is een SDK beschikbaar voor ontwikkelaars die Azure Storage op Android-apparaten gebruiken. Raadpleeg de [Azure Storage SDK voor Android][Azure Storage SDK for Android] voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van wachtrij opslag hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

* [Azure Storage SDK voor Java][Azure Storage SDK for Java]
* [Azure Storage Client SDK-referentie][Azure Storage Client SDK Reference]
* [REST-API voor Azure Storage-services][Azure Storage Services REST API]
* [Blog van Azure Storage team][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK-referentie]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
