---
title: Gebeurtenissen verzenden of ontvangen vanuit Azure Event Hubs met .NET (laatste)
description: In dit artikel vindt u een walkthrough voor het maken van een .NET Core-toepassing die gebeurtenissen verzendt/ontvangt van/naar Azure Event Hubs met behulp van het nieuwste Azure.Messaging.EventHubs-pakket.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 7bb9d3ce4c80761362c1ea564f6a632bc7a7f68a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398289"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Gebeurtenissen verzenden naar en ontvangen van gebeurtenissen vanuit Azure Event Hubs - .NET Core (Azure.Messaging.EventHubs) 
In deze snelle start ziet u hoe u gebeurtenissen verzendt naar en ontvangt vanuit een gebeurtenishub met behulp van de **Azure.Messaging.EventHubs** .NET Core-bibliotheek. 

> [!IMPORTANT]
> Deze quickstart maakt gebruik van de nieuwe **Azure.Messaging.EventHubs-bibliotheek.** Zie [Gebeurtenissen verzenden en ontvangen met Microsoft.Azure.EventHubs](event-hubs-dotnet-standard-getstarted-send.md)voor een snelle start waarbij gebruik wordt gemaakt van de oude **Microsoft.Azure.EventHubs-bibliotheek** . 



## <a name="prerequisites"></a>Vereisten
Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. De Azure Event Hubs-clientbibliotheek maakt gebruik van nieuwe functies die zijn geïntroduceerd in C# 8.0.  U de bibliotheek nog steeds gebruiken met oudere versies van C#, maar een deel van de functionaliteit ervan is niet beschikbaar.  Als u deze functies wilt inschakelen, moet u [zich richten op .NET Core 3.0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) of [de taalversie opgeven die](/dotnet/csharp/language-reference/configure-language-version#override-a-default) u wilt gebruiken (8.0 of hoger). Als u Visual Studio gebruikt, zijn versies vóór Visual Studio 2019 niet compatibel met de tools die nodig zijn om C# 8.0-projecten te bouwen. Visual Studio 2019, inclusief de gratis Community-editie, is [hier](https://visualstudio.microsoft.com/vs/) te downloaden
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de **verbindingstekenreeks voor de naamruimte van gebeurtenishubs** door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingstekenreeks later in deze snelstart.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie ziet u hoe u een .NET Core-consoletoepassing maakt om gebeurtenissen naar een gebeurtenishub te verzenden. 

### <a name="create-a-console-application"></a>Een consoletoepassing maken

