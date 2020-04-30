---
title: 'Snelstartgids: Azure Queue Storage-bibliotheek V12-java'
description: Informatie over het gebruik van de Azure Queue Java V12-bibliotheek voor het maken van een wachtrij en het toevoegen van berichten aan de wachtrij. Vervolgens leert u hoe u berichten leest en verwijdert uit de wachtrij. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 013281db2821c99f6942edd1322f4978e100c144
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81729855"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Quick Start: Azure Queue Storage-client bibliotheek V12 voor Java

Aan de slag met de Azure Queue Storage-client bibliotheek versie 12 voor Java. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten die later worden opgehaald en verwerkt. Volg deze stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Gebruik de Azure Queue Storage-client bibliotheek V12 voor Java naar:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten van een wachtrij ontvangen en verwijderen
* Een wachtrij verwijderen

[API reference documentation](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | Voor[beelden](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples) van de API-referentie[bibliotheek broncode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [pakket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | 

## <a name="prerequisites"></a>Vereisten

* JDK-versie 8 of hoger van [Java Development Kit](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-client bibliotheek V12 voor Java.

### <a name="create-the-project"></a>Het project maken

Maak een Java-toepassing *met de naam queues-Quick Start-V12*.

1. In een console venster (zoals cmd, Power shell of bash), gebruikt u maven om een nieuwe console-app te maken met de naam *wachtrijen-Quick Start-V12*. Typ de volgende **MVN** -opdracht voor het maken van een Hello World! Java-project.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. De uitvoer van het project genereren ziet er ongeveer als volgt uit:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Schakel over naar de nieuwe map *wacht rijen-Quick Start-V12* .

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand *pom. XML* in de tekst editor. Voeg het volgende afhankelijkheids element toe aan de groep met afhankelijkheden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Ga naar de map */src/main/Java/com/queues/QuickStart*
1. Open het *app. java* -bestand in uw editor
1. De `System.out.println("Hello world!");` instructie verwijderen
1. Instructies `import` toevoegen

Hier volgt de code:

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Object model

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrij bericht kan Maxi maal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteits limiet van een opslag account. Wacht rijen worden vaak gebruikt om een achterstand te maken voor het asynchroon verwerken van werk. Queue Storage biedt drie typen resources:

* Het opslag account
* Een wachtrij in het opslag account
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de architectuur van de wachtrij opslag](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende Java-klassen om te communiceren met deze resources:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): met `QueueClientBuilder` de klasse wordt een `QueueClient` object geconfigureerd en geïnstantieerd.
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): met `QueueServiceClient` de kunt u alle wacht rijen in uw opslag account beheren.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): de `QueueClient` klasse biedt u de mogelijkheid om een afzonderlijke wachtrij en de bijbehorende berichten te beheren en te bewerken.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): de `QueueMessageItem` klasse vertegenwoordigt de afzonderlijke objecten die worden geretourneerd bij het aanroepen van [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-client bibliotheek voor Java:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages -in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten van een wachtrij ontvangen en verwijderen](#receive-and-delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op. De connection string wordt de omgevings variabele opgeslagen die u hebt gemaakt in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) .

Voeg deze code toe in `main` de-methode:

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Een wachtrij maken

Kies een naam voor de nieuwe wachtrij. De onderstaande code voegt een GUID-waarde toe aan de wachtrij naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> De naam van een wachtrij mag alleen kleine letters, cijfers en afbreek streepjes bevatten en moet beginnen met een letter of een cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie de [naamgeving van wacht rijen en meta gegevens](/rest/api/storageservices/naming-queues-and-metadata)voor meer informatie over de naamgeving van wacht rijen.


Maak een instantie van de klasse [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) . Roep vervolgens de [Create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) -methode aan om de wachtrij in uw opslag account te maken.

Voeg deze code toe aan het einde van `main` de methode:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Berichten toevoegen aan een wachtrij

Met het volgende code fragment worden berichten toegevoegd aan de wachtrij door de [SendMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) -methode aan te roepen. Er wordt ook een [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) opgeslagen dat door `sendMessage` een aanroep wordt geretourneerd. Het resultaat wordt gebruikt om het bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van `main` de methode:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) aan te roepen. Met `peelkMessages` de methode worden een of meer berichten van het begin van de wachtrij opgehaald, maar wordt de zicht baarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van `main` de methode:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

De inhoud van een bericht bijwerken door de methode [updateMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) aan te roepen. De `updateMessage` -methode kan de time-out en inhoud van de zicht baarheid van een bericht wijzigen. De inhoud van het bericht moet een teken reeks met UTF-8-code ring zijn met een grootte van Maxi maal 64 KB. Naast de nieuwe inhoud voor het bericht, geeft u de bericht-ID en de pop-ontvangst `SendMessageResult` door met behulp van de die eerder in de code is opgeslagen. De bericht-ID en pop-ontvangst bevestiging bepalen welk bericht moet worden bijgewerkt.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen en verwijderen

Down load eerder toegevoegde berichten door de [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) -methode aan te roepen. De voorbeeld code verwijdert ook berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval wordt de verwerking gewoon het bericht op de console weer gegeven.

De app wordt gepauzeerd voor gebruikers invoer `System.console().readLine();` door aan te roepen voordat de berichten worden ontvangen en verwijderd. Controleer in uw [Azure Portal](https://portal.azure.com) dat de resources correct zijn gemaakt, voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij om ze te kunnen verwerken.

Voeg deze code toe aan het einde van `main` de methode:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de wachtrij te verwijderen met de methode [Delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--) .

Voeg deze code toe aan het einde van `main` de methode:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en toegevoegd aan een Azure-wachtrij. De code vermeldt de berichten in de wachtrij, haalt deze vervolgens op en verwijdert deze voordat u de wachtrij definitief verwijdert.

In het console venster gaat u naar de toepassingsmap en bouwt u de toepassing en voert u deze uit.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende `mvn` opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Wanneer de app wordt onderbroken voordat er berichten worden ontvangen, controleert u uw opslag account in de [Azure Portal](https://portal.azure.com). Controleer of de berichten in de wachtrij staan.

Druk op **Enter** om de berichten te ontvangen en te verwijderen. Wanneer u hierom wordt gevraagd, drukt u nogmaals op **Enter** om de wachtrij te verwijderen en de demo te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een wachtrij maakt en er berichten aan toevoegt met behulp van Java-code. Vervolgens hebt u geleerd hoe u berichten kunt bekijken, ophalen en verwijderen. Ten slotte hebt u geleerd hoe u een berichten wachtrij verwijdert.

Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Azure voor Java-cloudontwikkelaars](https://docs.microsoft.com/azure/developer/java/)

* Als u meer voor beelden wilt zien van Azure Queue Storage-voor beeld-apps, gaat u verder met [Azure Queue Storage SDK V12 Java-client bibliotheek voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
