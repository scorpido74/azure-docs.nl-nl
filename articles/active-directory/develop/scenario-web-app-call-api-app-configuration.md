---
title: Een webapp configureren die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het configureren van de code van een webapp die web-API's aanroept
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
ms.openlocfilehash: b0f60e13ca4bc5115f9a49885c3c659ad1147fcc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881890"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Een web-app die web-API's aanroept: codeconfiguratie

Zoals weergegeven in de [web-app die zich aanmeldt in het scenario van gebruikers,](scenario-web-app-sign-user-overview.md) gebruikt de web-app de [OAuth 2.0-autorisatiecodestroom](v2-oauth2-auth-code-flow.md) om de gebruiker aan te melden. Deze stroom heeft twee stappen:

1. Vraag een autorisatiecode aan. In dit deel wordt een privédialoog met de gebruiker aan het Microsoft-identiteitsplatform gedelegeerd. Tijdens die dialoog meldt de gebruiker zich aan en stemt hij in met het gebruik van web-API's. Wanneer de privédialoog succesvol eindigt, ontvangt de web-app een autorisatiecode op de omleidingsURI.
1. Vraag een toegangstoken voor de API aan door de autorisatiecode in te wisselen.

De [web-app die in gebruikersscenario's tekent,](scenario-web-app-sign-user-overview.md) besloeg alleen de eerste stap. Hier leert u hoe u uw web-app wijzigen, zodat deze niet alleen gebruikers aantekent, maar nu ook web-API's roept.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotheken die web-app-scenario's ondersteunen

De volgende bibliotheken in de Microsoft Authentication Library (MSAL) ondersteunen de autorisatiecodestroom voor web-apps:

| MSAL-bibliotheek | Beschrijving |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Ondersteuning voor .NET Framework- en .NET Core-platforms. Niet ondersteund worden Universal Windows Platform (UWP), Xamarin.iOS en Xamarin.Android, omdat deze platforms worden gebruikt om openbare clienttoepassingen te bouwen. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL voor Python | Ondersteuning voor Python-webtoepassingen. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL voor Java | Ondersteuning voor Java-webapplicaties. |

Selecteer het tabblad voor het platform waarin u geïnteresseerd bent:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Omdat de aanmelding van de gebruiker is gedelegeerd aan de Middenware van De Open ID Connect (OIDC), moet u communiceren met het OIDC-proces. Hoe u met elkaar omgaat, is afhankelijk van het framework dat u gebruikt.

Voor ASP.NET Core abonneer je je op middleware OIDC-evenementen:

- Je laat ASP.NET Core een autorisatiecode aanvragen via de Open ID Connect middleware. ASP.NET of ASP.NET Core laat de gebruiker zich aanmelden en toestemming geven.
- U abonneert u op de web-app om de autorisatiecode te ontvangen. Dit abonnement wordt gedaan met behulp van een C#-gemachtigde.
- Wanneer de autorisatiecode is ontvangen, gebruikt u MSAL-bibliotheken om deze in te wisselen. De resulterende toegangstokens en vernieuwingstokens worden opgeslagen in de tokencache. De cache kan worden gebruikt in andere delen van de toepassing, zoals controllers, om andere tokens in stilte te verkrijgen.

Codevoorbeelden in dit artikel en de volgende worden uit de [ASP.NET Core-webapp incrementele zelfstudie, hoofdstuk 2 ,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)gehaald. U verwijzen naar die tutorial voor de volledige implementatie details.

> [!NOTE]
> Om de codevoorbeelden hier volledig te begrijpen, moet u vertrouwd zijn met [ASP.NET Core-fundamenten,](https://docs.microsoft.com/aspnet/core/fundamentals)en in het bijzonder met [afhankelijkheidsinjectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) en [-opties](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Omdat de aanmelding van de gebruiker is gedelegeerd aan de Middenware van De Open ID Connect (OIDC), moet u communiceren met het OIDC-proces. Hoe u met elkaar omgaat, is afhankelijk van het framework dat u gebruikt.

Voor ASP.NET, u zich abonneren op middleware OIDC evenementen:

- Je laat ASP.NET Core een autorisatiecode aanvragen via de Open ID Connect middleware. ASP.NET of ASP.NET Core laat de gebruiker zich aanmelden en toestemming geven.
- U abonneert u op de web-app om de autorisatiecode te ontvangen. Dit abonnement wordt gedaan met behulp van een C#-gemachtigde.
- Wanneer de autorisatiecode is ontvangen, gebruikt u MSAL-bibliotheken om deze in te wisselen. De resulterende toegangstokens en vernieuwingstokens worden opgeslagen in de tokencache. De cache kan worden gebruikt in andere delen van de toepassing, zoals controllers, om andere tokens in stilte te verkrijgen.

Codevoorbeelden in dit artikel en de volgende worden uit het voorbeeld van de [ASP.NET web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)gehaald. U verwijzen naar dat voorbeeld voor volledige implementatiedetails.

# <a name="java"></a>[Java](#tab/java)

Codevoorbeelden in dit artikel en de volgende worden geëxtraheerd uit de [Java-webtoepassing die Microsoft Graph aanroept](https://github.com/Azure-Samples/ms-identity-java-webapp), een voorbeeld van een web-app dat MSAL voor Java gebruikt.
In het voorbeeld kan MSAL voor Java momenteel de URL van autorisatiecode produceren en wordt de navigatie naar het autorisatieeindpunt voor het Microsoft-identiteitsplatform verwerkt. Het is ook mogelijk om Sprint-beveiliging te gebruiken om de gebruiker aan te melden. U verwijzen naar het voorbeeld voor de volledige implementatiedetails.

# <a name="python"></a>[Python](#tab/python)

Codevoorbeelden in dit artikel en de volgende worden geëxtraheerd uit de [Python-webtoepassing die Microsoft Graph aanroept,](https://github.com/Azure-Samples/ms-identity-python-webapp)een voorbeeld van een web-app dat MSAL gebruikt. Python.
Het voorbeeld laat momenteel MSAL. Python produceert de URL van autorisatiecode en verwerkt de navigatie naar het autorisatieeindpunt voor het Microsoft-identiteitsplatform. U verwijzen naar het voorbeeld voor de volledige implementatiedetails.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code waarmee de autorisatiecode wordt ingewisseld

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

In ASP.NET Core, `Startup.cs` in het bestand, abonneert u zich op het `OnAuthorizationCodeReceived` OpenID Connect-evenement. Roep vanaf deze gebeurtenis `AcquireTokenFromAuthorizationCode` de MSAL.NET-methode aan. Met deze methode worden de volgende tokens opgeslagen in de tokencache:

- Het *toegangstoken* voor `scopes`het gevraagde .
- Een *vernieuwingstoken*. Dit token wordt gebruikt om het toegangstoken te vernieuwen wanneer het bijna verloopt, of om een ander token te krijgen namens dezelfde gebruiker, maar voor een andere bron.

De [ASP.NET Core Web app tutorial](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) biedt u herbruikbare code voor uw web apps.

Hieronder volgt de code van [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Het beschikt over oproepen naar:

- De `AddMicrosoftIdentityPlatformAuthentication` methode, die verificatie toevoegt aan de web-app.
- De `AddMsal` methode, die de mogelijkheid toevoegt om web-API's aan te roepen.
- De `AddInMemoryTokenCaches` methode, die gaat over het kiezen van een token-cache implementatie.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead`wordt gedefinieerd in [Constants.cs#L5:](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

U hebt de inhoud `AddMicrosoftIdentityPlatformAuthentication` van de [web-app die gebruikers aantekent](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)- codeconfiguratie - al bestudeerd.

### <a name="the-addmsal-method"></a>De methode Addmsal

De code `AddMsal` voor bevindt zich in [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

De `AddMsal` methode zorgt ervoor dat:

- De ASP.NET Core-webapp vraagt zowel een ID-token`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`voor de gebruiker als een verificatiecode ( ).
- Het `offline_access` bereik wordt toegevoegd. Dit bereik verkrijgt toestemming van de gebruiker voor de toepassing om een vernieuwingstoken te krijgen.
- De app schrijft zich `OnAuthorizationCodeReceived` in voor het OIDC-evenement en wisselt de oproep in met `ITokenAcquisition`behulp van MSAL.NET, die hier is ingekapseld in een herbruikbare component die implementeert.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>De methode TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

De `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` methode bevindt zich in [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Het zorgt ervoor dat:

- ASP.NET probeert de verificatiecode niet parallel aan`context.HandleCodeRedemption();`MSAL.NET ( ) in te wisselen.
- De claims in het ID-token zijn beschikbaar voor MSAL om een tokencachesleutel voor het account van de gebruiker te berekenen.
- Indien nodig wordt een instantie van de MSAL.NET toepassing gemaakt.
- De code wordt ingewisseld door de MSAL.NET toepassing.
- Het nieuwe ID-token wordt tijdens het `context.HandleCodeRedemption(null, result.IdToken);`gesprek met ASP.NET Core gedeeld met . Het toegangstoken wordt niet gedeeld met ASP.NET Core. Het blijft in de MSAL.NET tokencache die is gekoppeld aan de gebruiker, waar het klaar is om te worden gebruikt in ASP.NET Core-controllers.

Hier is de relevante `TokenAcquisition`code voor:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>De tokenacquisition.buildconfidentialclienttoepassingsmethode

In ASP.NET Core gebruikt het bouwen van de `HttpContext`vertrouwelijke clienttoepassing informatie die zich in de . De `HttpContext` bijbehorende aan de aanvraag `CurrentHttpContext` wordt geopend met behulp van de eigenschap. `HttpContext`heeft informatie over de URL voor de web-app en `ClaimsPrincipal`over de aangemelde gebruiker (in een ). 

De `BuildConfidentialClientApplication` methode maakt ook gebruik van de ASP.NET Core-configuratie. De configuratie heeft een sectie 'AzureAD' en is ook gebonden aan beide volgende elementen:

- De `_applicationOptions` gegevensstructuur van type [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- De `azureAdOptions` instantie van het type [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), gedefinieerd in ASP.NET Core `Authentication.AzureAD.UI`.

Ten slotte moet de toepassing tokencaches onderhouden. Daar kom je meer over te weten in het volgende gedeelte.

De code `GetOrBuildConfidentialClientApplication()` voor de methode is in [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Het maakt gebruik van leden die werden geïnjecteerd door `TokenAcquisition` afhankelijkheidinjectie (doorgegeven in de constructor van in [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Hier is de `GetOrBuildConfidentialClientApplication`code voor:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Samenvatting

`AcquireTokenByAuthorizationCode`is echt de methode die de autorisatiecode inwisselt die ASP.NET aanvragen, en dat de tokens krijgt die worden toegevoegd aan MSAL.NET user-token cache. Vanuit de cache worden de tokens vervolgens gebruikt in de ASP.NET Core-controllers.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET behandelt zaken die vergelijkbaar zijn met ASP.NET Core, behalve dat `OnAuthorizationCodeReceived` de configuratie van OpenID Connect en het abonnement op de gebeurtenis plaatsvinden in het [bestand App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) De concepten zijn ook vergelijkbaar met die in ASP.NET Core, behalve dat in ASP.NET moet u de `RedirectUri` in [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)opgeven . Deze configuratie is iets minder robuust dan die in ASP.NET Core, omdat u deze moet wijzigen wanneer u uw toepassing implementeert.

Hier is de code voor Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Zie [Web-app die gebruikers aantekent: Codeconfiguratie](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) om te begrijpen hoe het Java-voorbeeld de autorisatiecode krijgt. Nadat de app de code heeft ontvangen, is de [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Gemachtigden `AuthHelper.processAuthenticationCodeRedirect` naar de methode in [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Oproepen `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

De `getAuthResultByAuthCode` methode wordt gedefinieerd in [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Het maakt een `ConfidentialClientApplication`MSAL, `acquireToken()` `AuthorizationCodeParameters` en vervolgens gesprekken met gemaakt van de autorisatiecode.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

De autorisatiecodestroom wordt aangevraagd zoals weergegeven in [de web-app die gebruikers aantekent: Codeconfiguratie](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). De code wordt vervolgens `authorized` ontvangen op de `/getAToken` functie, die Flask routes van de URL. Zie [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) voor de volledige context van deze code:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

In plaats van een klantgeheim, kan de vertrouwelijke clienttoepassing ook zijn identiteit bewijzen met behulp van een clientcertificaat of een clientbewering.
Het gebruik van client beweringen is een geavanceerd scenario, gedetailleerd in [client beweringen](msal-net-client-assertions.md).

## <a name="token-cache"></a>Tokencache

> [!IMPORTANT]
> De token-cache implementatie voor web apps of web API's is anders dan de implementatie voor desktop toepassingen, die vaak [op basis van bestanden](scenario-desktop-acquire-token.md#file-based-token-cache).
> Om redenen van veiligheid en prestaties is het belangrijk om ervoor te zorgen dat er voor web-apps en web-API's één tokencache per gebruikersaccount is. U moet de tokencache voor elk account serialiseren.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

De ASP.NET kernzelfstudie maakt gebruik van afhankelijkheidsinjectie om u de implementatie van de tokencache in het Startup.cs-bestand voor uw toepassing te laten beslissen. Microsoft.Identity.Web wordt geleverd met vooraf gebouwde token-cache serialisten beschreven in [Token cache serialisatie](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Een interessante mogelijkheid is om te kiezen ASP.NET Core [gedistribueerde geheugen caches:](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Zie ook de ASP.NET Core Web [app tutorials | Token caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fase van de tutorial.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

De token-cache implementatie voor web apps of web API's is anders dan de implementatie voor desktop toepassingen, die vaak [op basis van bestanden](scenario-desktop-acquire-token.md#file-based-token-cache).

De web-app implementatie kan de ASP.NET sessie of het servergeheugen gebruiken. Zie bijvoorbeeld hoe de cache-implementatie is aangesloten na het maken van de MSAL.NET-toepassing in [MsalAppBuilder.cs#L39-L51:](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java biedt methoden om de tokencache te serialiseren en te deserialiseren. Het Java-voorbeeld verwerkt de serialisatie van `getAuthResultBySilentFlow` de sessie, zoals weergegeven in de methode in [AuthHelper.java#L99-L122:](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

De details `SessionManagementHelper` van de klasse is opgenomen in de [MSAL monster voor Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

In het Python-voorbeeld wordt één cache per account gegarandeerd door voor elk verzoek een vertrouwelijke clienttoepassing opnieuw te maken en deze vervolgens te serialiseren in de cache van de sessiein de periode van de fles:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Volgende stappen

Op dit punt, wanneer de gebruiker zich aanmeldt, wordt een token opgeslagen in de tokencache. Laten we eens kijken hoe het vervolgens wordt gebruikt in andere delen van de web-app.

> [!div class="nextstepaction"]
> [Een web-app die web-API's aanroept: accounts verwijderen uit de cache bij wereldwijde afmelding](scenario-web-app-call-api-sign-in.md)
