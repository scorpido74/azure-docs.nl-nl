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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72385606"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>Zelfstudie: Meldingen verzenden naar specifieke apparaten met Universal Windows Platform-toepassingen

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overzicht

In deze zelfstudie ziet u hoe u Azure Notification Hubs gebruiken om nieuwsmeldingen uit te zenden. Deze zelfstudie heeft betrekking op Windows Store- of Windows Phone 8.1-toepassingen (niet-Silverlight). Zie [Pushmeldingen naar specifieke Windows Phone-apparaten met Azure Notification Hubs](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)als u windows Phone 8.1 Silverlight target.

In deze zelfstudie leert u hoe u Azure Notification Hubs gebruiken om meldingen te pushen naar specifieke Windows-apparaten met een UWP-toepassing (Universal Windows Platform). Nadat u de zelfstudie hebt voltooid, u zich registreren voor de categorieën breaking news waarin u geïnteresseerd bent. Je ontvangt alleen pushmeldingen voor die categorieën.

Als u uitzendscenario's wilt inschakelen, neemt u een of meer *tags op* wanneer u een registratie maakt in de meldingshub. Wanneer meldingen naar een tag worden verzonden, ontvangen alle apparaten die zijn geregistreerd voor de tag de melding. Zie [Routerings- en tagexpressies](notification-hubs-tags-segment-push-message.md)voor meer informatie over tags.

> [!NOTE]
> Windows Store- en Windows Phone-projectversies 8.1 en eerder worden niet ondersteund in Visual Studio 2019. Zie [Targeting en compatibiliteit van Visual Studio 2019 Platform](/visualstudio/releases/2019/compatibility)voor meer informatie.

In deze zelfstudie doet u de volgende taken:

> [!div class="checklist"]
> * Categorieselectie toevoegen aan de mobiele app
> * Registreren voor meldingen
> * Getagde meldingen verzenden
> * De app uitvoeren en meldingen genereren

## <a name="prerequisites"></a>Vereisten

U moet de zelfstudie [Meldingen verzenden naar Universal Windows Platform-apps met behulp van Azure Notification Hubs][get-started] hebben voltooid voordat u aan deze zelfstudie begint.  

## <a name="add-category-selection-to-the-app"></a>Categorieselectie toevoegen aan de app

De eerste stap is het toevoegen van UI-elementen aan de bestaande hoofdpagina, zodat gebruikers categorieën kunnen selecteren waarvoor ze zich willen registreren. De geselecteerde categorieën worden op het apparaat opgeslagen. Wanneer de app wordt gestart, wordt een apparaatregistratie gemaakt in uw meldingshub, met de geselecteerde categorieën als tags.

1. Open het *projectbestand MainPage.xaml* en kopieer de `Grid` volgende code in het element:

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

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en selecteer**Klasse** **toevoegen** > . Geef **in Nieuw item toevoegen**de naam van de *klasseMeldingen*en selecteer **Toevoegen**. Voeg indien nodig `public` de modifier toe aan de klassendefinitie.

