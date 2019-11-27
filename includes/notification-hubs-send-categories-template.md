---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228156"
---
In deze sectie verzendt u het laatste nieuws als gelabelde sjabloonmeldingen vanuit een .NET-console-app.

1. Maak in Visual Studio een nieuwe Visual C#-consoletoepassing:
    1. Selecteer in het menu **bestand** > **Nieuw** > **project**.
    1. Selecteer in **een nieuw project maken**de optie **console-app (.NET Framework)** voor C# in de lijst met sjablonen en selecteer **volgende**.
    1. Voer een naam in voor de app.
    1. Voor **oplossing**kiest **u toevoegen aan oplossing**en selecteert u **maken** om het project te maken.

1. Selecteer **Hulpprogram ma's** > **NuGet package manager** > **Package Manager-console** en voer vervolgens in het console venster de volgende opdracht uit:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Met deze actie wordt een verwijzing toegevoegd aan de Azure Notification Hubs SDK met behulp van het pakket [micro soft. Azure. notification hubs] .

1. Open het *Program.cs* -bestand en voeg de volgende `using`-instructie toe:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Voeg in de klasse `Program` de volgende methode toe of vervang de methode als deze al bestaat:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Met deze code wordt een sjabloonmelding verzonden voor elk van de zes labels in de tekenreeksmatrix. Het gebruik van labels zorgt ervoor dat apparaten meldingen ontvangen voor de geregistreerde categorieën.

1. Vervang in de voorgaande code de tijdelijke aanduidingen `<hub name>` en `<connection string with full access>` door de naam van de meldingenhub en de verbindingsreeks voor *DefaultFullSharedAccessSignature* uit het dashboard van de meldingenhub.

1. Voeg in de methode `Main()` de volgende regels toe:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Bouw de console-app.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Micro soft. Azure. notification hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
