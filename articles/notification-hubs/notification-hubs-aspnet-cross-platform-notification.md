---
title: Kruis platform meldingen verzenden naar gebruikers met Azure Notification Hubs (ASP.NET)
description: Meer informatie over het gebruik van Notification Hubs sjablonen voor het verzenden van een neutraal-melding die gericht is op alle platformen.
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
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: cea0d63c20af781fcfc6ba5d7c06061b12992702
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212023"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Kruis platform meldingen verzenden naar gebruikers met Notification Hubs

In een vorige zelf studie [Gebruikers op de hoogte stellen met Notification Hubs], hebt u geleerd hoe u meldingen pusht naar alle apparaten die zijn geregistreerd voor een specifieke geverifieerde gebruiker. In deze zelf studie zijn meerdere aanvragen vereist om een melding te verzenden naar elk ondersteund client platform. Azure Notification Hubs ondersteunt sjablonen, waarmee u kunt opgeven hoe een specifiek apparaat meldingen wil ontvangen. Deze methode vereenvoudigt het verzenden van cross-platform meldingen.

In dit artikel ziet u hoe u kunt profiteren van sjablonen voor het verzenden van, in één aanvraag, een neutraal-melding die gericht is op alle platformen. Zie [overzicht van Azure notification hubs][Templates]voor meer gedetailleerde informatie over sjablonen.

> [!IMPORTANT]
> Windows Phone projecten 8,1 en lager worden niet ondersteund in Visual Studio 2017. Zie [Geschikte platforms voor Visual Studio 2017 en compatibiliteit](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) voor meer informatie.

> [!NOTE]
> Met Notification Hubs kan een apparaat meerdere sjablonen met dezelfde tag registreren. In dit geval wordt een inkomend bericht dat de tag bedoelt, in meerdere meldingen bezorgd op het apparaat, één voor elke sjabloon. Met dit proces kunt u hetzelfde bericht weer geven in meerdere visuele meldingen, zoals een badge en als een pop-upmelding in een Windows Store-app.

## <a name="send-cross-platform-notifications-using-templates"></a>Cross-platform meldingen verzenden met behulp van sjablonen

Ga als volgt te werk om cross-platform meldingen te verzenden met behulp van sjablonen:

1. Vouw in de Solution Explorer in Visual Studio de map **controllers** uit en open vervolgens het bestand RegisterController.cs.

2. Zoek het code blok in de `Put` methode die een nieuwe registratie maakt en vervang de `switch` inhoud door de volgende code:

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

    Met deze code wordt de platformspecifieke methode aangeroepen voor het maken van een sjabloon registratie in plaats van een systeem eigen registratie. Omdat sjabloon registraties zijn afgeleid van systeem eigen registraties, hoeft u geen bestaande registraties te wijzigen.

3. Vervang in `Notifications` de-controller de `sendNotification` -methode door de volgende code:

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

    Met deze code wordt op hetzelfde moment een melding verzonden naar alle platformen, zonder dat u een systeem eigen Payload hoeft op te geven. Notification Hubs bouwt en levert de juiste Payload aan elk apparaat met de opgegeven *Label* waarde, zoals opgegeven in de geregistreerde sjablonen.

4. Publiceer uw WebApi-back-end-project opnieuw.

5. Voer de client-app opnieuw uit en controleer of de registratie is geslaagd.

6. Beschrijving Implementeer de client-app op een tweede apparaat en voer vervolgens de app uit.
    Op elk apparaat wordt een melding weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u deze zelf studie hebt voltooid, kunt u meer informatie vinden over Notification Hubs en sjablonen in de volgende onderwerpen:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Overzicht van Azure notification hubs][Templates]: Bevat gedetailleerde informatie over sjablonen.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Gebruikers op de hoogte stellen met Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
