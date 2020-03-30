---
title: Aan de slag met verificatie in de App Xamarin Forms
description: Meer informatie over het gebruik van mobiele apps om gebruikers van uw App Xamarin Forms te verifiëren bij identiteitsproviders zoals AAD, Google, Facebook, Twitter en Microsoft.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458763"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Verificatie toevoegen aan uw App Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overzicht
In dit onderwerp ziet u hoe u gebruikers van een mobiele app van app-service verifiëren vanuit uw clienttoepassing. In deze zelfstudie voegt u verificatie toe aan het quickstartproject xamarin Forms met behulp van een identiteitsprovider die wordt ondersteund door App Service. Nadat u met succes is geverifieerd en geautoriseerd door uw mobiele app, wordt de waarde van de gebruikersnaam weergegeven en hebt u toegang tot beperkte tabelgegevens.

## <a name="prerequisites"></a>Vereisten
Voor het beste resultaat met deze zelfstudie raden we je aan om eerst de zelfstudie van de [App Xamarin Forms maken][1] te voltooien. Nadat u deze zelfstudie hebt voltooid, hebt u een Xamarin Forms-project dat een TodoList-app met meerdere platforms is.

Als u het gedownloade project voor snelle startserver niet gebruikt, moet u het verificatieuitbreidingspakket aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor][2]meer informatie over serverextensiepakketten.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Uw app registreren voor verificatie en App Services configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Uw app toevoegen aan de toegestane URL's voor omleiding

Veilige verificatie vereist dat u een nieuw URL-schema voor uw app definieert. Hierdoor kan het verificatiesysteem worden omgeleid naar uw app zodra het verificatieproces is voltooid. In deze zelfstudie gebruiken we de _URL-appname_ overal. U echter elk URL-schema gebruiken dat u kiest. Het moet uniek zijn voor uw mobiele applicatie. Ga als u de omleiding aan de serverzijde inschakelen:

1. Selecteer uw App-service in de [Azure-portal.][8]

2. Klik op de optie **Verificatie / Autorisatie.**

3. Voer in de toegestane externe `url_scheme_of_your_app://easyauth.callback` **omleidings-URL's**.  De **url_scheme_of_your_app** in deze tekenreeks is het URL-schema voor uw mobiele toepassing.  Het moet de normale URL-specificatie voor een protocol volgen (gebruik alleen letters en cijfers en begin met een letter).  U moet een notitie maken van de tekenreeks die u kiest, omdat u uw mobiele toepassingscode op verschillende plaatsen moet aanpassen met het URL-schema.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Verificatie toevoegen aan de draagbare klassenbibliotheek
Mobile Apps gebruikt de [extensie LoginAsync][3] op de [MobileServiceClient][4] om een gebruiker aan te melden met App Service-verificatie. In dit voorbeeld wordt een door de server beheerde verificatiestroom gebruikt die de aanmeldingsinterface van de provider in de app weergeeft. Zie [Verificatie met serverbeheer][5]voor meer informatie . Als u een betere gebruikerservaring wilt bieden in uw productie-app, moet u overwegen in plaats daarvan [verificatie met clientbeheerde verificatie][6]te gebruiken.

Als u wilt verifiëren met een Xamarin Forms-project, definieert u een **IAuthenticate-interface** in de portable class-bibliotheek voor de app. Voeg vervolgens een **aanmeldingsknop** toe aan de gebruikersinterface die is gedefinieerd in de draagbare klassebibliotheek, waarop u klikt om verificatie te starten. Gegevens worden geladen vanuit de back-end van de mobiele app na succesvolle verificatie.

Implementeer de **IAuthenticate-interface** voor elk platform dat door uw app wordt ondersteund.

1. Open in Visual Studio of Xamarin Studio App.cs van het project met **Portable** in de `using` naam, dat is Portable Class Library-project, en voeg vervolgens de volgende verklaring toe:

        using System.Threading.Tasks;
