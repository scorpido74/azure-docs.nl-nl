---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met .NET (nieuwste)
description: Dit artikel bevat een overzicht van het maken van een .NET core-toepassing die gebeurtenissen naar/van Azure Event Hubs verzendt/ontvangt met behulp van het meest recente Azure. Messa ging. Event hubs-pakket.
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
ms.date: 04/20/2020
ms.author: spelluru
ms.openlocfilehash: fd4b41cc2fe97ad0c2f075884e21f4f2ffc01561
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159451"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Gebeurtenissen verzenden naar en ontvangen van Azure Event Hubs-.NET core (Azure. Messa ging. Event hubs) 
In deze Quick start ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van de **Azure. Messa ging. Event hubs** .net core-bibliotheek. 

> [!IMPORTANT]
> Deze Snelstartgids maakt gebruik van de nieuwe **Azure. Messa ging. Event hubs** -bibliotheek. Zie [gebeurtenissen verzenden en ontvangen met behulp van de micro soft. Azure. Event hubs-bibliotheek](event-hubs-dotnet-standard-getstarted-send.md)voor een Snelstartgids die gebruikmaakt van de oude bibliotheek **micro soft. Azure. Event hubs** . 



## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u [Event hubs Overview](event-hubs-about.md) voordat u deze Snelstartgids uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure abonnement**. Als u Azure-Services, met inbegrip van Azure Event Hubs, wilt gebruiken, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/free/) of de voor delen van uw MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- **Micro soft Visual Studio 2019**. De Azure Event Hubs-client bibliotheek maakt gebruik van nieuwe functies die zijn geïntroduceerd in C# 8,0.  U kunt de bibliotheek nog steeds gebruiken met oudere versies van C#, maar sommige functies zijn niet beschikbaar.  Als u deze functies wilt inschakelen, moet u [.net Core 3,0 bereiken](/dotnet/standard/frameworks#how-to-specify-target-frameworks) of [de taal versie opgeven](/dotnet/csharp/language-reference/configure-language-version#override-a-default) die u wilt gebruiken (8,0 of hoger). Als u Visual Studio gebruikt, versies vóór Visual Studio 2019 zijn niet compatibel met de hulpprogram ma's die nodig zijn om C# 8,0-projecten te bouwen. Visual Studio 2019, met inbegrip van de gratis Community Edition, kan [hier](https://visualstudio.microsoft.com/vs/) worden gedownload
- **Een event hubs naam ruimte en een event hub maken**. De eerste stap is het gebruik van de [Azure Portal](https://portal.azure.com) om een naam ruimte van het type Event hubs te maken en de beheer referenties te verkrijgen die uw toepassing nodig heeft om met de Event hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de **Connection String voor de Event hubs naam ruimte** door de volgende instructies uit het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de connection string verderop in deze Quick Start.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een .NET core-console toepassing maakt voor het verzenden van gebeurtenissen naar een Event Hub. 

### <a name="create-a-console-application"></a>Een consoletoepassing maken

1. Start Visual Studio 2019. 
1. Selecteer **een nieuw project maken**. 
1. Ga als volgt te werk in het dialoog venster **een nieuw project maken** : als u dit dialoog venster niet ziet, selecteert u **bestand** in het menu, selecteert u **Nieuw**en selecteert u vervolgens **project**. 
    1. Selecteer **C#** voor de programmeer taal.
    1. Selecteer een **console** voor het type toepassing. 
    1. Selecteer **console-app (.net core)** in de lijst met resultaten. 
    1. Selecteer vervolgens **volgende**. 

        ![Het dialoogvenster New Project](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Voer **EventHubsSender** in voor de project naam **EventHubsQuickStart** voor de naam van de oplossing en selecteer **OK** om het project te maken. 

    ![C# >-console-app](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **tools** > **NuGet package manager** > **Package Manager console** in het menu. 
1. Voer de volgende opdracht uit om het **Azure. Messa ging. Event hubs** NuGet-pakket te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

1. Voeg de volgende `using` -instructies toe boven aan het **Program.cs** -bestand:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Voeg constanten toe aan `Program` de klasse voor de Event hubs Connection String en de naam van de Event hub. Vervang tijdelijke aanduidingen tussen vier Kante haken door de juiste waarden die u hebt verkregen bij het maken van de Event Hub. Zorg ervoor dat `{Event Hubs namespace connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Vervang de `Main` -methode door de `async Main` volgende methode. Zie de opmerkingen bij de code voor meer informatie. 

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
6. Voer het programma uit en wacht op het bevestigings bericht. 
7. In de Azure Portal kunt u controleren of de Event Hub de berichten heeft ontvangen. Schakel over naar de weer gave **berichten** in het gedeelte **metrische gegevens** . Vernieuw de pagina om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weer gegeven dat de berichten zijn ontvangen. 

    [![Controleer of het Event Hub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Voor de volledige bron code met meer informatieve opmerkingen raadpleegt u [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie wordt beschreven hoe u een .NET core-console toepassing schrijft die berichten ontvangt van een Event Hub met behulp van een gebeurtenis processor. De gebeurtenis processor vereenvoudigt het ontvangen van gebeurtenissen van Event hubs door permanente controle punten en parallelle ontvangst van deze event hubs te beheren. Een gebeurtenis processor is gekoppeld aan een specifieke Event hub en een Consumer groep. Er worden gebeurtenissen van meerdere partities in de Event Hub ontvangen en door gegeven aan een handler-gemachtigde voor verwerking met code die u opgeeft. 


> [!NOTE]
> Als u gebruikmaakt van Azure Stack hub, ondersteunt dat platform mogelijk een andere versie van de opslag-BLOB-SDK dan die doorgaans beschikbaar is op Azure. Als u bijvoorbeeld werkt met [Azure stack hub versie 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), is de hoogste beschik bare versie van de opslag service versie 2017-11-09. In dit geval moet u, naast de volgende stappen in deze sectie, ook code toevoegen om de API-versie 2017-11-09 van de Storage-service te richten. Zie voor een voor beeld van het richten op een specifieke opslag-API-versie [dit voor beeld op github](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). Raadpleeg [Azure stack hub Storage: verschillen en overwegingen](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)voor meer informatie over de versies van de Azure Storage-service die op Azure stack hub worden ondersteund.

### <a name="create-an-azure-storage-and-a-blob-container"></a>Een Azure Storage en een BLOB-container maken
In deze Quick Start gebruikt u Azure Storage als controlepunt opslag. Volg deze stappen om een Azure Storage-account te maken. 

1. [Een Azure Storage-account maken](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Een blob-container maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De connection string ophalen voor het opslag account](../storage/common/storage-configure-connection-string.md)

    Noteer de connection string en de naam van de container. U gebruikt deze in de receive-code. 


### <a name="create-a-project-for-the-receiver"></a>Een project maken voor de ontvanger

1. Klik in het Solution Explorer venster met de rechter muisknop op de oplossing **EventHubQuickStart** , wijs **toevoegen**aan en selecteer **Nieuw project**. 
1. Selecteer **console-app (.net core)** en selecteer **volgende**. 
1. Voer **EventHubsReceiver** in als **project naam**en selecteer **maken**. 

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **tools** > **NuGet package manager** > **Package Manager console** in het menu. 
1. Voer de volgende opdracht uit om het **Azure. Messa ging. Event hubs** NuGet-pakket te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Voer de volgende opdracht uit om het **Azure. Messa ging. Event hubs. processor** NuGet-pakket te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>De methode Main bijwerken 

1. Voeg de volgende `using` instructies toe boven aan het **Program.cs** -bestand.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Voeg constanten toe aan `Program` de klasse voor de Event hubs Connection String en de naam van de Event hub. Vervang tijdelijke aanduidingen tussen vier Kante haken door de juiste waarden die u hebt verkregen bij het maken van de Event Hub. Vervang tijdelijke aanduidingen tussen vier Kante haken door de juiste waarden die u hebt verkregen bij het maken van de Event Hub en het opslag account (toegangs sleutels-primaire connection string). Zorg ervoor dat `{Event Hubs namespace connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Vervang de `Main` -methode door de `async Main` volgende methode. Zie de opmerkingen bij de code voor meer informatie. 

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
1. Voeg nu de volgende methoden voor gebeurtenis-en fout afhandeling toe aan de klasse. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
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
    > Zie [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs)voor de volledige bron code met meer informatieve opmerkingen.
6. Voer de toepassing receiver uit. 
1. Er wordt een bericht weer gegeven dat de gebeurtenis is ontvangen. 

    ![Gebeurtenis ontvangen](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Deze gebeurtenissen zijn de drie gebeurtenissen die u eerder naar de Event Hub hebt verzonden door het programma Sender uit te voeren. 


## <a name="next-steps"></a>Volgende stappen
Bekijk de voor beelden op GitHub. 

- [Voor beelden Event Hubs op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Voor beelden van gebeurtenis processoren op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Voor beeld van op rollen gebaseerd toegangs beheer (RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
