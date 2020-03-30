---
title: Aan de slag met wachtrijopslag met Visual Studio (ASP.NET Core)
description: Aan de slag met Azure-wachtrijopslag in een ASP.NET Core-project in Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300022"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Aan de slag met wachtrijopslag en Visual Studio connected services (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

In dit artikel wordt beschreven hoe u aan de slag met Azure Queue-opslag in Visual Studio nadat u een Azure-opslagaccount hebt gemaakt of verwezen in een ASP.NET Core-project met behulp van de functie Visual Studio **Connected Services.** De **bewerking Verbonden Services** installeert de juiste NuGet-pakketten om toegang te krijgen tot Azure-opslag in uw project en voegt de verbindingstekenreeks voor het opslagaccount toe aan uw projectconfiguratiebestanden. (Zie [Opslagdocumentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.)

Azure-wachtrijopslag is een service voor het opslaan van grote aantallen berichten die overal ter wereld toegankelijk zijn via geverifieerde oproepen via HTTP of HTTPS. Een bericht met één wachtrij kan maximaal 64 kilobytes (KB) groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount. Zie Ook [Aan de slag met Azure Queue-opslag met .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie over het programmatisch manipuleren van wachtrijen.

Maak eerst een Azure-wachtrij in uw opslagaccount om aan de slag te gaan. In dit artikel wordt vervolgens uitgelegd hoe u een wachtrij in C# maakt en hoe u basiswachtrijbewerkingen uitvoeren, zoals het toevoegen, wijzigen, lezen en verwijderen van wachtrijberichten.  De code maakt gebruik van de Azure Storage Client Library voor .NET. Zie [ASP.NET](https://www.asp.net)voor meer informatie over ASP.NET.

Sommige Azure Storage API's zijn asynchroon en de code in dit artikel gaat ervan uit dat async-methoden worden gebruikt. Zie [Asynchrone programmering](https://docs.microsoft.com/dotnet/csharp/async) voor meer informatie.

## <a name="access-queues-in-code"></a>Wachtrijen in code openen

Als u wachtrijen wilt openen in ASP.NET Core-projecten, neemt u de volgende items op in een C#-bronbestand dat toegang heeft tot Azure-wachtrijopslag. Gebruik al deze code voor de code in de volgende secties.

1. Voeg de `using` nodige instructies toe:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Download `CloudStorageAccount` een object dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de gegevens van de opslagverbinding en opslagaccount op te halen uit de Azure-serviceconfiguratie:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Zorg `CloudQueueClient` ervoor dat een object verwijst naar de wachtrijobjecten in uw opslagaccount:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Een `CloudQueue` object naar een specifieke wachtrij verwijzen:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Een wachtrij in code maken

Als u de Azure-wachtrij `CreateIfNotExistsAsync`in code wilt maken, belt u:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Een bericht toevoegen aan een wachtrij

Als u een bericht wilt invoegen `CloudQueueMessage` in een `AddMessageAsync` bestaande wachtrij, maakt u een nieuw object en roept u de methode aan. Een `CloudQueueMessage` object kan worden gemaakt op basis van een tekenreeks (in utf-8-indeling) of een bytearray.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Een bericht in een wachtrij lezen

U het bericht voor in een wachtrij bekijken zonder het `PeekMessageAsync` uit de wachtrij te verwijderen door de methode aan te roepen:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Een bericht in een wachtrij lezen en verwijderen

Uw code kan een bericht in twee stappen uit een wachtrij verwijderen (de queue) verwijderen (de queue).

1. Bel `GetMessageAsync` om het volgende bericht in een wachtrij te krijgen. Een bericht `GetMessageAsync` dat wordt geretourneerd, wordt onzichtbaar voor andere codeleesberichten uit deze wachtrij. Standaard blijft het bericht onzichtbaar gedurende 30 seconden.
1. Als u wilt eindigen met `DeleteMessageAsync`het verwijderen van het bericht uit de wachtrij, belt u .

Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. De volgende `DeleteMessageAsync` codeoproepen direct nadat het bericht is verwerkt:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Extra opties voor het dequeueren van berichten

Er zijn twee manieren om het ophalen van berichten uit een wachtrij aan te passen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht. In het volgende `GetMessages` codevoorbeeld wordt de methode gebruikt om 20 berichten in één gesprek te ontvangen. Vervolgens verwerkt het elk `foreach` bericht met behulp van een lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd er rekening mee dat de timer van vijf minuten voor alle berichten tegelijkertijd wordt gestart, dus na vijf minuten worden berichten die niet zijn verwijderd weer zichtbaar.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De `FetchAttributes` methode vraagt de wachtrijservice om de wachtrijkenmerken op te halen, inclusief het aantal berichten. De `ApproximateMethodCount` eigenschap retourneert de `FetchAttributes` laatste waarde die door de methode is opgehaald, zonder de wachtrijservice aan te roepen.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Het Async-Wacht-patroon gebruiken met algemene wachtrij-API's

In dit voorbeeld ziet u hoe u het async-wachtpatroon gebruikt met algemene wachtrij-API's die eindigen met `Async`. Wanneer een async-methode wordt gebruikt, wordt de lokale uitvoering van de async-wacht opgeschort totdat de aanroep is voltooid. Met dit gedrag kan de huidige thread ander werk doen dat prestatieknelpunten helpt voorkomen en de algehele responsiviteit van uw toepassing verbetert.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

Als u een wachtrij en alle berichten in `Delete` deze wachtrij wilt verwijderen, roept u de methode op het wachtrijobject aan:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