1. Voeg de `using` volgende instructies toe aan het nieuwe bestand:

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

    Als u meer dan één sjabloon wilt registreren, geeft u bijvoorbeeld een sjabloonnaam op, bijvoorbeeld *simpleWNSTemplateExample*. U geef de sjablonen een naam zodat u ze kunt bijwerken of verwijderen. U meer dan één sjabloon registreren om er een te hebben voor pop-upmeldingen en één voor tegels.

    >[!NOTE]
    > Met Notification Hubs kan een apparaat meerdere sjablonen registreren met dezelfde tag. In dit geval resulteert een binnenkomend bericht dat zich richt op de tag in meerdere meldingen die naar het apparaat worden verzonden, één voor elke sjabloon. Met dit proces u hetzelfde bericht weergeven in meerdere visuele meldingen, zoals zowel als badge als als een pop-upmelding in een Windows Store-app.

    Zie [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie.

1. Voeg in het *App.xaml.cs-projectbestand* de `App` volgende eigenschap toe aan de klasse:

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    U gebruikt deze eigenschap om een `Notifications`-exemplaar te maken en te gebruiken.

    Vervang de tijdelijke aanduidingen `<hub name>` en `<connection string with listen access>` in de code door de naam van de meldingshub en de verbindingsreeks voor **DefaultFullSharedAccessSignature** die u eerder hebt opgevraagd.

   > [!NOTE]
   > Omdat referenties die worden gedistribueerd met een client-app meestal niet beveiligd zijn, moet u met uw client-app alleen de sleutel voor *listen*-toegang distribueren. Uw app kan dan worden geregistreerd voor meldingen, maar bestaande registraties kunnen niet worden gewijzigd, en er kunnen geen meldingen worden verzonden. De sleutel voor volledige toegang wordt gebruikt in een beveiligde back-endservice voor het verzenden van meldingen en het wijzigen van bestaande registraties.

1. Voeg in het *MainPage.xaml.cs-bestand* de volgende regel toe:

    ```csharp
    using Windows.UI.Popups;
    ```

1. Voeg in het *MainPage.xaml.cs* bestand de volgende methode toe:

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

    Met deze methode maakt u een lijst met categorieën en gebruikt u de klasse `Notifications` om de lijst op te slaan in de lokale opslag. Daarnaast worden de bijbehorende tags geregistreerd bij uw meldingshub. Wanneer de categorieën veranderen, wordt de registratie opnieuw gemaakt met de nieuwe categorieën.

Uw app kan nu een set categorieën opslaan in de lokale opslag op het apparaat. De app wordt geregistreerd bij de meldingshub wanneer gebruikers de categorieselectie wijzigen.

## <a name="register-for-notifications"></a>Registreren voor meldingen

In dit gedeelte registreert u zich tijdens het opstarten bij de meldingshub met behulp van de categorieën die u hebt opgeslagen in de lokale opslag.

> [!NOTE]
> Omdat de kanaal-URI die wordt toegewezen door WNS (Windows Notification Service) op elk moment kan veranderen, moet u zich regelmatig registreren voor meldingen om fouten te voorkomen. In dit voorbeeld wordt er elke keer dat de app wordt gestart een registratie voor meldingen vastgelegd. Voor apps die u vaak uitvoert, bijvoorbeeld meer dan eens per dag, u de registratie waarschijnlijk overslaan om bandbreedte te behouden als er minder dan een dag is verstreken sinds de vorige registratie.

1. Als u `notifications` de klasse wilt gebruiken om u te abonneren `InitNotificationsAsync` op basis van categorieën, opent u het *App.xaml.cs-bestand* en werkt u de methode bij.

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    Dit proces zorgt ervoor dat wanneer de app wordt gestart, de categorieën worden opgehaald uit lokale opslag. Vervolgens vraagt zij om registratie van deze categorieën. U hebt `InitNotificationsAsync` de methode gemaakt als onderdeel van de [apps Meldingen verzenden naar universele Windows Platform-apps met behulp van de][get-started] zelfstudie Van Azure Notification Hubs.
2. Voeg in het *MainPage.xaml.cs* projectbestand de `OnNavigatedTo` volgende code toe aan de methode:

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

De app is nu klaar Het kan een reeks categorieën opslaan in de lokale opslag van het apparaat. Wanneer gebruikers de categorieselectie wijzigen, worden de opgeslagen categorieën gebruikt om zich te registreren bij de meldingshub. In het volgende gedeelte definieert u een back-end die categoriemeldingen naar deze app kan verzenden.

## <a name="run-the-uwp-app"></a>De UWP-app uitvoeren

1. Druk in Visual Studio op F5 om de app te compileren en te starten. De UI van de app biedt een reeks schakelopties waarmee u de categorieën kunt kiezen waarop u zich wilt abonneren.

   ![De app Breaking News](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. Schakel een of meer categoriewissels in en selecteer **Vervolgens Abonneren**.

   De app zet de geselecteerde categorieën om in tags en vraagt bij Notification Hubs een nieuwe apparaatregistratie aan voor de geselecteerde tags. De app geeft de geregistreerde categorieën weer in een dialoogvenster.

    ![Schakelopties voor categorieën en de knop Subscribe](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>Een console-app maken om gecodeerde meldingen te verzenden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>De console-app uitvoeren om gecodeerde meldingen te verzenden

Voer de app uit die in de vorige sectie is gemaakt. Meldingen voor de geselecteerde categorieën worden weergegeven als pop-upmeldingen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u belangrijk nieuws per categorie kunt broadcasten. De back-endapplicatie pusht gelabelde meldingen naar apparaten die zich hebben geregistreerd om meldingen voor die tag te ontvangen. Ga naar de volgende zelfstudie voor meer informatie over het pushen van meldingen naar specifieke gebruikers die onafhankelijk zijn van welk apparaat ze gebruiken:

> [!div class="nextstepaction"]
> [Gelokaliseerde pushmeldingen verzenden naar Windows-apps met Azure Notification Hubs](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

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
