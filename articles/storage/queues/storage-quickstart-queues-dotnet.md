---
title: 'Snelstart: Azure Queue storage library v12 - .NET'
description: Meer informatie over het gebruik van de Azure Queue .NET v12-bibliotheek om een wachtrij te maken en berichten toe te voegen aan de wachtrij. Vervolgens leert u hoe u berichten uit de wachtrij lezen en verwijderen. U leert ook hoe u een wachtrij verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: c69aa91596ff203445aa4fa3ccd59001ffe16649
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78197484"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Snelstart: Azure Queue storage client library v12 voor .NET

Aan de slag met de Azure Queue-opslagclientbibliotheekversie 12 voor .NET. Azure Queue-opslag is een service voor het opslaan van grote aantallen berichten voor later ophalen en verwerken. Volg deze stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken.

> [!NOTE]
> Zie [Snelaan deed de Azure Storage SDK v11 voor .NET gebruiken om een wachtrij te beheren om](storage-quickstart-queues-dotnet-legacy.md)aan de slag te gaan met de vorige SDK-versie.

Gebruik de Azure Queue-opslagclientbibliotheek v12 voor .NET om:

* Een wachtrij maken
* Berichten toevoegen aan een wachtrij
* Berichten in een wachtrij bekijken
* Een bericht in een wachtrij bijwerken
* Berichten ontvangen vanuit een wachtrij
* Berichten uit een wachtrij verwijderen
* Een wachtrij verwijderen

