---
title: Azure Event Hubs-gebeurtenissen voor verzenden/ontvangen met de .NET Framework
description: 'Snelstartgids: in dit artikel vindt u een overzicht van het maken van een .NET Framework-toepassing die gebeurtenissen naar Azure Event Hubs verzendt.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/20/2019
ms.author: shvija
ms.openlocfilehash: 385430d993afe8b7a0ad57991d3c93eebd46ddcb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365343"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Quick Start: gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van .NET Framework
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelf studie ziet u hoe u .NET Framework- C# console toepassingen maakt in om gebeurtenissen te verzenden naar of gebeurtenissen te ontvangen van een eventhub. 

## <a name="prerequisites"></a>Vereisten
Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- [Micro soft Visual Studio 2019](https://visualstudio.com).
- **Een event hubs naam ruimte en een event hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Vervolgens haalt u de **Connection String voor de Event hub naam ruimte** door de volgende instructies uit het artikel: [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze zelfstudie.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een .NET Framework-console toepassing maakt om gebeurtenissen te verzenden naar een Event Hub. 

### <a name="create-a-console-application"></a>Een consoletoepassing maken

Maak in Visual Studio een nieuw Visual C# bureaublad-app-project met behulp van de projectsjabloon**Consoletoepassing**. Noem het project **Afzender**.
   
![Consoletoepassing maken](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op het project **Afzender** en klik op **NuGet-pakketten beheren voor oplossing**. 
2. Klik op het tabblad **Bladeren** en zoek naar `WindowsAzure.ServiceBus`. Klik op **Installeren** en accepteer de gebruiksvoorwaarden. 
   
    ![Service Bus NuGet-pakket installeren](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Er wordt door Visual Studio een verwijzing naar het [ NuGet-pakket Azure Service Bus-bibliotheek](https://www.nuget.org/packages/WindowsAzure.ServiceBus) gedownload, geïnstalleerd en toegevoegd.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

1. Voeg aan het begin van het bestand `using`Program.cs**de volgende**-instructies toe:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Voeg de volgende velden toe aan de klasse **Program**, waarbij u de waarden van de tijdelijke aanduiding vervangt door de naam van de Event Hub die u in de vorige sectie hebt gemaakt, en de verbindingsreeks op naamruimteniveau die u eerder hebt opgeslagen. U kunt connection string voor uw Event Hub kopiëren vanuit **verbindings reeks-primaire** sleutel onder **RootManageSharedAccessKey** op de pagina event hub in de Azure Portal. Zie [Get Connection String](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)voor gedetailleerde stappen.
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Voeg de volgende methode toe aan de klasse **Program**:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Met deze methode worden er continu gebeurtenissen naar uw Event Hub verzonden, met een vertraging van 200 ms.
4. Voeg tot slot de volgende regels toe aan de methode **Main**:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Voer het programma uit en controleer of er geen fouten zijn.
  
## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie schrijft u een .NET Framework-console toepassing die berichten ontvangt van een Event Hub met behulp van de [Event processor host](event-hubs-event-processor-host.md). De [EventProcessorHost](event-hubs-event-processor-host.md) is een .NET-klasse die het ontvangen van gebeurtenissen van Event Hubs vereenvoudigt door permanente controlepunten en parallelle ontvangst van deze Event Hubs te beheren. Met de EventProcessorHost kunt u gebeurtenissen splitsen over meerdere ontvangers, zelfs als deze worden gehost in verschillende knooppunten. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Een consoletoepassing maken

Maak in Visual Studio een nieuw Visual C# bureaublad-app-project met behulp van de projectsjabloon**Consoletoepassing**. Noem het project **Ontvanger**.
   
![Consoletoepassing maken](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op het project **Ontvanger** en klik op **NuGet-pakketten beheren voor oplossing**.
2. Klik op het tabblad **Bladeren** en zoek naar `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klik op **Installeren** en accepteer de gebruiksvoorwaarden.
   
    ![Zoeken naar Event Processor Host NuGet-pakket](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Door Visual Studio wordt een verwijzing naar het [ NuGet-pakket Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), inclusief alle bijbehorende afhankelijkheden, gedownload, geïnstalleerd en toegevoegd.

### <a name="implement-the-ieventprocessor-interface"></a>De IEventProcessor-interface implementeren

1. Klik met de rechtermuisknop op het project **Ontvanger**, klik op **Toevoegen** en op **Klasse**. Noem de nieuwe klasse **SimpleEventProcessor** en klik op **Toevoegen** om de klasse te maken.
   
    ![De klasse SimpleEventProcessor toevoegen](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Voeg de volgende instructies toe aan het begin van het bestand SimpleEventProcessor.cs:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Vervang de volgende code voor de hoofdtekst van de klasse:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Deze klasse wordt aangeroepen door de **EventProcessorHost** om gebeurtenissen te verwerken die worden ontvangen van de Event Hub. De klasse `SimpleEventProcessor` gebruikt een stopwatch om periodiek de controlepuntmethode voor de context **EventProcessorHost** aan te roepen. Op deze manier gaat er nooit meer werk verloren dan in vijf minuten kan worden verwerkt, als de ontvanger opnieuw wordt opgestart.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>De Main-methode bijwerken voor gebruik van SimpleEventProcessor

1. Voeg in de klasse **Program** de volgende `using`-instructie toe aan het begin van het bestand:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Vervang de `Main` methode in de klasse `Program` door de volgende code, waarbij u de Event Hub naam en de connection string op naam ruimte niveau die u eerder hebt opgeslagen, en het opslag account en de sleutel die u in de vorige secties hebt gekopieerd, vervangt. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Voer het programma uit en controleer of er geen fouten zijn.
  
## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event hubs](event-hubs-features.md).
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
