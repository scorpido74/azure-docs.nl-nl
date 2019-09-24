---
title: Meldingen verzenden naar specifieke apparaten (Universal Windows Platform) | Microsoft Docs
description: Gebruik Azure Notification Hubs met tags in de registratie om belangrijk nieuws te verzenden naar een Universal Windows Platform-app.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: efe668e42e04942cc0d9fc99670057ab5bdd302a
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212120"
---
# <a name="tutorial-push-notifications-to-specific-windows-devices-running-universal-windows-platform-applications"></a>Zelfstudie: Pushmeldingen verzenden naar specifieke Windows-apparaten met Universal Windows Platform-toepassingen

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie leert u hoe u Azure Notification Hubs gebruikt om belangrijke nieuwsberichten te broadcasten naar Windows Store- of Windows Phone 8.1-toepassingen (zonder Silverlight). Als u ontwikkelt voor Windows Phone 8.1 Silverlight, raadpleegt u de [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)-versie.

In deze zelfstudie leert u hoe u Azure Notification Hubs gebruikt om pushmeldingen te versturen naar specifieke Windows-apparaten waarop UWP-toepassingen (Universal Windows Platform) worden uitgevoerd. Nadat u de zelfstudie hebt voltooid, kunt u zich registreren voor de nieuwscategorieën waarin u geïnteresseerd bent om alleen voor die categorieën pushmeldingen te ontvangen als er belangrijk nieuws is.

Broadcast-scenario's zijn mogelijk door een of meer *tags* (of labels) toe te voegen wanneer u een registratie maakt in Notifications Hub. Wanneer meldingen worden verzonden naar een tag, ontvangen alle apparaten die zich hebben geregistreerd voor de tag de melding. Zie [Tags in registraties](notification-hubs-tags-segment-push-message.md) voor meer informatie over tags.

> [!NOTE]
> Projectversies 8.1 en lager van Windows Store en Windows Phone worden niet ondersteund in Visual Studio 2017. Zie [Geschikte platforms voor Visual Studio 2017 en compatibiliteit](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) voor meer informatie.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Categorieselectie toevoegen aan de mobiele app
> * Registreren voor meldingen
> * Getagde melding verzenden
> * De app uitvoeren en meldingen genereren

## <a name="prerequisites"></a>Vereisten

U moet de [Zelfstudie: Verzend meldingen naar universeel Windows-platform-apps met behulp][get-started] van Azure notification hubs voordat u met deze zelf studie begint.  

## <a name="add-category-selection-to-the-app"></a>Categorieselectie toevoegen aan de app

De eerste stap is het toevoegen van UI-elementen aan de bestaande hoofdpagina, zodat gebruikers categorieën kunnen selecteren waarvoor ze zich willen registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, wordt er een apparaatregistratie gemaakt in uw meldingshub, met de geselecteerde categorieën als tags.

1. Open het projectbestand MainPage.xaml en kopieer de volgende code naar het `Grid`-element:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

2. Klik in **Solution Explorer** met de rechtermuisknop op het project en voeg een nieuwe klasse toe: **Meldingen**. Voeg de modifier **public** toe aan de klassedefinitie en voeg de volgende `using`-instructies toe aan het nieuwe codebestand:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

