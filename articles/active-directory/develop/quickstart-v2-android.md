---
title: Micro soft Identity platform Android Quick Start | Azure
description: Meer informatie over hoe Android-toepassingen een API kunnen aanroepen waarvoor toegangs tokens zijn vereist door het micro soft Identity platform-eind punt.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fee5d20bd831b9278f041753a9d40820b3e295e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902931"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snelstart: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een Android-app

In deze Snelstartgids wordt gebruikgemaakt van een code voorbeeld om te laten zien hoe een Android-toepassing persoonlijke, werk-of school accounts kan aanmelden met het micro soft-identiteits platform, en vervolgens een toegangs token op te halen en de Microsoft Graph-API aan te roepen.

Toepassingen moeten worden vertegenwoordigd door een app-object in Azure Active Directory, zodat het micro soft Identity-platform tokens kan delen met uw toepassing.

> [!div renderon="docs"]
> Het code voorbeeld wordt geleverd met een standaard `redirect_uri` vooraf geconfigureerd in het `AndroidManifest.xml` bestand, zodat u niet eerst uw eigen App-object hoeft te registreren. Een `redirect_uri` is gedeeltelijk gebaseerd op de handtekening sleutel van uw app. Het voorbeeld project is vooraf geconfigureerd met een handtekening sleutel, zodat de beschik bare `redirect_uri` zal werken. Zie voor meer informatie over het registreren van een app-object en het integreren ervan met uw toepassing, de [gebruikers voor aanmelden en bel de Microsoft Graph van een Android-app](tutorial-v2-android.md) zelf studie.