2. Voeg in App.cs `IAuthenticate` de volgende interfacedefinitie toe vlak voor de `App` klassendefinitie.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Als u de interface wilt initialiseren met een platformspecifieke implementatie, voegt u de volgende statische leden toe aan de klasse **App.**

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Open TodoList.xaml vanuit het project Portable Class Library en voeg het volgende **knopelement** toe in het element *buttonsPanel* layout, na de bestaande knop:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Met deze knop wordt serververificatie geactiveerd met de back-end van uw mobiele app.
5. Open TodoList.xaml.cs van het project Portable Class Library `TodoList` en voeg het volgende veld toe aan de klasse:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Vervang de methode **OnAppearing** door de volgende code:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Deze code zorgt ervoor dat gegevens alleen worden vernieuwd van de service nadat u bent geverifieerd.
7. Voeg de volgende handler voor de **gebeurtenis Klik toe** aan de klasse **TodoList:**

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Sla uw wijzigingen op en herbouw het project Portable Class Library en controleer geen fouten.

## <a name="add-authentication-to-the-android-app"></a>Verificatie toevoegen aan de Android-app
In deze sectie ziet u hoe u de **IAuthenticate-interface** implementeert in het Android-app-project. Sla deze sectie over als u geen Android-apparaten ondersteunt.

1. Klik in Visual Studio of Xamarin Studio met de rechtermuisknop op het **droid-project** en **stel in als StartUp Project**.
2. Druk op F5 om het project in de foutopsporing te starten en controleer vervolgens of een niet-afgehandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart. De 401-code wordt geproduceerd omdat de toegang op de backend beperkt is tot geautoriseerde gebruikers.
3. Open MainActivity.cs in het Android-project en voeg de volgende `using` instructies toe:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Werk de klasse **MainActivity** bij om de **IAuthenticate-interface** als volgt te implementeren:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Werk de klasse **MainActivity** bij door een **mobileservicegebruikersveld** en een **verificatiemethode** toe te voegen, die vereist is door de **IAuthenticate-interface:**

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Als u een andere identiteitsprovider dan Facebook gebruikt, kiest u een andere waarde voor [MobileServiceAuthenticationProvider.][7]

6. Werk het **Bestand AndroidManifest.xml bij** door `<application>` de volgende XML in het element toe te voegen:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Vervang `{url_scheme_of_your_app}` door uw URL-schema.
7. Voeg de volgende code toe aan de **methode OnCreate** van de klasse **MainActivity** voordat de aanroep wordt aangenomen om: `LoadApplication()`

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Deze code zorgt ervoor dat de authenticator wordt geïnitialiseerd voordat de app wordt geladen.
8. Herbouwen van de app, uitvoeren, vervolgens inloggen met de verificatie provider die u hebt gekozen en controleren of u in staat zijn om gegevens te openen als een geverifieerde gebruiker.

### <a name="troubleshooting"></a>Problemen oplossen

**De toepassing crashte met`Java.Lang.NoSuchMethodError: No static method startActivity`**

In sommige gevallen worden conflicten in de ondersteuningspakketten weergegeven als slechts een waarschuwing in de Visual-studio, maar de toepassing crasht met deze uitzondering bij runtime. In dit geval moet u ervoor zorgen dat alle ondersteuningspakketten waarnaar in uw project wordt verwezen, dezelfde versie hebben. Het [Azure Mobile Apps NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) heeft `Xamarin.Android.Support.CustomTabs`-afhankelijkheid voor het Android-platform, dus als uw project gebruikmaakt van nieuwere ondersteuningspakketten, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

## <a name="add-authentication-to-the-ios-app"></a>Verificatie toevoegen aan de iOS-app
In deze sectie ziet u hoe u de **IAuthenticate-interface** implementeert in het iOS-app-project. Sla deze sectie over als u geen iOS-apparaten ondersteunt.

1. Klik in Visual Studio of Xamarin Studio met de rechtermuisknop op het **iOS-project** en **stel in als StartUp Project**.
2. Druk op F5 om het project in de foutopsporing te starten en controleer vervolgens of een niet-afgehandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart. De 401-respons wordt geproduceerd omdat de toegang op de backend beperkt is tot geautoriseerde gebruikers.
3. Open AppDelegate.cs in het iOS-project en voeg de volgende `using` instructies toe:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Werk de klasse **AppDelegate** bij om de **interface IAuthenticate** als volgt te implementeren:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Werk de klasse **AppDelegate** bij door een **mobileservicegebruikersveld** en een **verificatiemethode** toe te voegen, die vereist is door de **iAuthenticate-interface:**

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Als u een andere identiteitsprovider dan Facebook gebruikt, kiest u een andere waarde voor [MobileServiceAuthenticationProvider].
    
