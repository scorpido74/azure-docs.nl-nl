---
title: Notification Hubs enterprise push-architectuur
description: Meer informatie over het gebruik van Azure Notification Hubs in een bedrijfsomgeving
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264030"
---
# <a name="enterprise-push-architectural-guidance"></a>Hulp voor architectuur via pushmeldingen van het bedrijf

Ondernemingen zijn vandaag de dag geleidelijk op weg naar het creëren van mobiele applicaties voor hun eindgebruikers (extern) of voor de werknemers (intern). Ze hebben bestaande backend systemen in de plaats zij het mainframes of sommige LoB-toepassingen, die moeten worden geïntegreerd in de mobiele applicatie-architectuur. In deze handleiding wordt beschreven hoe u deze integratie het beste doen en mogelijke oplossingen voor veelvoorkomende scenario's aanbevelen.

Een frequente vereiste is voor het verzenden van push-melding naar de gebruikers via hun mobiele applicatie wanneer een gebeurtenis van belang optreedt in de backend systemen. Een bankklant die de bankapp op een iPhone heeft, wil bijvoorbeeld een melding ontvangen wanneer een debet wordt gedaan boven een bepaald bedrag van de rekening of een intranetscenario waarin een werknemer van de financiële afdeling die een budgetgoedkeuringsapp op een Windows Phone wil op de hoogte te worden gesteld wanneer de goedkeuringsaanvraag wordt ontvangen.

De bankrekening of goedkeuring verwerking is waarschijnlijk worden gedaan in een aantal backend systeem, die een push naar de gebruiker moet initiëren. Er kunnen meerdere van dergelijke backend-systemen zijn, die allemaal dezelfde logica moeten bouwen om te pushen wanneer een gebeurtenis een melding activeert. De complexiteit hier ligt in de integratie van verschillende backend systemen samen met een enkele push-systeem waar de eindgebruikers kunnen hebben geabonneerd op verschillende meldingen en er kunnen zelfs meerdere mobiele toepassingen. Bijvoorbeeld mobiele intranet-apps waarbij één mobiele toepassing meldingen van meerdere van dergelijke backendsystemen wil ontvangen. De backend systemen weten niet of moeten weten van push semantiek / technologie, zodat een gemeenschappelijke oplossing hier traditioneel is geweest om een component, die de backend systemen polls voor alle gebeurtenissen van belang en is verantwoordelijk voor het verzenden van de push-berichten naar de klant.

Een betere oplossing is het gebruik van Azure Service Bus - Topic/Subscription-model, wat de complexiteit vermindert en de oplossing schaalbaar maakt.

Hier is de algemene architectuur van de oplossing (gegeneraliseerd met meerdere mobiele apps, maar even van toepassing wanneer er slechts een mobiele app)

## <a name="architecture"></a>Architectuur

![][1]

Het belangrijkste stuk in dit architecturale diagram is Azure Service Bus, die een onderwerpen / abonnementen programmeermodel biedt (meer over het op [Service Bus Pub / Sub programmering).] De ontvanger, die in dit geval de mobiele backend is (meestal [Azure Mobile Service], die een push naar de mobiele apps initieert) ontvangt geen berichten rechtstreeks van de backend-systemen, maar in plaats daarvan een tussenliggende abstractielaag die wordt geleverd door Azure Service [Bus], waarmee mobiele backend berichten van een of meer backend-systemen kan ontvangen. Er moet een servicebusonderwerp worden gemaakt voor elk van de backend-systemen, bijvoorbeeld Account, HR, Finance, wat in principe "onderwerpen" van belang is, die berichten initieert die als pushmelding moeten worden verzonden. De backend systemen sturen berichten naar deze onderwerpen. Een Mobile Backend kan zich abonneren op een of meer van dergelijke onderwerpen door een Service Bus-abonnement te maken. Het geeft de mobiele backend recht op een melding van het bijbehorende backend-systeem. Mobiele backend blijft luisteren naar berichten op hun abonnementen en zodra een bericht binnenkomt, keert het terug en stuurt het als melding naar de meldingshub. Meldingshubs leveren het bericht uiteindelijk af naar de mobiele app. Hier is de lijst van belangrijke onderdelen:

1. Backend-systemen (LoB/Legacy-systemen)
   * Servicebusonderwerp maken
   * Verzendt bericht
1. Mobiele backend
   * Service-abonnement maken
   * Ontvangt bericht (van backendsysteem)
   * Hiermee verzendt u een melding naar clients (via Azure Notification Hub)
1. Mobiele toepassing
   * Ontvangen en weergeven van kennisgeving

### <a name="benefits"></a>Voordelen

1. De ontkoppeling tussen de ontvanger (mobiele app/service via Notification Hub) en afzender (backendsystemen) maakt het mogelijk om extra backendsystemen te integreren met minimale verandering.
1. Het maakt ook het scenario van meerdere mobiele apps in staat om gebeurtenissen te ontvangen van een of meer backend systemen.  

## <a name="sample"></a>Voorbeeld

