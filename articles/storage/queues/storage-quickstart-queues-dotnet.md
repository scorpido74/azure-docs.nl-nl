---
title: 'Quick Start: Azure Queue-opslag bibliotheek V12-.NET'
description: Meer informatie over het gebruik van de Azure Queue .NET V12-bibliotheek voor het maken van een wachtrij en het toevoegen van berichten aan de wachtrij. Vervolgens leert u hoe u berichten leest en verwijdert uit de wachtrij. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 71a714124cecfc4f985d448371042c8aff092a11
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463851"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Quick Start: Azure Queue Storage-client bibliotheek V12 voor .NET

Aan de slag met de Azure Queue Storage-client bibliotheek versie 12 voor .NET. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten die later worden opgehaald en verwerkt. Volg deze stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

> [!NOTE]
> Om aan de slag te gaan met de vorige SDK-versie, raadpleegt u [Quick Start: de Azure Storage SDK V11 voor .net gebruiken om een wachtrij te beheren](storage-quickstart-queues-dotnet-legacy.md).

Gebruik de Azure Queue Storage-client bibliotheek V12 voor .NET voor het volgende:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten van een wachtrij ontvangen
* Berichten uit een wachtrij verwijderen
* Een wachtrij verwijderen

[API-referentie documentatie](/dotnet/api/azure.storage.queues) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | [pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | -voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [.net core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem. Zorg ervoor dat u de SDK en niet de runtime ophaalt.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-client bibliotheek V12 voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak een .NET core-toepassing met de naam *QueuesQuickstartV12*.

1. Gebruik in een console venster (zoals cmd, Power shell of bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam *QueuesQuickstartV12*. Met deze opdracht maakt u een eenvoudig ' C# Hallo wereld '-project met één bron bestand: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Schakel over naar de zojuist gemaakte *QueuesQuickstartV12* -map.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u zich nog steeds in de toepassingsmap bevindt, installeert u de Azure Queue Storage-client bibliotheek voor .NET-pakket met behulp van de opdracht `dotnet add package`.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Open het *Program.cs* -bestand in de editor
1. De `Console.WriteLine("Hello World!");`-instructie verwijderen
1. `using`-instructies toevoegen
1. De declaratie van de `Main` methode bijwerken ter [ondersteuning van async-code](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Hier volgt de code:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
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

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): met de `QueueServiceClient` kunt u de alle wacht rijen in uw opslag account beheren.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): de klasse `QueueClient` biedt u de mogelijkheid om een afzonderlijke wachtrij en de bijbehorende berichten te beheren en te bewerken.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): de klasse `QueueMessage` vertegenwoordigt de afzonderlijke objecten die worden geretourneerd bij het aanroepen van [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-client bibliotheek voor .NET:

* [De connection string ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten van een wachtrij ontvangen](#receive-messages-from-a-queue)
* [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op. De connection string wordt opgeslagen in de omgevings variabele die u hebt gemaakt in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) .

Voeg deze code toe in de `Main` methode:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Een wachtrij maken

Kies een naam voor de nieuwe wachtrij. De onderstaande code voegt een GUID-waarde toe aan de wachtrij naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> De naam van een wachtrij mag alleen kleine letters, cijfers en afbreek streepjes bevatten en moet beginnen met een letter of een cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie de [naamgeving van wacht rijen en meta gegevens](/rest/api/storageservices/naming-queues-and-metadata)voor meer informatie over de naamgeving van wacht rijen.


Maak een instantie van de klasse [QueueClient](/dotnet/api/azure.storage.queues.queueclient) . Vervolgens roept u de [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) -methode aan om de wachtrij in uw opslag account te maken.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Berichten toevoegen aan een wachtrij

Met het volgende code fragment worden asynchroon berichten toegevoegd aan de wachtrij door de methode [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) aan te roepen. Er wordt ook een [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) opgeslagen dat door een `SendMessageAsync`-aanroep wordt geretourneerd. De ontvangst wordt gebruikt om het bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) aan te roepen. Met de methode `PeekMessagesAsync` worden een of meer berichten van het begin van de wachtrij opgehaald, maar wordt de zicht baarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

De inhoud van een bericht bijwerken door de methode [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) aan te roepen. Met de methode `UpdateMessageAsync` kan de time-out en inhoud van de zicht baarheid van een bericht worden gewijzigd. De inhoud van het bericht moet een teken reeks met UTF-8-code ring zijn met een grootte van Maxi maal 64 KB. In combi natie met de nieuwe inhoud voor het bericht geeft u de waarden van de `SendReceipt` die eerder in de code zijn opgeslagen. De `SendReceipt` waarden bepalen welk bericht moet worden bijgewerkt.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

Down load eerder toegevoegde berichten door de [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) -methode aan te roepen.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Berichten uit de wachtrij verwijderen nadat ze zijn verwerkt. In dit geval wordt de verwerking gewoon het bericht op de console weer gegeven.

De app wordt gepauzeerd voor gebruikers invoer door `Console.ReadLine` aan te roepen voordat de berichten worden verwerkt en verwijderd. Controleer in uw [Azure Portal](https://portal.azure.com) dat de resources correct zijn gemaakt, voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij om ze te kunnen verwerken.

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de wachtrij te verwijderen met de methode [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync) .

Voeg deze code toe aan het einde van de `Main` methode:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en toegevoegd aan een Azure-wachtrij. De code vermeldt de berichten in de wachtrij, haalt deze vervolgens op en verwijdert deze voordat u de wachtrij definitief verwijdert.

In het console venster gaat u naar de toepassingsmap en bouwt u de toepassing en voert u deze uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Wanneer de app wordt onderbroken voordat er berichten worden ontvangen, controleert u uw opslag account in de [Azure Portal](https://portal.azure.com). Controleer of de berichten in de wachtrij staan.

Druk op **Enter** om de berichten te ontvangen en te verwijderen. Wanneer u hierom wordt gevraagd, drukt u nogmaals op **Enter** om de wachtrij te verwijderen en de demo te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een wachtrij maakt en er berichten aan toevoegt met behulp van asynchrone .NET-code. Vervolgens hebt u geleerd hoe u berichten kunt bekijken, ophalen en verwijderen. Ten slotte hebt u geleerd hoe u een berichten wachtrij verwijdert.

Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Azure voor ontwikkelaars van .NET en .NET Core](https://docs.microsoft.com/dotnet/azure/)

* Zie de [Azure Storage-bibliotheken voor .net voor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)meer informatie.
* Als u meer voor beelden wilt zien van Azure Queue Storage-voor beeld-apps, gaat u naar [Azure Queue Storage V12 .net-client bibliotheek voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
