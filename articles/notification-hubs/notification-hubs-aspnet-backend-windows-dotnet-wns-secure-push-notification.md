---
title: Azure Notification Hubs Secure Push voor Windows
description: Meer informatie over het verzenden van beveiligde pushmeldingen in Azure. Codevoorbeelden geschreven in C# met .NET API.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530728"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Veilig pushmeldingen van Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Overzicht

Push-notificatie-ondersteuning in Microsoft Azure stelt u in staat om toegang te krijgen tot een gebruiksvriendelijke, multi-platform, geschaalde push-infrastructuur, wat de implementatie van pushmeldingen voor zowel consumenten- als bedrijfstoepassingen voor mobiele platforms.

Vanwege wettelijke of beveiligingsbeperkingen wil een toepassing soms iets opnemen in de melding dat niet kan worden verzonden via de standaard push-notificatie-infrastructuur. In deze zelfstudie wordt beschreven hoe u dezelfde ervaring bereiken door gevoelige informatie te verzenden via een beveiligde, geverifieerde verbinding tussen het clientapparaat en de back-end van de app.

Op een hoog niveau is de stroom als volgt:

1. De back-end van de app:
   * Slaat veilige payload op in back-end database.
   * Hiermee wordt de id van deze melding naar het apparaat verzonden (er wordt geen beveiligde informatie verzonden).
2. De app op het apparaat, bij ontvangst van de melding:
   * Het apparaat neemt contact op met de back-end met het verzoek om de veilige lading.
   * De app kan de payload weergeven als een melding op het apparaat.

Het is belangrijk op te merken dat in de voorafgaande stroom (en in deze zelfstudie), we aannemen dat het apparaat een verificatietoken opslaat in lokale opslag, nadat de gebruiker zich heeft aanmeldt. Dit garandeert een volledig naadloze ervaring, omdat het apparaat de beveiligde payload van de melding kan ophalen met behulp van dit token. Als uw toepassing geen verificatietokens op het apparaat opslaat of als deze tokens kunnen worden verlopen, moet de apparaat-app, na ontvangst van de melding, een algemene melding weergeven waarin de gebruiker wordt gevraagd de app te starten. De app verifieert vervolgens de gebruiker en toont de payload van de melding.

Deze secure push-zelfstudie laat zien hoe je een pushmelding veilig verzenden. De zelfstudie bouwt voort op de zelfstudie [Gebruikers melden,](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) dus u moet eerst de stappen in die zelfstudie voltooien.

> [!NOTE]
> In deze zelfstudie wordt ervan uitgegaan dat u uw meldingshub hebt gemaakt en geconfigureerd zoals beschreven in [Aan de slag met Meldingenhubs (Windows Store).](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
> Houd er ook rekening mee dat Windows Phone 8.1 Windows -referenties (niet Windows Phone) vereist en dat achtergrondtaken niet werken op Windows Phone 8.0 of Silverlight 8.1. Voor Windows Store-toepassingen u meldingen alleen ontvangen via een achtergrondtaak als de app is ingeschakeld voor vergrendelingsscherm (klik op het selectievakje in het appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Het Windows Phone-project wijzigen

1. Voeg in het **Project NotifyUserWindowsPhone** de volgende code toe aan App.xaml.cs om de push-achtergrondtaak te registreren. Voeg de volgende regel code toe aan het einde van de `OnLaunched()`-methode:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Voeg nog steeds in App.xaml.cs de `OnLaunched()` volgende code toe direct na de methode:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. Voeg de `using` volgende instructies toe boven aan het App.xaml.cs bestand:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. Klik in het menu **Bestand** in Visual Studio op **Alles opslaan**.

## <a name="create-the-push-background-component"></a>De component Push-achtergrond maken

De volgende stap is het maken van de push achtergrond component.

1. Klik in Solution Explorer met de rechtermuisknop op het knooppunt op het bovenste niveau van de oplossing **(Oplossing SecurePush** in dit geval) en klik vervolgens op **Toevoegen**en klik vervolgens op **Nieuw project**.
2. **Store-apps**uitvouwen en vervolgens op **Windows Phone Apps**klikken en vervolgens op Windows **Runtime Component (Windows Phone)** klikken. Geef het project **PushBackgroundComponent**een naam en klik op **OK** om het project te maken.

    ![][12]
3. Klik in Solution Explorer met de rechtermuisknop op het **pushbackgroundcomponent-project (Windows Phone 8.1)** en klik vervolgens op **Toevoegen**en klik vervolgens op **Klasse**. Geef de `PushBackgroundTask.cs`nieuwe klasse een naam. Klik **op Toevoegen** om de klasse te genereren.
4. Vervang de volledige `PushBackgroundComponent` inhoud van de definitie van de naamruimte door de volgende code en vervang de tijdelijke aanduiding `{back-end endpoint}` door het back-end eindpunt dat is verkregen terwijl u uw back-end implementeert:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. Klik in Solution Explorer met de rechtermuisknop op het **pushbackgroundcomponent-project (Windows Phone 8.1)** en klik vervolgens op **NuGet-pakketten beheren**.
6. Klik aan de linkerkant op **Online**.
7. Typ **Http Client** in **het zoekvak**.
8. Klik in de lijst met resultaten op **Microsoft HTTP-clientbibliotheken**en klik vervolgens op **Installeren**. Voltooi de installatie.
9. Typ nu **Json.net** in **het zoekvak** van NuGet. Installeer het **Json.NET** pakket en sluit vervolgens het nuget-pakketbeheervenster.
10. Voeg de `using` volgende instructies boven `PushBackgroundTask.cs` aan het bestand toe:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. Klik in Solution Explorer in het project **NotifyUserWindowsPhone (Windows Phone 8.1)** met de rechtermuisknop **op Verwijzingen**en klik vervolgens op **Naslagwaarde toevoegen...**. Schakel in het dialoogvenster Referentiebeheer het selectievakje naast **PushBackgroundComponent**in en klik op **OK**.
12. Dubbelklik in Solution Explorer op **Package.appxmanifest** in het project **NotifyUserWindowsPhone (Windows Phone 8.1).** Stel **onder Meldingen** **pop-up ingesteld op** **ja**.

    ![][3]
13. Klik nog steeds in **Package.appxmanifest**op het menu **Declaratie** bovenaan. Klik in de vervolgkeuzelijst **Beschikbare declaratie** op **Achtergrondtaken**en klik vervolgens op **Toevoegen**.
14. Schakel in **Package.appxmanifest**onder **Eigenschappen**de controle **Pushmelding**in .
15. Typ In **Package.appxmanifest**onder **App-instellingen**het type **PushBackgroundComponent.PushBackgroundTask** in het **veld Toegangspunt.**

    ![][13]
16. Klik in het menu **Bestand** op **Alles opslaan**.

## <a name="run-the-application"></a>De toepassing uitvoeren

Ga als volgt te werk om de toepassing uit te voeren:

1. Voer in Visual Studio de **API-toepassing AppBackend** uit. Er wordt een ASP.NET webpagina weergegeven.
2. Voer in Visual Studio de **Windows Phone-app NotifyUser (Windows Phone 8.1)** uit. De Windows Phone-emulator wordt uitgevoerd en laadt de app automatisch.
3. Voer in de gebruikersinterface **van de Gebruikersinterface van De Gebruiker van WindowsPhone** een gebruikersnaam en wachtwoord in. Dit kan elke string zijn, maar ze moeten dezelfde waarde hebben.
4. Klik in de gebruikersinterface **van de Gebruikersinterface van De Gebruiker** van WindowsPhone op Aanmelden en registreer **u**. Klik vervolgens op **Push verzenden**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
