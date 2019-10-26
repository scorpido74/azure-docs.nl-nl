---
title: Bureau blad-app voor het aanroepen van web-Api's (het verkrijgen van een token voor de app)-micro soft Identity-platform
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept (een token voor de app aanschaffen |)
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
ms.date: 10/24/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0eaeaf915ad480306c114d7ab79e88e95c336eb
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893911"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Bureau blad-app die web-Api's aanroept-een Token ophalen

Wanneer u `IPublicClientApplication`hebt gemaakt, gebruikt u dit om een token te verkrijgen dat u vervolgens gebruikt om een web-API aan te roepen.

## <a name="recommended-pattern"></a>Aanbevolen patroon

De Web-API is gedefinieerd door de `scopes`. Ongeacht de ervaring die u in uw toepassing opgeeft, is het patroon dat u wilt gebruiken:

- Er wordt systematisch geprobeerd een token op te halen uit de token cache door de `AcquireTokenSilent` aan te roepen
- Als deze aanroep mislukt, gebruikt u de `AcquireToken` stroom die u wilt gebruiken (deze wordt aangeduid door `AcquireTokenXX`)

### <a name="in-msalnet"></a>In MSAL.NET

```CSharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```
### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doel-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
    
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift

```swift
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
}
```

Hier volgt nu de details van de verschillende manieren om tokens te verkrijgen in een bureaublad toepassing

## <a name="acquiring-a-token-interactively"></a>Een token interactief aanschaffen

In het volgende voor beeld wordt de minimale code weer gegeven voor het interactief ophalen van een token voor het lezen van het profiel van de gebruiker met Microsoft Graph.

### <a name="in-msalnet"></a>In MSAL.NET

```CSharp
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

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doel-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

### <a name="mandatory-parameters"></a>Verplichte para meters

`AcquireTokenInteractive` heeft slechts één verplichte para meter ``scopes``, die een opsomming bevat van teken reeksen die de bereiken definiëren waarvoor een token is vereist. Als het token voor de Microsoft Graph is, kunt u de vereiste bereiken vinden in API-verwijzing van elke micro soft Graph API in de sectie met de naam ' permissions '. Als u bijvoorbeeld [de contact personen van de gebruiker wilt weer geven](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), moet de scope ' gebruiker. read ', ' Contacts. read ' worden gebruikt. Zie ook [Microsoft Graph permissions Reference](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)(Engelstalig).

Op Android moet u ook de bovenliggende activiteit (met behulp van `.WithParentActivityOrWindow`) opgeven, zodat het token na de interactie terugkeert naar die bovenliggende activiteit. Als u deze niet opgeeft, wordt er een uitzonde ring gegenereerd bij het aanroepen van `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Specifieke optionele para meters in MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Gebruikers interface is interactief, maar is belang rijk. `AcquireTokenInteractive` heeft één specifieke optionele para meter die het mogelijk maakt om op te geven voor platforms die het ondersteunen, de bovenliggende gebruikers interface. Bij gebruik in een bureaublad toepassing heeft `.WithParentActivityOrWindow` een ander type, afhankelijk van het platform:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Opmerkingen

