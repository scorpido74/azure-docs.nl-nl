---
title: Meldingen verzenden naar Universal Windows Platform-apps met behulp van Azure Notification Hubs | Microsoft Docs
description: Lean hoe u Azure Notification Hubs gebruiken om meldingen naar een Windows Universal Platform-toepassing te pushen.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: f78f24ee68545b386169e29a5a52ccc572849ad7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80127059"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Zelfstudie: Meldingen verzenden naar universele Windows Platform-apps met Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

In deze zelfstudie maakt u een Notification Hub om pushmeldingen te verzenden naar een UWP-app (Universal Windows Platform). U maakt een lege Windows Store-app die pushmeldingen ontvangt via Windows Push Notification Service (WNS). Vervolgens gebruikt u uw meldingshub om pushmeldingen uit te zenden naar alle apparaten waarop uw app wordt uitgevoerd.

> [!NOTE]
> De volledige code voor deze zelfstudie vindt u op [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

U neemt de volgende stappen:

> [!div class="checklist"]
> * Een app maken in Windows Store
> * Een Notification Hub maken
> * Een voorbeeld-app voor Windows maken
> * Testmeldingen verzenden

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Microsoft Visual Studio 2017 of hoger. Het voorbeeld in deze zelfstudie maakt gebruik van [Visual Studio 2019](https://www.visualstudio.com/products).
- [Ontwikkelhulpprogramma's voor UWP-apps geïnstalleerd](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Een actief Windows Store-account
- Controleer of **Meldingen van apps en andere afzenders** worden ingeschakeld. 
    - Venster **Instellingen starten** op uw computer.
    - Selecteer de **tegel Systeem.**
    - Selecteer **Meldingen & acties** in het linkermenu. 
    - Controleer of de instelling **Meldingen van apps en andere afzenders** is ingeschakeld. Als dit niet is ingeschakeld, schakelt u het in.

Het voltooien van deze zelfstudie is een vereiste voor alle andere Notification Hubs-zelfstudies voor UWP-apps.

## <a name="create-an-app-in-windows-store"></a>Een app maken in Windows Store

Als u pushmeldingen naar UWP-apps wilt verzenden, koppelt u uw app aan de Windows Store. Vervolgens configureert u de Notification Hub voor integratie met WNS.

1. Navigeer naar het [Windows-ontwikkelaarscentrum](https://partner.microsoft.com/dashboard/windows/first-run-experience), meld u aan met uw Microsoft-account en selecteer vervolgens **Een nieuwe app maken**.

    ![Knop Nieuwe app](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Typ een naam voor uw app en selecteer **Productnaam reserveren**. Hiermee maakt u een nieuwe Windows Store-registratie voor uw app.

    ![Naam van de Store-app](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Vouw **productbeheer**uit, selecteer **WNS/MPNS**en selecteer vervolgens **de site Live Services**. Meld u aan bij uw Microsoft-account. De pagina voor toepassingsregistratie wordt geopend in een nieuw tabblad. U ook rechtstreeks naar de pagina [Mijn toepassingen](https://apps.dev.microsoft.com) navigeren en uw toepassingsnaam selecteren om naar deze pagina te gaan.

    ![WNS MPNS-pagina](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Let op het wachtwoord **Toepassingsgeheimen** en de **package security identifier (SID)**.

    >[!WARNING]
    >Het toepassingsgeheim en de pakket-SID zijn belangrijke beveiligingsreferenties. Deel deze waarden met niemand en distribueer ze niet met uw app.

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>WNS-instellingen voor de hub configureren

1. Selecteer in de categorie **MELDINGSINSTELLINGEN** de optie **Windows (WNS)**.
2. Voer waarden in voor **pakket-SID** en **Beveiligingssleutel** die u in de vorige sectie hebt genoteerd.
3. Klik op **Opslaan** op de werkbalk.

    ![De vakken Pakket-SID en Beveiligingssleutel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

De Notification Hub is nu geconfigureerd om met WNS te kunnen werken. U hebt de verbindingsreeksen om uw app te registreren en meldingen te verzenden.

## <a name="create-a-sample-windows-app"></a>Een voorbeeld-app voor Windows maken

1. Open in Visual Studio het menu **File** en selecteer **New** en vervolgens **Project**.
2. Voer in het dialoogvenster **Een nieuw project maken** de volgende stappen uit:

    1. Typ **Windows Universal**in het zoekvak bovenaan.
    2. Selecteer in de zoekresultaten De optie **Lege app (Universeel Windows)** en selecteer **Volgende**.

       ![Het dialoogvenster Nieuw project](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. Voer in het dialoogvenster **Uw nieuwe project configureren** een **projectnaam**en een **locatie** voor de projectbestanden in.
    4. Selecteer **Maken**.

3. Accepteer de standaardwaarden voor het **doel** en de **minimale** platformversies en selecteer **OK**.
4. Klik in Solution Explorer met de rechtermuisknop op het project van de Windows Store-app, selecteer **Publiceren**en selecteer **Vervolgens App koppelen aan de Store**. Hierop wordt de wizard **Uw app koppelen aan Windows Store** weergegeven.
5. Meld u in de wizard aan met uw Microsoft-account.
6. Selecteer de app die u in stap 2 hebt geregistreerd, selecteer **Volgende** en selecteer vervolgens **koppelen**. Hierdoor worden de vereiste registratiegegevens voor Windows Store toegevoegd aan het toepassingsmanifest.
7. Klik met de rechtermuisknop op de oplossing in Visual Studio en selecteer **Manage NuGet Packages**. Het venster **Manage NuGet Packages** wordt geopend.
8. Voer in het zoekvak **WindowsAzure.Messaging.Managed** in, selecteer **Install** en accepteer de gebruiksvoorwaarden.

    ![Het venster NuGet-pakketten beheren][20]

    Met deze actie wordt een verwijzing gedownload, geïnstalleerd en toegevoegd. Deze verwijst naar de Azure Notification Hubs-bibliotheek voor Windows door gebruik te maken van het [Microsoft.Azure.NotificationHubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Open het projectbestand `App.xaml.cs` en voeg de volgende instructies toe:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. Zoek in het `App.xaml.cs` bestand van `App` het project de `InitNotificationsAsync` klasse en voeg de volgende methodedefinitie toe. Vervang `<your hub name>` de naam van de meldingshub die u `<Your DefaultListenSharedAccessSignature connection string>` in `DefaultListenSharedAccessSignature` de Azure-portal hebt gemaakt en vervang de verbindingstekenreeks van de pagina Toegangspolitie van uw **meldingshub:**

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
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

    Met deze code wordt de kanaal-URI voor de app opgehaald uit WNS en wordt vervolgens die kanaal-URI voor uw Notification Hub geregistreerd.

    >[!NOTE]
    > Vervang de tijdelijke aanduiding `hub name` door de naam van de Notification Hub die in de Azure-portal wordt weergegeven. Vervang ook de tijdelijke plaatsaanduiding van de verbindingstekenreeks door de verbindingsreeks `DefaultListenSharedAccessSignature` die u in een eerdere sectie hebt verkregen op de pagina **Toegangsbeleid** van uw Notification Hub.

11. Voeg bovenaan de gebeurtenis-handler `OnLaunched` in `App.xaml.cs` de volgende oproep naar de nieuwe methode `InitNotificationsAsync` toe:

    ```csharp
    InitNotificationsAsync();
    ```

    Met deze actie wordt gegarandeerd dat de kanaal-URI in uw Notification Hub wordt geregistreerd telkens wanneer de toepassing wordt gestart.

12. Als u de app wilt uitvoeren, drukt u de toets **F5** op het toetsenbord. Er wordt een dialoogvenster met de registratiesleutel weergegeven. Klik op **OK** om het dialoogvenster te sluiten.

    ![Registratie voltooid](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Uw app is nu gereed om pop-upmeldingen te ontvangen.

## <a name="send-test-notifications"></a>Testmeldingen verzenden

U kunt het ontvangen van meldingen in de app snel testen door meldingen te verzenden in [Azure Portal](https://portal.azure.com/).

1. Ga in de Azure Portal naar het tabblad Overzicht en selecteer **Test verzenden** op de werkbalk.

    ![Knop Verzenden testen](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. Voer in het venster **Test verzenden** de volgende acties uit:
    1. Selecteer **Windows** bij **Platforms**.
    2. Selecteer **Pop-up** bij **Meldingstype**.
    3. Selecteer **Verzenden**.

        ![Het venster Verzenden testen](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Bekijk het resultaat van de verzendbewerking in de lijst **Resultaat** onder in het venster. U ziet ook een waarschuwingsbericht.

    ![Resultaat van de verzendbewerking](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. U ziet de melding **Testbericht** op het bureaublad.

    ![Melding](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Volgende stappen
U hebt zendmeldingen naar al uw Windows-apparaten verzonden via de portal of een console-app. Ga verder met de volgende zelfstudie als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke apparaten:

> [!div class="nextstepaction"]
>[Pushmeldingen verzenden naar specifieke apparaten](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: https://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: https://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: https://msdn.microsoft.com/library/windows/apps/hh779719.aspx
