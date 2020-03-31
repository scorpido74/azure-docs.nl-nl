---
title: Aan de slag met verificatie in Xamarin Android
description: Meer informatie over het gebruik van mobiele apps om gebruikers van uw Android-app van Xamarin te verifiëren bij identiteitsproviders zoals AAD, Google, Facebook, Twitter en Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458950"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Verificatie toevoegen aan uw Xamarin.Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overzicht
In dit onderwerp ziet u hoe u gebruikers van een mobiele app verifiëren vanuit uw clienttoepassing. In deze zelfstudie voegt u verificatie toe aan het quickstart-project met behulp van een identiteitsprovider die wordt ondersteund door Azure Mobile Apps. Nadat de waarde van de gebruikersnaam is geverifieerd en geautoriseerd in de mobiele app, wordt de waarde van de gebruikersnaam weergegeven.

Deze zelfstudie is gebaseerd op de QuickStart van de Mobiele App. Je moet ook eerst de tutorial voltooien [Maak een Xamarin.Android-app]. Als u het gedownloade project voor snelle startserver niet gebruikt, moet u het verificatieuitbreidingspakket aan uw project toevoegen. Zie [Werken met de .NET backendserver SDK voor Azure Mobile Apps voor](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)meer informatie over serverextensiepakketten.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>Uw app registreren voor verificatie en App Services configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Uw app toevoegen aan de toegestane URL's voor omleiding

Veilige verificatie vereist dat u een nieuw URL-schema voor uw app definieert. Hierdoor kan het verificatiesysteem worden omgeleid naar uw app zodra het verificatieproces is voltooid. In deze zelfstudie gebruiken we de _URL-appname_ overal. U echter elk URL-schema gebruiken dat u kiest. Het moet uniek zijn voor uw mobiele applicatie. Ga als u de omleiding aan de serverzijde inschakelen:

1. Selecteer in de [Azure-portal] uw App-service.

2. Klik op de optie **Verificatie / Autorisatie.**

3. Voer in de toegestane externe `url_scheme_of_your_app://easyauth.callback` **omleidings-URL's**.  De **url_scheme_of_your_app** in deze tekenreeks is het URL-schema voor uw mobiele toepassing.  Het moet de normale URL-specificatie voor een protocol volgen (gebruik alleen letters en cijfers en begin met een letter).  U moet een notitie maken van de tekenreeks die u kiest, omdat u uw mobiele toepassingscode op verschillende plaatsen moet aanpassen met het URL-schema.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Voer in Visual Studio of Xamarin Studio het clientproject uit op een apparaat of emulator. Controleer of een niet-behandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart. Dit gebeurt omdat de app probeert toegang te krijgen tot de back-end van uw mobiele app als niet-geverifieerde gebruiker. De *TodoItem-tabel* vereist nu verificatie.

Vervolgens werkt u de client-app bij om bronnen op te vragen bij de back-end van de mobiele app met een geverifieerde gebruiker.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Verificatie toevoegen aan de app
De app wordt bijgewerkt om gebruikers te verplichten op de **knop Aanmelden** te tikken en te verifiëren voordat gegevens worden weergegeven.

1. Voeg de volgende code toe aan de klasse **TodoActiviteit:**
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Hiermee wordt een nieuwe methode gemaakt om een gebruiker en een methodehandler voor een nieuwe **knop Aanmelden** te verifiëren. De gebruiker in de bovenstaande voorbeeldcode wordt geverifieerd met behulp van een Facebook-login. Een dialoogvenster wordt gebruikt om de gebruikersnaam weer te geven zodra deze is geverifieerd.
   
   > [!NOTE]
   > Als u een andere identiteitsprovider dan Facebook gebruikt, wijzigt u de waarde die hierboven wordt doorgegeven aan **LoginAsync** in een van de volgende opties: *MicrosoftAccount,* *Twitter,* *Google*of *WindowsAzureActiveDirectory*.
   > 
   > 
2. Verwijder of reageer de volgende coderegel in de methode **OnCreate:**
   
        OnRefreshItemsSelected ();
3. Voeg in het bestand Activity_To_Do.axml de volgende knopdefinitie *logingebruiker* toe voordat de bestaande knop *AddItem* wordt gebruikt:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Voeg het volgende element toe aan het bronnenbestand Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Open het bestand AndroidManifest.xml en `<application>` voeg de volgende code toe in XML-element:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. Voer in Visual Studio of Xamarin Studio het clientproject uit op een apparaat of emulator en meld u aan bij de door u gekozen identiteitsprovider. Wanneer u met succes bent ingelogd, geeft de app uw inlog-id en de lijst met todo-items weer en u de gegevens bijgewerkt.

## <a name="troubleshooting"></a>Problemen oplossen

**De toepassing crashte met`Java.Lang.NoSuchMethodError: No static method startActivity`**

In sommige gevallen worden conflicten in de ondersteuningspakketten weergegeven als slechts een waarschuwing in de Visual-studio, maar de toepassing crasht met deze uitzondering bij runtime. In dit geval moet u ervoor zorgen dat alle ondersteuningspakketten waarnaar in uw project wordt verwezen, dezelfde versie hebben. Het [Azure Mobile Apps NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) heeft `Xamarin.Android.Support.CustomTabs`-afhankelijkheid voor het Android-platform, dus als uw project gebruikmaakt van nieuwere ondersteuningspakketten, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

<!-- URLs. -->
[Een Xamarin.Android-app maken]: app-service-mobile-xamarin-android-get-started.md
