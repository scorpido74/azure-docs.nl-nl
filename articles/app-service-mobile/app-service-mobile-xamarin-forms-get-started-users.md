---
title: Aan de slag met verificatie voor Mobile Apps in Xamarin Forms-app | Microsoft Docs
description: Meer informatie over het gebruik van Mobile Apps voor het verifiëren van gebruikers van uw Xamarin Forms-app via verschillende id-providers, waaronder AAD, Google, Facebook, Twitter en micro soft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d9bd698535b09ecb5c484eefcbe31228eb99e04f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388410"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Verificatie toevoegen aan uw Xamarin Forms-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
In dit onderwerp wordt beschreven hoe u gebruikers van een mobiele app van App Service kunt verifiëren vanuit uw client toepassing. In deze zelf studie voegt u verificatie toe aan het Quick start-project van Xamarin-formulieren met behulp van een id-provider die wordt ondersteund door App Service. Nadat de verificatie is voltooid en geautoriseerd door uw mobiele app, wordt de waarde voor de gebruikers-ID weer gegeven en kunt u toegang krijgen tot beperkte tabel gegevens.

## <a name="prerequisites"></a>Vereisten
Voor het beste resultaat met deze zelf studie raden wij u aan eerst de zelf studie [een Xamarin Forms-app maken][1] uit te voeren. Nadat u deze zelf studie hebt voltooid, hebt u een Xamarin-formulier project dat een multi-platform TodoList-app is.

Als u het gedownloade Quick Start Server-project niet gebruikt, moet u het uitbreidings pakket voor verificatie toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps][2]voor meer informatie over server extensie pakketten.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Uw app registreren voor verificatie en App Services configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uw app toevoegen aan de toegestane externe omleidings-Url's

Voor beveiligde verificatie moet u een nieuw URL-schema definiëren voor uw app. Hierdoor kan het verificatie systeem terugkeren naar uw app nadat het verificatie proces is voltooid. In deze zelf studie gebruiken we het URL-schema _AppName_ in. U kunt echter elk gewenst URL-schema gebruiken. Deze moet uniek zijn voor uw mobiele toepassing. De omleiding aan de server zijde inschakelen:

1. Selecteer uw App Service in de [Azure Portal][8].

2. Klik op de menu optie voor **verificatie/autorisatie** .

3. Voer in de **toegestane externe omleidings-url's**`url_scheme_of_your_app://easyauth.callback` in.  De **url_scheme_of_your_app** in deze teken reeks is het URL-schema voor uw mobiele toepassing.  De standaard URL-specificatie voor een protocol moet volgen (alleen letters en cijfers gebruiken en beginnen met een letter).  U moet een notitie maken van de teken reeks die u kiest, omdat u de code van uw mobiele toepassing moet aanpassen aan het URL-schema op verschillende locaties.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Verificatie toevoegen aan de draag bare klassen bibliotheek
Mobile Apps maakt gebruik van de [LoginAsync][3] -extensie methode op de [mobileserviceclient te maken][4] om zich aan te melden bij een gebruiker met app service-verificatie. In dit voor beeld wordt een door de server beheerde verificatie stroom gebruikt waarmee de aanmeldings interface van de provider in de app wordt weer gegeven. Zie voor meer informatie [Server-beheerde verificatie][5]. Als u een betere gebruikers ervaring in uw productie-app wilt bieden, kunt u overwegen een door [client beheerde verificatie][6]te gebruiken.

Als u wilt verifiëren met een Xamarin Forms-project, definieert u een **IAuthenticate** -interface in de draag bare klassen bibliotheek voor de app. Voeg vervolgens een **aanmeldings** knop toe aan de gebruikers interface die is gedefinieerd in de draag bare klassen bibliotheek, waarop u kunt klikken om de verificatie te starten. De gegevens worden vanaf de back-end van de mobiele app geladen nadat de verificatie is geslaagd.

Implementeer de **IAuthenticate** -interface voor elk platform dat wordt ondersteund door uw app.

1. Open in Visual Studio of Xamarin Studio App.cs van het project met een **draag bare** naam, een draagbaar Class Library-project en voeg de volgende `using`-instructie toe:

        using System.Threading.Tasks;