6. Werk de klasse **AppDelegate** bij door de overbelasting van de **OpenUrl-methode** als volgt toe te voegen:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Voeg de volgende coderegel toe aan de `LoadApplication()`methode **FinishedLaunching** voordat de aanroep wordt aangenomen:

        App.Init(this);

    Deze code zorgt ervoor dat de authenticator wordt geïnitialiseerd voordat de app wordt geladen.

8. Open Info.plist en voeg een **URL-type**toe . Stel de **id** in op een naam van uw keuze, de **URL-schema's** op het URL-schema voor uw app en de **rol** naar Geen.

9. Herbouwen van de app, uitvoeren, vervolgens inloggen met de verificatie provider die u hebt gekozen en controleren of u in staat zijn om gegevens te openen als een geverifieerde gebruiker.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Verificatie toevoegen aan windows 10-app-projecten (inclusief telefoon)
In dit gedeelte ziet u hoe u de **IAuthenticate-interface** implementeert in de Windows 10-app-projecten. Dezelfde stappen gelden voor UwP-projecten (Universal Windows Platform), maar met behulp van het **UWP-project** (met bekende wijzigingen). Sla deze sectie over als u geen Windows-apparaten ondersteunt.

1. Klik in Visual Studio met de rechtermuisknop op het **UWP-project** en **stel in als StartUp-project**.
2. Druk op F5 om het project in de foutopsporing te starten en controleer vervolgens of een niet-afgehandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart. De 401-respons vindt plaats omdat de toegang op de backend beperkt is tot geautoriseerde gebruikers.
3. Open MainPage.xaml.cs voor het Windows-app-project en voeg de volgende `using` instructies toe:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Vervang `<your_Portable_Class_Library_namespace>` de naamruimte voor uw draagbare klasbibliotheek.
4. Werk de klasse **MainPage** als volgt bij om de **IAuthenticate-interface** te implementeren:

        public sealed partial class MainPage : IAuthenticate
5. Werk de klasse **MainPage** bij door een **mobileservicegebruikersveld** en een **verificatiemethode** toe te voegen, die vereist is door de **IAuthenticate-interface:**

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Als u een andere identiteitsprovider dan Facebook gebruikt, kiest u een andere waarde voor [MobileServiceAuthenticationProvider.][7]

1. Voeg de volgende regel code toe in de constructor `LoadApplication()`voor de klasse **MainPage** voordat de aanroep wordt aangenomen:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Vervang `<your_Portable_Class_Library_namespace>` de naamruimte voor uw draagbare klasbibliotheek.

3. Als u **UWP**gebruikt, voegt u de volgende **overschrijfmethode OnActivated** toe aan de klasse **App:**

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Open Package.appxmanifest en voeg een **Protocol-verklaring** toe. Stel de **weergavenaam** in op een naam van uw keuze en het **URL-schema** voor uw app.

4. Herbouwen van de app, uitvoeren, vervolgens inloggen met de verificatie provider die u hebt gekozen en controleren of u in staat zijn om gegevens te openen als een geverifieerde gebruiker.

## <a name="next-steps"></a>Volgende stappen
Nu u deze basisverificatiezelfstudie hebt voltooid, u overwegen verder te gaan naar een van de volgende zelfstudies:

* [Pushmeldingen toevoegen aan uw app](app-service-mobile-xamarin-forms-get-started-push.md)

  Informatie over het toevoegen van ondersteuning van pushmeldingen aan uw app en het configureren van de backend voor mobiele apps voor gebruik van Azure Notification Hubs voor het verzenden van pushmeldingen.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Informatie over het toevoegen van offlineondersteuning aan uw app met een back-end voor mobiele apps. Met offline synchronisatie kunnen eindgebruikers communiceren met een mobiele app - gegevens weergeven, toevoegen of wijzigen - zelfs als er geen netwerkverbinding is.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