- Op .NET Standard is de verwachte `object` een `Activity` op Android, een `UIViewController` op iOS, een `NSWindow` op MAC en een `IWin32Window` of `IntPr` in Windows.
- In Windows moet u `AcquireTokenInteractive` aanroepen vanuit de gebruikers interface-thread zodat de Inge sloten browser de juiste context voor de synchronisatie van gebruikers interface krijgt.  Het aanroepen van de UI-thread kan ertoe leiden dat berichten niet goed worden gepompt en/of deadlock met de gebruikers interface. Een manier om MSAL aan te roepen vanuit de UI-thread als u zich al in de UI-thread bevindt, is het `Dispatcher` op WPF te gebruiken.
- Als u gebruikmaakt van WPF, kunt u met behulp van `WindowInteropHelper.Handle`-klasse een venster van een WPF-besturings element ophalen. De aanroep is vervolgens van een WPF-besturings element (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` wordt gebruikt om de interactiviteit met de gebruiker te beheren door een prompt op te geven

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

De klasse definieert de volgende constanten:

- ``SelectAccount``: Hiermee wordt de STS gedwongen het dialoog venster voor account selectie weer te geven met accounts waarvoor de gebruiker een sessie heeft. Deze optie is handig wanneer ontwikkel aars van toepassingen willen laten kiezen uit verschillende identiteiten. Deze optie verstuurt MSAL om ``prompt=select_account`` te verzenden naar de ID-provider. Deze optie is de standaard instelling en het is een goede taak om de best mogelijke ervaring te bieden op basis van de beschik bare informatie (account, aanwezigheid van een sessie voor de gebruiker, enzovoort). ...). Wijzig deze alleen als u er geen goede reden voor hebt.
- ``Consent``: de ontwikkelaar van de toepassing kan ervoor zorgen dat de gebruiker om toestemming wordt gevraagd, zelfs als toestemming werd verleend. In dit geval stuurt MSAL `prompt=consent` naar de ID-provider. Deze optie kan worden gebruikt in sommige toepassingen met een gerichte beveiliging waarbij het beheer van de organisatie vereist dat de gebruiker het dialoog venster voor toestemming wordt weer gegeven telkens wanneer de toepassing wordt gebruikt.
- ``ForceLogin``: de ontwikkelaar van de toepassing kan de gebruiker door de service om referenties wordt gevraagd, zelfs als deze prompt niet nodig is. Deze optie kan nuttig zijn als het ophalen van een token mislukt, zodat de gebruiker zich opnieuw aanmeldt. In dit geval stuurt MSAL `prompt=login` naar de ID-provider. We hebben de IT-afdeling weer gegeven in sommige toepassingen met een gerichte beveiliging waarbij het beheer van de organisatie vereist dat de gebruiker zich opnieuw aanmeldt, telkens wanneer ze toegang hebben tot specifieke delen van een toepassing.
- ``Never`` (alleen voor .NET 4,5 en WinRT) wordt de gebruiker niet gevraagd, maar probeert in plaats daarvan de cookie te gebruiken die is opgeslagen in de verborgen Inge sloten webweergave (zie hieronder: webweergaven in MSAL.NET). Het gebruik van deze optie kan mislukken en in dat geval `AcquireTokenInteractive` een uitzonde ring gegenereerd om aan te geven dat er een GEBRUIKERSINTERFACE interactie is vereist. u moet een andere `Prompt`-para meter gebruiken.
- ``NoPrompt``: er wordt geen prompt verzonden naar de ID-provider. Deze optie is alleen nuttig voor het Azure AD B2C bewerken van profiel beleid (Zie [B2C-specifiek](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Deze optie wordt gebruikt in een geavanceerd scenario waarin u de gebruiker vooraf toestemming wilt geven voor verschillende bronnen (en niet de incrementele toestemming wilt gebruiken, die doorgaans wordt gebruikt met MSAL.NET/het micro soft Identity platform). Zie [How to: de gebruiker vooraf toestemming geven voor verschillende bronnen](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)voor meer informatie.

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Een web-UI is een mechanisme voor het aanroepen van een browser. Dit mechanisme kan een specifiek UI-webbrowser-besturings element zijn of een manier om het openen van de browser te delegeren.
MSAL biedt web-UI-implementaties voor de meeste platformen, maar er zijn nog steeds gevallen waarin u de browser zelf wilt hosten: 

- platforms die niet expliciet onder MSAL vallen, bijvoorbeeld Ultra meer, eenheid, mono op bureau blad
- u wilt de gebruikers interface testen uw toepassing en wilt een geautomatiseerde browser gebruiken die kan worden gebruikt met selenium 
- de browser en de app met MSAL bevinden zich in afzonderlijke processen

##### <a name="at-a-glance"></a>Een overzicht

Hiervoor geeft u MSAL een `start Url`op dat moet worden weer gegeven in een browser van de keuze, zodat de eind gebruiker de gebruikers naam kan invoeren, enzovoort. Zodra de verificatie is voltooid, moet uw app teruggaan naar MSAL de `end Url`, die een code bevat die door Azure AD is geleverd.
De host van de `end Url` is altijd de `redirectUri`. Als u de `end Url` wilt onderscheppen, kunt u het volgende doen: 

- browser omleidingen bewaken totdat de `redirect Url` is bereikt of
- de browser omleiden naar een URL die u bewaken

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi is een uitbreidings punt

`WithCustomWebUi` is een uitbreidings punt waarmee u uw eigen gebruikers interface kunt opgeven in open bare client toepassingen en waarmee de gebruiker het/authorize-eind punt van de identiteits provider kan passeren en zich kan aanmelden en toestemming kan geven. MSAL.NET kan, vervolgens de verificatie code inwisselen en een Token ophalen. Het is bijvoorbeeld in Visual Studio om electrons-toepassingen (voor instance-to-feedback) de webinteractie te bieden, maar laat MSAL.NET het meeste werk doen. U kunt deze ook gebruiken als u UI-automatisering wilt bieden. In open bare client toepassingen gebruikt MSAL.NET de PKCE Standard ([RFC 7636-proef sleutel voor code uitwisseling door OAuth open bare clients](https://tools.ietf.org/html/rfc7636)) om ervoor te zorgen dat de beveiliging wordt geëerbiedigd: alleen MSAL.net kunnen de code inwisselen.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>WithCustomWebUi gebruiken

Als u `.WithCustomWebUI`wilt gebruiken, moet u het volgende doen:
  
  1. Implementeer de `ICustomWebUi`-interface (Zie [hier](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)). In principe moet u één methode implementeren `AcquireAuthorizationCodeAsync` het accepteren van de URL van de autorisatie code (berekend door MSAL.NET), zodat de gebruiker de interactie met de ID-provider kan door lopen en vervolgens de URL terugstuurt op basis waarvan de ID-provider zou beschikken geeft uw implementatie weer (inclusief de autorisatie code). Als u problemen ondervindt, moet uw implementatie een `MsalExtensionException` uitzonde ring genereren voor de samen werking met MSAL.
  2. In uw `AcquireTokenInteractive`-oproep kunt u `.WithCustomUI()` modificator gebruiken om het exemplaar van uw aangepaste web-UI door te geven

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Voor beelden van implementatie van ICustomWebUi in test Automation-SeleniumWebUI

Het MSAL.NET-team heeft onze UI-tests herschreven om gebruik te maken van dit uitbreidings mechanisme. Als u geïnteresseerd bent, kunt u de klasse [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) in de bron code MSAL.net bekijken

##### <a name="providing-a-great-experience-with-systemwebviewoptions"></a>Een fantastische ervaring bieden met SystemWebViewOptions

Met MSAL.NET 4,1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) kunt u het volgende opgeven:

- de URI waarnaar moet worden genavigeerd (`BrowserRedirectError`) of het HTML-fragment dat moet worden weer gegeven (`HtmlMessageError`) in geval van aanmeld-en toestemmings fouten in de webbrowser van het systeem
- de URI waarnaar moet worden genavigeerd (`BrowserRedirectSuccess`) of het HTML-fragment dat moet worden weer gegeven (`HtmlMessageSuccess`) in het geval van een geslaagde aanmelding/toestemming.
- de actie die moet worden uitgevoerd om de systeem browser te starten. Hiervoor kunt u uw eigen implementatie opgeven door de gemachtigde van `OpenBrowserAsync` in te stellen. De klasse biedt ook een standaard implementatie voor twee browsers: `OpenWithEdgeBrowserAsync` en `OpenWithChromeEdgeBrowserAsync`, respectievelijk voor micro soft Edge en [micro soft Edge in chroom](https://www.windowscentral.com/faq-edge-chromium).

Als u deze structuur wilt gebruiken, kunt u een van de volgende zaken schrijven:

```CSharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andere optionele para meters

Meer informatie over de andere optionele para meters voor `AcquireTokenInteractive` van de referentie documentatie voor [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-verificatie

Als u zich wilt aanmelden voor een domein gebruiker op een domein of Azure AD-computer, moet u het volgende gebruiken:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Beperkingen

- AcquireTokenByIntegratedWindowsAuth (IWA) is alleen bruikbaar voor **federatieve** gebruikers, dat wil zeggen, gebruikers die zijn gemaakt in een Active Directory en die worden ondersteund door Azure Active Directory. Gebruikers die rechtstreeks zijn gemaakt in AAD, zonder dat ze een AD- **back-up kunnen maken** , kunnen deze verificatie stroom niet gebruiken. Deze beperking heeft geen invloed op de gebruikers naam en het wacht woord.
- IWA is bedoeld voor apps die zijn geschreven voor .NET Framework-, .NET core-en UWP-platforms
- IWA niet overs laan van MFA (multi-factor Authentication). Als MFA is geconfigureerd, kan IWA mislukken als een MFA-Challenge vereist is, omdat MFA gebruikers interactie vereist.
  > [!NOTE]
  > Dit is een lastigheid. IWA is niet-interactief, maar MFA vereist een interactiviteit van de gebruiker. U kunt niet bepalen wanneer de identiteits provider MFA moet aanvragen om te worden uitgevoerd, de Tenant beheerder. Vanuit onze waarnemingen is MFA vereist wanneer u zich aanmeldt vanuit een ander land, wanneer er geen verbinding is via VPN met een bedrijfs netwerk, en soms zelfs wanneer verbinding via VPN. U verwacht geen deterministische set regels, Azure Active Directory gebruikt AI om voortdurend te leren of MFA vereist is. Als IWA mislukt, moet u een gebruikers prompt (interactieve verificatie of programma code stroom) terugvallen.

- De door gegeven instantie in de `PublicClientApplicationBuilder` moet zijn:
  - tenants (van het formulier `https://login.microsoftonline.com/{tenant}/` waarbij `tenant` de GUID is van de Tenant-ID of een domein dat is gekoppeld aan de Tenant.
  - voor alle werk-en school accounts (`https://login.microsoftonline.com/organizations/`)
  - Persoonlijke micro soft-accounts worden niet ondersteund (u kunt geen/veelvoorkomende-of/consumers-tenants gebruiken)

- Geïntegreerde Windows-verificatie is een stille stroom:
  - de gebruiker van uw toepassing moet eerder hebben ingestemd om de toepassing te gebruiken
  - of de Tenant beheerder moet eerder toestemming hebben gegeven voor alle gebruikers in de Tenant om de toepassing te gebruiken.
  - Met andere woorden:
    - u als ontwikkelaar hebt op de knop **Grant** op het Azure portal voor uzelf gedrukt.
    - of een Tenant beheerder heeft de knop **toestemming geven/ingetrokken beheerder voor {Tenant domein}** ingedrukt op het tabblad **API-machtigingen** van de registratie voor de toepassing (Zie [machtigingen toevoegen voor toegang tot Web-api's](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - of u hebt een manier gegeven waarop gebruikers toestemming kunnen geven voor de toepassing (Zie [toestemming van individuele gebruiker aanvragen](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - of u hebt een manier gegeven waarop de Tenant beheerder toestemming kan geven voor de toepassing (Zie toestemming van de [beheerder](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Deze stroom is ingeschakeld voor .net desktop-, .net core-en Windows universele (UWP)-apps. Op .NET Core is alleen de overbelasting van de gebruikers naam beschikbaar, omdat het .NET Core-platform de gebruikers naam niet kan vragen aan het besturings systeem.
  
Zie [machtigingen en toestemming van micro soft-identiteits platform](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) voor meer informatie over toestemming

### <a name="how-to-use-it"></a>Het gebruiken

Normaal gesp roken hebt u slechts één para meter (`scopes`) nodig. Afhankelijk van de manier waarop de Windows-beheerder het beleid heeft ingesteld, is het mogelijk dat toepassingen op uw Windows-computer de aangemelde gebruiker niet kunnen opzoeken. In dat geval gebruikt u een tweede methode `.WithUsername()` en geeft u de gebruikers naam van de aangemelde gebruiker door als UPN-`joe@contoso.com`.

In het volgende voor beeld ziet u het meest recente geval, met uitleg over het soort uitzonde ringen dat u kunt krijgen en de oplossingen

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Zie [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods) voor de lijst met mogelijke para meters op AcquireTokenByIntegratedWindowsAuthentication.

## <a name="username--password"></a>Gebruikers naam/wacht woord

U kunt ook een token verkrijgen door de gebruikers naam en het wacht woord op te geven. Deze stroom is beperkt en niet aanbevolen, maar er zijn nog steeds gebruik van gevallen waarin het nodig is.

### <a name="this-flow-isnt-recommended"></a>Deze stroom wordt niet aanbevolen

Deze stroom wordt **niet aanbevolen** omdat uw toepassing die een gebruiker vraagt om zijn of haar wacht woord niet veilig is. Zie [dit artikel](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)voor meer informatie over dit probleem. De voorkeurs stroom voor het op de achtergrond verkrijgen van een token op computers die lid zijn van een Windows-domein is [geïntegreerde Windows-verificatie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Anders kunt u ook de [code stroom](https://aka.ms/msal-net-device-code-flow) van het apparaat gebruiken

> [!NOTE] 
> Hoewel dit in sommige gevallen handig is (DevOps-scenario's), als u gebruikers naam/wacht woord wilt gebruiken in interactieve scenario's waarin u uw eigen gebruikers interface opgeeft, moet u nadenken over hoe u deze kunt verlaten. Door gebruik te maken van gebruikers naam/wacht woord worden een aantal dingen genoteerd:
>
> - kern tenants van moderne identiteit: wacht woord wordt gevist, opnieuw afgespeeld. Omdat we dit concept hebben van een share geheim dat kan worden onderschept.
> Dit is niet compatibel met een wacht woord.
> - gebruikers die MFA nodig hebben, kunnen zich niet aanmelden (omdat er geen interactie is)
> - Gebruikers kunnen eenmalige aanmelding niet uitvoeren

### <a name="constraints"></a>Beperkingen

De volgende beperkingen zijn ook van toepassing:

- De gebruikers naam/wacht woord stroom is niet compatibel met voorwaardelijke toegang en multi-factor Authentication. als gevolg hiervan is het niet mogelijk om deze stroom te gebruiken als uw app wordt uitgevoerd in een Azure AD-Tenant waarbij de Tenant beheerder multi-factor Authentication vereist. Veel organisaties doen dat.
- Het werkt alleen voor werk-en school accounts (niet MSA)
- De stroom is beschikbaar op .net desktop en .net core, maar niet op UWP

### <a name="b2c-specifics"></a>B2C-specifiek

[Meer informatie over het gebruik van ROPC met B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Hoe gebruikt u dit?

`IPublicClientApplication`bevat de methode `AcquireTokenByUsernamePassword`

In het volgende voor beeld wordt een vereenvoudigd geval weer gegeven

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

In het volgende voor beeld ziet u het meest recente geval, met uitleg over het soort uitzonde ringen dat u kunt krijgen en de oplossingen

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Zie [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods) voor meer informatie over alle wijzigingen die kunnen worden toegepast op `AcquireTokenByUsernamePassword`.

## <a name="command-line-tool-without-web-browser"></a>Opdracht regel programma (zonder webbrowser)

### <a name="device-code-flow-why-and-how"></a>Toestel code stroom waarom? en hoe?

Als u een opdracht regel programma schrijft (dat geen Webbe sturings elementen heeft) en u de eerdere stromen niet wilt gebruiken, moet u `AcquireTokenWithDeviceCode`gebruiken.

Interactieve verificatie met Azure AD vereist een webbrowser (Zie het [gebruik van webbrowsers](https://aka.ms/msal-net-uses-web-browser)voor meer informatie). Om gebruikers te verifiëren op apparaten of besturings systemen die geen webbrowser bieden, kan de gebruiker met de programma code stroom echter een ander apparaat (bijvoorbeeld een andere computer of een mobiele telefoon) gebruiken om zich interactief aan te melden. Door de apparaatcode stroom te gebruiken, haalt de toepassing tokens op via een proces met twee stappen dat speciaal is ontworpen voor deze apparaten/besturings systemen. Voor beelden van dergelijke toepassingen zijn toepassingen die worden uitgevoerd op iOT, of opdracht regel programma's (CLI). Het idee is dat:

1. Wanneer gebruikers verificatie is vereist, biedt de app een code en wordt de gebruiker gevraagd om een ander apparaat (zoals een smartphone met Internet verbinding) te gebruiken om te navigeren naar een URL (bijvoorbeeld `https://microsoft.com/devicelogin`), waarbij de gebruiker wordt gevraagd de code in te voeren. Dit heeft tot gevolg dat de webpagina een normale verificatie-ervaring krijgt, met inbegrip van toestemming prompts en multi-factor Authentication, indien nodig.

2. Wanneer de verificatie is geslaagd, ontvangt de opdracht regel-app de vereiste tokens via een back-upkanaal en wordt deze gebruikt voor het uitvoeren van de Web-API.

### <a name="code"></a>Coderen

`IPublicClientApplication`bevat een methode met de naam `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Deze methode neemt de volgende para meters:

- De `scopes` om een toegangs token aan te vragen voor
- Een call back waarbij de `DeviceCodeResult` wordt ontvangen

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Met de volgende voorbeeld code wordt het meest recente geval weer gegeven, met uitleg over het soort uitzonde ringen dat u kunt krijgen en de oplossing.

```CSharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();
           
    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification 

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using 
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the 
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are 
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the 
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled. 
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads 
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

## <a name="file-based-token-cache"></a>Op bestanden gebaseerde token cache

In MSAL.NET wordt standaard een token cache in het geheugen beschikbaar gesteld.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialisatie kan worden aangepast in Windows-bureau blad-apps en web-apps/web-Api's

In het geval van .NET Framework en .NET Core kunt u, als u dit niet doet, de token cache in het geheugen voor de duur van de toepassing. Houd er rekening mee dat MSAL .NET Desktop/core-toepassingen geen toegang hebben tot het bestands systeem, **maar ook** webtoepassingen of Web-API, om te begrijpen waarom de serialisatie niet uit het vak is opgelegd. Deze web-apps en Web-Api's kunnen gebruikmaken van bepaalde specifieke cache mechanismen zoals data bases, gedistribueerde caches, redis-caches enzovoort. Als u een permanente token cache-toepassing in .NET desktop of Core wilt hebben, moet u de serialisatie aanpassen.

Klassen en interfaces die betrokken zijn bij de serialisatie van token-cache zijn de volgende typen:

- ``ITokenCache``, waarmee gebeurtenissen worden gedefinieerd voor het abonneren op aanvragen voor de serialisatie van tokens en voor de methoden voor het serialiseren of deserialiseren van de cache in verschillende indelingen (ADAL v 3.0, MSAL 2. x en MSAL 3. x = ADAL v 5.0)
- ``TokenCacheCallback`` is een call back die is door gegeven aan de gebeurtenissen, zodat u de serialisatie kunt afhandelen. ze worden aangeroepen met argumenten van het type ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` biedt alleen de ``ClientId`` van de toepassing en een verwijzing naar de gebruiker waarvoor het token beschikbaar is

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET maakt token caches voor u en biedt u de `IToken`-cache wanneer u de `UserTokenCache` en `AppTokenCache` eigenschappen van een toepassing aanroept. U zou de interface niet zelf moeten implementeren. Uw verantwoordelijkheid, wanneer u een aangepaste token cache-serialisatie implementeert, is:
>
> - Reageer op `BeforeAccess` en `AfterAccess` gebeurtenissen (of een *async* -tegen hanger). De gemachtigde van de`BeforeAccess` is verantwoordelijk voor het deserialiseren van de cache, terwijl de `AfterAccess` één verantwoordelijk is voor het serialiseren van de cache.
> - Onderdeel van deze gebeurtenissen Store of het laden van blobs, die door het gebeurtenis argument worden door gegeven aan de gewenste opslag.

De strategieën verschillen, afhankelijk van of u een token cache-serialisatie schrijft voor een open bare-client toepassing (Desktop) of een vertrouwelijke client toepassing (Web-app/Web-API, daemon-app).

Sinds MSAL v2. x hebt u verschillende opties, afhankelijk van of u de cache alleen wilt serialiseren naar de MSAL.NET-indeling (Unified Format cache die gebruikelijk is bij MSAL, maar ook op de platformen), of als u ook de [verouderde](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache wilt ondersteunen serialisatie van ADAL v3.

De aanpassing van de token cache-serialisatie voor het delen van de SSO-status tussen ADAL.NET 3. x, ADAL.NET 5. x en MSAL.NET wordt uitgelegd in een deel van het volgende voor beeld: [Active-Directory-DotNet-v1-naar-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Eenvoudige token cache-serialisatie (alleen MSAL)

Hieronder ziet u een voor beeld van een Naïve-implementatie van aangepaste serialisatie van een token cache voor desktop toepassingen. Hier ziet u de token cache van de gebruiker in een bestand in dezelfde map als de toepassing.

Nadat u de toepassing hebt gemaakt, schakelt u de serialisatie in door de toepassing aan te roepen ``TokenCacheHelper.EnableSerialization()`` door te geven `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Deze helperklasse ziet eruit als het volgende code fragment:

```CSharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Een preview van een productkwaliteits token op basis van een op bestanden gebaseerde serialisatiefunctie voor open bare client toepassingen (voor bureaublad toepassingen die worden uitgevoerd op Windows, Mac en Linux) is beschikbaar in de open-source bibliotheek van [micro soft. Identity. client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . U kunt deze in uw toepassingen insluiten vanuit het volgende nuget-pakket: [micro soft. Identity. client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Uitsluiting. De bibliotheek micro soft. Identity. client. Extensions. Msal is een uitbrei ding van MSAL.NET. Klassen in deze bibliotheken kunnen in de toekomst worden MSAL.NET, zoals of met breuk wijzigingen.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Dubbele token cache-serialisatie (MSAL Unified cache + ADAL v3)

Als u de token cache-serialisatie wilt implementeren met behulp van de Unified cache-indeling (gemeen schappelijk in ADAL.NET 4. x en MSAL.NET 2. x, en met andere MSALs van dezelfde generatie of ouder, op hetzelfde platform), kunt u inspiratie krijgen met de volgende code :

```CSharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Deze keer ziet de helper class er als volgt uit:

```CSharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen vanuit de bureau blad-app](scenario-desktop-call-api.md)
