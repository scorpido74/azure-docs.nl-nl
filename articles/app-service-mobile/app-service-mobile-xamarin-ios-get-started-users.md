---
title: Aan de slag met verificatie in Xamarin iOS
description: Meer informatie over het gebruik van mobiele apps om gebruikers van uw Xamarin iOS-app te verifiëren bij identiteitsproviders zoals AAD, Google, Facebook, Twitter en Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461314"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Verificatie toevoegen aan uw Xamarin.iOS-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overzicht

In dit onderwerp ziet u hoe u gebruikers van een mobiele app van app-service verifiëren vanuit uw clienttoepassing. In deze zelfstudie voegt u verificatie toe aan het Quickstart-project Xamarin.iOS met behulp van een identiteitsprovider die wordt ondersteund door App Service. Nadat u met succes is geverifieerd en geautoriseerd door uw mobiele app, wordt de waarde van de gebruikersnaam weergegeven en hebt u toegang tot beperkte tabelgegevens.

U moet eerst de zelfstudie voltooien [Maak een Xamarin.iOS-app]. Als u het gedownloade project voor snelle startserver niet gebruikt, moet u het verificatieuitbreidingspakket aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Uw app registreren voor verificatie en App Services configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Uw app toevoegen aan de toegestane URL's voor omleiding

Veilige verificatie vereist dat u een nieuw URL-schema voor uw app definieert. Hierdoor kan het verificatiesysteem worden omgeleid naar uw app zodra het verificatieproces is voltooid. In deze zelfstudie gebruiken we de _URL-appname_ overal. U echter elk URL-schema gebruiken dat u kiest. Het moet uniek zijn voor uw mobiele applicatie. Ga als u de omleiding aan de serverzijde inschakelen:

1. Selecteer uw App-service in de [Azure-portal.](https://portal.azure.com/)

2. Klik op de optie **Verificatie / Autorisatie.**

3. Voer in de toegestane externe `url_scheme_of_your_app://easyauth.callback` **omleidings-URL's**.  De **url_scheme_of_your_app** in deze tekenreeks is het URL-schema voor uw mobiele toepassing.  Het moet de normale URL-specificatie voor een protocol volgen (gebruik alleen letters en cijfers en begin met een letter).  U moet een notitie maken van de tekenreeks die u kiest, omdat u uw mobiele toepassingscode op verschillende plaatsen moet aanpassen met het URL-schema.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Voer in Visual Studio of Xamarin Studio het clientproject uit op een apparaat of emulator. Controleer of een niet-behandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart. De fout wordt geregistreerd op de console van de foutopsporing. In Visual Studio ziet u de fout dus in het uitvoervenster.

    Deze ongeautoriseerde fout treedt op omdat de app probeert toegang te krijgen tot de back-end van uw mobiele app als niet-geverifieerde gebruiker. De *TodoItem-tabel* vereist nu verificatie.

Vervolgens werkt u de client-app bij om bronnen op te vragen bij de back-end van de mobiele app met een geverifieerde gebruiker.

## <a name="add-authentication-to-the-app"></a>Verificatie toevoegen aan de app
In deze sectie wijzigt u de app om een inlogscherm weer te geven voordat gegevens worden weergegeven. Wanneer de app wordt gestart, wordt er geen verbinding gemaakt met uw app-service en worden geen gegevens weergegeven. Na de eerste keer dat de gebruiker de vernieuwingsbeweging uitvoert, wordt het inlogscherm weergegeven; na een succesvolle login wordt de lijst met todo-items weergegeven.

1. Open in het clientproject het bestand **QSTodoService.cs** en `MobileServiceUser` voeg de volgende instructie en met accessoire toe aan de klasse QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Voeg nieuwe methode met de naam **Authenticeren** toe aan **QSTodoService** met de volgende definitie:

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
    > Als u een andere identiteitsprovider dan een Facebook gebruikt, wijzigt u de waarde die hierboven wordt doorgegeven aan **LoginAsync** in een van de volgende opties: _MicrosoftAccount,_ _Twitter,_ _Google_of _WindowsAzureActiveDirectory_.

3. Open **QSTodoListViewController.cs**. Wijzig de methodedefinitie van **ViewDidLoad** en verwijder de aanroep naar **RefreshAsync()** aan het einde:

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

4. Wijzig de methode **RefreshAsync** om te verifiëren of de eigenschap **Gebruiker** null is. Voeg de volgende code toe boven aan de methodedefinitie:

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

5. Open **AppDelegate.cs,** voeg de volgende methode toe:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Open het bestand **Info.plist** en navigeer naar **URL-typen** in de sectie **Geavanceerd.** Configureer nu de **id** en de **URL-schema's** van uw URL-type en klik op **URL-type toevoegen**. **URL-schema's** moeten hetzelfde zijn als uw {url_scheme_of_your_app}.
7. Voer in Visual Studio, verbonden met uw Mac Host of Visual Studio voor Mac, het clientproject uit dat gericht is op een apparaat of emulator. Controleer of de app geen gegevens weergeeft.

    Voer de vernieuwingsbeweging uit door de lijst met items naar beneden te halen, waardoor het inlogscherm wordt weergegeven. Zodra u geldige referenties hebt ingevoerd, geeft de app de lijst met todo-items weer en u de gegevens bijgewerkt.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Een Xamarin.iOS-app maken]: app-service-mobile-xamarin-ios-get-started.md