1. Start Visual Studio 2019. 
1. Selecteer **Een nieuw project maken**. 
1. Voer in het dialoogvenster **Een nieuw project maken** de volgende stappen uit: Als u dit dialoogvenster niet ziet, selecteert u **Bestand** in het menu, selecteert u **Nieuw**en selecteert u **Project**. 
    1. Selecteer **C#** voor de programmeertaal.
    1. Selecteer **Console** voor het type toepassing. 
    1. Selecteer **Console App (.NET Core)** in de lijst met resultaten. 
    1. Selecteer vervolgens **Volgende**. 

        ![Het dialoogvenster New Project](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Voer **EventHubsSender** in voor de **projectnaam, EventHubsQuickStart** voor de naam van de oplossing en selecteer **OK** om het project te maken. 

    ![C# > Console-app](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **Extra** > **NuGet Package Manager** > Package**Manager Console** in het menu. 
1. Voer de volgende opdracht uit om het **NuGet-pakket Azure.Messaging.EventHubs** NuGet te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

1. Voeg de `using` volgende instructies toe aan de bovenkant van het **Program.cs** bestand:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Voeg constanten `Program` toe aan de klasse voor de verbindingstekenreeks Gebeurtenishubs en de naam van de gebeurtenishub. Vervang tijdelijke aanduidingen tussen haakjes met de juiste waarden die u hebt gekregen bij het maken van de gebeurtenishub. Zorg ervoor dat `{Event Hubs namespace connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Vervang `Main` de methode `async Main` door de volgende methode. Zie de code opmerkingen voor meer informatie. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Bouw het project en zorg ervoor dat er geen fouten zijn.
6. Voer het programma uit en wacht op het bevestigingsbericht. 
7. In de Azure-portal u controleren of de gebeurtenishub de berichten heeft ontvangen. Overschakelen naar **berichtenweergave** in de sectie **Statistieken.** Vernieuw de pagina om de grafiek bij te werken. Het kan enkele seconden duren voordat het is aangetoond dat de berichten zijn ontvangen. 

    [![Controleren of de gebeurtenishub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Zie [dit bestand op de GitHub voor](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs) de volledige broncode met meer informatieve opmerkingen

## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie ziet u hoe u een .NET Core-consoletoepassing schrijft die berichten ontvangt van een gebeurtenishub met behulp van een gebeurtenisprocessor. De gebeurtenisprocessor vereenvoudigt het ontvangen van gebeurtenissen van gebeurtenishubs door permanente controlepunten en parallelle ontvangsts van die gebeurtenishubs te beheren. Een gebeurtenisprocessor is gekoppeld aan een specifieke gebeurtenishub en een consumentengroep. Het ontvangt gebeurtenissen van meerdere partities in de gebeurtenishub, waardoor ze worden doorgedeeld aan een handlergemachtigde voor verwerking met behulp van code die u opgeeft. 


> [!NOTE]
> Als u op Azure Stack Hub werkt, kan dat platform een andere versie van Storage Blob SDK ondersteunen dan die welke doorgaans beschikbaar zijn op Azure. Als u bijvoorbeeld op [Azure Stack Hub-versie 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)draait, is de hoogst beschikbare versie voor de opslagservice versie 2017-11-09. In dit geval moet u naast de volgende stappen in deze sectie ook code toevoegen om de API-versie 2017-11-09 van de opslagservice te targeten. Zie [dit voorbeeld op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)voor een voorbeeld over het targeten van een specifieke Storage API-versie. Voor meer informatie over de Azure Storage-serviceversies die worden ondersteund op Azure Stack Hub, raadpleegt u [Azure Stack Hub-opslag: verschillen en overwegingen](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-an-azure-storage-and-a-blob-container"></a>Een Azure Storage en een blobcontainer maken
In deze snelstart gebruikt u Azure Storage als controlepuntarchief. Volg deze stappen om een Azure Storage-account te maken. 

1. [Een Azure Storage-account maken](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Een blob-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De verbindingstekenreeks naar het opslagaccount weerleggen](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Noteer de verbindingstekenreeks en de containernaam. Je gebruikt ze in de ontvangstcode. 


### <a name="create-a-project-for-the-receiver"></a>Een project maken voor de ontvanger

1. Klik in het venster Solution Explorer met de rechtermuisknop op de **Oplossing EventHubQuickStart,** **wijs Toevoegen aan**en selecteer Nieuw **project**. 
1. Selecteer **Console-app (.NET-kern)** en selecteer **Volgende**. 
1. Voer **EventHubsReceiver** in voor de **projectnaam**en selecteer **Maken**. 

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **Extra** > **NuGet Package Manager** > Package**Manager Console** in het menu. 
1. Voer de volgende opdracht uit om het **NuGet-pakket Azure.Messaging.EventHubs** NuGet te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Voer de volgende opdracht uit om het **NuGet-pakket Azure.Messaging.EventHubs.Processor** NuGet te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>De hoofdmethode bijwerken 

1. Voeg de `using` volgende instructies toe boven aan het **Program.cs** bestand.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Voeg constanten `Program` toe aan de klasse voor de verbindingstekenreeks Gebeurtenishubs en de naam van de gebeurtenishub. Vervang tijdelijke aanduidingen tussen haakjes met de juiste waarden die u hebt gekregen bij het maken van de gebeurtenishub. Vervang tijdelijke aanduidingen tussen haakjes door de juiste waarden die u hebt gekregen bij het maken van de gebeurtenishub en het opslagaccount (toegangssleutels - primaire verbindingstekenreeks). Zorg ervoor dat `{Event Hubs namespace connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Vervang `Main` de methode `async Main` door de volgende methode. Zie de code opmerkingen voor meer informatie. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Voeg nu de volgende gebeurtenis- en fouthandlermethoden toe aan de klasse. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
            return Task.CompletedTask; 
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Bouw het project en zorg ervoor dat er geen fouten zijn.

    > [!NOTE]
    > Zie [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs)voor de volledige broncode met meer informatieve opmerkingen.
6. Voer de ontvangertoepassing uit. 
1. U ziet een bericht dat de gebeurtenis is ontvangen. 

    ![Evenement ontvangen](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Deze gebeurtenissen zijn de drie gebeurtenissen die u eerder naar de gebeurtenishub hebt verzonden door het verzendprogramma uit te voeren. 


## <a name="next-steps"></a>Volgende stappen
Bekijk de voorbeelden op GitHub. 

- [Voorbeelden van gebeurtenishubs op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Voorbeelden van gebeurtenisverwerkers op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [RBAC-voorbeeld (Role-based access control)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