[API-naslagdocumentatie](/dotnet/api/azure.storage.queues) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Azure-opslagaccount - [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidige [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem. Zorg ervoor dat u de SDK en niet de runtime.

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Queue-opslagclientbibliotheek v12 voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak een .NET Core-toepassing met de naam *QueuesQuickstartV12*.

1. Gebruik de `dotnet new` opdracht om in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe console-app te maken met de naam *QueuesQuickstartV12*. Met deze opdracht wordt een eenvoudig "Hello World" C#-project gemaakt met één bronbestand: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Overschakelen naar de nieuw gemaakte *queuesQuickstartV12-map.*

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Installeer de azure queue-opslagclientbibliotheek voor .NET-pakket met `dotnet add package` behulp van de opdracht terwijl u zich nog in de toepassingsmap bevindt.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Het *Program.cs-bestand* openen in uw editor
1. De `Console.WriteLine("Hello World!");` instructie verwijderen
1. Richtlijnen `using` toevoegen
1. De `Main` methodedeclaratie bijwerken om [async-code](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main) te ondersteunen



Hier is de code:

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

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een werkachterstand te maken om asynchroon te verwerken. Wachtrijopslag biedt drie soorten resources:

* Het opslagaccount
* Een wachtrij in het opslagaccount
* Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram met wachtrijopslagarchitectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende .NET-klassen om met deze bronnen te communiceren:

* [QueueServiceClient:](/dotnet/api/azure.storage.queues.queueserviceclient) `QueueServiceClient` Hiermee u alle wachtrijen in uw opslagaccount beheren.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` Met de klasse u een afzonderlijke wachtrij en de bijbehorende berichten beheren en manipuleren.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` De klasse vertegenwoordigt de afzonderlijke objecten die zijn geretourneerd bij het aanroepen [van ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) in een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u de volgende acties uitvoeren met de Azure Queue-opslagclientbibliotheek voor .NET:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een wachtrij maken](#create-a-queue)
* [Berichten toevoegen aan een wachtrij](#add-messages-to-a-queue)
* [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
* [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
* [Berichten ontvangen vanuit een wachtrij](#receive-messages-from-a-queue)
* [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
* [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op. De verbindingstekenreeks wordt opgeslagen in de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `Main` toe in de methode:

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

Bepaal een naam voor de nieuwe wachtrij. De onderstaande code voegt een GUID-waarde toe aan de naam van de wachtrij om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en koppeltekens bevatten en moeten beginnen met een letter of een getal. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet ook tussen de 3 en 63 tekens lang zijn. Zie [Wachtrijen en metagegevens voor](/rest/api/storageservices/naming-queues-and-metadata)meer informatie over het benoemen van wachtrijen.


Maak een instantie van de klasse [QueueClient.](/dotnet/api/azure.storage.queues.queueclient) Roep vervolgens de [createAsync-methode](/dotnet/api/azure.storage.queues.queueclient.createasync) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe `Main` aan het einde van de methode:

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

In het volgende codefragment worden berichten asynchroon toegevoegd aan de wachtrij door de methode [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) aan te roepen. Het slaat ook een [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) geretourneerd uit een `SendMessageAsync` oproep. Het ontvangstbewijs wordt gebruikt om het bericht later in het programma bij te werken.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de [methode PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) aan te roepen. De `PeekMessagesAsync` methode haalt een of meer berichten op aan de voorkant van de wachtrij, maar verandert niets aan de zichtbaarheid van het bericht.

Voeg deze code toe `Main` aan het einde van de methode:

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

Werk de inhoud van een bericht bij door de [methode UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) aan te roepen. De `UpdateMessageAsync` methode kan de zichtbaarheidstime-out en inhoud van een bericht wijzigen. De inhoud van het bericht moet een utf-8 gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Samen met de nieuwe inhoud voor het bericht `SendReceipt` geeft u de waarden door van de waarden die eerder in de code zijn opgeslagen. De `SendReceipt` waarden bepalen welk bericht moet worden bijgewerkt.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Berichten ontvangen vanuit een wachtrij

Download eerder toegevoegde berichten door de [methode ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) aan te roepen.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Verwijder berichten uit de wachtrij nadat ze zijn verwerkt. In dit geval wordt het bericht alleen weergegeven op de console.

De app pauzeert voor `Console.ReadLine` gebruikersinvoer door te bellen voordat deze wordt verwerkt en de berichten wordt verwijderd. Controleer in uw [Azure-portal](https://portal.azure.com) of de resources correct zijn gemaakt voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij voor een nieuwe kans om ze te verwerken.

Voeg deze code toe `Main` aan het einde van de methode:

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

Met de volgende code worden de resources die de app heeft gemaakt door de wachtrij te verwijderen met de [deleteasync-methode,](/dotnet/api/azure.storage.queues.queueclient.deleteasync) verwijderd.

Voeg deze code toe `Main` aan het einde van de methode:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt en voegt drie berichten toe aan een Azure-wachtrij. De code geeft een lijst van de berichten in de wachtrij en haalt ze op en verwijdert ze, voordat u de wachtrij uiteindelijk verwijdert.

Navigeer in het consolevenster naar de toepassingsmap en bouw en voer de toepassing uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

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

Wanneer de app wordt onderbroken voordat u berichten ontvangt, controleert u uw opslagaccount in de [Azure-portal.](https://portal.azure.com) Controleer of de berichten in de wachtrij staan.

Druk op **enter** om de berichten te ontvangen en te verwijderen. Druk de sein **enter** opnieuw in om de wachtrij te verwijderen en de demo af te ronden wanneer u daarom wordt gevraagd.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een wachtrij maken en er berichten aan toevoegen met behulp van asynchrone .NET-code. Vervolgens heb je geleerd om te gluren, op te halen en berichten te verwijderen. Ten slotte hebt u geleerd hoe u een berichtenwachtrij verwijderen.

Voor tutorials, voorbeelden, snelle starts en andere documentatie, bezoek:

> [!div class="nextstepaction"]
> [Azure voor ontwikkelaars van .NET en .NET Core](https://docs.microsoft.com/dotnet/azure/)

* Zie de Azure [Storage-bibliotheken voor .NET voor](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)meer informatie.
* Ga verder naar voorbeeldvoorbeelden van [Azure Queue-opslag v12 .NET-clientbibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)voor meer voorbeeld-apps voor Azure Queue-opslag .
* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
