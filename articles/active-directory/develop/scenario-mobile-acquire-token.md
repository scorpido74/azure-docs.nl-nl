---
title: Een token aanschaffen om een web-API aan te roepen (mobiele apps) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-API's aanroept. (Ontvang een token voor de app.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf967525283f28d5829d80b75e40e263f7eaedef
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882740"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Een token aanvragen voor een mobiele app die web-API's aanroept

Voordat uw app beveiligde web-API's kan aanroepen, heeft deze een toegangstoken nodig. In dit artikel u het proces doorlopen om een token te krijgen met behulp van Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Een bereik definiëren

Wanneer u een token aanvraagt, moet u een bereik definiëren. Het bereik bepaalt welke gegevens uw app kan openen.  

De eenvoudigste manier om een scope te definiëren is `App ID URI` door `.default`de gewenste web API's te combineren met de scope. Deze definitie vertelt Microsoft identity platform dat uw app alle scopes vereist die zijn ingesteld in de portal.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Tokens ontvangen

### <a name="acquire-tokens-via-msal"></a>Tokens verkrijgen via MSAL

MSAL stelt apps in staat om tokens in stilte en interactief te verwerven. Wanneer u `AcquireTokenSilent()` `AcquireTokenInteractive()`belt of MSAL een toegangstoken retourneert voor de gevraagde scopes. Het juiste patroon is om een stille aanvraag te doen en vervolgens terug te vallen op een interactief verzoek.

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
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

Probeer eerst in stilte een token te verkrijgen:

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

Als MSAL `MSALErrorInteractionRequired`terugkeert, probeer dan het verwerven van tokens interactief:

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

MSAL voor iOS en macOS ondersteunt verschillende modifiers om een token interactief of geruisloos te krijgen:
* [Algemene parameters voor het verkrijgen van een token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parameters voor het verkrijgen van een interactief token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parameters voor het verkrijgen van een stille token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

In het volgende voorbeeld ziet u de minimale code om een token interactief te krijgen. In het voorbeeld wordt Microsoft Graph gebruikt om het profiel van de gebruiker te lezen.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Verplichte parameters in MSAL.NET

`AcquireTokenInteractive`heeft slechts één `scopes`verplichte parameter: . De `scopes` parameter somt tekenreeksen op die de scopes definiëren waarvoor een token vereist is. Als het token voor Microsoft Graph is, u de vereiste scopes vinden in de API-verwijzing van elke Microsoft Graph-API. Ga in de verwijzing naar de sectie Machtigingen. 

Als u bijvoorbeeld de contactpersonen van de gebruiker wilt [aanbieden,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)gebruikt u het bereik 'User.Read', 'Contacts.Read'. Zie [Verwijzing naar microsoft graph-machtigingen voor](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)meer informatie .

Op Android u bovenliggende activiteit opgeven `PublicClientApplicationBuilder`wanneer u de app maakt met behulp van. Als u de bovenliggende activiteit op dat moment niet opgeeft, u deze later opgeven door te gebruiken `.WithParentActivityOrWindow` zoals in de volgende sectie. Als u bovenliggende activiteit opgeeft, wordt het token na de interactie teruggezet naar die bovenliggende activiteit. Als u dit niet opgeeft, wordt er met het `.ExecuteAsync()` gesprek een uitzondering gemaakt.

#### <a name="specific-optional-parameters-in-msalnet"></a>Specifieke optionele parameters in MSAL.NET

In de volgende secties worden de optionele parameters in MSAL.NET. 

##### <a name="withprompt"></a>MetPrompt

De `WithPrompt()` parameter regelt de interactiviteit met de gebruiker door een prompt op te geven.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

De klasse definieert de volgende constanten:

- `SelectAccount`hiermee wordt de beveiligingstokenservice (STS) naar het dialoogvenster accountselectie dwingt. Het dialoogvenster bevat de accounts waarvoor de gebruiker een sessie heeft. U deze optie gebruiken wanneer u de gebruiker wilt laten kiezen uit verschillende identiteiten. Met deze optie wordt `prompt=select_account` MSAL naar de identiteitsprovider gestuurd. 
    
    De `SelectAccount` constante is de standaard, en het biedt effectief de best mogelijke ervaring op basis van de beschikbare informatie. De beschikbare informatie kan bestaan uit account, aanwezigheid van een sessie voor de gebruiker, enzovoort. Wijzig deze standaard niet, tenzij u een goede reden hebt om het te doen.
- `Consent`stelt u in staat om de gebruiker om toestemming te vragen, zelfs als er al eerder toestemming is verleend. In dit geval stuurt `prompt=consent` MSAL naar de identiteitsprovider. 

    U de `Consent` constante gebruiken in beveiligingsgerichte toepassingen waarbij gebruikers het dialoogvenster toestemming moeten zien telkens wanneer ze de toepassing gebruiken.
- `ForceLogin`stelt de service in staat om de gebruiker om referenties te vragen, zelfs als de prompt niet nodig is. 

    Deze optie kan handig zijn als de tokenacquisitie mislukt en u de gebruiker opnieuw wilt laten inloggen. In dit geval stuurt `prompt=login` MSAL naar de identiteitsprovider. U deze optie gebruiken in beveiligingsgerichte toepassingen waarbij de organisatiegovernance vereist dat de gebruiker zich aanmeldt telkens wanneer hij toegang heeft tot specifieke delen van de toepassing.
- `Never`is voor alleen .NET 4.5 en Windows Runtime (WinRT). Deze constante zal de gebruiker niet vragen, maar het zal proberen om de cookie te gebruiken die is opgeslagen in de verborgen ingesloten webweergave. Zie [Webbrowsers gebruiken met MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)voor meer informatie.

    Als deze optie `AcquireTokenInteractive` mislukt, wordt er met een uitzondering gesmeerd om u te laten weten dat er een ui-interactie nodig is. Dan moet je `Prompt` een andere parameter gebruiken.
- `NoPrompt`stuurt geen prompt naar de identiteitsprovider. 

    Deze optie is alleen handig voor bewerkprofielbeleid in Azure Active Directory B2C. Zie [B2C-bijzonderheden](https://aka.ms/msal-net-b2c-specificities)voor meer informatie.

##### <a name="withextrascopetoconsent"></a>MetExtraScopeToConsent

Gebruik `WithExtraScopeToConsent` de modifier in een geavanceerd scenario waarbij u wilt dat de gebruiker vooraf toestemming geeft voor verschillende bronnen. U deze modifier gebruiken wanneer u geen incrementele toestemming wilt gebruiken, die normaal gesproken wordt gebruikt met MSAL.NET of Microsoft-identiteitsplatform 2.0. Zie [Toestemming van de gebruiker vooraf voor verschillende bronnen voor](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)meer informatie .

Hier is een codevoorbeeld: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andere optionele parameters

Zie de `AcquireTokenInteractive` [referentiedocumentatie voor AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)voor meer informatie over de andere optionele parameters voor .

### <a name="acquire-tokens-via-the-protocol"></a>Tokens verkrijgen via het protocol

We raden niet aan om het protocol direct te gebruiken om tokens te krijgen. Als u dit wel doet, ondersteunt de app geen scenario's waarbij eenmalige aanmelding (SSO), apparaatbeheer en voorwaardelijke toegang zijn.

Wanneer u het protocol gebruikt om tokens voor mobiele apps te ontvangen, voert u twee verzoeken in: 

* Zorg voor een autorisatiecode.
* Ruil de code in voor een token.

#### <a name="get-an-authorization-code"></a>Een autorisatiecode aanvragen

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Toegang krijgen en het token vernieuwen

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
