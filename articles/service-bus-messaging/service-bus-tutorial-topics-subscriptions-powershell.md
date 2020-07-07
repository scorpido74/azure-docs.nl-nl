---
title: 'Zelfstudie: Het voorraadassortiment van een detailhandelsbedrijf bijwerken via het publiceren van/abonneren op kanalen en via onderwerpfilters met Azure PowerShell | Microsoft Docs'
description: In deze zelfstudie leert u hoe u berichten naar een onderwerp en abonnement kunt versturen en ervan kunt ontvangen, en hoe u filterregels kunt gebruiken en toevoegen met behulp van Azure PowerShell.
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: tutorial
ms.openlocfilehash: b1c6390d66d01bbe91500fc3878ad138eea208cc
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341620"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Zelfstudie: Voorraad bijwerken met PowerShell en onderwerpen/abonnementen

Microsoft Azure Service Bus is een multitenant-cloudberichtenservice waarmee u gegevens kunt versturen tussen toepassingen en services. Asynchrone bewerkingen bieden flexibele, Brokered Messaging, samen met gestructureerde FIFO-berichtenuitwisseling (first in, first out) en mogelijkheden voor publiceren/abonneren. 

In deze snelstartgids wordt beschreven hoe u berichten naar een Service Bus-wachtrij verzendt en van de wachtrij ontvangt. U gebruikt daarbij PowerShell om een berichtennaamruimte te maken en binnen deze naamruimte een wachtrij te maken. Ook wordt beschreven hoe u de autorisatiereferenties binnen die naamruimte kunt verkrijgen. De procedure laat vervolgens zien hoe u berichten naar deze wachtrij verzendt en van de wachtrij ontvangt met behulp van de [.NET Standard-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Bus-onderwerp en een of meer abonnementen op dat onderwerp maken met Azure PowerShell
> * Onderwerpfilters toevoegen met Azure PowerShell
> * Twee berichten met verschillende inhoud maken
> * De berichten verzenden en controleren of ze zijn ontvangen in de verwachte abonnementen
> * Berichten ontvangen van de abonnementen

Een voorbeeld van dit scenario is het bijwerken van het voorraadassortiment voor meerdere winkels. In dit scenario ontvangt elke winkel of reeks winkels berichten op basis waarvan ze hun assortiment kunnen bijwerken. Deze zelfstudie laat zien hoe dit scenario met behulp van abonnementen en filters kan worden geïmplementeerd. Eerst maakt u een onderwerp met drie abonnementen, voegt u een aantal regels en filters toe, en vervolgens verzendt en ontvangt u berichten van het onderwerp en de abonnementen.

![onderwerp](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][] aan voordat u begint.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

1. [Visual Studio 2017 update 3 (versie 15.3, 26730.01)](https://www.visualstudio.com/vs) of hoger.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), versie 2.0 of later.

Voor deze zelfstudie hebt u de nieuwste versie van Azure PowerShell nodig. Als u Azure PowerShell wilt installeren of upgraden, raadpleegt u [Azure PowerShell installeren en configureren][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Voer de volgende opdrachten uit om u aan te melden bij Azure. Deze stappen zijn niet nodig als u PowerShell-opdrachten uitvoert in Cloud Shell: 

1. Installeer de PowerShell-module voor Service Bus:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Voer de volgende opdracht uit om u aan te melden bij Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

4. Stel de context van het huidige abonnement in of vraag het actieve abonnement op:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Resources inrichten

Voer na het aanmelden bij Azure de volgende opdrachten uit om Service Bus-resources in te richten. Zorg dat u alle tijdelijke aanduidingen vervangt door de juiste waarden:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Zodra de cmdlet `Get-AzServiceBusKey` is uitgevoerd, kopieert en plakt u de verbindingsreeks en de naam van de wachtrij die u hebt geselecteerd naar een tijdelijke locatie, zoals Kladblok. U hebt deze gegevens nodig in de volgende stap.

## <a name="send-and-receive-messages"></a>Berichten verzenden en ontvangen

Wanneer de naamruimte en wachtrij zijn gemaakt en u over de benodigde referenties beschikt, kunt u berichten gaan verzenden en ontvangen. U kunt de code in [deze GitHub-voorbeeldmap](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart) bestuderen.

Ga als volgt te werk om de code uit te voeren:

1. Kloon de [Service Bus GitHub-opslagplaats](https://github.com/Azure/azure-service-bus/) met behulp van de volgende opdracht:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Open een PowerShell-prompt.

3. Navigeer naar de voorbeeldmap `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Als u dit nog niet hebt gedaan, haalt u de verbindingsreeks op met behulp van de volgende PowerShell-cmdlet. Zorg dat u `my-resourcegroup` en `namespace-name` vervangt door uw specifieke waarden: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. Typ de volgende opdracht bij de PowerShell-prompt:

   ```shell
   dotnet build
   ```
6. Navigeer naar de map `\bin\Debug\netcoreapp2.0`.
7. Typ de volgende opdracht om het programma uit te voeren. Zorg ervoor dat u `myConnectionString` vervangt door de waarde die u eerder hebt verkregen, en vervang `myQueueName` door de naam van de wachtrij die u hebt gemaakt:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Bekijk tien berichten die naar de wachtrij zijn verzonden en vervolgens van de wachtrij zijn ontvangen:

   ![programma-uitvoer](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit om de resourcegroep, de naamruimte en alle gerelateerde resources te verwijderen:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

Deze sectie bevat meer informatie over de werking van de voorbeeldcode. 

### <a name="get-connection-string-and-queue"></a>Verbindingsreeks en wachtrij ophalen

De verbindingsreeks en de naam van de wachtrij worden als opdrachtregelargumenten doorgegeven aan de methode `Main()`. `Main()` declareert twee tekenreeksvariabelen die deze waarden kunnen omvatten:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
De methode `Main()` start vervolgens de asynchrone berichtenlus `MainAsync()`.

### <a name="message-loop"></a>Berichtenlus

De methode `MainAsync()` maakt een wachtrijclient met de opdrachtregelargumenten, roept een ontvangende berichtenhandler met de naam `RegisterOnMessageHandlerAndReceiveMessages()` aan, en verzendt de berichtenreeks:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

De methode `RegisterOnMessageHandlerAndReceiveMessages()` stelt enkele opties voor de berichtenhandler in en roept vervolgens de methode `RegisterMessageHandler()` van de wachtrijclient aan, waardoor het ontvangen begint:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Berichten verzenden

De bewerkingen voor het maken en verzenden van berichten vinden in de methode `SendMessagesAsync()` plaats:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Berichten verwerken

De methode `ProcessMessagesAsync()` bevestigt, verwerkt en voltooit de ontvangst van de berichten:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u resources ingericht met behulp van Azure PowerShell en vervolgens berichten verzonden en ontvangen van een Service Bus-onderwerp en de daarbij behorende abonnementen. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Service Bus-onderwerp en een of meer abonnementen voor dat onderwerp maken met Azure Portal
> * Onderwerpfilters toevoegen met .NET-code
> * Twee berichten met verschillende inhoud maken.
> * De berichten verzenden en controleren of ze zijn ontvangen in de verwachte abonnementen
> * Berichten ontvangen van de abonnementen

Meer voorbeelden voor het verzenden en ontvangen van berichten vindt u in [de Service Bus-voorbeelden op GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Ga naar de volgende zelfstudie voor meer informatie over het gebruik van de mogelijkheden voor publiceren/abonneren van Service Bus.

> [!div class="nextstepaction"]
> [Voorraad bijwerken met behulp van PowerShell en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-cli.md)

[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure PowerShell installeren en configureren]: /powershell/azure/install-Az-ps
