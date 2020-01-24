---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Azure AD B2C met de micro soft-verificatie bibliotheek voor Android (MSAL. Android
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696518"
---
# <a name="use-msal-for-android-with-b2c"></a>MSAL voor Android gebruiken met B2C

Met micro soft Authentication Library (MSAL) kunnen ontwikkel aars van toepassingen gebruikers met sociale en lokale identiteiten verifiëren met behulp van [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C is een service voor identiteits beheer. Gebruik deze functie om aan te passen en te beheren hoe klanten zich registreren, aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken.

## <a name="configure-known-authorities-and-redirect-uri"></a>Bekende instanties en omleidings-URI configureren

In MSAL voor Android worden B2C-beleid (gebruikers ritten) geconfigureerd als afzonderlijke autoriteiten.

Op basis van een B2C-toepassing met twee beleids regels:
- Aanmelden/aanmelden
    * Aangeroepen `B2C_1_SISOPolicy`
- Profiel bewerken
    * Aangeroepen `B2C_1_EditProfile`

In het configuratie bestand voor de app worden twee `authorities`gedeclareerd. Eén voor elk beleid. De eigenschap `type` van elke instantie is `B2C`.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

De `redirect_uri` moet zijn geregistreerd in de app-configuratie en ook in `AndroidManifest.xml` om omleiding te ondersteunen tijdens de [autorisatie code toekennings stroom](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication initialiseren

`IPublicClientApplication` is gebouwd door een fabrieks methode zodat de configuratie van de toepassing asynchroon kan worden geparseerd.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Interactief een token verkrijgen

Als u een token interactief wilt aanschaffen met MSAL, bouwt u een `AcquireTokenParameters`-exemplaar op en levert u het aan de methode `acquireToken`. De onderstaande token aanvraag maakt gebruik van de `default`-instantie.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Een token op de achtergrond vernieuwen

Als u een token op de achtergrond wilt verkrijgen met MSAL, bouwt u een `AcquireTokenSilentParameters`-exemplaar op en levert u dit aan de `acquireTokenSilentAsync`-methode. In tegens telling tot de `acquireToken` methode moet de `authority` worden opgegeven om een token op de achtergrond te verkrijgen.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Een beleid opgeven

Omdat beleids regels in B2C worden weer gegeven als afzonderlijke instanties, wordt het aanroepen van een ander beleid dan de standaard waarde bereikt door een `fromAuthority`-component op te geven bij het maken van `acquireToken` of `acquireTokenSilent` para meters.  Bijvoorbeeld:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Beleid voor wachtwoord wijziging verwerken

De gebruikers stroom voor het registreren of aanmelden van het lokale account bevat een**verg eten wacht woord?** gekoppeld. Als u op deze koppeling klikt, wordt de gebruikers stroom voor wacht woord opnieuw instellen niet automatisch geactiveerd.

In plaats daarvan wordt de fout code `AADB2C90118` naar uw app geretourneerd. Uw app moet deze fout code verwerken door een specifieke gebruikers stroom uit te voeren waarmee het wacht woord opnieuw wordt ingesteld.

De volgende implementatie kan worden gebruikt in uw `AuthenticationCallback`om een fout code voor het opnieuw instellen van een wacht woord te ondervangen:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>IAuthenticationResult gebruiken

Een geslaagde token verwerving resulteert in een `IAuthenticationResult`-object. Het bevat het toegangs token, de gebruikers claims en de meta gegevens.

### <a name="get-the-access-token-and-related-properties"></a>Het toegangs token en de bijbehorende eigenschappen ophalen

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Het geautoriseerde account ophalen

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken claims

Claims die in de IdToken worden geretourneerd, worden gevuld door de Security Token Service (STS), niet door MSAL. Afhankelijk van de ID-provider (IdP) die wordt gebruikt, zijn sommige claims mogelijk niet aanwezig. Sommige id bieden momenteel geen `preferred_username` claim. Omdat deze claim door MSAL wordt gebruikt voor het opslaan in de cache, wordt de waarde van de tijdelijke aanduiding `MISSING FROM THE TOKEN RESPONSE`gebruikt. Zie [overzicht van tokens in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)voor meer informatie over B2C IdToken claims.

## <a name="managing-accounts-and-policies"></a>Accounts en beleids regels beheren

B2C behandelt elk beleid als een afzonderlijke instantie. De toegangs tokens, vernieuwings tokens en ID-tokens die door elk beleid worden geretourneerd, zijn dus niet uitwisselbaar. Dit betekent dat elk beleid een afzonderlijk `IAccount`-object retourneert waarvan de tokens niet kunnen worden gebruikt om andere beleids regels aan te roepen.

Elk beleid voegt een `IAccount` toe aan de cache voor elke gebruiker. Als een gebruiker zich aanmeldt bij een toepassing en twee beleids regels aanroept, hebben ze twee `IAccount`s. Als u deze gebruiker uit de cache wilt verwijderen, moet u `removeAccount()` voor elk beleid aanroepen.

Wanneer u tokens voor een beleid met `acquireTokenSilent`verlengt, geeft u hetzelfde `IAccount` op dat is geretourneerd door eerdere aanroepen van het beleid naar `AcquireTokenSilentParameters`. Als u een account opgeeft dat door een ander beleid wordt geretourneerd, treedt er een fout op.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Active Directory B2C (Azure AD B2C) op [Wat is Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
