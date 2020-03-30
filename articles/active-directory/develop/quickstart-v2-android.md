---
title: Microsoft identity platform Android quickstart | Azure
description: Lees hoe Android-toepassingen een API kunnen aanroepen waarvoor toegangstokens per eindpunt van het Microsoft-identiteitsplatform vereist zijn.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: a184b035e3296f82ecdacf74a99ea7148d99bd49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271118"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snelstart: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een Android-app

Deze quickstart maakt gebruik van een codevoorbeeld om aan te tonen hoe een Android-toepassing persoonlijke, werk- of schoolaccounts kan aanmelden met behulp van het Microsoft-identiteitsplatform, en vervolgens een toegangstoken te krijgen en de Microsoft Graph-API aan te roepen. (Zie [hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.)

Toepassingen moeten worden vertegenwoordigd door een app-object in Azure Active Directory, zodat het Microsoft-identiteitsplatform tokens aan uw toepassing kan verstrekken.

> [!div renderon="docs"]
> Voor het gemak wordt het codevoorbeeld geleverd met een standaard `redirect_uri` vooraf geconfigureerd in het `AndroidManifest.xml` bestand, zodat u niet eerst uw eigen app-object hoeft te registreren. A `redirect_uri` is deels gebaseerd op de ondertekeningssleutel van uw app. Het voorbeeldproject is vooraf geconfigureerd met een `redirect_uri` ondertekeningssleutel, zodat de meegeleverde werkt. Zie de gebruikers van de Aanmelding en bel de Microsoft Graph vanuit een zelfstudie [van een Android-app voor](tutorial-v2-android.md) meer informatie over het registreren van een app-object en het integreren ervan met uw toepassing.


> [!NOTE]
> **Vereisten**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal 
>  Voor het codevoorbeeld voor deze quickstart moet u een omleidingsURI toevoegen die compatibel is met de Auth-broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-android/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken
>
> ### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden 
> [!div class="sxs-lookup" renderon="portal"]
> Voer het project uit met Android Studio.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om uit te voeren
> We hebben uw project geconfigureerd met waarden van de eigenschappen van uw app en het is klaar om te worden uitgevoerd. 
> De voorbeeld-app start op het scherm **Modus één account.** Een standaardscope, **user.read,** wordt standaard geleverd, die wordt gebruikt bij het lezen van uw eigen profielgegevens tijdens de Microsoft Graph API-aanroep. De URL voor de Microsoft Graph API-aanroep wordt standaard weergegeven. U beide veranderen als u dat wenst.
>
> ![MSAL-voorbeeld-app met één en meerdere accountgebruik](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Gebruik het app-menu om te wisselen tussen één en meerdere accountmodi.
>
> Log in de modus Één account in met een werk- of thuisaccount:
>
> 1. Selecteer **Grafiekgegevens interactief opvragen** om de gebruiker om zijn referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder aan het scherm.
> 2. Nadat u bent aangemeld, selecteert **u Grafiekgegevens in stilte opvragen** om een oproep te doen naar de Microsoft Graph-API zonder de gebruiker opnieuw om referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder aan het scherm.
>
> In de modus Meerdere accounts u dezelfde stappen herhalen.  Bovendien u het aangemelde account verwijderen, dat ook de gecachede tokens voor dat account verwijdert.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Stap 1: Download de voorbeeld-app
>
> [Download de code](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Stap 2: De voorbeeld-app uitvoeren
>
> Selecteer uw emulator of fysiek apparaat in de **vervolgkeuzelijst van** android studio en voer de app uit.
>
> De voorbeeld-app start op het scherm **Modus één account.** Een standaardscope, **user.read,** wordt standaard geleverd, die wordt gebruikt bij het lezen van uw eigen profielgegevens tijdens de Microsoft Graph API-aanroep. De URL voor de Microsoft Graph API-aanroep wordt standaard weergegeven. U beide veranderen als u dat wenst.
>
> ![MSAL-voorbeeld-app met één en meerdere accountgebruik](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Gebruik het app-menu om te wisselen tussen één en meerdere accountmodi.
>
> Log in de modus Één account in met een werk- of thuisaccount:
>
> 1. Selecteer **Grafiekgegevens interactief opvragen** om de gebruiker om zijn referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder aan het scherm.
> 2. Nadat u bent aangemeld, selecteert **u Grafiekgegevens in stilte opvragen** om een oproep te doen naar de Microsoft Graph-API zonder de gebruiker opnieuw om referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph-API onder aan het scherm.
>
> In de modus Meerdere accounts u dezelfde stappen herhalen.  Bovendien u het aangemelde account verwijderen, dat ook de gecachede tokens voor dat account verwijdert.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Schermafbeelding van de voorbeeld-app](media/quickstart-v2-android/android-intro.svg)


De code is ingedeeld in fragmenten die laten zien hoe je een enkele en meerdere accounts MSAL app te schrijven. De codebestanden zijn als volgt georganiseerd:

| File  | Demonstreert  |
|---------|---------|
| Hoofdactiviteit | Beheert de gebruikersinterface |
| MSGraphRequestWrapper  | Roept de Microsoft Graph API aan met behulp van het token van MSAL |
| MultipleAccountModeFragment  | Initialiseert een toepassing met meerdere accounts, laadt een gebruikersaccount en krijgt een token om de Microsoft Graph API aan te roepen |
| SingleAccountModeFragment SingleAccountModeFragment | Initialiseert een toepassing met één account, laadt een gebruikersaccount en krijgt een token om de Microsoft Graph API aan te roepen |
| res/auth_config_multiple_account.json  | Het configuratiebestand met meerdere account |
| res/auth_config_single_account.json  | Het configuratiebestand met één account |
| Gradle Scripts/build.grade (Module:app) | De MSAL-bibliotheekafhankelijkheden worden hier toegevoegd |

We zullen nu kijken naar deze bestanden in meer detail en roepen de MSAL-specifieke code in elk.

### <a name="adding-msal-to-the-app"></a>MSAL toevoegen aan de app

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) is de bibliotheek die wordt gebruikt om gebruikers aan te melden en tokens aan te vragen die worden gebruikt om toegang te krijgen tot een API die is beschermd door het identiteitsplatform van Microsoft. Gradle 3.0+ installeert de bibliotheek wanneer u het volgende toevoegt aan **Gradle Scripts** > **build.gradle (Module: app)** onder **Afhankelijkheden:**

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

U dit zien in het voorbeeldproject in build.gradle (Module: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

Dit instrueert Gradle om MSAL te downloaden en te bouwen van maven central.

### <a name="msal-imports"></a>MSAL-invoer

De invoer die relevant is voor `com.microsoft.identity.client.*`de MSAL-bibliotheek is .  U ziet bijvoorbeeld welke `import com.microsoft.identity.client.PublicClientApplication;` de naamruimte voor `PublicClientApplication` de klasse is, die uw openbare clienttoepassing vertegenwoordigt.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Dit bestand laat zien hoe u een MSAL-app voor één account maakt en een Microsoft Graph-API aanroept.

Apps met één account worden slechts door één gebruiker gebruikt.  U bijvoorbeeld slechts één account hebben waarmee u zich aanmeldt bij uw toewijzingsapp.

#### <a name="single-account-msal-initialization"></a>MSAL-initialisatie met één account

In, `auth_config_single_account.json` `onCreateView()`in , `PublicClientApplication` wordt één account gemaakt met `auth_config_single_account.json` behulp van de config-informatie die in het bestand is opgeslagen.  Zo initialiseer je de MSAL-bibliotheek voor gebruik in een MSAL-app met één account:

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

#### <a name="sign-in-a-user"></a>Aanmelden voor een gebruiker

In `SingleAccountModeFragment.java`, de code aan te `initializeUI()`melden `signInButton` in een gebruiker is in , in de klik handler.

Bel `signIn()` voordat je tokens probeert te verkrijgen. `signIn()`gedraagt zich `acquireToken()` alsof wordt aangeroepen, wat resulteert in een interactieve prompt voor de gebruiker om zich aan te melden.

Aanmelden bij een gebruiker is een asynchrone bewerking. Er wordt een callback aangenomen die de Microsoft Graph API aanroept en de gebruikersinterface bijwerkt zodra de gebruiker zich aanmeldt:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Een gebruiker afmelden

In `SingleAccountModeFragment.java`, de code af te `initializeUI()`melden `signOutButton` een gebruiker is in , in de klik handler.  Het uittekenen van een gebruiker is een asynchrone bewerking. Als u de gebruiker uitschrijft, wordt ook de tokencache voor dat account gewist. Er wordt een callback gemaakt om de gebruikersinterface bij te werken zodra het gebruikersaccount is afgemeld:

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

#### <a name="get-a-token-interactively-or-silently"></a>Ontvang een token interactief of geruisloos

Als u het minste aantal prompts aan de gebruiker wilt presenteren, krijgt u meestal een token in stilte. Als er een fout is, probeer dan interactief te worden token. De eerste keer `signIn()`dat de app aanroept, `acquireToken()`fungeert het effectief als een oproep aan , die de gebruiker om referenties zal vragen.

Sommige situaties waarin de gebruiker wordt gevraagd om zijn account te selecteren, hun referenties in te voeren of toestemming te geven voor de machtigingen die uw app heeft aangevraagd, zijn:

* De eerste keer dat de gebruiker zich aanmeldt bij de toepassing
* Als een gebruiker zijn wachtwoord opnieuw instelt, moet hij zijn of haar referenties invoeren
* Als de toestemming wordt ingetrokken
* Als uw app expliciet toestemming vereist
* Wanneer uw toepassing voor het eerst toegang tot een bron aanvraagt
* Wanneer MFA of ander beleid voor voorwaardelijke toegang vereist is

De code om een token interactief te krijgen, dat wil zeggen `SingleAccountModeFragment.java`met `initializeUI()`UI `callGraphApiInteractiveButton` waarbij de gebruiker betrokken is, bevindt zich in , in , in de klikhandler:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Als de gebruiker zich `acquireTokenSilentAsync()` al heeft aangemeld, kunnen apps `initializeUI()`in `callGraphApiSilentButton` stilte tokens aanvragen, zoals weergegeven in , in de klikhandler:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Een account laden

De code om een `SingleAccountModeFragment.java` account `loadAccount()`te laden is in .  Het laden van het account van de gebruiker is een asynchrone bewerking, dus callbacks die moeten worden verwerkt wanneer het account wordt geladen, gewijzigd of er een fout optreedt, wordt doorgegeven aan MSAL.  De volgende code `onAccountChanged()`verwerkt ook , die optreedt wanneer een account wordt verwijderd, de gebruiker verandert in een ander account, enzovoort.

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

#### <a name="call-microsoft-graph"></a>Microsoft Graph bellen

Wanneer een gebruiker is aangemeld, wordt de aanroep naar `callGraphAPI()` Microsoft Graph `SingleAccountModeFragment.java`gedaan via een HTTP-verzoek waarmee wordt gedefinieerd in . Deze functie is een wrapper die het voorbeeld vereenvoudigt door het `authenticationResult` uitvoeren van een aantal taken, zoals het verkrijgen van de toegang token van de en verpakking van de oproep naar de MSGraphRequestWrapper, en het weergeven van de resultaten van de oproep.

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

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Dit is het configuratiebestand voor een MSAL-app die één account gebruikt.

Zie [Het Configuratiebestand van Android MSAL begrijpen](msal-configuration.md) voor een uitleg van deze velden.

Let op `"account_mode" : "SINGLE"`de aanwezigheid van , die deze app configureert om een enkel account te gebruiken.

`"client_id"`is vooraf geconfigureerd voor het gebruik van een app-objectregistratie die Microsoft bijhoudt.
`"redirect_uri"`is vooraf geconfigureerd om de ondertekeningssleutel te gebruiken die bij het codevoorbeeld is geleverd.

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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Dit bestand laat zien hoe u een MSAL-app met meerdere accounten maakt en een Microsoft Graph-API aanroept.

Een voorbeeld van een app met meerdere accounts is een e-mail-app waarmee u werken met meerdere gebruikersaccounts, zoals een werkaccount en een persoonlijk account.

#### <a name="multiple-account-msal-initialization"></a>MsAL-initialisatie met meerdere accounten

In `MultipleAccountModeFragment.java` het bestand `onCreateView()`wordt een object`IMultipleAccountPublicClientApplication`voor meerdere account-apps ( ) `auth_config_multiple_account.json file`gemaakt met behulp van de config-informatie die is opgeslagen in :

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
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

Het `MultipleAccountPublicClientApplication` gemaakte object wordt opgeslagen in een klasselidvariabele, zodat het kan worden gebruikt om te communiceren met de MSAL-bibliotheek om tokens te verkrijgen en het gebruikersaccount te laden en te verwijderen.

#### <a name="load-an-account"></a>Een account laden

Meerdere account-apps `getAccounts()` bellen meestal om het account te selecteren dat moet worden gebruikt voor MSAL-bewerkingen. De code voor het laden `MultipleAccountModeFragment.java` van `loadAccounts()`een account staat in het bestand in .  Het laden van het account van de gebruiker is een asynchrone bewerking. Een callback verwerkt dus de situaties waarin het account wordt geladen, verandert of er een fout optreedt.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
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

#### <a name="get-a-token-interactively-or-silently"></a>Ontvang een token interactief of geruisloos

Sommige situaties waarin de gebruiker wordt gevraagd om zijn account te selecteren, hun referenties in te voeren of toestemming te geven voor de machtigingen die uw app heeft aangevraagd, zijn:

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als een gebruiker zijn wachtwoord opnieuw instelt, moet hij zijn of haar referenties invoeren 
* Als de toestemming wordt ingetrokken 
* Als uw app expliciet toestemming vereist 
* Wanneer uw toepassing voor het eerst toegang tot een bron aanvraagt
* Wanneer MFA of ander beleid voor voorwaardelijke toegang vereist is

Meerdere account-apps moeten doorgaans tokens interactief aanschaffen, dat wil zeggen met `acquireToken()`ui waarbij de gebruiker betrokken is, met een oproep naar .  De code om een token interactief `MultipleAccountModeFragment.java` te `initializeUI()`krijgen `callGraphApiInteractiveButton` bevindt zich in het bestand in , in de klikhandler:

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
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Apps mogen niet vereisen dat de gebruiker zich elke keer aanmeldt wanneer hij of zij een token aanvraagt. Als de gebruiker zich `acquireTokenSilentAsync()` al heeft aangemeld, kunnen apps tokens aanvragen `MultipleAccountModeFragment.java` zonder`initializeUI()` de `callGraphApiSilentButton` gebruiker te vragen, zoals in het bestand wordt weergegeven, in de klikhandler:

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

De code om een account te verwijderen en eventuele tokens `MultipleAccountModeFragment.java` in `initializeUI()` de cache voor het account, staat in het bestand in de handler voor de knop Account verwijderen. Voordat u een account verwijderen, hebt u een accountobject `getAccounts()` nodig, dat u verkrijgt van MSAL-methoden zoals en `acquireToken()`. Omdat het verwijderen van een account `onRemoved` een asynchrone bewerking is, wordt de callback geleverd om de gebruikersinterface bij te werken.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
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

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Dit is het configuratiebestand voor een MSAL-app die meerdere accounts gebruikt.

Zie [Het Android MSAL-configuratiebestand begrijpen](msal-configuration.md) voor een uitleg van de verschillende velden.

In tegenstelling tot het [auth_config_single_account.json-configuratiebestand](#auth_config_single_accountjson) `"account_mode" : "MULTIPLE"` `"account_mode" : "SINGLE"` heeft dit config-bestand in plaats van omdat dit een app met meerdere accounten is.

`"client_id"`is vooraf geconfigureerd voor het gebruik van een app-objectregistratie die Microsoft bijhoudt.
`"redirect_uri"`is vooraf geconfigureerd om de ondertekeningssleutel te gebruiken die bij het codevoorbeeld is geleverd.

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

Probeer de [gebruikers van sign-in en bel de Microsoft Graph vanuit een zelfstudie van een Android-app](tutorial-v2-android.md) voor een stapsgewijze handleiding voor het bouwen van een Android-app die een toegangstoken krijgt en deze gebruikt om de Microsoft Graph API te noemen.

> [!div class="nextstepaction"]
> [Android-zelfstudie voor Graph API-aanroepen](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL voor Android-bibliotheek wiki

Meer informatie over MSAL-bibliotheek voor Android:

> [!div class="nextstepaction"]
> [MSAL voor Android-bibliotheek wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
