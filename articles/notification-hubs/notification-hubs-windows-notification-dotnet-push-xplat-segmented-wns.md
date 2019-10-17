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
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 9151870836b1a616a79e54275ed185a425c11f0c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72385606"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Zelf studie: meldingen verzenden naar specifieke apparaten met Universeel Windows-platform-toepassingen

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overzicht

In deze zelf studie leert u hoe u Azure Notification Hubs kunt gebruiken om de uittellingen van nieuws berichten te verzenden. In deze zelf studie worden de toepassingen Windows Store of Windows Phone 8,1 (niet-Silverlight) besproken. Als u Windows Phone 8,1 Silverlight hebt, raadpleegt u [Push meldingen naar specifieke Windows Phone apparaten met behulp van Azure notification hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

In deze zelf studie leert u hoe u Azure Notification Hubs kunt gebruiken om meldingen te pushen naar specifieke Windows-apparaten met een Universeel Windows-platform-toepassing (UWP). Nadat u de zelf studie hebt voltooid, kunt u zich registreren voor de laatste nieuws categorieën waarin u bent geïnteresseerd. U ontvangt alleen push meldingen voor deze categorieën.

Als u uitzend scenario's wilt inschakelen, neemt u een of meer *Tags* op wanneer u een registratie maakt in de notification hub. Wanneer meldingen naar een tag worden verzonden, ontvangen alle apparaten die zijn geregistreerd voor het label de melding. Zie [route ring en label expressies](notification-hubs-tags-segment-push-message.md)voor meer informatie over tags.

> [!NOTE]
> Windows Store en Windows Phone project versies 8,1 en lager worden niet ondersteund in Visual Studio 2019. Zie [Visual Studio 2019-platform doelen en-compatibiliteit](/visualstudio/releases/2019/compatibility)voor meer informatie.

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
> * Categorieselectie toevoegen aan de mobiele app
> * Registreren voor meldingen
> * Getagde meldingen verzenden
> * De app uitvoeren en meldingen genereren

## <a name="prerequisites"></a>Vereisten

Voltooi de [zelf studie: verzend meldingen naar universeel Windows-platform-apps met behulp van Azure notification hubs voordat u met][get-started] deze zelf studie begint.  

## <a name="add-category-selection-to-the-app"></a>Categorieselectie toevoegen aan de app

De eerste stap is het toevoegen van UI-elementen aan de bestaande hoofdpagina, zodat gebruikers categorieën kunnen selecteren waarvoor ze zich willen registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, maakt deze een apparaatregistratie in uw notification hub, met de geselecteerde categorieën als Tags.

1. Open het project bestand *MainPage. xaml* en kopieer de volgende code in het element `Grid`:

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

1. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer  > -**klasse** **toevoegen**. Geef in **Nieuw item toevoegen**de klasse- *meldingen*een naam en selecteer **toevoegen**. Voeg, indien nodig, de `public`-modificator toe aan de klassedefinitie.

1. Voeg de volgende `using`-instructies toe aan het nieuwe bestand:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. Kopieer de volgende code naar de nieuwe klasse `Notifications`:

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

    Als u meer dan één sjabloon wilt registreren, geeft u een sjabloon naam op, bijvoorbeeld *simpleWNSTemplateExample*. U geef de sjablonen een naam zodat u ze kunt bijwerken of verwijderen. U kunt meer dan één sjabloon registreren voor pop-upmeldingen en één voor tegels.

    >[!NOTE]
    > Met Notification Hubs kan een apparaat meerdere sjablonen registreren met behulp van dezelfde tag. In dit geval wordt een inkomend bericht dat de tag bedoelt, in meerdere meldingen bezorgd bij het apparaat, één voor elke sjabloon. Met dit proces kunt u hetzelfde bericht weer geven in meerdere visuele meldingen, zoals een badge en als een pop-upmelding in een Windows Store-app.

    Zie [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie.

1. Voeg in het project bestand *app.xaml.cs* de volgende eigenschap toe aan de klasse `App`:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    U gebruikt deze eigenschap om een `Notifications`-exemplaar te maken en te gebruiken.

    Vervang de tijdelijke aanduidingen `<hub name>` en `<connection string with listen access>` in de code door de naam van de meldingshub en de verbindingsreeks voor **DefaultFullSharedAccessSignature** die u eerder hebt opgevraagd.

   > [!NOTE]
   > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor *listen*-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

1. Voeg in het bestand *MainPage.xaml.cs* de volgende regel toe:

    ```csharp
    using Windows.UI.Popups;
    ```

1. Voeg in het bestand *MainPage.xaml.cs* de volgende methode toe:

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
> Omdat de kanaal-URI die wordt toegewezen door WNS (Windows Notification Service) op elk moment kan veranderen, moet u zich regelmatig registreren voor meldingen om fouten te voorkomen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u regel matig uitvoert, kunt u meer dan één keer per dag de registratie overs Laan als er minder dan een dag is verstreken sinds de vorige registratie.

1. Als u de klasse `notifications` wilt gebruiken om u te abonneren op basis van categorieën, opent u het *app.xaml.cs* -bestand en werkt u vervolgens de `InitNotificationsAsync`-methode bij.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Dit proces zorgt ervoor dat bij het starten van de app de categorieën worden opgehaald uit de lokale opslag. Vervolgens wordt de registratie van deze categorieën opgevraagd. U hebt de methode `InitNotificationsAsync` gemaakt als onderdeel van het [verzenden van meldingen naar universeel Windows-platform-apps met behulp van Azure notification hubs][get-started] zelf studie.
2. In het project bestand *MainPage.xaml.cs* voegt u de volgende code toe aan de `OnNavigatedTo`-methode:

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

De app is nu klaar Hiermee kan een set categorieën worden opgeslagen in de lokale opslag van het apparaat. Wanneer gebruikers de categorie selectie wijzigen, worden de opgeslagen categorieën gebruikt voor registratie bij de notification hub. In het volgende gedeelte definieert u een back-end die categoriemeldingen naar deze app kan verzenden.

## <a name="run-the-uwp-app"></a>De UWP-app uitvoeren

1. Selecteer in Visual Studio F5 om de app te compileren en te starten. De UI van de app biedt een reeks schakelopties waarmee u de categorieën kunt kiezen waarop u zich wilt abonneren.

   ![De app Breaking News](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Schakel een of meer categorie in of uit en selecteer vervolgens **Abonneren**.

   De app zet de geselecteerde categorieën om in tags en vraagt bij Notification Hubs een nieuwe apparaatregistratie aan voor de geselecteerde tags. In de app worden de geregistreerde categorieën in een dialoog venster weer gegeven.

    ![Schakelopties voor categorieën en de knop Subscribe](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Een console-app maken om gelabelde meldingen te verzenden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>De console-app uitvoeren om gelabelde meldingen te verzenden

Voer de app uit die u in de vorige sectie hebt gemaakt. Meldingen voor de geselecteerde categorieën worden weergegeven als pop-upmeldingen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u belangrijk nieuws per categorie kunt broadcasten. De back-endtoepassing pusht gelabelde meldingen naar apparaten die zijn geregistreerd voor het ontvangen van meldingen voor die tag. Ga door naar de volgende zelf studie voor meer informatie over het pushen van meldingen naar specifieke gebruikers, onafhankelijk van het apparaat dat ze gebruiken:

> [!div class="nextstepaction"]
> [Gelokaliseerde meldingen naar Windows-apps pushen met behulp van Azure Notification Hubs](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

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
