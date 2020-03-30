---
title: Aan de slag met wachtrijopslag met Visual Studio (cloudservices)
description: Azure Queue-opslag gebruiken in een cloudserviceproject in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met visual studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603bb2b9a862ad4ed2cbde63e2d82b9a82fbeaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298789"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Aan de slag met aan Azure Queue Storage en Visual Studio verbonden services (cloudserviceprojecten)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag met Azure Queue-opslag in Visual Studio nadat u een Azure-opslagaccount in een cloudservicesproject hebt gemaakt of ernaar hebt verwezen met behulp van het dialoogvenster **Verbonden services toevoegen** van Visual Studio.

We laten je zien hoe je een wachtrij in code maakt. We laten u ook zien hoe u basiswachtrijbewerkingen uitvoeren, zoals het toevoegen, wijzigen, lezen en verwijderen van wachtrijberichten. De voorbeelden zijn geschreven in C#-code en gebruiken de [Microsoft Azure Storage Client Library voor .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Met de bewerking **Verbonden services toevoegen** worden de juiste NuGet-pakketten geïnstalleerd om toegang te krijgen tot Azure-opslag in uw project en wordt de verbindingstekenreeks voor het opslagaccount toegevoegd aan uw projectconfiguratiebestanden.

* Zie [Aan de slag met Azure Queue-opslag met .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie over het manipuleren van wachtrijen in code.
* Zie [Opslagdocumentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.
* Zie [Cloud Services-documentatie](https://azure.microsoft.com/documentation/services/cloud-services/) voor algemene informatie over Azure-cloudservices.
* Zie [ASP.NET](https://www.asp.net) voor meer informatie over het programmeren ASP.NET toepassingen.

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount.

## <a name="access-queues-in-code"></a>Wachtrijen in code openen
Als u wachtrijen wilt openen in Visual Studio Cloud Services-projecten, moet u de volgende items opnemen in een C#-bronbestand dat toegang heeft tot Azure Queue-opslag.

1. Zorg ervoor dat de naamruimtedeclaratie boven aan het C#-bestand deze **bevat met behulp van** instructies.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Download een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de tekenreeks van uw opslagverbinding en opslagaccountgegevens uit de Azure-serviceconfiguratie te halen.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Zorg ervoor dat een **CloudQueueClient-object** verwijst naar de wachtrijobjecten in uw opslagaccount.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Zorg ervoor dat een **CloudQueue-object** verwijst naar een specifieke wachtrij.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**LET OP:** Gebruik alle bovenstaande code voor de code in de volgende monsters.

## <a name="create-a-queue-in-code"></a>Een wachtrij in code maken
Als u de wachtrij in code wilt maken, voegt u een aanroep toe aan **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Een bericht toevoegen aan een wachtrij
Als u een bericht wilt invoegen in een bestaande wachtrij, maakt u een nieuw **cloudqueuemessage-object** en roept u vervolgens de methode **AddMessage** aan.

Een **CloudQueueMessage-object** kan worden gemaakt op basis van een tekenreeks (in UTF-8-indeling) of een bytearray.

Hier is een voorbeeld dat het bericht 'Hallo, Wereld' invoegt.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Een bericht in een wachtrij lezen
U kunt het bericht vooraan in een wachtrij bekijken zonder het uit de wachtrij te verwijderen, door de methode **PeekMessage** aan te roepen.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Een bericht in een wachtrij lezen en verwijderen
Uw code kan een bericht in twee stappen uit een wachtrij verwijderen (de wachtrij) verwijderen.

1. Bel **GetMessage** om het volgende bericht in een wachtrij te krijgen. Een bericht dat wordt geretourneerd door **GetMessage**, wordt onzichtbaar voor andere codes die berichten lezen uit deze wachtrij. Standaard blijft het bericht onzichtbaar gedurende 30 seconden.
2. Als u wilt eindigen met het verwijderen van het bericht uit de wachtrij, roept u **DeleteMessage aan**.

Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. De volgende code roept **DeleteMessage** direct nadat het bericht is verwerkt.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Extra opties gebruiken om wachtrijberichten te verwerken en te verwijderen
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen.

* U een partij berichten (tot 32) krijgen.
* U een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd heeft om elk bericht volledig te verwerken. In het volgende codevoorbeeld wordt de methode **GetMessages** gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp van een **foreach**-lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd voor ogen dat de periode van 5 minuten voor alle berichten op hetzelfde moment start. Nadat er 5 minuten zijn verstreken sinds de aanroep van **GetMessages**, worden dus alle berichten die niet zijn verwijderd, opnieuw zichtbaar.

Hier volgt een voorbeeld:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen
U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode **FetchAttributes** vraagt de Queue-service de wachtrij-kenmerken, zoals het aantal berichten, op te halen. De eigenschap **ApproximateMethodCount** retourneert de laatste waarde die is opgehaald door de methode **FetchAttributes,** zonder de wachtrijservice aan te roepen.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Het Async-wachtpatroon gebruiken met algemene Azure Queue API's
In dit voorbeeld ziet u hoe u het Async-Await-patroon gebruikt met algemene Azure Queue API's. Het voorbeeld roept de async-versie van elk van de gegeven methoden, dit kan worden gezien door de **Async** post-fix van elke methode. Wanneer een async-methode wordt gebruikt, wordt de lokale uitvoering opgeschort totdat de aanroep is voltooid. Met dit gedrag kan de huidige thread ander werk doen dat prestatieknelpunten helpt voorkomen en de algehele responsiviteit van uw toepassing verbetert. Zie [Async en Await (C# en Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) voor meer informatie over het gebruik van het Async-Await-patroon in .NET.

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Een wachtrij verwijderen
Als u een wachtrij en alle berichten hierin wilt verwijderen, roept u de methode **Delete** aan in het wachtrijobject.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

