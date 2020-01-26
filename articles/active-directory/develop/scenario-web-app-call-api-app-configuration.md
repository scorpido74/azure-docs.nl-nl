---
title: Een web-app configureren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het configureren van de code van een web-app die web-Api's aanroept
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759157"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Een web-app die web-Api's aanroept: code configuratie

Zoals wordt weer gegeven in de [Web-app die het scenario voor gebruikers aanmeldt](scenario-web-app-sign-user-overview.md) , gebruikt de Web-App de [OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md) om de gebruiker te ondertekenen in. Deze stroom heeft twee stappen:

1. Een autorisatie code aanvragen. Dit onderdeel delegeert een persoonlijke dialoog met de gebruiker naar het micro soft Identity-platform. Tijdens dat dialoog venster wordt de gebruiker aangemeld en wordt er gebruikgemaakt van het gebruik van web-Api's. Wanneer de persoonlijke dialoog is beëindigd, ontvangt de web-app een autorisatie code op de omleidings-URI.
1. Een toegangs token voor de API aanvragen door de autorisatie code in te wisselen.

De [Web-app die zich in gebruikers scenario's aanmeldt](scenario-web-app-sign-user-overview.md) , bedekt alleen de eerste stap. Hier leest u hoe u uw web-app kunt aanpassen, zodat u niet alleen gebruikers in ondertekent, maar ook Web-Api's aanroept.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotheken die scenario's voor web-apps ondersteunen

De volgende bibliotheken in de micro soft Authentication Library (MSAL) ondersteunen de autorisatie code stroom voor web-apps:

| MSAL-bibliotheek | Beschrijving |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Ondersteuning voor .NET Framework-en .NET core-platforms. Niet ondersteund zijn Universeel Windows-platform (UWP), Xamarin. iOS en Xamarin. Android, omdat deze platformen worden gebruikt voor het bouwen van open bare client toepassingen. |
| ![MSAL python](media/sample-v2-code/logo_python.png) <br/> MSAL voor Python | Ondersteuning voor python-webtoepassingen. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL voor Java | Ondersteuning voor Java-webtoepassingen. |

Selecteer het tabblad voor het platform waarin u bent geïnteresseerd:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Omdat het aanmelden van de gebruiker is gedelegeerd naar de middleware Open ID Connect (OIDC), moet u communiceren met het OIDC-proces. Hoe u communiceert, is afhankelijk van het Framework dat u gebruikt.

Voor ASP.NET Core meldt u zich aan bij middleware OIDC-gebeurtenissen:

- U kunt ASP.NET Core een autorisatie code aanvragen met behulp van de Open ID Connect middleware. Met ASP.NET of ASP.NET Core kunnen gebruikers zich aanmelden en toestemming geven.
- U bent geabonneerd op de web-app om de autorisatie code te ontvangen. Dit abonnement wordt uitgevoerd met behulp C# van een gemachtigde.
- Wanneer de autorisatie code wordt ontvangen, gebruikt u MSAL-bibliotheken om deze te inwisselen. De resulterende toegangs tokens en vernieuwings tokens worden opgeslagen in de token cache. De cache kan worden gebruikt in andere onderdelen van de toepassing, zoals controllers, om andere tokens op de achtergrond te verkrijgen.

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de [Stapsgewijze zelf studie voor de ASP.net core-web-app, hoofd stuk 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Mogelijk wilt u deze zelf studie raadplegen voor volledige implementatie details.

> [!NOTE]
> Als u de code voorbeelden hier volledig wilt begrijpen, moet u bekend zijn met [ASP.net core fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals), met name bij het [invoegen van afhankelijkheden](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) en [Opties](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Omdat het aanmelden van de gebruiker is gedelegeerd naar de middleware Open ID Connect (OIDC), moet u communiceren met het OIDC-proces. Hoe u communiceert, is afhankelijk van het Framework dat u gebruikt.

Voor ASP.NET kunt u zich abonneren op middleware OIDC-gebeurtenissen:

- U kunt ASP.NET Core een autorisatie code aanvragen met behulp van de Open ID Connect middleware. Met ASP.NET of ASP.NET Core kunnen gebruikers zich aanmelden en toestemming geven.
- U bent geabonneerd op de web-app om de autorisatie code te ontvangen. Dit abonnement wordt uitgevoerd met behulp C# van een gemachtigde.
- Wanneer de autorisatie code wordt ontvangen, gebruikt u MSAL-bibliotheken om deze te inwisselen. De resulterende toegangs tokens en vernieuwings tokens worden opgeslagen in de token cache. De cache kan worden gebruikt in andere onderdelen van de toepassing, zoals controllers, om andere tokens op de achtergrond te verkrijgen.

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Mogelijk wilt u dit voor beeld raadplegen voor volledige implementatie details.

# <a name="javatabjava"></a>[Java](#tab/java)

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de Java- [Webtoepassing die Microsoft Graph aanroept](https://github.com/Azure-Samples/ms-identity-java-webapp), een voor beeld van een web-app dat gebruikmaakt van MSAL voor Java.
Met het voor beeld wordt momenteel MSAL voor Java de autorisatie code-URL geproduceerd en wordt de navigatie naar het verificatie-eind punt voor het micro soft Identity-platform verwerkt. Het is ook mogelijk Sprint beveiliging te gebruiken om de gebruiker te ondertekenen in. Mogelijk wilt u het voor beeld voor volledige implementatie gegevens raadplegen.

# <a name="pythontabpython"></a>[Python](#tab/python)

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de python- [webtoepassing Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), een voor beeld van een web-app dat gebruikmaakt van MSAL. Python.
Met het voor beeld kunt u momenteel MSAL. Python produceert de autorisatie code-URL en verwerkt de navigatie naar het autorisatie-eind punt voor het micro soft Identity-platform. Mogelijk wilt u het voor beeld voor volledige implementatie gegevens raadplegen.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code die de autorisatie code inwisselt

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

In ASP.NET Core kunt u in het `Startup.cs`-bestand zich abonneren op de `OnAuthorizationCodeReceived` OpenID Connect Connect-gebeurtenis. Roep de methode MSAL.NET `AcquireTokenFromAuthorizationCode` aan bij deze gebeurtenis. Met deze methode worden de volgende tokens opgeslagen in de token cache:

- Het *toegangs token* voor de aangevraagde `scopes`.
- Een *vernieuwings token*. Dit token wordt gebruikt om het toegangs token te vernieuwen wanneer het bijna is verlopen, of om een ander token namens dezelfde gebruiker te verkrijgen, maar voor een andere bron.

De [ASP.net core-zelf studie voor web-apps](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) biedt u herbruikbare code voor uw web-apps.

Hier volgt de code van [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). De IT-functie roept het volgende aan:

- De `AddMicrosoftIdentityPlatformAuthentication` methode waarmee verificatie wordt toegevoegd aan de web-app.
- De methode `AddMsal`, die de mogelijkheid biedt om Web-Api's aan te roepen.
- De methode `AddInMemoryTokenCaches`, die wordt gebruikt voor het kiezen van een token-cache-implementatie.

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

`Constants.ScopeUserRead` is gedefinieerd in [constanten. cs # N5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

U hebt de inhoud van `AddMicrosoftIdentityPlatformAuthentication` al bestudeerd in de [Web-app die de gebruikers code configuratie tekent](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>De methode AddMsal

De code voor `AddMsal` bevindt zich in [micro soft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

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

- De ASP.NET Core web-app vraagt zowel een ID-token voor de gebruiker als een verificatie code (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- Het `offline_access` bereik wordt toegevoegd. Dit bereik vraagt de gebruiker toestemming voor de toepassing om een vernieuwings token op te halen.
- De app wordt geabonneerd op de OIDC-`OnAuthorizationCodeReceived` gebeurtenis en de oproep wordt ingewisseld met behulp van MSAL.NET, die hier wordt ingekapseld in een herbruikbaar onderdeel dat `ITokenAcquisition`implementeert.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>De methode TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

De `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` methode bevindt zich in [micro soft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Dit zorgt ervoor dat:

- ASP.NET probeert de verificatie code niet parallel in te wisselen met MSAL.NET (`context.HandleCodeRedemption();`).
- De claims in het ID-token zijn beschikbaar voor MSAL om een token cache sleutel te berekenen voor het account van de gebruiker.
- Als dat nodig is, wordt een exemplaar van de MSAL.NET-toepassing gemaakt.
- De code wordt ingewisseld door de MSAL.NET-toepassing.
- Het nieuwe ID-token wordt gedeeld met ASP.NET Core tijdens het aanroepen van `context.HandleCodeRedemption(null, result.IdToken);`. Het toegangs token is niet gedeeld met ASP.NET Core. Het blijft in de MSAL.NET-token cache die aan de gebruiker is gekoppeld, waar deze klaar is om te worden gebruikt in ASP.NET Core-controllers.

Hier volgt de relevante code voor `TokenAcquisition`:

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>De methode TokenAcquisition. BuildConfidentialClientApplication

In ASP.NET Core maakt het bouwen van de vertrouwelijke client toepassing gebruik van informatie in de `HttpContext`. De `HttpContext` die aan de aanvraag is gekoppeld, wordt geopend met behulp van de eigenschap `CurrentHttpContext`. `HttpContext` heeft informatie over de URL voor de web-app en over de aangemelde gebruiker (in een `ClaimsPrincipal`). 

De methode `BuildConfidentialClientApplication` gebruikt ook de ASP.NET Core configuratie. De configuratie bevat een sectie ' AzureAD ' en is ook gekoppeld aan de volgende elementen:

- De `_applicationOptions` gegevens structuur van het type [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- Het `azureAdOptions` exemplaar van het type [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs), gedefinieerd in ASP.net core `Authentication.AzureAD.UI`.

Ten slotte moet de toepassing token caches onderhouden. Meer informatie hierover vindt u in de volgende sectie.

De code voor de `GetOrBuildConfidentialClientApplication()` methode bevindt zich in [micro soft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Het maakt gebruik van leden die zijn geïnjecteerd door de injectie van de afhankelijkheid (door gegeven in de constructor van `TokenAcquisition` in [micro soft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Hier volgt de code voor `GetOrBuildConfidentialClientApplication`:

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

`AcquireTokenByAuthorizationCode` is in feite de methode die de autorisatie code gebruikt die ASP.NET aanvragen, en die de tokens ophaalt die worden toegevoegd aan MSAL.NET User-token cache. De tokens worden vervolgens uit de cache gebruikt in de ASP.NET Core-controllers.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET verwerkt dingen op dezelfde manier als ASP.NET Core, behalve dat de configuratie van OpenID Connect Connect en het abonnement op de `OnAuthorizationCodeReceived` gebeurtenis plaatsvindt in het [App_Start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -bestand. De concepten zijn ook vergelijkbaar met die in ASP.NET Core, behalve dat u in ASP.NET de `RedirectUri` moet opgeven in [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Deze configuratie is iets minder krachtig dan het in ASP.NET Core, omdat u het moet wijzigen wanneer u de toepassing implementeert.

Hier volgt de code voor Startup.Auth.cs:

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

# <a name="javatabjava"></a>[Java](#tab/java)

Bekijk de [Web-app die zich in gebruikers aanmeldt: code configuratie](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) om te begrijpen hoe het Java-voor beeld de autorisatie code ontvangt. Nadat de app de code heeft ontvangen, wordt de [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Gemachtigden aan de `AuthHelper.processAuthenticationCodeRedirect`-methode in [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Roept `getAuthResultByAuthCode`op.

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

De `getAuthResultByAuthCode` methode is gedefinieerd in [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Er wordt een MSAL-`ConfidentialClientApplication`gemaakt en vervolgens `acquireToken()` aangeroepen met `AuthorizationCodeParameters` die zijn gemaakt op basis van de autorisatie code.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

De autorisatie code stroom is aangevraagd zoals weer gegeven in [de web-app die zich aanmeldt bij gebruikers: code configuratie](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). De code wordt vervolgens ontvangen op de `authorized` functie, die de kolf van de `/getAToken`-URL stuurt. Zie [app. py # l30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) voor de volledige context van deze code:

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

In plaats van een client geheim kan de vertrouwelijke client toepassing ook de identiteit bewijzen met behulp van een client certificaat of een client bevestiging.
Het gebruik van client verklaringen is een geavanceerd scenario dat wordt beschreven in [client verklaringen](msal-net-client-assertions.md).

## <a name="token-cache"></a>Token cache

> [!IMPORTANT]
> De implementatie van de token-cache voor web-apps of Web-Api's wijkt af van de implementatie voor desktop toepassingen, die vaak [op bestanden is gebaseerd](scenario-desktop-acquire-token.md#file-based-token-cache).
> Uit veiligheids overwegingen is het belang rijk om ervoor te zorgen dat voor web-apps en Web-Api's één token cache per gebruikers account is. U moet de token cache voor elk account serialiseren.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

De zelf studie voor ASP.NET core maakt gebruik van afhankelijkheids injectie om u te laten bepalen welke implementatie van de token cache in het Startup.cs-bestand voor uw toepassing. Micro soft. Identity. web wordt geleverd met vooraf ontwikkelde token-cache-serialisatiefunctie beschreven in de [serialisatie van de token cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Een interessante mogelijkheid is om ASP.NET Core [gedistribueerde geheugen caches](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)te kiezen:

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

Zie ook de ASP.NET Core zelf studies voor de web-app voor meer informatie over de token-cache providers [| De token caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fase van de zelf studie.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

De implementatie van de token-cache voor web-apps of Web-Api's wijkt af van de implementatie voor desktop toepassingen, die vaak [op bestanden is gebaseerd](scenario-desktop-acquire-token.md#file-based-token-cache).

De implementatie van de web-app kan gebruikmaken van de ASP.NET-sessie of het server geheugen. Zie bijvoorbeeld hoe de cache-implementatie is gehookd na het maken van de MSAL.NET-toepassing in [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java biedt methoden voor het serialiseren en deserialiseren van de token cache. In het Java-voor beeld wordt de serialisatie vanuit de sessie afgehandeld, zoals wordt weer gegeven in de methode `getAuthResultBySilentFlow` in [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

De details van de `SessionManagementHelper` klasse vindt u in het [MSAL-voor beeld voor Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="pythontabpython"></a>[Python](#tab/python)

In het python-voor beeld wordt één cache per account gegarandeerd door het opnieuw maken van een vertrouwelijke client toepassing voor elke aanvraag en deze vervolgens te serialiseren in de kolf sessie cache:

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

Wanneer de gebruiker zich aanmeldt, wordt er op dit punt een token opgeslagen in de token cache. Laten we eens kijken hoe het wordt gebruikt in andere onderdelen van de web-app.

> [!div class="nextstepaction"]
> [Een web-app die web-Api's aanroept: Verwijder accounts uit de cache op Global Sign-out](scenario-web-app-call-api-sign-in.md)
