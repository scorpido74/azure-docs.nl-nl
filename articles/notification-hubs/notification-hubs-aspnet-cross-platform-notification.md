---
title: Cross-platform meldingen verzenden naar gebruikers met Azure Notification Hubs (ASP.NET)
description: Meer informatie over het gebruik van Notification Hubs-sjablonen om in één aanvraag een platformagnostische melding te verzenden die zich richt op alle platforms.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127051"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Cross-platformmeldingen verzenden met Azure Notification Hubs

Deze zelfstudie bouwt voort op de vorige zelfstudie, [Meldingen verzenden naar specifieke gebruikers met behulp van Azure Notification Hubs.] In die zelfstudie wordt beschreven hoe u meldingen pusht naar alle apparaten die zijn geregistreerd bij een specifieke geverifieerde gebruiker. Die aanpak vereiste meerdere verzoeken om een melding te sturen naar elk ondersteund clientplatform. Azure Notification Hubs ondersteunt sjablonen, waarmee u opgeven hoe een specifiek apparaat meldingen wil ontvangen. Deze methode vereenvoudigt het verzenden van cross-platform meldingen.

In dit artikel wordt uitgelegd hoe u gebruik maken van sjablonen om een melding te verzenden die zich richt op alle platforms. In dit artikel wordt één verzoek gebruikt om een platformneutrale melding te verzenden. Zie [Overzicht van meldingenhubs][Templates]voor meer gedetailleerde informatie over sjablonen.

> [!IMPORTANT]
> Windows Phone-projecten 8.1 en eerder worden niet ondersteund in Visual Studio 2019. Zie [Targeting en compatibiliteit van Visual Studio 2019 Platform](/visualstudio/releases/2019/compatibility)voor meer informatie.

> [!NOTE]
> Met Notification Hubs kan een apparaat meerdere sjablonen registreren met dezelfde tag. In dit geval resulteert een binnenkomend bericht dat zich richt op de tag in meerdere meldingen die naar het apparaat worden verzonden, één voor elke sjabloon. Met dit proces u hetzelfde bericht weergeven in meerdere visuele meldingen, zoals zowel als badge als als een pop-upmelding in een Windows Store-app.

## <a name="send-cross-platform-notifications-using-templates"></a>Cross-platform meldingen verzenden met sjablonen

In deze sectie wordt de voorbeeldcode gebruikt die u hebt ingebouwd in de [zelfstudie Meldingen verzenden naar specifieke gebruikers] met behulp van de zelfstudie van Azure Notification Hubs. U kunt het voorbeeld downloaden uit [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Ga als volgt te werk om cross-platform meldingen te verzenden met behulp van sjablonen:

1. Vouw in Visual Studio in **Solution Explorer**de map **Controllers** uit en open het *RegisterController.cs-bestand.*

1. Zoek het codeblok `Put` in de methode waarmee een nieuwe `switch` registratie wordt gemaakt en vervang de inhoud door de volgende code:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Deze code roept de platformspecifieke methode aan om een sjabloonregistratie te maken in plaats van een native registratie. Omdat sjabloonregistraties afkomstig zijn van native registraties, hoeft u bestaande registraties niet te wijzigen.

1. Open **in Solution Explorer**in de map **Controllers** het *NotificationsController.cs* bestand. Vervang de `Post`-methode door de volgende code:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Deze code stuurt een melding naar alle platforms tegelijk. Je geeft geen eigen lading op. Notification Hubs bouwt en levert het juiste laadvermogen aan elk apparaat met de opgegeven tagwaarde, zoals gespecificeerd in de geregistreerde sjablonen.

1. Uw Web API-project opnieuw publiceren.

1. Voer de client-app opnieuw uit om te controleren of de registratie is geslaagd.

1. Implementeer de client-app optioneel op een tweede apparaat en voer de app uit. Op elk apparaat wordt een melding weergegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u deze zelfstudie hebt voltooid, vindt u meer informatie over meldingenhubs en sjablonen in deze artikelen:

* Zie de [pushmeldingen voor specifieke Windows-apparaten met zelfstudie universal Windows Platform-toepassingen][Use Notification Hubs to send breaking news] voor een ander scenario voor het gebruik van sjablonen.
* Zie [Overzicht van meldingenhubs][Templates]voor meer gedetailleerde informatie over sjablonen.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Meldingen verzenden naar specifieke gebruikers met behulp van Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