![Scherm afbeelding van de voor beeld-app](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Vereisten**
> * Android Studio 
> * Android 16 +

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal 
>  Het code voorbeeld voor deze Quick Start werkt alleen als u een omleidings-URI toevoegt die compatibel is met de auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-android/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken
>
> ### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden 
> * [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ### <a name="step-3-configure-your-project"></a>Stap 3: Uw project configureren
> 1. Pak het project uit en open het in Android Studio.
> 2. Open in **app** > **src** - > **hoofd** > **Res** > **RAW**- **auth_config_multiple_account. json** en vervang deze door de volgende code:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "MULTIPLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 3. Open in **app** > **src** - > **hoofd** > **Res** > **RAW**- **auth_config_single_account. json** en vervang deze door de volgende code:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "SINGLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 4. Open **AndroidManifest. XML**in **app** > **src** > **Main**.
> 5. Vervang het **<activity android:name="com.microsoft.identity.client.BrowserTabActivity">** knoop punt in het knoop punt **manifest\application** door het volgende:  
> ```xml
> &lt;!--Intent filter to catch Microsoft's callback after Sign In--&gt;
> &lt;activity android:name=&quot;com.microsoft.identity.client.BrowserTabActivity&quot;&gt;
>     &lt;intent-filter&gt;
>     &lt;action android:name=&quot;android.intent.action.VIEW&quot; /&gt;
>     &lt;category android:name=&quot;android.intent.category.DEFAULT&quot; /&gt;
>     &lt;category android:name=&quot;android.intent.category.BROWSABLE&quot; /&gt;
>         &lt;!--
>             Add in your scheme/host from registered redirect URI 
>             note that the leading &quot;/&quot; is required for android:path
>         --&gt;
>         &lt;data android:scheme=&quot;msauth&quot;
>             android:host=&quot;Enter_the_Package_Name&quot;
>             android:path=&quot;Enter_the_Signature_Hash&quot;
>             android:scheme = &quot;msauth&quot; /&gt;
>     &lt;/intent-filter&gt;
> &lt;/activity&gt;
> ```
> 6. Voer de app uit.
> De voor beeld-app wordt gestart op het scherm met **één account modus** . Een standaard bereik, **User. Read**, wordt standaard gegeven, dat wordt gebruikt bij het lezen van uw eigen profiel gegevens tijdens de aanroep van de Microsoft Graph-API. De URL voor de API-aanroep van Microsoft Graph is standaard opgenomen. U kunt deze desgewenst wijzigen.
>
> ![MSAL-voor beeld-app met één account of meerdere accounts](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Gebruik het app-menu om één of meerdere account modi te wijzigen.
>
> Meld u in de modus voor één account aan met een werk-of thuis account:
>
> 1. Selecteer **grafiek gegevens interactief ophalen** om de gebruiker om referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder in het scherm.
> 2. Zodra u bent aangemeld, selecteert u **grafiek gegevens ophalen** op de achtergrond om de Microsoft Graph-API aan te roepen zonder dat de gebruiker om referenties wordt gevraagd. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder in het scherm.
>
> In de modus voor meerdere accounts kunt u dezelfde stappen herhalen.  Daarnaast kunt u het aangemelde account verwijderen, waardoor ook de tokens in de cache voor dat account worden verwijderd.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Deze Quick Start ondersteunt Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Stap 1: de voor beeld-app ophalen
>
> [Down load de code](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Stap 2: de voor beeld-app uitvoeren
>
> Selecteer uw emulator of fysiek apparaat in de vervolg keuzelijst **beschik bare apparaten** van Android Studio en voer de app uit.
>
> De voor beeld-app wordt gestart op het scherm met **één account modus** . Een standaard bereik, **User. Read**, wordt standaard gegeven, dat wordt gebruikt bij het lezen van uw eigen profiel gegevens tijdens de aanroep van de Microsoft Graph-API. De URL voor de API-aanroep van Microsoft Graph is standaard opgenomen. U kunt deze desgewenst wijzigen.
>
> ![MSAL-voor beeld-app met één account of meerdere accounts](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Gebruik het app-menu om één of meerdere account modi te wijzigen.
>
> Meld u in de modus voor één account aan met een werk-of thuis account:
>
> 1. Selecteer **grafiek gegevens interactief ophalen** om de gebruiker om referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder in het scherm.
> 2. Zodra u bent aangemeld, selecteert u **grafiek gegevens ophalen** op de achtergrond om de Microsoft Graph-API aan te roepen zonder dat de gebruiker om referenties wordt gevraagd. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder in het scherm.
>
> In de modus voor meerdere accounts kunt u dezelfde stappen herhalen.  Daarnaast kunt u het aangemelde account verwijderen, waardoor ook de tokens in de cache voor dat account worden verwijderd.

## <a name="how-the-sample-works"></a>Hoe het voor beeld werkt

De code is ingedeeld in fragmenten die laten zien hoe u een MSAL-app met één en meerdere accounts schrijft. De code bestanden zijn als volgt ingedeeld:

| Bestand  | Laat zien  |
|---------|---------|
| MainActivity | Beheert de gebruikers interface |
| MSGraphRequestWrapper  | Roept de Microsoft Graph-API aan met behulp van het token dat is verschaft door MSAL |
| MultipleAccountModeFragment  | Initialiseert een toepassing met meerdere accounts, laadt een gebruikers account en haalt een token op om de Microsoft Graph-API aan te roepen |
| SingleAccountModeFragment | Initialiseert een toepassing met één account, laadt een gebruikers account en haalt een token op om de Microsoft Graph-API aan te roepen |
| res/auth_config_multiple_account. json  | Het configuratie bestand voor meerdere accounts |
| res/auth_config_single_account. json  | Het configuratie bestand voor één account |
| Gradle scripts/build. gradatie (module: app) | De MSAL-bibliotheek afhankelijkheden worden hier toegevoegd |

Deze bestanden worden nu uitvoeriger weer gegeven en u kunt de MSAL code in elk nummer aanroepen.

### <a name="adding-msal-to-the-app"></a>MSAL toevoegen aan de app

MSAL ([com. micro soft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door micro soft Identity platform. Gradle 3.0 + installeert de bibliotheek wanneer u het volgende toevoegt aan **Gradle-Scripts** > **Build. Gradle (module: app)** onder **afhankelijkheden**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

U kunt dit zien in het voorbeeld project in build. gradle (module: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.+'
    ...
}
```

Hiermee wordt Gradle om MSAL van Maven Central te downloaden en te bouwen.

### <a name="msal-imports"></a>MSAL-import bewerkingen

De Imports die relevant zijn voor de MSAL-bibliotheek, worden `com.microsoft.identity.client.*`.  U ziet bijvoorbeeld `import com.microsoft.identity.client.PublicClientApplication;`. Dit is de naam ruimte voor de klasse `PublicClientApplication` die uw open bare client toepassing vertegenwoordigt.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. java

Dit bestand laat zien hoe u een MSAL-app met één account maakt en een Microsoft Graph-API aanroept.

Apps met één account worden alleen gebruikt door één gebruiker.  Zo hebt u mogelijk slechts één account waarmee u zich aanmeldt bij uw toewijzings-app met.

#### <a name="single-account-msal-initialization"></a>MSAL-initialisatie met één account

In `auth_config_single_account.json`wordt in `onCreateView()`één account `PublicClientApplication` gemaakt met behulp van de configuratie-informatie die is opgeslagen in het `auth_config_single_account.json`-bestand.  Zo initialiseert u de MSAL-bibliotheek voor gebruik in een MSAL-app met één account:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Een gebruiker aanmelden

In `SingleAccountModeFragment.java`is de code voor het aanmelden van een gebruiker in `initializeUI()`, in de `signInButton` op handler.

Roep `signIn()` aan voordat u probeert tokens te verkrijgen. `signIn()` gedraagt zich alsof `acquireToken()` wordt aangeroepen, wat resulteert in een interactieve prompt waarmee de gebruiker zich kan aanmelden.

Een gebruiker die zich aanmeldt, is een asynchrone bewerking. Er wordt een call back door gegeven die de Microsoft Graph-API aanroept en de gebruikers interface bijwerkt zodra de gebruiker zich aanmeldt:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Een gebruiker afmelden

In `SingleAccountModeFragment.java`bevindt de code voor het afmelden van een gebruiker zich in `initializeUI()`in de `signOutButton` op handler.  Het ondertekenen van een gebruiker is een asynchrone bewerking. Als u het ondertekenen van de gebruiker uitschakelt, wordt ook de token cache voor dat account gewist. Er wordt een call back gemaakt om de gebruikers interface bij te werken nadat het gebruikers account is afgemeld:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Een token interactief of op de achtergrond ophalen

Als u het minste aantal prompts voor de gebruiker wilt presen teren, ontvangt u normaal gesp roken een token op de achtergrond. Als er een fout optreedt, probeert u vervolgens interactief een token te verkrijgen. De eerste keer dat de app `signIn()`aanroept, fungeert het effectief als een aanroep van `acquireToken()`, waarmee de gebruiker wordt gevraagd om referenties.

Sommige situaties waarbij de gebruiker kan worden gevraagd om hun account te selecteren, hun referenties in te voeren of toestemming te geven voor de machtigingen die uw app heeft aangevraagd:

* De eerste keer dat de gebruiker zich aanmeldt bij de toepassing
* Als een gebruiker het wacht woord opnieuw instelt, moeten ze hun referenties invoeren
* Als toestemming is ingetrokken
* Als uw app expliciet toestemming vereist
* Wanneer uw toepassing voor de eerste keer toegang tot een resource vraagt
* Wanneer MFA of een ander beleid voor voorwaardelijke toegang is vereist

De code voor het interactief ophalen van een token, dat wil zeggen met de gebruikers interface die de gebruiker gaat betrekken, bevindt zich in `SingleAccountModeFragment.java`in `initializeUI()`in de `callGraphApiInteractiveButton` op handler:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Als de gebruiker zich al heeft aangemeld, kunt u met `acquireTokenSilentAsync()` toestaan dat apps tokens op de achtergrond aanvragen, zoals wordt weer gegeven in `initializeUI()`, in de `callGraphApiSilentButton` op handler:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Een account laden

De code voor het laden van een account bevindt zich in `SingleAccountModeFragment.java` in `loadAccount()`.  Het laden van het gebruikers account is een asynchrone bewerking, dus retour aanroepen om te kunnen afhandelen wanneer het account wordt geladen, gewijzigd of een fout optreedt, worden door gegeven aan MSAL.  De volgende code verwerkt ook `onAccountChanged()`, die plaatsvindt wanneer een account wordt verwijderd, de gebruiker wordt gewijzigd in een ander account, enzovoort.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Microsoft Graph aanroepen

Wanneer een gebruiker is aangemeld, wordt de aanroep van Microsoft Graph gedaan via een HTTP-aanvraag door `callGraphAPI()` die in `SingleAccountModeFragment.java`is gedefinieerd. Deze functie is een wrapper waarmee het voor beeld wordt vereenvoudigd door bepaalde taken uit te voeren, zoals het ophalen van het toegangs token van de `authenticationResult`, het aanroepen van de aanroep naar de MSGraphRequestWrapper en het weer geven van de resultaten van de aanroep.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account. json

Dit is het configuratie bestand voor een MSAL-app die gebruikmaakt van één account.

Zie [het configuratie bestand van de Android-MSAL begrijpen](msal-configuration.md) voor een uitleg van deze velden.

Let op de aanwezigheid van `"account_mode" : "SINGLE"`, waarmee deze app wordt geconfigureerd voor het gebruik van één account.

`"client_id"` is vooraf geconfigureerd voor het gebruik van een app-object registratie die micro soft onderhoudt.
`"redirect_uri"`is vooraf geconfigureerd voor het gebruik van de handtekening sleutel die is opgenomen in het code voorbeeld.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. java

Dit bestand laat zien hoe u een MSAL-app met meerdere accounts maakt en een Microsoft Graph-API aanroept.

Een voor beeld van een app met meerdere accounts is een e-mail-app waarmee u kunt werken met meerdere gebruikers accounts, zoals een werk account en een persoonlijk account.

#### <a name="multiple-account-msal-initialization"></a>Initialisatie van meerdere account MSAL

In het `MultipleAccountModeFragment.java` bestand, in `onCreateView()`, wordt een app-object (`IMultipleAccountPublicClientApplication`) voor meerdere accounts gemaakt met behulp van de configuratie-informatie die is opgeslagen in de `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Het gemaakte `MultipleAccountPublicClientApplication`-object wordt opgeslagen in een klassen lidvariabele, zodat het kan worden gebruikt om te communiceren met de MSAL-bibliotheek om tokens te verkrijgen en te laden en het gebruikers account te verwijderen.

#### <a name="load-an-account"></a>Een account laden

Meerdere account-Apps bellen `getAccounts()` meestal om het account te selecteren dat moet worden gebruikt voor MSAL-bewerkingen. De code voor het laden van een account bevindt zich in het `MultipleAccountModeFragment.java`-bestand in `loadAccounts()`.  Het laden van het gebruikers account is een asynchrone bewerking. Een call back verwerkt dus de situaties waarin het account wordt geladen, gewijzigd of een fout optreedt.

```java
/**
     * Load currently signed-in accounts, if there's any.
    */
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Een token interactief of op de achtergrond ophalen

Sommige situaties waarbij de gebruiker kan worden gevraagd om hun account te selecteren, hun referenties in te voeren of toestemming te geven voor de machtigingen die uw app heeft aangevraagd:

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als een gebruiker het wacht woord opnieuw instelt, moeten ze hun referenties invoeren 
* Als toestemming is ingetrokken 
* Als uw app expliciet toestemming vereist 
* Wanneer uw toepassing voor de eerste keer toegang tot een resource vraagt
* Wanneer MFA of een ander beleid voor voorwaardelijke toegang is vereist

Meerdere account-apps moeten doorgaans tokens interactief verkrijgen, dat wil zeggen met de gebruikers interface van de gebruiker, met een aanroep van `acquireToken()`.  De code voor het interactief ophalen van een token bevindt zich in het `MultipleAccountModeFragment.java` bestand in `initializeUI()`in de `callGraphApiInteractiveButton` op handler:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Voor apps moet de gebruiker zich niet elke keer aanmelden wanneer ze een token aanvragen. Als de gebruiker al is aangemeld, kunnen apps met `acquireTokenSilentAsync()` tokens aanvragen zonder dat de gebruiker om toestemming wordt gevraagd, zoals wordt weer gegeven in het `MultipleAccountModeFragment.java`-bestand, in`initializeUI()` in de `callGraphApiSilentButton` op handler:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Een account verwijderen

De code voor het verwijderen van een account en alle tokens in de cache voor het account, bevindt zich in het `MultipleAccountModeFragment.java` bestand in `initializeUI()` in de handler voor de knop account verwijderen. Voordat u een account kunt verwijderen, hebt u een account object nodig, dat u ophaalt van MSAL-functies, zoals `getAccounts()` en `acquireToken()`. Omdat het verwijderen van een account een asynchrone bewerking is, wordt de `onRemoved` terugbellen opgegeven om de gebruikers interface bij te werken.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. json

Dit is het configuratie bestand voor een MSAL-app die gebruikmaakt van meerdere accounts.

Zie [het configuratie bestand van de Android-MSAL begrijpen](msal-configuration.md) voor een uitleg van de verschillende velden.

In tegens telling tot het configuratie bestand [auth_config_single_account. json](#auth_config_single_accountjson) heeft dit configuratie bestand `"account_mode" : "MULTIPLE"` in plaats van `"account_mode" : "SINGLE"` omdat dit een app voor meerdere accounts is.

`"client_id"` is vooraf geconfigureerd voor het gebruik van een app-object registratie die micro soft onderhoudt.
`"redirect_uri"`is vooraf geconfigureerd voor het gebruik van de handtekening sleutel die is opgenomen in het code voorbeeld.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>De stappen voor het maken van de toepassing die wordt gebruikt in deze snelstart

Probeer de [aanmeldings gebruikers uit en bel de Microsoft Graph vanuit een Android-app](tutorial-v2-android.md) -zelf studie voor een stapsgewijze hand leiding voor het bouwen van een Android-app waarmee een toegangs token wordt opgehaald en wordt gebruikt om de Microsoft Graph-API aan te roepen.

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
