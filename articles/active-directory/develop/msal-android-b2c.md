---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Azure AD B2C met de Microsoft-verificatiebibliotheek voor Android (MSAL). Android)
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696518"
---
# <a name="use-msal-for-android-with-b2c"></a>MSAL voor Android gebruiken met B2C

Met Microsoft Authentication Library (MSAL) kunnen toepassingsontwikkelaars gebruikers met sociale en lokale identiteiten verifiëren met [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Azure AD B2C is een service voor identiteitsbeheer. Gebruik het om aan te passen en te bepalen hoe klanten zich aanmelden, zich aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken.

## <a name="configure-known-authorities-and-redirect-uri"></a>Bekende autoriteiten configureren en URI omleiden

In MSAL voor Android worden B2C-beleidsregels (gebruikersreizen) geconfigureerd als afzonderlijke autoriteiten.

Gezien een B2C-toepassing met twee beleidsregels:
- Aanmelden / Aanmelden
    * Genoemd`B2C_1_SISOPolicy`
- Profiel bewerken
    * Genoemd`B2C_1_EditProfile`

Het configuratiebestand voor de `authorities`app zou twee declareren. Eén voor elk beleid. Het `type` eigendom van `B2C`elke autoriteit is .

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

Deze `redirect_uri` moeten worden geregistreerd in de `AndroidManifest.xml` app-configuratie en ook in om omleiding te ondersteunen tijdens de [autorisatiecode-subsidiestroom.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication initialiseren

`IPublicClientApplication`is gebouwd volgens een fabrieksmethode om de toepassingsconfiguratie asynchroon te laten ontleeden.

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

## <a name="interactively-acquire-a-token"></a>Interactief een token aanschaffen

Als u een token interactief wilt `AcquireTokenParameters` aanschaffen met MSAL, bouwt u een instantie en levert u deze aan de `acquireToken` methode. De tokenaanvraag hieronder `default` maakt gebruik van de autoriteit.

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

## <a name="silently-renew-a-token"></a>Een token in stilte vernieuwen

Als u een token in stilte `AcquireTokenSilentParameters` wilt aanschaffen met `acquireTokenSilentAsync` MSAL, bouwt u een instantie en levert u deze aan de methode. In `acquireToken` tegenstelling tot `authority` de methode moet de moet worden opgegeven om een token in stilte te verkrijgen.

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

Omdat beleid in B2C wordt weergegeven als afzonderlijke autoriteiten, wordt een beroep `fromAuthority` doen op `acquireToken` een `acquireTokenSilent` ander beleid dan de standaardinstelling bereikt door een clausule op te geven bij het construeren of parameters.  Bijvoorbeeld:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Beleid voor wachtwoordwijziging afhandelen

In de gebruikersstroom van het lokale account of de aanmeldingsstroom wordt een wachtwoord **'Wachtwoord vergeten?**' weergegeven Link. Als u op deze koppeling klikt, wordt een gebruikersstroom voor het opnieuw instellen van wachtwoorden niet automatisch geactiveerd.

In plaats daarvan `AADB2C90118` wordt de foutcode teruggestuurd naar uw app. Uw app moet deze foutcode verwerken door een specifieke gebruikersstroom uit te voeren waarmee het wachtwoord wordt gereset.

Om een wachtwoord reset foutcode te vangen, `AuthenticationCallback`kan de volgende implementatie worden gebruikt in uw:

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

Een succesvolle tokenacquisitie `IAuthenticationResult` resulteert in een object. Het bevat het toegangstoken, gebruikersclaims en metagegevens.

### <a name="get-the-access-token-and-related-properties"></a>De toegangstoken en gerelateerde eigenschappen opvragen

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

### <a name="get-the-authorized-account"></a>De geautoriseerde account opmaken

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

### <a name="idtoken-claims"></a>IdToken-claims

Claims die in het IdToken worden geretourneerd, worden ingevuld door de Security Token Service (STS), niet door MSAL. Afhankelijk van de gebruikte identiteitsprovider (IdP) kunnen sommige claims afwezig zijn. Sommige id-leden geven de `preferred_username` claim momenteel niet. Omdat deze claim door MSAL wordt gebruikt voor `MISSING FROM THE TOKEN RESPONSE`caching, wordt een tijdelijke aanduidingswaarde op zijn plaats gebruikt. Zie Overzicht van tokens in Azure [Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)voor meer informatie over B2C IdToken-claims.

## <a name="managing-accounts-and-policies"></a>Accounts en beleid beheren

B2C behandelt elk beleid als een afzonderlijke autoriteit. De toegangstokens, vernieuwingstokens en ID-tokens die van elk beleid worden geretourneerd, zijn dus niet uitwisselbaar. Dit betekent dat elk `IAccount` beleid een afzonderlijk object retourneert waarvan de tokens niet kunnen worden gebruikt om andere beleidsregels aan te roepen.

Elk beleid `IAccount` voegt een toe aan de cache voor elke gebruiker. Als een gebruiker zich aanmeldt bij een toepassing en zich `IAccount`beroept op twee beleidsregels, hebben ze twee s. Als u deze gebruiker uit de `removeAccount()` cache wilt verwijderen, moet u voor elk beleid bellen.

Wanneer u tokens verlengt `acquireTokenSilent`voor een `IAccount` beleid met, moet u hetzelfde `AcquireTokenSilentParameters`bieden dat is geretourneerd van eerdere aanroepingen van het beleid naar . Als u een account opgeeft dat door een ander beleid wordt geretourneerd, wordt er een fout gemaakt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Active Directory B2C (Azure AD B2C) bij [Wat is Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