### <a name="prerequisites"></a>Vereisten

Voer de volgende zelfstudies in om vertrouwd te raken met de concepten en algemene & configuratiestappen:

1. [Service Bus Pub/Sub-programmering] - In deze zelfstudie worden de details van het werken met servicebusonderwerpen/-abonnementen uitgelegd, hoe u een naamruimte maakt met onderwerpen/abonnementen, hoe u berichten van & ontvangt.
2. [Notification Hubs - Windows Universal-zelfstudie] - In deze zelfstudie wordt uitgelegd hoe u een Windows Store-app instelt en Meldingshubs gebruikt om meldingen te registreren en vervolgens meldingen te ontvangen.

### <a name="sample-code"></a>Voorbeeldcode

De volledige voorbeeldcode is beschikbaar bij [Notification Hub Samples]. Het is opgesplitst in drie componenten:

1. **EnterprisePushBackendSystem**

    a. Dit project maakt gebruik van het **WindowsAzure.ServiceBus** NuGet-pakket en is gebaseerd op [servicebuspub/subprogrammering.]

    b. Deze applicatie is een eenvoudige C# console app om een LoB-systeem te simuleren, die het bericht initieert dat moet worden afgeleverd bij de mobiele app.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic`wordt gebruikt om het onderwerp ServiceBus te maken.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage`wordt gebruikt om de berichten naar dit servicebusonderwerp te verzenden. Deze code stuurt gewoon een set willekeurige berichten naar het onderwerp periodiek voor het doel van het voorbeeld. Normaal gesproken is er een backend-systeem, dat berichten verzendt wanneer een gebeurtenis plaatsvindt.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **Ontvangenenverzonden**

    a. Dit project maakt gebruik van de *NuGet-pakketten WindowsAzure.ServiceBus* en **Microsoft.Web.WebJobs.Publish** NuGet en is gebaseerd op [Service Bus Pub/Sub-programmering.]

    b. De volgende console-app wordt uitgevoerd als een [Azure WebJob,] omdat deze continu moet worden uitgevoerd om te luisteren naar berichten van de LoB/backend-systemen. Deze applicatie maakt deel uit van uw mobiele backend.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription`wordt gebruikt om een Service Bus-abonnement te maken voor het onderwerp waarin het backendsysteem berichten verzendt. Afhankelijk van het bedrijfsscenario maakt dit onderdeel een of meer abonnementen op overeenkomstige onderwerpen (sommige kunnen bijvoorbeeld berichten ontvangen van het HR-systeem, sommige van het finance-systeem, enzovoort)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`wordt gebruikt om het bericht van het onderwerp te lezen met behulp van het abonnement en als het lezen succesvol is, maak dan een melding (in het voorbeeldscenario een Windows native toast-melding) die met Azure Notification Hubs naar de mobiele toepassing moet worden verzonden.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Voor het publiceren van deze app als **WebJob,** klikt u met de rechtermuisknop op de oplossing in Visual Studio en selecteert **u Publiceren als WebJob**

    ![][2]

    f. Selecteer uw publicatieprofiel en maak een nieuwe Azure-website als deze nog niet bestaat, waarin deze WebJob wordt host en zodra u de website hebt **gepubliceerd.**

    ![][3]

    g. Configureer de taak als 'Continu uitvoeren' zodat u bij het inloggen op de [Azure-portal] iets als volgt moet zien:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Deze toepassing is een Windows Store-toepassing, die pop-upmeldingen ontvangt van de WebJob die wordt uitgevoerd als onderdeel van uw mobiele back-end en deze weergeeft. Deze code is gebaseerd op [Notification Hubs - Windows Universal tutorial].  

    b. Zorg ervoor dat uw toepassing is ingeschakeld om pop-upmeldingen te ontvangen.

    c. Controleer of bij het opstarten van de app de volgende registratiecode voor meldingshubs wordt aangeroepen (na het vervangen van de `HubName` en `DefaultListenSharedAccessSignature` de waarden:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Het voorbeeld uitvoeren

1. Zorg ervoor dat uw WebJob succesvol wordt uitgevoerd en continu wordt uitgevoerd.
2. Voer de **EnterprisePushMobileApp**uit, waarmee de Windows Store-app wordt gestart.
3. Voer de **EnterprisePushBackendSystem-consoletoepassing** uit, die de LoB-backend simuleert en berichten begint te verzenden en pop-upmeldingen moet worden weergegeven als de volgende afbeelding:

    ![][5]

4. De berichten werden oorspronkelijk verzonden naar Service Bus-onderwerpen, die werden gecontroleerd door Service Bus-abonnementen in uw webtaak. Nadat een bericht was ontvangen, is er een melding gemaakt en naar de mobiele app verzonden. U de WebJob-logboeken doorzoeken om de verwerking te bevestigen wanneer u naar de koppeling Logboeken in [Azure-portal] voor uw webtaak gaat:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Voorbeelden van meldingshubs]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile-service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub-programmering]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs - Windows Universal-zelfstudie]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure-portal]: https://portal.azure.com/
