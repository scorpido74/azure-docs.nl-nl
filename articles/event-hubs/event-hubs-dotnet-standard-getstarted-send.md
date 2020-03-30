---
title: Gebeurtenissen verzenden en ontvangen vanuit Azure Event Hubs met .NET (oud)
description: In dit artikel vindt u een walkthrough voor het maken van een .NET Core-app die gebeurtenissen verzendt/ontvangt van/naar Azure Event Hubs met behulp van het oude Microsoft.Azure.EventHubs-pakket.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 1d399ffec2cd702dcfa39310803bc780b85bfde1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187465"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met .NET Core (Microsoft.Azure.EventHubs)
In deze quickstart ziet u hoe u gebeurtenissen verzendt naar en ontvangt vanuit een gebeurtenishub met behulp van de **Microsoft.Azure.EventHubs** .NET Core-bibliotheek.

> [!WARNING]
> Deze quickstart maakt gebruik van het oude **Microsoft.Azure.EventHubs-pakket.** Zie [Gebeurtenissen verzenden en ontvangen met Azure.Messaging.EventHubs](get-started-dotnet-standard-send-v2.md)voor een snelle start die gebruik maakt van de nieuwste **Azure.Messaging.EventHubs-bibliotheek.** Zie de [handleiding voor het migreren van Microsoft.Azure.EventHubs naar Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)om uw toepassing te verplaatsen van het gebruik van de oude bibliotheek naar de nieuwe.

## <a name="prerequisites"></a>Vereisten
Als u nieuw bent in Azure Event Hubs, raadpleegt u [het overzicht van gebeurtenishubs](event-hubs-about.md) voordat u dit snel doet. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, waaronder Azure Event Hubs, hebt u een abonnement nodig.  Als u geen bestaand Azure-account hebt, u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen voor MSDN-abonnees gebruiken wanneer u een [account maakt.](https://azure.microsoft.com)
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [.NET core Visual Studio 2015- of 2017-hulpprogramma's](https://www.microsoft.com/net/core). 
- **Maak een naamruimte voor gebeurtenishubs en een gebeurtenishub**. De eerste stap is om de [Azure-portal](https://portal.azure.com) te gebruiken om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die uw toepassing nodig heeft om te communiceren met de gebeurtenishub. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Download vervolgens de **verbindingstekenreeks voor de naamruimte van de gebeurtenishub** door instructies uit het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingstekenreeks later in deze snelstart.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie ziet u hoe u een .NET Core-consoletoepassing maakt om gebeurtenissen naar een gebeurtenishub te verzenden. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U ook de stappen in deze quickstart volgen om uw eigen stappen te maken.


### <a name="create-a-console-application"></a>Een consoletoepassing maken

Start Visual Studio. Klik in het menu **File** op **New** en klik vervolgens op **Project**. Maak een .NET Core-consoletoepassing.

![Nieuw project](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

Voeg [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) het NuGet-pakket van de .NET Core-bibliotheek toe aan uw project door de volgende stappen te volgen: 

1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.
2. Klik op het tabblad **Bladeren**, zoek naar Microsoft.Azure.EventHubs en selecteer het pakket **Microsoft.Azure.EventHubs**. Klik op **Installeren** om de installatie te voltooien en sluit vervolgens dit dialoogvenster.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

1. Voeg aan het begin van het bestand Program.cs de volgende `using`-instructies toe:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Voeg constanten toe aan de `Program`-klasse voor de Event Hubs-verbindingsreeks en entiteitspad (afzonderlijke event hub-naam). Vervang de tijdelijke aanduidingen tussen punthaken door de juiste waarden die zijn verkregen bij het maken van de event hub. Zorg ervoor dat `{Event Hubs connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Voeg als volgt een nieuwe methode met de naam `MainAsync` toe aan de klasse `Program`:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Voeg als volgt een nieuwe methode met de naam `SendMessagesToEventHub` toe aan de klasse `Program`:

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Voeg de volgende code toe aan de methode `Main` in de klasse `Program`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Zo zou het bestand Program.cs er moeten uitzien.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Voer het programma uit en controleer of er geen fouten zijn.

## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie ziet u hoe u een .NET Core-consoletoepassing schrijft die berichten ontvangt van een gebeurtenishub met behulp van de [Gebeurtenisprocessorhost.](event-hubs-event-processor-host.md) De [Gebeurtenisprocessorhost](event-hubs-event-processor-host.md) is een .NET-klasse die het ontvangen van gebeurtenissen van gebeurtenishubs vereenvoudigt door permanente controlepunten en parallelle ontvangsts van die gebeurtenishubs te beheren. Met de EventProcessorHost kunt u gebeurtenissen splitsen over meerdere ontvangers, zelfs als deze worden gehost in verschillende knooppunten. In dit voorbeeld wordt het gebruik van de EventProcessorHost gedemonstreerd voor één ontvanger.
> [!NOTE]
> U deze quickstart downloaden als voorbeeld van `EventHubConnectionString` `EventHubName`de `StorageAccountName` `StorageAccountKey` [GitHub,](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)vervangen en, en `StorageContainerName` tekenreeksen met uw gebeurtenishubwaarden, en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Een consoletoepassing maken

Start Visual Studio. Klik in het menu **File** op **New** en klik vervolgens op **Project**. Maak een .NET Core-consoletoepassing.

![Nieuw project](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

Voeg de NuGet-pakketten voor [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) en [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard-bibliotheek met de volgende stappen aan uw project toe: 

1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.
2. Klik op het tabblad **Bladeren**, zoek naar **Microsoft.Azure.EventHubs** en selecteer het pakket **Microsoft.Azure.EventHubs**. Klik op **Installeren** om de installatie te voltooien en sluit vervolgens dit dialoogvenster.
3. Herhaal stap 1 en 2 en installeer het **Microsoft.Azure.EventHubs.Processor**-pakket.

### <a name="implement-the-ieventprocessor-interface"></a>De IEventProcessor-interface implementeren

1. Klik in Solution Explorer met de rechtermuisknop op het project, klik op **Toevoegen** en klik vervolgens op **Klasse**. Noem de nieuwe klasse **SimpleEventProcessor**.

2. Open het bestand SimpleEventProcessor.cs en voeg de volgende `using`-instructies toe aan het begin van het bestand.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementeer de `IEventProcessor`-interface. Vervang de volledige inhoud van de `SimpleEventProcessor`-klasse door de volgende code:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>De Main-methode bijwerken voor gebruik van SimpleEventProcessor

1. Voeg aan het begin van het bestand Program.cs de volgende `using`-instructies toe.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Voeg constanten toe aan de `Program`-klasse voor de verbindingsreeks van de event hub, naam van de event hub, naam van opslagaccountcontainer, opslagaccountnaam en opslagaccountsleutel. Voeg de volgende code toe, waarbij u de tijdelijke aanduidingen vervangt door de bijbehorende waarden:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Voeg als volgt een nieuwe methode met de naam `MainAsync` toe aan de klasse `Program`:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Voeg de volgende coderegel toe aan de methode `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Zo zou het bestand Program.cs er moeten uitzien:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Voer het programma uit en controleer of er geen fouten zijn.


## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [RBAC-monsters (Role-based access control).](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    Deze voorbeelden maken gebruik van de oude **Microsoft.Azure.EventHubs-bibliotheek,** maar u deze eenvoudig bijwerken naar de nieuwste **Azure.Messaging.EventHubs-bibliotheek.** Zie de [handleiding voor het migreren van Microsoft.Azure.EventHubs naar Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).)
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)


