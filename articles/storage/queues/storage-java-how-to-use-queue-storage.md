---
title: Wachtrijopslag gebruiken vanuit Java - Azure Storage
description: Meer informatie over het gebruik van wachtrijopslag om wachtrijen te maken en te verwijderen en berichten in te voegen, op te halen en te verwijderen met de Azure Storage-clientbibliotheek voor Java.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 7658b8541e7a79a5e547a6649b35681446e34b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067130"
---
# <a name="how-to-use-queue-storage-from-java"></a>Queue Storage gebruiken met Java

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

In deze handleiding ziet u hoe u veelvoorkomende scenario's uitvoeren met de Azure Queue-opslagservice. De voorbeelden zijn geschreven in Java en maken gebruik van de [Azure Storage SDK voor Java][Azure Storage SDK for Java]. De behandelde scenario's omvatten **het invoegen,** **gluren,** **krijgen**en **verwijderen van** wachtrijberichten, evenals het **maken** en **verwijderen van** wachtrijen. Zie de sectie Volgende [stappen](#next-steps) voor meer informatie over wachtrijen.

> [!NOTE]
> Er is een SDK beschikbaar voor ontwikkelaars die Azure Storage op Android-apparaten gebruiken. Raadpleeg de [Azure Storage SDK voor Android][Azure Storage SDK for Android] voor meer informatie.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Een Java-toepassing maken

In deze handleiding gebruikt u opslagfuncties die lokaal kunnen worden uitgevoerd binnen een Java-toepassing of in code die wordt uitgevoerd in een webtoepassing in Azure.

Hiervoor moet u de Java Development Kit (JDK) installeren en een Azure-opslagaccount maken in uw Azure-abonnement. Zodra u dit hebt gedaan, moet u controleren of uw ontwikkelingssysteem voldoet aan de minimale vereisten en afhankelijkheden die worden vermeld in de [Azure Storage SDK voor Java-opslagplaats][Azure Storage SDK for Java] op GitHub. Als uw systeem aan deze vereisten voldoet, u de instructies voor het downloaden en installeren van de Azure-opslagbibliotheken voor Java op uw systeem vanuit die opslagplaats volgen. Zodra u deze taken hebt voltooid, u een Java-toepassing maken die de voorbeelden in dit artikel gebruikt.

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren om toegang te krijgen tot wachtrijopslag

Voeg de volgende importinstructies toe aan de bovenkant van het Java-bestand waar u Azure-opslag-API's wilt gebruiken om toegang te krijgen tot wachtrijen:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Een azure-opslagverbindingstekenreeks instellen

Een Azure-opslagclient gebruikt een opslagverbindingstekenreeks om eindpunten en referenties voor toegang tot gegevensbeheerservices op te slaan. Bij uitvoering in een clienttoepassing moet u de opslagverbindingstekenreeks opgeven in de volgende indeling, met de naam van uw opslagaccount en de primaire toegangssleutel voor het opslagaccount dat in [Azure Portal](https://portal.azure.com) wordt vermeld voor de waarden *AccountName* en *AccountKey*. In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

In een toepassing die wordt uitgevoerd binnen een rol in Microsoft Azure, kan deze tekenreeks worden opgeslagen in het serviceconfiguratiebestand *ServiceConfiguration.cscfg*en kan deze worden geopend met een oproep naar de methode **RoleEnvironment.getConfigurationSettings.** Hier volgt een voorbeeld van het ophalen van de verbindingstekenreeks van een **Setting**-element met de naam *StorageConnectionString* in het serviceconfiguratiebestand:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

In de volgende voorbeelden wordt ervan uitgegaan dat u een van deze twee methoden hebt gebruikt om de opslagverbindingstekenreeks op te halen.

## <a name="how-to-create-a-queue"></a>Hoe: Een wachtrij maken
Met een **CloudQueueClient-object** u referentieobjecten voor wachtrijen opvragen. Met de volgende code wordt een **CloudQueueClient-object** gemaakt. (Opmerking: er zijn aanvullende manieren om **CloudStorageAccount-objecten** te maken; zie **CloudStorageAccount** voor meer informatie in de [Azure Storage Client SDK Reference].)

Gebruik het object **CloudQueueClient** om een verwijzing te krijgen naar de wachtrij die u wilt gebruiken. U de wachtrij maken als deze niet bestaat.

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

## <a name="how-to-add-a-message-to-a-queue"></a>How to: Een bericht toevoegen aan een wachtrij
Voor het invoegen van een bericht in een bestaande wachtrij maakt u eerst een nieuwe **CloudQueueMessage**. Roep vervolgens de **methode addMessage** aan. Een **CloudQueueMessage** kan worden gemaakt vanuit een tekenreeks (in UTF-8-indeling) of een bytematrix. Hier is code die een wachtrij maakt (als deze niet bestaat) en het bericht 'Hallo, Wereld' invoegt.

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

## <a name="how-to-peek-at-the-next-message"></a>Hoe: Gluren bij het volgende bericht
U het bericht voor in een wachtrij bekijken zonder het uit de wachtrij te verwijderen door **peekMessage**te bellen.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>How to: De inhoud van een bericht in de wachtrij wijzigen
U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zichtbaarheid met 60 seconden verlengd. Het verlengen van de time-out voor zichtbaarheid slaat de status van het werk op die aan het bericht is gekoppeld en geeft de client nog een minuut om aan het bericht te blijven werken. U kunt deze techniek gebruiken om uit meerdere stappen bestaande werkstromen in berichten in de wachtrij te volgen zonder dat u helemaal opnieuw hoeft te beginnen als een verwerkingsstap vanwege een hardware- of softwarefout is mislukt. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

In het volgende codevoorbeeld wordt gezocht in de wachtrij met berichten, wordt het eerste bericht gevonden dat overeenkomt met 'Hallo, Wereld' voor de inhoud en wijzigt u vervolgens de inhoud en uitgangen van het bericht.

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

Als alternatief wordt in het volgende codevoorbeeld alleen het eerste zichtbare bericht in de wachtrij bijgewerkt.

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

## <a name="how-to-get-the-queue-length"></a>How to: De wachtrijlengte oppakken
U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode **downloadKenmerken** vraagt de wachtrijservice om verschillende huidige waarden, waaronder een telling van het aantal berichten dat zich in een wachtrij bevindt. Het aantal is slechts bij benadering omdat berichten kunnen worden toegevoegd of verwijderd nadat de wachtrijservice op uw verzoek reageert. Met de methode **getApproximateMessageCount** retourneert u de laatste waarde die door de aanroep is opgehaald naar **downloadKenmerken,** zonder de wachtrijservice te bellen.

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

## <a name="how-to-dequeue-the-next-message"></a>Hoe: Het volgende bericht dequeue
Uw code ontbelt een bericht uit een wachtrij in twee stappen. Wanneer u **ophalenMessage**belt, krijgt u het volgende bericht in een wachtrij. Een bericht dat wordt geretourneerd uit **retrieveMessage** wordt onzichtbaar voor andere codeleesberichten uit deze wachtrij. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u wilt eindigen met het verwijderen van het bericht uit de wachtrij, moet u ook **deleteMessage**aanroepen. Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw coderoept **deleteMessage** direct nadat het bericht is verwerkt.

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

## <a name="additional-options-for-dequeuing-messages"></a>Extra opties voor het dequeueren van berichten
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht.

In het volgende codevoorbeeld wordt de methode **ophalenBerichten** gebruikt om 20 berichten in één gesprek te ontvangen. Vervolgens verwerkt het elk bericht met behulp van een **voor** lus. Het stelt ook de onzichtbaarheid time-out op vijf minuten (300 seconden) voor elk bericht. De vijf minuten start voor alle berichten op hetzelfde moment, dus wanneer vijf minuten zijn verstreken sinds de oproep om Berichten op te **halen,** alle berichten die niet zijn verwijderd zal weer zichtbaar worden.

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

## <a name="how-to-list-the-queues"></a>Hoe: De wachtrijen weergeven
Als u een lijst met de huidige wachtrijen wilt verkrijgen, belt u de methode **CloudQueueClient.listQueues(),** waarmee een verzameling **CloudQueue-objecten** wordt teruggegeven.

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

## <a name="how-to-delete-a-queue"></a>How to: Een wachtrij verwijderen
Als u een wachtrij en alle berichten in deze wachtrij wilt verwijderen, roept u de methode **DeleteIfExists** aan op het object **CloudQueue.**

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

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van wachtrijopslag hebt geleerd, volgt u deze koppelingen om meer te weten te komen over complexere opslagtaken.

* [Azure Storage SDK voor Java][Azure Storage SDK for Java]
* [Azure Storage Client SDK-referentie][Azure Storage Client SDK Reference]
* [REST-API voor Azure Storage-services][Azure Storage Services REST API]
* [Blog van azure-opslagteam][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK-referentie]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
