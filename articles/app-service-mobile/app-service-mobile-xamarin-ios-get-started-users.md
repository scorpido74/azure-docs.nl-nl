---
title: Aan de slag met authenticatie in Xamarin iOS
description: Meer informatie over het gebruik van Mobile Apps voor het verifiëren van gebruikers van uw Xamarin iOS-app met id-providers zoals AAD, Google, Facebook, Twitter en micro soft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 0f2c78c3d4b18e7c662c4f7345938ddab377229b
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668266"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Authenticatie toevoegen aan uw Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht

In dit onderwerp wordt beschreven hoe u gebruikers van een mobiele app van App Service kunt verifiëren vanuit uw client toepassing. In deze zelf studie voegt u verificatie toe aan het Quick start-project Xamarin. iOS met behulp van een id-provider die wordt ondersteund door App Service. Nadat de verificatie is voltooid en geautoriseerd door uw mobiele app, wordt de waarde van de gebruikers-ID weer gegeven en kunt u toegang krijgen tot beperkte tabel gegevens.

U moet eerst [een Xamarin. IOS-app maken]om de zelf studie te volt ooien. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u het uitbreidings pakket voor verificatie toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Uw app registreren voor verificatie en App Services configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Uw app toevoegen aan de toegestane externe omleidings-Url's

Voor beveiligde verificatie moet u een nieuw URL-schema definiëren voor uw app. Hierdoor kan het verificatie systeem terugkeren naar uw app nadat het verificatie proces is voltooid. In deze zelf studie gebruiken we het URL-schema _AppName_ in. U kunt echter elk gewenst URL-schema gebruiken. Deze moet uniek zijn voor uw mobiele toepassing. De omleiding aan de server zijde inschakelen:

1. Selecteer uw App Service in de [Azure Portal](https://portal.azure.com/).

2. Klik op de menu optie voor **verificatie/autorisatie** .

3. In de **toegestane externe omleidings-url's**voert u `url_scheme_of_your_app://easyauth.callback`in.  De **url_scheme_of_your_app** in deze teken reeks is het URL-schema voor uw mobiele toepassing.  De standaard URL-specificatie voor een protocol moet volgen (alleen letters en cijfers gebruiken en beginnen met een letter).  U moet een notitie maken van de teken reeks die u kiest, omdat u de code van uw mobiele toepassing moet aanpassen aan het URL-schema op verschillende locaties.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* In Visual Studio of Xamarin Studio voert u het-client project uit op een apparaat of emulator. Controleer of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) wordt gegenereerd nadat de app is gestart. De fout wordt geregistreerd in de console van het fout opsporingsprogramma. In Visual Studio ziet u de fout in het uitvoer venster.

    Deze niet-geautoriseerde fout treedt op omdat de app de back-end van uw mobiele app probeert te openen als een niet-geverifieerde gebruiker. Voor de tabel *TodoItem* is nu verificatie vereist.

Vervolgens werkt u de client-app bij om resources op te vragen bij de back-end van de mobiele app met een geverifieerde gebruiker.

## <a name="add-authentication-to-the-app"></a>Verificatie toevoegen aan de app
In deze sectie wijzigt u de app zodat er een aanmeldings scherm wordt weer gegeven voordat gegevens worden weer gegeven. Wanneer de app wordt gestart, wordt er geen verbinding gemaakt met uw App Service en worden er geen gegevens weer gegeven. Na de eerste keer dat de gebruiker de vernieuwings beweging heeft uitgevoerd, wordt het aanmeldings scherm weer gegeven. Nadat de aanmelding is geslaagd, wordt de lijst met TODO-items weer gegeven.

1. Open in het client project het bestand **QSTodoService.cs** en voeg het volgende toe met de instructie en `MobileServiceUser` met accessor aan de klasse QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Voeg een nieuwe methode met de naam **verifiëren** toe aan **QSTodoService** met de volgende definitie:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Als u een andere ID-provider gebruikt dan een Facebook, wijzigt u de waarde die is door gegeven aan **LoginAsync** boven aan een van de volgende opties: _MicrosoftAccount_, _Twitter_, _Google_of _WindowsAzureActiveDirectory_.

3. Open **QSTodoListViewController.cs**. Wijzig de methode definitie van **ViewDidLoad** de aanroep naar **RefreshAsync ()** aan het einde te verwijderen:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. Wijzig de methode **RefreshAsync** om te verifiëren of de eigenschap **User** null is. Voeg de volgende code toe boven aan de methode definitie:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Open **AppDelegate.cs**en voeg de volgende methode toe:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Open **info. plist** -bestand, navigeer naar **URL-typen** in de sectie **Geavanceerd** . Configureer nu de **id** -en **URL-SCHEMA'S** van uw URL-type en klik op **URL-type toevoegen**. **URL-schema's** moeten hetzelfde zijn als uw {url_scheme_of_your_app}.
7. In Visual Studio, dat is verbonden met uw Mac-host of Visual Studio voor Mac, voert u het client project uit dat is gericht op een apparaat of emulator. Controleer of er geen gegevens worden weer gegeven in de app.

    Voer de vernieuwings beweging uit door de lijst met items op te halen, waardoor het aanmeldings scherm wordt weer gegeven. Zodra u geldige referenties hebt ingevoerd, wordt in de app de lijst met TODO-items weer gegeven en kunt u updates voor de gegevens maken.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Een Xamarin. iOS-app maken]: app-service-mobile-xamarin-ios-get-started.md
