---
title: Aan de slag met Azure Service Bus-onderwerpen en -abonnementen | Microsoft Docs
description: Een C# .NET Core-consoletoepassing schrijven die Service Bus Messaging-onderwerpen en -abonnementen gebruikt.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/27/2019
ms.author: aschhab
ms.openlocfilehash: 3fba1d62b9347303d630c80733c4fbfa279b5296
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560101"
---
# <a name="get-started-with-service-bus-topics"></a>Aan de slag met Service Bus-onderwerpen

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Deze zelfstudie bestaat uit de volgende stappen:

1. Schrijf een .NET Core-consoletoepassing om een set berichten naar het onderwerp te verzenden.
2. Schrijf een .NET Core-consoletoepassing om deze berichten van het abonnement te ontvangen.

## <a name="prerequisites"></a>Vereisten

1. Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U uw [Visual Studio- of MSDN-abonneevoordelen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Volg stappen in de [Quickstart: gebruik de Azure-portal om een ServiceBus-onderwerp en abonnementen op het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md) om de volgende taken uit te voeren:
    1. Een **naamruimte**voor servicebus maken .
    2. Haal de **verbindingstekenreeks**.
    3. Maak een **onderwerp** in de naamruimte.
    4. Maak **één abonnement op** het onderwerp in de naamruimte.
3. [Visual Studio 2017 update 3 (versie 15.3, 26730.01)](https://www.visualstudio.com/vs) of later.
4. [NET Core SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of later.
 
## <a name="send-messages-to-the-topic"></a>Berichten naar het onderwerp verzenden

Maak een C#-consoletoepassing met Visual Studio om berichten naar het onderwerp te verzenden.

### <a name="create-a-console-application"></a>Een consoletoepassing maken

Start Visual Studio en maak een nieuwe **consoletoepassing (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Het Service Bus NuGet-pakket toevoegen

1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.
2. Klik op het tabblad **Bladeren**, zoek naar **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** en selecteer het item **Microsoft.Azure.ServiceBus**. Klik op **Installeren** om de installatie te voltooien en sluit vervolgens dit dialoogvenster.
   
    ![Een NuGet-pakket selecteren][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Code schrijven om berichten naar het onderwerp te verzenden

1. Voeg in Program.cs de volgende `using` instructies aan het begin van de naamruimtedefinitie toe voor de klassedeclaratie:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Declareer binnen de klasse `Program` de volgende variabelen. Stel de variabele `ServiceBusConnectionString` in op de verbindingstekenreeks die u hebt verkregen tijdens het maken van de naamruimte en stel `TopicName` in op de naam die u hebt gebruikt bij het maken van het onderwerp:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Vervang `Main()` de methode door de volgende **async-methode** `Main` die berichten asynchroon verzendt met de methode SendMessagesAsync die u in de volgende stap toevoegt. 

    ```csharp
    public static async Task Main(string[] args)
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```
5. Voeg direct na de `Main` methode de volgende `SendMessagesAsync()` methode toe voor het uitvoeren van het werk van het verzenden van het aantal berichten dat is opgegeven door `numberOfMessagesToSend` (momenteel ingesteld op 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Zo zou het verzendbestand Program.cs er moeten uitzien.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            public static async Task Main(string[] args)
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
    
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");
    
                // Send messages.
                await SendMessagesAsync(numberOfMessages);
    
                Console.ReadKey();
    
                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Voer het programma uit en controleer Azure Portal: klik op de naam van uw onderwerp in het venster **Overzicht** voor de naamruimte. Het scherm **Essentials** voor het onderwerp wordt weergegeven. In de lijst met abonnementen onderaan het venster ziet u dat de waarde voor **Aantal berichten** voor het abonnement nu **10** is worden. Telkens wanneer die u de zendtoepassing uitvoert zonder de berichten op te halen (zoals in het volgende gedeelte wordt beschreven), wordt deze waarde verhoogd met 10. U zult ook zien dat de huidige grootte voor het onderwerp de waarde voor **Huidige** in het venster **Essentials** vergroot wanneer de app een bericht toevoegt aan het onderwerp.
   
      ![Berichtgrootte][topic-message]

## <a name="receive-messages-from-the-subscription"></a>Berichten ontvangen van het abonnement

Als u de berichten wilt ontvangen die u hebt verzonden, maakt u een andere .NET Core-consoletoepassing en installeert u het **Microsoft.Azure.ServiceBus** NuGet-pakket, vergelijkbaar met de vorige afzendertoepassing.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Schrijven van code voor het ontvangen van berichten van het abonnement

1. Voeg in Program.cs de volgende `using` instructies aan het begin van de naamruimtedefinitie toe voor de klassedeclaratie:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Declareer binnen de klasse `Program` de volgende variabelen. Stel de variabele `ServiceBusConnectionString` in op de verbindingstekenreeks die u hebt verkregen tijdens het maken van de naamruimte, stel `TopicName` in op de naam die u hebt gebruikt bij het maken van het onderwerp en stel `SubscriptionName` in op de naam die u hebt gebruikt bij het maken van het abonnement voor het onderwerp:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Vervang `Main()` de methode door de volgende **asyncmethode.** `Main` Het roept `RegisterOnMessageHandlerAndReceiveMessages()` de methode aan die u in de volgende stap zult toevoegen. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();    
    }
   ```
5. Voeg direct na de methode `Main()` de volgende methode toe die de berichtenhandler registreert en de berichten ontvangt die zijn verzonden door de zendtoepassing:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Voeg direct na de vorige methode de volgende methode `ProcessMessagesAsync()` toe voor het verwerken van de ontvangen berichten:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Voeg tot slot de volgende methode toe om eventuele uitzonderingen af te handelen:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Zo zou het ontvangstbestand Program.cs er moeten uitzien:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            public static async Task Main(string[] args)
            {    
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);
        
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");
        
                // Register subscription message handler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
        
                Console.ReadKey();
        
                await subscriptionClient.CloseAsync();    
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Voer het programma uit en controleer de portal opnieuw. Merk op dat het **aantal berichten** en **de huidige** waarden nu **0**zijn .
   
    ![Lengte van het onderwerp][topic-message-receive]

Gefeliciteerd! Met de standaard .NET-bibliotheek, hebt u nu een onderwerp en abonnement gemaakt, 10 berichten verzonden en die berichten ontvangen.

> [!NOTE]
> U servicebusbronnen beheren met [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) Met de Service Bus Explorer kunnen gebruikers eenvoudig verbinding maken met een naamruimte van een ServiceBus en berichtenentiteiten beheren. De tool biedt geavanceerde functies zoals import/export functionaliteit of de mogelijkheid om onderwerp, wachtrijen, abonnementen, relay services, meldinghubs en evenementenhubs te testen. 

## <a name="next-steps"></a>Volgende stappen

Bekijk onze [GitHub-opslagplaats met voorbeelden](https://github.com/Azure/azure-service-bus/tree/master/samples) voor Service Bus die enkele van de meer geavanceerde functies van Service Bus Messaging laten zien.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
