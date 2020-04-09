---
title: Een token aanschaffen om een web-API aan te roepen (bureaublad-app) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een bureaublad-app die web-API's aanroept om een token voor de app te verkrijgen
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885315"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Bureaublad-app die web-API's aanroept: een token aanschaffen

Nadat u een instantie van de openbare clienttoepassing hebt gebouwd, gebruikt u deze om een token te verkrijgen dat u vervolgens gebruikt om een web-API aan te roepen.

## <a name="recommended-pattern"></a>Aanbevolen patroon

De web-API wordt `scopes`gedefinieerd door zijn . Wat de ervaring die u in uw toepassing biedt, het patroon te gebruiken is:

- Probeer systematisch een token uit de tokencache te halen door te bellen `AcquireTokenSilent`.
- Als deze oproep mislukt, gebruikt u de `AcquireToken` stroom die u `AcquireTokenXX`wilt gebruiken, die hier wordt weergegeven door .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
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

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doelstelling-C:

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
Swift:

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
---

Hier zijn de verschillende manieren om tokens te verwerven in een desktopapplicatie.

## <a name="acquire-a-token-interactively"></a>Een token interactief aanschaffen

In het volgende voorbeeld ziet u minimale code om een token interactief te krijgen voor het lezen van het profiel van de gebruiker met Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>In MSAL.NET

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

### <a name="mandatory-parameters"></a>Verplichte parameters

`AcquireTokenInteractive`heeft slechts één ``scopes``verplichte parameter, die een opsomming van tekenreeksen bevat die de scopes definiëren waarvoor een token vereist is. Als het token voor Microsoft Graph is, zijn de vereiste scopes te vinden in de API-verwijzing van elke Microsoft Graph-API in de sectie 'Machtigingen'. Als u bijvoorbeeld de contactpersonen van de gebruiker wilt [weergeven,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)moet het bereik 'User.Read', 'Contacts.Read' worden gebruikt. Zie [Verwijzing naar microsoft graph-machtigingen voor](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)meer informatie .

Op Android moet u ook de bovenliggende activiteit opgeven met behulp van `.WithParentActivityOrWindow`, zoals getoond, zodat het token na de interactie terugkeert naar die bovenliggende activiteit. Als u dit niet opgeeft, wordt `.ExecuteAsync()`er een uitzondering gemaakt bij het aanroepen.

### <a name="specific-optional-parameters-in-msalnet"></a>Specifieke optionele parameters in MSAL.NET

#### <a name="withparentactivityorwindow"></a>MetParentActivityOrVenster

De gebruikersinterface is belangrijk omdat het interactief is. `AcquireTokenInteractive`heeft één specifieke optionele parameter die voor platforms die deze ondersteunen, de bovenliggende gebruikersinterface kan opgeven. Bij gebruik in een `.WithParentActivityOrWindow` desktoptoepassing heeft u een ander type, dat afhankelijk is van het platform.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Opmerkingen:

- Op .NET Standard `object` wordt `Activity` de `UIViewController` verwachte wordt `NSWindow` verwacht op `IWin32Window` Android, op iOS, op MAC en op `IntPr` Windows.
- In Windows moet `AcquireTokenInteractive` u bellen vanuit de ui-thread, zodat de ingesloten browser de juiste ui-synchronisatiecontext krijgt. Als u niet belt vanuit de ui-thread, kunnen berichten niet goed worden gepompt en worden impassescenario's met de gebruikersinterface weergegeven. Een manier om Microsoft Authentication Libraries (MSALs) aan te roepen vanuit de ui-thread `Dispatcher` als u niet op de ui-thread zit, is door de op WPF te gebruiken.
- Als u WPF gebruikt om een venster te krijgen van een `WindowInteropHelper.Handle` WPF-besturingselement, u de klasse gebruiken. Dan is de oproep is`this`van een WPF controle ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>MetPrompt

`WithPrompt()`wordt gebruikt om de interactiviteit met de gebruiker te beheren door een prompt op te geven.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

De klasse definieert de volgende constanten:

- ``SelectAccount``dwingt de STS om het dialoogvenster accountselectie te presenteren dat accounts bevat waarvoor de gebruiker een sessie heeft. Deze optie is handig wanneer toepassingsontwikkelaars gebruikers willen laten kiezen uit verschillende identiteiten. Met deze optie wordt ``prompt=select_account`` MSAL naar de identiteitsprovider gestuurd. Dit is de standaardoptie. Het doet goed werk van het verstrekken van de best mogelijke ervaring op basis van de beschikbare informatie, zoals account en aanwezigheid van een sessie voor de gebruiker. Verander het niet tenzij je een goede reden hebt om het te doen.
- ``Consent``stelt de ontwikkelaar van de toepassing in staat om de gebruiker te dwingen om toestemming te vragen, zelfs als er eerder toestemming is verleend. In dit geval stuurt `prompt=consent` MSAL naar de identiteitsprovider. Deze optie kan worden gebruikt in sommige beveiligingsgerichte toepassingen waarbij de organisatiegovernance vereist dat de gebruiker telkens bij het gebruik van de toepassing het toestemmingsdialoogvenster wordt gepresenteerd.
- ``ForceLogin``stelt de toepassingsontwikkelaar in staat om de gebruiker te laten vragen om referenties door de service, zelfs als deze gebruikersprompt mogelijk niet nodig is. Deze optie kan handig zijn om de gebruiker opnieuw te laten inloggen als het verkrijgen van een token mislukt. In dit geval stuurt `prompt=login` MSAL naar de identiteitsprovider. Soms wordt het gebruikt in beveiligingsgerichte toepassingen waarbij de organisatiegovernance vereist dat de gebruiker zich opnieuw aanmeldt telkens wanneer hij toegang heeft tot specifieke delen van een toepassing.
- ``Never``(alleen voor .NET 4.5 en WinRT) zal de gebruiker niet vragen, maar in plaats daarvan probeert de cookie te gebruiken die is opgeslagen in de verborgen ingesloten webweergave. Zie webweergaven in MSAL.NET voor meer informatie. Als u deze optie gebruikt, kan dit mislukken. In dat `AcquireTokenInteractive` geval wordt een uitzondering gemaakt om te melden dat een ui-interactie nodig is. U moet een andere `Prompt` parameter gebruiken.
- ``NoPrompt``stuurt geen prompt naar de identiteitsprovider. Deze optie is alleen handig voor Azure Active Directory (Azure AD) B2C-profielbeleid bewerken. Zie [Azure AD B2C-specificaties](https://aka.ms/msal-net-b2c-specificities)voor meer informatie.

#### <a name="withextrascopetoconsent"></a>MetExtraScopeToConsent

Deze modifier wordt gebruikt in een geavanceerd scenario waarin u wilt dat de gebruiker vooraf vooraf toestemming geeft voor verschillende bronnen en u geen incrementele toestemming wilt gebruiken, die normaal gesproken wordt gebruikt met MSAL.NET/the Microsoft-identiteitsplatform. Zie [Toestemming van de gebruiker vooraf voor verschillende bronnen voor](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)meer informatie .

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>MetCustomWebUi

Een web-gebruikersinterface is een mechanisme om een browser aan te roepen. Dit mechanisme kan een speciaal UI-webbrowserbesturingselement zijn of een manier om het openen van de browser te delegeren.
MSAL biedt web-ui-implementaties voor de meeste platforms, maar er zijn gevallen waarin u de browser zelf wilt hosten:

- Platforms die niet expliciet worden gedekt door MSAL, bijvoorbeeld Blazor, Unity en Mono op desktops.
- U wilt ui testen van uw toepassing en gebruik maken van een geautomatiseerde browser die kan worden gebruikt met Selenium.
- De browser en de app die MSAL uitvoeren, bevinden zich in afzonderlijke processen.

##### <a name="at-a-glance"></a>In één oogopslag

Om dit te bereiken, `start Url`geeft u aan MSAL , die moet worden weergegeven in een browser van keuze, zodat de eindgebruiker kan items zoals hun gebruikersnaam in te voeren.
Nadat de verificatie is voltooid, moet uw `end Url`app worden teruggestuurd naar MSAL, dat een code bevat die is verstrekt door Azure AD.
De gastheer `end Url` van `redirectUri`is altijd . Om `end Url`te onderscheppen, doen een van de volgende dingen:

- Monitor browser omleidingen totdat `redirect Url` wordt geraakt.
- Laat de browser doorverwijzen naar een URL die u controleert.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>MetCustomWebUi is een uitbreidbaarheidspunt

`WithCustomWebUi`is een uitbreidbaarheidspunt dat u gebruiken om uw eigen gebruikersinterface in openbare clienttoepassingen te bieden. U de gebruiker ook door het /Authorize-eindpunt van de identiteitsprovider laten gaan en deze laten inloggen en toestemming geven. MSAL.NET vervolgens de verificatiecode inwisselen en een token krijgen. Het wordt bijvoorbeeld gebruikt in Visual Studio om elektronentoepassingen te hebben (bijvoorbeeld Visual Studio Feedback) die de webinteractie bieden, maar het aan MSAL.NET laten om het meeste werk te doen. U het ook gebruiken als u u i-automatisering wilt bieden. In openbare clienttoepassingen gebruikt MSAL.NET de PkCE-standaard (Proof Key for Code Exchange) om ervoor te zorgen dat de beveiliging wordt gerespecteerd. Alleen MSAL.NET kunnen de code inwisselen. Zie [RFC 7636 - Proof Key for Code Exchange door OAuth Public Clients voor](https://tools.ietf.org/html/rfc7636)meer informatie.

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Gebruiken metCustomWebUi

Volg `.WithCustomWebUI`deze stappen om deze stappen te gebruiken.

  1. Implementeer de `ICustomWebUi`-interface. Zie voor meer informatie [deze website.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70) Implementeer `AcquireAuthorizationCodeAsync`één methode en accepteer de URL van de autorisatiecode die wordt berekend door MSAL.NET. Laat de gebruiker vervolgens de interactie met de identiteitsprovider doorlopen en de URL terugsturen waarmee de identiteitsprovider uw implementatie zou hebben teruggeroepen, samen met de autorisatiecode. Als u problemen hebt, moet `MsalExtensionException` uw implementatie een uitzondering maken om netjes samen te werken met MSAL.
  2. Gebruik `AcquireTokenInteractive` in uw `.WithCustomUI()` gesprek de modifier die de instantie van uw aangepaste webgebruikersinterface passeert.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Voorbeelden van implementatie van ICustomWebUi in testautomatisering: SeleniumWebUI

Het MSAL.NET team heeft de UI-tests herschreven om dit uitbreidbaarheidsmechanisme te gebruiken. Als u geïnteresseerd bent, kijk naar de [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) klasse in de MSAL.NET broncode.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Bied een geweldige ervaring met SystemWebViewOptions

Vanaf MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)u opgeven:

- De URI om`BrowserRedirectError`naar ( ) of`HtmlMessageError`het HTML-fragment te gaan om () weer te geven in het geval van aanmeldings- of toestemmingsfouten in de webbrowser van het systeem.
- De URI om`BrowserRedirectSuccess`naar ( ) of`HtmlMessageSuccess`het HTML-fragment te gaan om () weer te geven in geval van succesvolle aanmelding of toestemming.
- De actie die moet worden uitgevoerd om de systeembrowser te starten. U uw eigen implementatie `OpenBrowserAsync` verzorgen door de gemachtigde in te stellen. De klasse biedt ook een standaard `OpenWithEdgeBrowserAsync` `OpenWithChromeEdgeBrowserAsync` implementatie voor twee browsers: en voor Microsoft Edge en [Microsoft Edge op Chromium](https://www.windowscentral.com/faq-edge-chromium), respectievelijk.

Als u deze structuur wilt gebruiken, schrijft u iets als het volgende voorbeeld:

```csharp
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

#### <a name="other-optional-parameters"></a>Andere optionele parameters

Zie AcquireTokenInteractiveParameterBuilder voor `AcquireTokenInteractive`meer informatie over alle andere optionele parameters voor , zie [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python biedt niet direct een interactieve tokenmethode voor aanschaffen. In plaats daarvan vereist de toepassing dat een autorisatieaanvraag wordt verzonden bij de implementatie van de interactiestroom van de gebruiker om een autorisatiecode te verkrijgen. Deze code kan vervolgens `acquire_token_by_authorization_code` worden doorgegeven aan de methode om het token te krijgen.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doelstelling-C:

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

Swift:

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
---

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

Als u zich wilt aanmelden voor een domeingebruiker op een domein of Azure AD-lid is geworden, gebruikt u Geïntegreerde Windows-verificatie (IWA).

### <a name="constraints"></a>Beperkingen

- Geïntegreerde Windows-verificatie is alleen bruikbaar voor *federatieve+* gebruikers, dat wil zeggen gebruikers die zijn gemaakt in Active Directory en worden ondersteund door Azure AD. Gebruikers die rechtstreeks in Azure AD zijn gemaakt zonder Active Directory-ondersteuning, bekend als *beheerde* gebruikers, kunnen deze verificatiestroom niet gebruiken. Deze beperking heeft geen invloed op de gebruikersnaam en wachtwoordstroom.
- IWA is voor apps geschreven voor .NET Framework, .NET Core en Universal Windows Platform (UWP) platforms.
- IWA omzeilt geen multifactorauthenticatie (MFA). Als MFA is geconfigureerd, kan IWA mislukken als een MFA-uitdaging vereist is, omdat MFA interactie met de gebruiker vereist.
  > [!NOTE]
  > Deze is lastig. IWA is niet-interactief, maar MFA vereist interactiviteit van de gebruiker. U bepaalt niet wanneer de identiteitsprovider MFA aanvraagt om te worden uitgevoerd, de tenantbeheerder wel. Uit onze waarnemingen is MFA vereist wanneer u zich aanmeldt vanuit een ander land, wanneer u niet via VPN bent verbonden met een bedrijfsnetwerk, en soms zelfs wanneer u via VPN bent verbonden. Verwacht geen deterministische set van regels. Azure AD gebruikt AI om continu te weten te komen of MFA vereist is. Terugvallen naar een gebruikersprompt, zoals interactieve verificatie of apparaatcodestroom als IWA uitvalt.

- De autoriteit `PublicClientApplicationBuilder` die is doorgegeven moet zijn:
  - Tenant van het `https://login.microsoftonline.com/{tenant}/`formulier `tenant` , waar is de GUID die de tenant-id vertegenwoordigt of een domein dat is gekoppeld aan de tenant.
  - Voor alle werk- `https://login.microsoftonline.com/organizations/`en schoolaccounts: .
  - Persoonlijke microsoft-accounts worden niet ondersteund. U geen /common of /consumers tenants gebruiken.

- Omdat geïntegreerde Windows-verificatie een stille stroom is:
  - De gebruiker van uw toepassing moet eerder toestemming hebben gegeven om de toepassing te gebruiken.
  - Of de tenantbeheerder moet eerder toestemming hebben gegeven aan alle gebruikers in de tenant om de toepassing te gebruiken.
  - Met andere woorden:
    - Of u als ontwikkelaar de **grant-knop** in de Azure-portal zelf hebt geselecteerd.
    - Of een tenantbeheerder heeft de **knop Beheerderstoestemming voor {tenantdomein} voor {tenantdomein}** geselecteerd op het tabblad **API-machtigingen** van de registratie voor de toepassing. Zie [Machtigingen toevoegen om toegang te krijgen tot webAPI's voor](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)meer informatie.
    - U hebt ook een manier geboden om gebruikers toestemming te geven voor de toepassing. Zie [Toestemming van de gebruiker aanvragen voor](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)meer informatie .
    - U hebt ook een manier geboden om de tenantbeheerder toestemming te geven voor de toepassing. Zie [Toestemming voor beheerders voor](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)meer informatie .

- Deze stroom is ingeschakeld voor .NET-bureaublad-, .NET Core- en UWP-apps.

Zie machtigingen en toestemming van [het Microsoft-identiteitsplatform](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)voor meer informatie over toestemming.

### <a name="learn-how-to-use-it"></a>Meer informatie over het gebruik ervan

# <a name="net"></a>[.NET](#tab/dotnet)

In MSAL.NET moet u het als gevolg van het:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normaal gesproken hebt u`scopes`slechts één parameter () nodig. Afhankelijk van de manier waarop uw Windows-beheerder het beleid heeft ingesteld, mogen toepassingen op uw Windows-machine mogelijk niet de aangemelde gebruiker opzoeken. Gebruik in dat geval bijvoorbeeld `.WithUsername()`een tweede methode en geef de gebruikersnaam van de aangemelde `joe@contoso.com`gebruiker door als UPN-indeling. Op .NET Core is alleen de overbelasting die de gebruikersnaam neemt beschikbaar omdat het .NET Core-platform de gebruikersnaam niet aan het besturingssysteem kan vragen.

De volgende steekproef presenteert de meest recente zaak, met uitleg over het soort uitzonderingen dat u krijgen en hun oplossingen.

```csharp
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

Zie [AcquireTokenByIntegratedWindowsParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)voor de lijst met mogelijke modifiers op AcquireTokenByIntegratedWindowsAuthentication.

# <a name="java"></a>[Java](#tab/java)

Dit extract is van de [MSAL Java dev monsters](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Deze stroom wordt nog niet ondersteund in MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Deze stroom is niet van toepassing op MacOS.

---

## <a name="username-and-password"></a>Gebruikersnaam en wachtwoord

U ook een token aanschaffen door de gebruikersnaam en het wachtwoord op te geven. Deze stroom is beperkt en niet aanbevolen, maar er zijn nog steeds use cases waar het nodig is.

### <a name="this-flow-isnt-recommended"></a>Deze stroom wordt niet aanbevolen

Deze stroom wordt *niet aanbevolen* omdat het niet veilig is om een gebruiker om zijn wachtwoord te vragen. Zie Wat is de oplossing voor het groeiende probleem van wachtwoorden voor meer [informatie?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) De voorkeursstroom voor het in stilte aanschaffen van een token op windows-domeinaangesloten machines is [Geïntegreerde Windows-verificatie.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication) U ook [de gegevensstroom van apparaatcodes](https://aka.ms/msal-net-device-code-flow)gebruiken.

> [!NOTE]
> Het gebruik van een gebruikersnaam en wachtwoord is in sommige gevallen handig, zoals DevOps-scenario's. Maar als u een gebruikersnaam en wachtwoord wilt gebruiken in interactieve scenario's waarin u uw eigen gebruikersinterface opgeeft, bedenk dan hoe u ervan af stappen. Door een gebruikersnaam en wachtwoord te gebruiken, geef je een aantal dingen op:
>
> - Kernprincipes van de moderne identiteit. Een wachtwoord kan worden gephished en opnieuw afgespeeld omdat een gedeeld geheim kan worden onderschept. Het is onverenigbaar met wachtwoordloos.
> - Gebruikers die MFA moeten doen, kunnen zich niet aanmelden omdat er geen interactie is.
> - Gebruikers kunnen geen enkele aanmelding (SSO) doen.

### <a name="constraints"></a>Beperkingen

De volgende beperkingen zijn ook van toepassing:

- De gebruikersnaam en wachtwoordstroom zijn niet compatibel met voorwaardelijke toegang en multifactorauthenticatie. Als uw app wordt uitgevoerd in een Azure AD-tenant waarbij de tenantbeheerder multifactorauthenticatie vereist, u deze stroom niet gebruiken. Veel organisaties doen dat.
- Het werkt alleen voor werk en school accounts (niet MSA).
- De stroom is beschikbaar op .NET desktop en .NET Core, maar niet op UWP.

### <a name="b2c-specifics"></a>B2C-specificaties

Zie [RoPC (Resource Owner Password Credentials) (ROPC) met B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)voor meer informatie.

### <a name="use-it"></a>Gebruik het

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`bevat de `AcquireTokenByUsernamePassword`methode .

In het volgende voorbeeld wordt een vereenvoudigd geval gepresenteerd.

```csharp
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

De volgende steekproef presenteert de meest recente zaak, met uitleg over het soort uitzonderingen dat u krijgen en hun oplossingen.

```csharp
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

Zie [AcquireTokenByPasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)voor meer informatie `AcquireTokenByUsernamePassword`over alle modifiers waarop u worden toegepast.

# <a name="java"></a>[Java](#tab/java)

Het volgende extract is afkomstig van de [MSAL Java dev monsters](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Dit extract is afkomstig van de [MSAL Python dev monsters.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Deze stroom wordt niet ondersteund op MSAL voor macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Opdrachtregelgereedschap zonder webbrowser

### <a name="device-code-flow"></a>Apparaatcodestroom

Als u een opdrachtregelgereedschap schrijft dat geen webbesturingselementen heeft en u de vorige stromen niet of wilt gebruiken, moet u de stroom van de apparaatcode gebruiken.

Interactieve verificatie met Azure AD vereist een webbrowser. Zie [Gebruik van webbrowsers](https://aka.ms/msal-net-uses-web-browser)voor meer informatie . Als u gebruikers wilt verifiëren op apparaten of besturingssystemen die geen webbrowser bieden, kan de apparaatcodestroom de gebruiker een ander apparaat gebruiken, zoals een computer of een mobiele telefoon, om zich interactief aan te melden. Door de apparaatcodestroom te gebruiken, verkrijgt de toepassing tokens via een proces in twee stappen dat is ontworpen voor deze apparaten of bess. Voorbeelden van dergelijke toepassingen zijn toepassingen die worden uitgevoerd op iOT of command-line tools (CLI). Het idee is dat:

1. Wanneer gebruikersverificatie vereist is, biedt de app een code voor de gebruiker. De gebruiker wordt gevraagd om een ander apparaat te gebruiken, zoals een smartphone `https://microsoft.com/devicelogin`met internetverbinding, om bijvoorbeeld naar een URL te gaan. Vervolgens wordt de gebruiker gevraagd om de code in te voeren. Dat gedaan, de webpagina leidt de gebruiker door middel van een normale authenticatie ervaring, die toestemming prompts en multifactor authenticatie omvat, indien nodig.

2. Bij succesvolle verificatie ontvangt de opdrachtregel-app de vereiste tokens via een backkanaal en gebruikt deze om de web-API-aanroepen uit te voeren die het nodig heeft.

### <a name="use-it"></a>Gebruik het

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`bevat een `AcquireTokenWithDeviceCode`methode met de naam .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Deze methode neemt als parameters:

- Het `scopes` aanvragen van een toegangstoken voor.
- Een callback die `DeviceCodeResult`de .

  ![Eigenschappen DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

De volgende voorbeeldcode presenteert de meest recente case, met uitleg over het soort uitzonderingen dat u krijgen en hun mitigatie.

```csharp
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
# <a name="java"></a>[Java](#tab/java)

Dit extract is van de [MSAL Java dev monsters](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Dit extract is afkomstig van de [MSAL Python dev monsters.](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Deze stroom is niet van toepassing op MacOS.

---

## <a name="file-based-token-cache"></a>Op bestanden gebaseerde tokencache

In MSAL.NET wordt standaard een in-memory tokencache geleverd.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialisatie is aanpasbaar in Windows-bureaublad-apps en web-apps of web-API's

In het geval van .NET Framework en .NET Core, als u niets extra's doet, duurt de in-memory tokencache voor de duur van de toepassing. Om te begrijpen waarom serialisatie niet uit de doos wordt geleverd, moet u er rekening mee brengen dat MSAL .NET-bureaublad- of .NET Core-toepassingen console- of Windows-toepassingen kunnen zijn (die toegang zouden hebben tot het bestandssysteem), *maar ook* webtoepassingen of web-API's. Deze web-apps en web-API's kunnen een aantal specifieke cachemechanismen gebruiken, zoals databases, gedistribueerde caches en Redis-caches. Als u een permanente toepassing voor tokencache wilt hebben in .NET-bureaublad of .NET Core, moet u de serialisatie aanpassen.

Klassen en interfaces die betrokken zijn bij serialisatie van tokencache zijn de volgende typen:

- ``ITokenCache``, die gebeurtenissen definieert om zich te abonneren op serialisatieaanvragen voor tokencache en methoden om de cache te serialiseren of te deserialiseren in verschillende indelingen (ADAL v3.0, MSAL 2.x en MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback``is een callback doorgegeven aan de gebeurtenissen, zodat u de serialisatie verwerken. Ze worden genoemd met argumenten ``TokenCacheNotificationArgs``van het type.
- ``TokenCacheNotificationArgs``alleen de ``ClientId`` toepassing en een verwijzing naar de gebruiker waarvoor het token beschikbaar is.

  ![Serialisatiediagram voor tokencache](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET maakt tokencaches voor u `IToken` en biedt u de `UserTokenCache` cache `AppTokenCache` wanneer u de eigenschappen en eigenschappen van een toepassing aanroept. Het is niet de bedoeling dat u de interface zelf implementeert. Uw verantwoordelijkheid, wanneer u een aangepaste token cache serialisatie implementeren, is om:
>
> - Reageer `BeforeAccess` op `AfterAccess` en gebeurtenissen, of hun *Async* tegenhanger. De`BeforeAccess` gemachtigde is verantwoordelijk voor het deserialiseren van de cache. De `AfterAccess` gemachtigde is verantwoordelijk voor het serialiseren van de cache.
> - Begrijp dat een deel van deze gebeurtenissen blobs opslaat of laadt, die door het gebeurtenisargument worden doorgegeven aan de opslag die u wilt.

De strategieën zijn anders afhankelijk van of u een tokencacheserialisatie schrijft voor een openbare clienttoepassing, zoals een bureaublad of een vertrouwelijke clienttoepassing, zoals een web-app of web-API of een daemon-app.

Sinds MSAL v2.x heb je verschillende opties. Uw keuze hangt af van de vraag of u de cache alleen wilt serialiseren met het MSAL.NET-formaat, een cache met een uniform formaat dat gebruikelijk is bij MSAL, maar ook op de verschillende platforms. U ook de legacy-tokencacheserialisatie van ADAL v3 ondersteunen. [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)

De aanpassing van tokencacheserialisatie om de SSO-status te delen tussen ADAL.NET 3.x, ADAL.NET 5.x en MSAL.NET wordt uitgelegd in een deel van het voorbeeld [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Eenvoudige tokencacheserialisatie (alleen MSAL)

Het volgende voorbeeld is een naïeve implementatie van aangepaste serialisatie van een tokencache voor desktoptoepassingen. Hier bevindt de cache van het gebruikerstoken zich in een bestand in dezelfde map als de toepassing.

Nadat u de toepassing hebt gemaakt, ``TokenCacheHelper.EnableSerialization()`` schakelt u `UserTokenCache`de serialisatie in door de toepassing te bellen en door te geven.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Deze helperklasse ziet eruit als het volgende codefragment:

```csharp
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

Een voorbeeld van een op de mediabasis voor tokencache op basis van productkwaliteit voor openbare clienttoepassingen voor desktoptoepassingen die worden uitgevoerd op Windows, Mac en Linux, is beschikbaar in de open-sourcebibliotheek [van Microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) U het opnemen in uw toepassingen via het volgende NuGet-pakket: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Disclaimer: De Microsoft.Identity.Client.Extensions.Msal bibliotheek is een extensie over MSAL.NET. Klassen in deze bibliotheken kunnen in de toekomst MSAL.NET, zoals het is of met het doorbreken van wijzigingen.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialisatie met dubbele tokencache (MSAL unified cache + ADAL v3)

U de serialisatie van tokencache implementeren met de indeling Unified cache. Dit formaat is gebruikelijk om 4.x en MSAL.NET 2.x, en met andere MSALs van de zelfde generatie of ouder, op het zelfde platform te ADAL.NET. Laat je inspireren door de volgende code:

```csharp
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

Deze keer ziet de helperklasse eruit als de volgende code:

```csharp
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
> [Een web-API aanroepen vanuit de bureaublad-app](scenario-desktop-call-api.md)
