---
title: 'Snelstart: Azure Queue storage library v12 - Java'
description: Meer informatie over het gebruik van de Azure Queue Java v12-bibliotheek om een wachtrij te maken en berichten toe te voegen aan de wachtrij. Vervolgens leert u hoe u berichten uit de wachtrij lezen en verwijderen. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 013281db2821c99f6942edd1322f4978e100c144
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729855"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Snelstart: Azure Queue storage client library v12 voor Java

Aan de slag met de Azure Queue-opslagclientbibliotheekversie 12 voor Java. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten voor later ophalen en verwerken. Volg deze stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken.

Gebruik de Azure Queue-opslagclientbibliotheek v12 voor Java om:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten uit een wachtrij ontvangen en verwijderen
* Een wachtrij verwijderen

[API-naslagdocumentatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

* [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) versie 8 of hoger
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Azure-opslagaccount - [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Queue-opslagclientbibliotheek v12 voor Java.

### <a name="create-the-project"></a>Het project maken

Maak een Java-applicatie met de naam *queues-quickstart-v12*.

1. Gebruik Maven in een consolevenster (zoals cmd, PowerShell of Bash) om een nieuwe console-app te maken met de naam *queues-quickstart-v12*. Typ de volgende **mvn-opdracht** om een 'Hallo wereld! Java-project.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. De output van het genereren van het project moet er ongeveer als volgt uitzien:

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

1. Schakel over naar de nieuw gemaakte *map snelstart-v12 voor wachtrijen.*

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Open het *pom.xml-bestand* in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep afhankelijkheden.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Navigeer naar de map */src/main/java/com/queues/quickstart*
1. Het *app.java-bestand* openen in uw editor
1. De `System.out.println("Hello world!");` instructie verwijderen
1. Richtlijnen `import` toevoegen

Hier is de code:

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

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken. Wachtrijopslag biedt drie soorten resources:

* Het opslagaccount
* Een wachtrij in het opslagaccount
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram met wachtrijopslagarchitectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende Java-klassen om met deze bronnen te communiceren:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): `QueueClientBuilder` de klasse configureert en `QueueClient` instantieert een object.
* [QueueServiceClient:](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html) `QueueServiceClient` Hiermee u alle wachtrijen in uw opslagaccount beheren.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): `QueueClient` Met de klasse u een afzonderlijke wachtrij en de bijbehorende berichten beheren en manipuleren.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): `QueueMessageItem` De klasse vertegenwoordigt de afzonderlijke objecten die zijn geretourneerd bij het aanroepen [van ontvangenBerichten](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) in een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u de volgende acties uitvoeren met de Azure Queue-opslagclientbibliotheek voor Java:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages -in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten uit een wachtrij ontvangen en verwijderen](#receive-and-delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op. De verbindingstekenreeks wordt opgeslagen in de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `main` toe in de methode:

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

Bepaal een naam voor de nieuwe wachtrij. De onderstaande code voegt een GUID-waarde toe aan de naam van de wachtrij om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en koppeltekens bevatten en moeten beginnen met een letter of een getal. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie [Wachtrijen en metagegevens voor](/rest/api/storageservices/naming-queues-and-metadata)meer informatie over het benoemen van wachtrijen.


Maak een instantie van de klasse [QueueClient.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) Roep vervolgens de [methode maken](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe `main` aan het einde van de methode:

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

In het volgende codefragment worden berichten toegevoegd aan de wachtrij door de [verzendmethode aan te](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) roepen. Het slaat ook een [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) geretourneerd van een `sendMessage` oproep. Het resultaat wordt gebruikt om het bericht later in het programma bij te werken.

Voeg deze code toe `main` aan het einde van de methode:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de [methode peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) aan te roepen. De `peelkMessages` methode haalt een of meer berichten op aan de voorkant van de wachtrij, maar verandert niets aan de zichtbaarheid van het bericht.

Voeg deze code toe `main` aan het einde van de methode:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

Werk de inhoud van een bericht bij door de [update-methode van Message](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) aan te roepen. De `updateMessage` methode kan de zichtbaarheidstime-out en inhoud van een bericht wijzigen. De inhoud van het bericht moet een utf-8 gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Samen met nieuwe inhoud voor het bericht, geef in `SendMessageResult` het bericht-ID en pop ontvangstbewijs met behulp van de die eerder is opgeslagen in de code. De bericht-id en de popontvangst geven aan welk bericht u wilt bijwerken.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Berichten uit een wachtrij ontvangen en verwijderen

Download eerder toegevoegde berichten door de methode [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) aan te roepen. De voorbeeldcode verwijdert ook berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval wordt het bericht alleen weergegeven op de console.

De app pauzeert voor `System.console().readLine();` gebruikersinvoer door te bellen voordat de berichten worden ontvangen en verwijderd. Controleer in uw [Azure-portal](https://portal.azure.com) of de resources correct zijn gemaakt voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij voor een nieuwe kans om ze te verwerken.

Voeg deze code toe `main` aan het einde van de methode:

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

Met de volgende code worden de resources die de app heeft gemaakt door de wachtrij te verwijderen met behulp van de [verwijdermethode,](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--) opgeschoond.

Voeg deze code toe `main` aan het einde van de methode:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt en voegt drie berichten toe aan een Azure-wachtrij. De code geeft een lijst van de berichten in de wachtrij en haalt ze op en verwijdert ze, voordat u de wachtrij uiteindelijk verwijdert.

Navigeer in het consolevenster naar de toepassingsmap en bouw en voer de toepassing uit.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de `mvn` volgende opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

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

Wanneer de app wordt onderbroken voordat u berichten ontvangt, controleert u uw opslagaccount in de [Azure-portal.](https://portal.azure.com) Controleer of de berichten in de wachtrij staan.

Druk op **enter** om de berichten te ontvangen en te verwijderen. Druk de sein **enter** opnieuw in om de wachtrij te verwijderen en de demo af te ronden wanneer u daarom wordt gevraagd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je een wachtrij maakt en er berichten aan toevoegt met Java-code. Vervolgens heb je geleerd om te gluren, op te halen en berichten te verwijderen. Ten slotte hebt u geleerd hoe u een berichtenwachtrij verwijderen.

Ga voor zelfstudies, voorbeelden, snelle starts en andere documentatie naar:

> [!div class="nextstepaction"]
> [Azure voor Java-cloudontwikkelaars](https://docs.microsoft.com/azure/developer/java/)

* Ga verder naar voorbeeldvoorbeelden van [Azure Queue-opslagSDK v12 Java-clientbibliotheek.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)
