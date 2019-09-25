---
title: Micro soft Identity platform Android Quick Start | Azure
description: Meer informatie over hoe Android-toepassingen een API kunnen aanroepen waarvoor toegangs tokens zijn vereist door het micro soft Identity platform-eind punt.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4dba67bcf38049939ac40437c3668ee988235
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268504"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Quickstart: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een Android-app

Deze snelstart bevat een codevoorbeeld die u laat zien hoe een Android-toepassing persoonlijke, werk- en schoolaccounts kan aanmelden, een toegangstoken kan ophalen en de Microsoft Graph API kan aanroepen.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Vereisten**
> * Android Studio 
> * Android 16 + is vereist 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Als u uw app wilt registreren,
> 1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) .
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://aka.ms/MobileAppReg) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `AndroidQuickstart`.
>      - U kunt op deze pagina andere configuraties overs Laan. 
>      - Klik op `Register` de knop.
> 1. Klik op de nieuwe app > Ga naar `Authentication`.  >  `Add Platform`  >  `Android`    
>      - Voer de pakket naam in vanuit uw Android Studio-project. 
>      - Een hand tekening-hash genereren. Raadpleeg de portal voor instructies.
> 1. Selecteer `Configure` en sla de JSON van de ***MSAL-configuratie*** op voor later. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Stap 1: Uw toepassing configureren
> Het code voorbeeld voor deze Quick Start werkt alleen als u een omleidings-URI toevoegt die compatibel is met de auth Broker. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-android/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

* [Het code voorbeeld downloaden](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Stap 3: Het project configureren

> [!div renderon="docs"]
> Als u optie 1 hierboven hebt geselecteerd, kunt u deze stappen overs Laan. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Pak het project uit en open het in Android Studio.
> 1. Open **auth_config. json**in **app** > **src** >  > RAW. > 
> 1. Bewerk **auth_config. json** en vervang deze door de JSON van de Azure Portal. Als u in plaats daarvan de wijzigingen hand matig wilt door voeren:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
>    ```
> 
> 1. Open **AndroidManifest. XML**in **app** > -**manifesten**.
> 1. Plak de volgende activiteit in het **manifest\application** -knoop punt: 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Voer de app uit. 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Deze Quick Start biedt ondersteuning voor Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Pak het project uit en open het in Android Studio.
> 1. Open **auth_config. json**in **app** > -**Res** > **RAW**.
> 1. Bewerk **auth_config. json** en vervang deze door de JSON van de Azure Portal. Als u in plaats daarvan deze wijzigingen hand matig wilt door voeren:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Open **AndroidManifest. XML**in **app** > -**manifesten**.
> 1. Voeg de volgende activiteit toe aan het knooppunt **manifest\application**. Met dit codefragment wordt een **BrowserTabActivity** geregistreerd, zodat het besturingssysteem uw toepassing kan hervatten wanneer de verificatie is voltooid:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Vervang `Enter_the_Package_Name` en`Enter_the_Signature_Hash` door de waarden die u hebt geregistreerd in het Azure Portal. 
> 1. Voer de app uit. 

## <a name="more-information"></a>Meer informatie

Lees de volgende secties voor meer informatie over deze snelstart.

### <a name="getting-msal"></a>MSAL ophalen

MSAL ([com. micro soft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door micro soft Identity platform. U kunt Gradle 3.0 + gebruiken om het te installeren door het volgende toe te voegen in **Gradle scripts** > **Build. Gradle (module: app)** onder **afhankelijkheden**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```java
import com.microsoft.identity.client.*;
```

Vervolgens initialiseert u MSAL met de volgende code:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Waar: ||
> |---------|---------|
> |`R.raw.auth_config` | Dit bestand bevat de configuraties voor uw toepassing, met inbegrip van uw app/client-ID, aanmeldings doelgroep, omleidings-URI en verschillende andere opties voor aanpassing. |

### <a name="requesting-tokens"></a>Tokens aanvragen

MSAL biedt twee methoden om tokens te verkrijgen: `acquireToken` en `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Een token interactief ophalen

In sommige situaties moeten gebruikers communiceren met het micro soft Identity-platform. In dergelijke gevallen is het mogelijk dat de eind gebruiker hun account moet selecteren, hun referenties moet invoeren of toestemming moet geven voor de machtigingen die uw app heeft aangevraagd. Bijvoorbeeld: 

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als een gebruiker het wacht woord opnieuw instelt, moeten de referenties worden ingevoerd 
* Als toestemming is ingetrokken 
* Als uw app expliciet toestemming vereist. 
* Wanneer uw toepassing voor de eerste keer toegang tot een resource vraagt
* Wanneer MFA of een ander beleid voor voorwaardelijke toegang is vereist

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Waar:||
> |---------|---------|
> | `SCOPES` | Bevat de bereiken die worden aangevraagd (dat wil zeggen `{ "user.read" }` voor Microsoft Graph of `{ "<Application ID URL>/scope" }` voor aangepaste web-API's (dat wil zeggen `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Callback uitgevoerd wanneer besturing na verificatie aan de toepassing wordt teruggegeven |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Een gebruikerstoken op de achtergrond ophalen

Voor apps mogen gebruikers zich niet elke keer aanmelden wanneer ze een token aanvragen. Als de gebruiker al is aangemeld, kunnen apps tokens op de achtergrond aanvragen.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Waar:||
> |---------|---------|
> | `SCOPES` | Bevat de bereiken die worden aangevraagd (dat wil zeggen `{ "user.read" }` voor Microsoft Graph of `{ "<Application ID URL>/scope" }` voor aangepaste web-API's (dat wil zeggen `api://<Application ID>/access_as_user`) |
> | `getAccounts(...)` | Bevat het account dat u probeert tokens op de achtergrond te verkrijgen |
> | `getAuthSilentCallback()` | Callback uitgevoerd wanneer besturing na verificatie aan de toepassing wordt teruggegeven |

## <a name="next-steps"></a>Volgende stappen

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>De stappen voor het maken van de toepassing die wordt gebruikt in deze snelstart

Volg de Android-zelfstudie voor een volledige stapsgewijze handleiding voor het bouwen van toepassingen en nieuwe functies, met inbegrip van een volledige uitleg van deze snelstart.

> [!div class="nextstepaction"]
> [Android-zelfstudie voor Graph API-aanroepen](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL voor Android-bibliotheek wiki

Meer informatie over MSAL-bibliotheek voor Android:

> [!div class="nextstepaction"]
> [MSAL voor Android-bibliotheek wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)