2. Voeg in App.cs de volgende `IAuthenticate`-interface definitie direct voor de definitie van de `App`-klasse.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Als u de interface wilt initialiseren met een platformspecifieke implementatie, voegt u de volgende statische leden toe aan de **app** -klasse.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Open TodoList. xaml vanuit het Portable Class Library-project, voeg het volgende **knop** element toe in het element *buttonsPanel* layout, na de bestaande knop:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Met deze knop wordt door de server beheerde verificatie door de back-end van uw mobiele app geactiveerd.
5. Open TodoList.xaml.cs uit het Portable Class Library-project en voeg het volgende veld toe aan de klasse `TodoList`:

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

    Met deze code zorgt u ervoor dat de gegevens van de service alleen worden vernieuwd nadat u bent geverifieerd.
7. Voeg de volgende handler voor de gebeurtenis **geklikt** toe aan de klasse **TodoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Sla uw wijzigingen op en bouw het Portable Class Library-project opnieuw op zonder fouten te controleren.

## <a name="add-authentication-to-the-android-app"></a>Verificatie toevoegen aan de Android-app
In deze sectie wordt uitgelegd hoe u de **IAuthenticate** -interface in het Android-app-project implementeert. Sla deze sectie over als u geen Android-apparaten ondersteunt.

1. Klik in Visual Studio of Xamarin Studio met de rechter muisknop op het **Droid** -project en stel dit in **als opstart project**.
2. Druk op F5 om het project in het fout opsporingsprogramma te starten en controleer vervolgens of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) optreedt nadat de app is gestart. De 401-code wordt gemaakt, omdat de toegang op de back-end alleen is beperkt tot gemachtigde gebruikers.
3. Open MainActivity.cs in het Android-project en voeg de volgende `using`-instructies toe:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Werk de **MainActivity** -klasse voor het implementeren van de **IAuthenticate** -interface als volgt bij:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Werk de **MainActivity** -klasse bij door een **MobileServiceUser** -veld toe te voegen en een methode voor **authenticatie** , die wordt vereist door de **IAuthenticate** -interface, als volgt:

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

    Als u een andere ID-provider dan Facebook gebruikt, kiest u een andere waarde voor [MobileServiceAuthenticationProvider][7].

6. Werk het bestand **AndroidManifest. XML** bij door de volgende XML toe te voegen in het element `<application>`:

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
    Vervang `{url_scheme_of_your_app}` door het URL-schema.
