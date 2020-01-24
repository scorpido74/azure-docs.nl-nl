---
title: Een token verkrijgen om een web-API (Mobile apps) aan te roepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept (een token voor de app ophalen)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 2a86e8352958524bc51b185712d6b60ec347b98e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702093"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Mobiele app die web-Api's aanroept-een Token ophalen

Voordat u beveiligde web-Api's kunt aanroepen, moet uw app een toegangs token hebben. Dit artikel begeleidt u bij het proces voor het ophalen van een token met behulp van de micro soft Authentication Library (MSAL).

## <a name="scopes-to-request"></a>Te aanvragen scopes

Wanneer u een token aanvraagt, moet u een bereik definiëren. Het bereik bepaalt welke gegevens uw app kan gebruiken.  

De eenvoudigste benadering is het combi neren van de `App ID URI` van de gewenste Web-API met het bereik `.default`. Op die manier krijgt u het micro soft-identiteits platform dat voor uw app alle scopes in de portal zijn vereist.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Tokens ophalen

### <a name="acquire-tokens-via-msal"></a>Tokens verkrijgen via MSAL

MSAL staat apps toe om tokens op de achtergrond en interactief te verkrijgen. U hoeft deze methoden alleen aan te roepen en MSAL retourneert een toegangs token voor de aangevraagde bereiken. Het juiste patroon is om een stille aanvraag uit te voeren en terug te vallen op een interactieve aanvraag.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Probeer eerst een token op de achtergrond te verkrijgen:**

Doel-C:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Swift

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Als MSAL retourneert `MSALErrorInteractionRequired`, kunt u tokens interactief ophalen:**

Doel-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

Swift

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL voor iOS en macOS ondersteunt diverse para meters bij het interactief of op de achtergrond ophalen van een token.
* [Algemene para meters bij het ophalen van een token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Para meters voor het verkrijgen van interactieve tokens](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Para meters voor de aanschaf van Silent tokens](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

In het volgende voor beeld wordt de minimale code weer gegeven voor het interactief ophalen van een token voor het lezen van het profiel van de gebruiker met Microsoft Graph.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Verplichte para meters in MSAL.NET

`AcquireTokenInteractive` heeft slechts één verplichte para meter ``scopes``, die een opsomming bevat van teken reeksen die de bereiken definiëren waarvoor een token is vereist. Als het token voor de Microsoft Graph is, kunt u de vereiste bereiken vinden in API-verwijzing van elke micro soft Graph API in de sectie met de naam ' permissions '. Als u bijvoorbeeld [de contact personen van de gebruiker wilt weer geven](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), moet de scope ' gebruiker. read ', ' Contacts. read ' worden gebruikt. Zie ook [Microsoft Graph permissions Reference](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)(Engelstalig).

Als u deze niet hebt opgegeven bij het bouwen van de app op Android, moet u ook de bovenliggende activiteit (met behulp van `.WithParentActivityOrWindow`hieronder) opgeven, zodat het token na de interactie terugkeert naar die bovenliggende activiteit. Als u deze niet opgeeft, wordt er een uitzonde ring gegenereerd bij het aanroepen van `.ExecuteAsync()`.

#### <a name="specific-optional-parameters-in-msalnet"></a>Specifieke optionele para meters in MSAL.NET

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` wordt gebruikt om de interactiviteit met de gebruiker te beheren door een prompt op te geven

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

De klasse definieert de volgende constanten:

- ``SelectAccount``: Hiermee wordt de STS gedwongen het dialoog venster voor account selectie weer te geven met accounts waarvoor de gebruiker een sessie heeft. Deze optie is handig wanneer ontwikkel aars van toepassingen willen laten kiezen uit verschillende identiteiten. Deze optie verstuurt MSAL om ``prompt=select_account`` te verzenden naar de ID-provider. Deze optie is de standaard instelling en het is een goede taak om de best mogelijke ervaring te bieden op basis van de beschik bare informatie (account, aanwezigheid van een sessie voor de gebruiker, enzovoort). ...). Wijzig deze alleen als u er geen goede reden voor hebt.
- ``Consent``: de ontwikkelaar van de toepassing kan ervoor zorgen dat de gebruiker om toestemming wordt gevraagd, zelfs als toestemming werd verleend. In dit geval stuurt MSAL `prompt=consent` naar de ID-provider. Deze optie kan worden gebruikt in sommige toepassingen met een gerichte beveiliging waarbij het beheer van de organisatie vereist dat de gebruiker het dialoog venster voor toestemming wordt weer gegeven telkens wanneer de toepassing wordt gebruikt.
- ``ForceLogin``: de ontwikkelaar van de toepassing kan de gebruiker door de service om referenties wordt gevraagd, zelfs als deze prompt niet nodig is. Deze optie kan nuttig zijn als het ophalen van een token mislukt, zodat de gebruiker zich opnieuw aanmeldt. In dit geval stuurt MSAL `prompt=login` naar de ID-provider. We hebben de IT-afdeling weer gegeven in sommige toepassingen met een gerichte beveiliging waarbij het beheer van de organisatie vereist dat de gebruiker zich opnieuw aanmeldt, telkens wanneer ze toegang hebben tot specifieke delen van een toepassing.
- ``Never`` (alleen voor .NET 4,5 en WinRT) wordt de gebruiker niet gevraagd, maar probeert in plaats daarvan de cookie te gebruiken die is opgeslagen in de verborgen Inge sloten webweergave (zie hieronder: webweergaven in MSAL.NET). Het gebruik van deze optie kan mislukken en in dat geval `AcquireTokenInteractive` een uitzonde ring gegenereerd om aan te geven dat er een GEBRUIKERSINTERFACE interactie is vereist. u moet een andere `Prompt`-para meter gebruiken.
- ``NoPrompt``: er wordt geen prompt verzonden naar de ID-provider. Deze optie is alleen nuttig voor het Azure AD B2C bewerken van profiel beleid (Zie [B2C-specifiek](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Deze wijzigings functie wordt gebruikt in een geavanceerd scenario waarbij de gebruiker vooraf toestemming moet geven aan verschillende bronnen (en niet de incrementele toestemming wilt gebruiken, die normaal gesp roken wordt gebruikt met MSAL.NET/de micro soft Identity platform v 2.0). Zie [How to: de gebruiker vooraf toestemming geven voor verschillende bronnen](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)voor meer informatie.

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andere optionele para meters

Meer informatie over de andere optionele para meters voor `AcquireTokenInteractive` van de referentie documentatie voor [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="acquire-tokens-via-the-protocol"></a>Tokens verkrijgen via het Protocol

Het is niet raadzaam het protocol rechtstreeks te gebruiken. Als u dat wel doet, ondersteunt de app geen enkele eenmalige aanmelding (SSO), Apparaatbeheer en scenario's voor voorwaardelijke toegang.

Wanneer u het protocol gebruikt om tokens voor mobiele apps op te halen, moet u twee aanvragen doen: een autorisatie code ophalen en deze voor een token uitwisselen.

#### <a name="get-authorization-code"></a>Autorisatiecode verkrijgen

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Token voor toegang en vernieuwing ophalen

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen](scenario-mobile-call-api.md)
