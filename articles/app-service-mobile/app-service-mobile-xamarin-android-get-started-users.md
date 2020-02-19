---
title: Aan de slag met verificatie in Xamarin Android
description: Meer informatie over het gebruik van Mobile Apps voor het verifiëren van gebruikers van uw Xamarin Android-app met id-providers zoals AAD, Google, Facebook, Twitter en micro soft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458950"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Verificatie toevoegen aan uw Xamarin. Android-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overzicht
In dit onderwerp wordt beschreven hoe u gebruikers van een mobiele app verifieert vanuit uw client toepassing. In deze zelf studie voegt u verificatie toe aan het Quick start-project met behulp van een id-provider die wordt ondersteund door Azure Mobile Apps. Nadat de authenticatie is geverifieerd en geautoriseerd in de mobiele app, wordt de waarde van de gebruikers-ID weer gegeven.

Deze zelf studie is gebaseerd op de Snelstartgids van de mobiele app. U moet ook eerst de zelf studie [een Xamarin. Android-app maken]. Als u het gedownloade Quick Start Server-project niet gebruikt, moet u het uitbreidings pakket voor verificatie toevoegen aan uw project. Zie [werken met de .net back-end server SDK voor Azure Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)voor meer informatie over server extensie pakketten.

## <a name="register"></a>Uw app registreren voor verificatie en App Services configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uw app toevoegen aan de toegestane externe omleidings-Url's

Voor beveiligde verificatie moet u een nieuw URL-schema definiëren voor uw app. Hierdoor kan het verificatie systeem terugkeren naar uw app nadat het verificatie proces is voltooid. In deze zelf studie gebruiken we het URL-schema _AppName_ in. U kunt echter elk gewenst URL-schema gebruiken. Deze moet uniek zijn voor uw mobiele toepassing. De omleiding aan de server zijde inschakelen:

1. Selecteer in [Azure Portal] uw App Service.

2. Klik op de menu optie voor **verificatie/autorisatie** .

3. In de **toegestane externe omleidings-url's**voert u `url_scheme_of_your_app://easyauth.callback`in.  De **url_scheme_of_your_app** in deze teken reeks is het URL-schema voor uw mobiele toepassing.  De standaard URL-specificatie voor een protocol moet volgen (alleen letters en cijfers gebruiken en beginnen met een letter).  U moet een notitie maken van de teken reeks die u kiest, omdat u de code van uw mobiele toepassing moet aanpassen aan het URL-schema op verschillende locaties.

4. Klik op **OK**.

5. Klik op **Opslaan**.

## <a name="permissions"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Visual Studio of Xamarin Studio voert u het-client project uit op een apparaat of emulator. Controleer of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) wordt gegenereerd nadat de app is gestart. Dit gebeurt omdat de app de back-end van uw mobiele app probeert te openen als een niet-geverifieerde gebruiker. Voor de tabel *TodoItem* is nu verificatie vereist.

Vervolgens werkt u de client-app bij om resources op te vragen bij de back-end van de mobiele app met een geverifieerde gebruiker.

## <a name="add-authentication"></a>Verificatie toevoegen aan de app
De app is bijgewerkt zodat gebruikers moeten tikken op de knop **Aanmelden** en moeten worden geauthenticeerd voordat gegevens worden weer gegeven.

1. Voeg de volgende code toe aan de klasse **TodoActivity** :
   
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
   
    Hiermee maakt u een nieuwe methode voor het verifiëren van een gebruiker en een methode-handler voor een nieuwe knop **Aanmelden** . De gebruiker in de voorbeeld code hierboven wordt geverifieerd met behulp van een Facebook-aanmelding. Er wordt een dialoog venster gebruikt om de gebruikers-ID na verificatie weer te geven.
   
   > [!NOTE]
   > Als u een andere ID-provider gebruikt dan Facebook, wijzigt u de waarde die is door gegeven aan **LoginAsync** erboven in een van de volgende opties: *MicrosoftAccount*, *Twitter*, *Google*of *WindowsAzureActiveDirectory*.
   > 
   > 
2. In de methode **OnCreate** kunt u de volgende regel code verwijderen of opmerkingen maken:
   
        OnRefreshItemsSelected ();
3. Voeg in het bestand Activity_To_Do. axml de volgende *LoginUser* -knop definitie toe vóór de bestaande *AddItem* -knop:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Voeg het volgende element toe aan het bestand strings. XML resources:
   
        <string name="login_button_text">Sign in</string>
5. Open het bestand AndroidManifest. XML, voeg de volgende code toe in `<application>` XML-element:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. In Visual Studio of Xamarin Studio voert u het-client project uit op een apparaat of emulator en meldt u zich aan met de gekozen ID-provider. Wanneer u bent aangemeld, worden uw aanmeldings-ID en de lijst met TODO-items weer gegeven in de app en kunt u updates voor de gegevens maken.

## <a name="troubleshooting"></a>Problemen oplossen

**De toepassing is vastgelopen met `Java.Lang.NoSuchMethodError: No static method startActivity`**

In sommige gevallen worden conflicten in de ondersteunings pakketten als alleen een waarschuwing in Visual Studio weer gegeven, maar wordt de toepassing tijdens runtime vastlopen met deze uitzonde ring. In dat geval moet u ervoor zorgen dat alle ondersteunings pakketten die in uw project worden verwezen, dezelfde versie hebben. Het [Azure Mobile Apps NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) heeft `Xamarin.Android.Support.CustomTabs`-afhankelijkheid voor het Android-platform, dus als uw project gebruikmaakt van nieuwere ondersteuningspakketten, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

<!-- URLs. -->
[Een Xamarin. Android-app maken]: app-service-mobile-xamarin-android-get-started.md