3. Kopieer de volgende code naar de nieuwe klasse `Notifications`:

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    Deze klasse gebruik de lokale opslag voor het opslaan van de nieuwscategorieën die dit apparaat moet ontvangen. In plaats van de methode `RegisterNativeAsync` aan te roepen, verstuurt u een aanroep naar `RegisterTemplateAsync` om u te registreren voor de categorieën met behulp van een registratiesjabloon.

    Als u meer dan één sjabloon wilt registreren (bijvoorbeeld één voor pop-upmeldingen en één voor tegels), geeft u een sjabloonnaam op (zoals 'VoorbeeldEenvoudigWNSsjabloon'). U geef de sjablonen een naam zodat u ze kunt bijwerken of verwijderen.

    >[!NOTE]
    >Als op een apparaat meerdere sjablonen met dezelfde tag zijn geregistreerd, worden er meerdere meldingen verstuurd naar het apparaat (één voor elke sjabloon) als er een bericht binnenkomt dat gericht is op deze tag. Dit gedrag is handig wanneer hetzelfde logische bericht moet resulteren in meerdere visual meldingen (bijvoorbeeld een badge en een pop-up weergeven in een Windows Store-toepassing).

    Zie [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie.

4. Voeg in het projectbestand App.xaml.cs de volgende eigenschap toe aan de `App`-klasse:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    U gebruikt deze eigenschap om een `Notifications`-exemplaar te maken en te gebruiken.

    Vervang de tijdelijke aanduidingen `<hub name>` en `<connection string with listen access>` in de code door de naam van de meldingshub en de verbindingsreeks voor *DefaultFullSharedAccessSignature* die u eerder hebt opgevraagd.

   > [!NOTE]
   > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor *listen*-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

5. Voeg in het bestand `MainPage.xaml.cs` de volgende regel toe:

    ```csharp
    using Windows.UI.Popups;
    ```

6. Voeg in het bestand `MainPage.xaml.cs` de volgende methode toe:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    Met deze methode maakt u een lijst met categorieën en gebruikt u de klasse `Notifications` om de lijst op te slaan in de lokale opslag. Daarnaast worden de bijbehorende tags geregistreerd bij uw meldingshub. Wanneer de categorieën worden gewijzigd, wordt de registratie opnieuw gemaakt met de nieuwe categorieën.

Uw app kan nu een set categorieën opslaan in de lokale opslag op het apparaat. De app wordt geregistreerd bij de meldingshub wanneer gebruikers de categorieselectie wijzigen.

## <a name="register-for-notifications"></a>Registreren voor meldingen

In dit gedeelte registreert u zich tijdens het opstarten bij de meldingshub met behulp van de categorieën die u hebt opgeslagen in de lokale opslag.

> [!NOTE]
> Omdat de kanaal-URI die wordt toegewezen door WNS (Windows Notification Service) op elk moment kan veranderen, moet u zich regelmatig registreren voor meldingen om fouten te voorkomen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u regelmatig uitvoert (meer dan één keer per dag), kunt u de registratie waarschijnlijk overslaan om bandbreedte te besparen als er minder dan een dag is verstreken sinds de vorige registratie.

1. Als u de klasse `notifications` wilt gebruiken om u te abonneren op basis van categorieën, opent u het bestand App.xaml.cs en werkt u vervolgens de `InitNotificationsAsync`-methode bij.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Dit proces zorgt ervoor dat wanneer de app wordt gestart, de categorieën worden opgehaald uit de lokale opslag en registratie van deze categorieën wordt aangevraagd. U hebt de `InitNotificationsAsync` methode gemaakt als onderdeel van de zelf studie [aan de slag met Notification hubs][get-started] .
2. Voeg in het projectbestand `MainPage.xaml.cs` de volgende code toe aan de `OnNavigatedTo`-methode:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    Met deze code wordt de hoofdpagina bijgewerkt op basis van de status van eerder opgeslagen categorieën.

De app is nu klaar en kan een set categorieën opslaan in de lokale opslag van apparaten. Deze categorieën worden gebruikt voor registratie bij de meldingshub wanneer gebruikers de categorieselectie wijzigen. In het volgende gedeelte definieert u een back-end die categoriemeldingen naar deze app kan verzenden.

## <a name="run-the-uwp-app"></a>De UWP-app uitvoeren 
1. Druk in Visual Studio op **F5** om de app te compileren en te starten. De UI van de app biedt een reeks schakelopties waarmee u de categorieën kunt kiezen waarop u zich wilt abonneren.

    ![De app Breaking News](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png)

2. Zet een of meer categorieën op On en klik vervolgens op **Subscribe**.

    De app zet de geselecteerde categorieën om in tags en vraagt bij Notification Hubs een nieuwe apparaatregistratie aan voor de geselecteerde tags. De geregistreerde categorieën worden geretourneerd en weergegeven in een dialoogvenster.

    ![Schakelopties voor categorieën en de knop Subscribe](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Een console-app maken om gelabelde meldingen te verzenden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>De console-app uitvoeren om gelabelde meldingen te verzenden

1. Voer de app uit die u in de vorige sectie hebt gemaakt.
2. Meldingen voor de geselecteerde categorieën worden weergegeven als pop-upmeldingen. Als u de melding selecteert, wordt het eerste UWP-app-venster weer geven. 

     ![Pop-upmeldingen](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png)


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u belangrijk nieuws per categorie kunt broadcasten. De back-endtoepassing verstuurt getagde pushmeldingen naar apparaten die zich hebben geregistreerd voor het ontvangen van meldingen voor die tag. Als u wilt weten hoe u pushmeldingen kunt verzenden naar specifieke gebruikers, ongeacht welk apparaat ze gebruiken, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Gelokaliseerde pushmeldingen verzenden](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: https://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