7. Voeg de volgende code toe aan de methode **OnCreate** van de klasse **MainActivity** vóór de aanroep van `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Deze code zorgt ervoor dat de verificator wordt geïnitialiseerd voordat de app wordt geladen.
8. Bouw de app opnieuw op, voer deze uit en meld u aan met de verificatie provider die u hebt gekozen en controleer of u toegang hebt tot gegevens als geverifieerde gebruiker.

### <a name="troubleshooting"></a>Problemen oplossen

**De toepassing is vastgelopen met `Java.Lang.NoSuchMethodError: No static method startActivity`**

In sommige gevallen worden conflicten in de ondersteunings pakketten als alleen een waarschuwing in Visual Studio weer gegeven, maar wordt de toepassing tijdens runtime vastlopen met deze uitzonde ring. In dat geval moet u ervoor zorgen dat alle ondersteunings pakketten die in uw project worden verwezen, dezelfde versie hebben. Het [Azure Mobile Apps NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) heeft `Xamarin.Android.Support.CustomTabs`-afhankelijkheid voor het Android-platform, dus als uw project gebruikmaakt van nieuwere ondersteuningspakketten, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

## <a name="add-authentication-to-the-ios-app"></a>Verificatie toevoegen aan de iOS-app
In deze sectie wordt uitgelegd hoe u de **IAuthenticate** -interface in het IOS-app-project implementeert. Sla deze sectie over als u geen iOS-apparaten ondersteunt.

1. Klik in Visual Studio of Xamarin Studio met de rechter muisknop op het **IOS** -project en stel dit in **als opstart project**.
2. Druk op F5 om het project in het fout opsporingsprogramma te starten en controleer vervolgens of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) optreedt nadat de app is gestart. Het 401-antwoord wordt gemaakt, omdat de toegang op de back-end alleen is beperkt tot gemachtigde gebruikers.
3. Open AppDelegate.cs in het iOS-project en voeg de volgende `using`-instructies toe:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Werk de **AppDelegate** -klasse voor het implementeren van de **IAuthenticate** -interface als volgt bij:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Werk de **AppDelegate** -klasse bij door een **MobileServiceUser** -veld toe te voegen en een methode voor **authenticatie** , die wordt vereist door de **IAuthenticate** -interface, als volgt:

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

    Als u een andere ID-provider dan Facebook gebruikt, kiest u een andere waarde voor [MobileServiceAuthenticationProvider].
    
6. Werk de **AppDelegate** -klasse bij door de **OpenUrl** -methode overload als volgt toe te voegen:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Voeg de volgende regel code toe aan de methode **FinishedLaunching** vóór de aanroep van `LoadApplication()`:

        App.Init(this);

    Deze code zorgt ervoor dat de verificator wordt geïnitialiseerd voordat de app wordt geladen.

8. Open info. plist en voeg een **URL-type**toe. Stel de **id** in op een naam van uw keuze, de **URL-SCHEMA'S** naar het URL-schema voor uw app en de **rol** op geen.

9. Bouw de app opnieuw op, voer deze uit en meld u aan met de verificatie provider die u hebt gekozen en controleer of u toegang hebt tot gegevens als geverifieerde gebruiker.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Verificatie toevoegen aan Windows 10 (inclusief telefoon)-app-projecten
In deze sectie wordt uitgelegd hoe u de **IAuthenticate** -interface implementeert in de projecten van Windows 10-apps. Dezelfde stappen zijn van toepassing op UWP-projecten (Universeel Windows-platform), maar met behulp van het **UWP** -project (met de genoteerde wijzigingen). Sla deze sectie over als u geen Windows-apparaten ondersteunt.

1. Klik in Visual Studio met de rechter muisknop op het project **UWP** en stel dit in **als opstart project**.
2. Druk op F5 om het project in het fout opsporingsprogramma te starten en controleer vervolgens of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) optreedt nadat de app is gestart. Het 401-antwoord treedt op omdat de toegang op de back-end alleen is beperkt tot gemachtigde gebruikers.
3. Open MainPage.xaml.cs voor het Windows-app-project en voeg de volgende `using`-instructies toe:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Vervang `<your_Portable_Class_Library_namespace>` door de naam ruimte voor uw draag bare klassen bibliotheek.
4. Werk de **Mainpage** -klasse voor het implementeren van de **IAuthenticate** -interface als volgt bij:

        public sealed partial class MainPage : IAuthenticate
5. Werk de **Mainpage** -klasse bij door een **MobileServiceUser** -veld toe te voegen en een methode voor **authenticatie** , die wordt vereist door de **IAuthenticate** -interface, als volgt:

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

    Als u een andere ID-provider dan Facebook gebruikt, kiest u een andere waarde voor [MobileServiceAuthenticationProvider][7].

1. Voeg de volgende regel code toe aan de constructor voor de klasse **Mainpage** voordat u de aanroep van `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Vervang `<your_Portable_Class_Library_namespace>` door de naam ruimte voor uw draag bare klassen bibliotheek.

3. Als u **UWP**gebruikt, voegt u de volgende **OnActivated** -methode onderdrukking toe aan de **app** -klasse:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Open Package. appxmanifest en voeg een **protocol** declaratie toe. Stel de **weergave naam** in op de naam van uw keuze en de **naam** van het URL-schema voor uw app.

4. Bouw de app opnieuw op, voer deze uit en meld u aan met de verificatie provider die u hebt gekozen en controleer of u toegang hebt tot gegevens als geverifieerde gebruiker.

## <a name="next-steps"></a>Volgende stappen
Nu u deze basis verificatie-zelf studie hebt voltooid, kunt u door gaan met een van de volgende zelf studies:

* [Pushmeldingen toevoegen aan uw app](app-service-mobile-xamarin-forms-get-started-push.md)

  Informatie over het toevoegen van ondersteuning van pushmeldingen aan uw app en het configureren van de backend voor mobiele apps voor gebruik van Azure Notification Hubs voor het verzenden van pushmeldingen.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Informatie over het toevoegen van offlineondersteuning aan uw app met een back-end voor mobiele apps. Met offline synchronisatie kunnen eind gebruikers communiceren met een mobiele app-gegevens weer geven, toevoegen of wijzigen, zelfs wanneer er geen netwerk verbinding is.

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
