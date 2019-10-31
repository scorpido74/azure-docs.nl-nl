---
title: Web-app die web-Api's aanroept (code configuratie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (de code configuratie van de app)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231ecdb6afae1fc36d11b2c12aa82c7e860bb708
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175317"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Web-app die web-Api's aanroept-code configuratie

Zoals u kunt zien in het [scenario web app Sign-in gebruikers](scenario-web-app-sign-user-overview.md), gebruikt de Web-App de OAuth 2.0- [autorisatie code stroom](v2-oauth2-auth-code-flow.md) om zich aan te melden bij de gebruiker. Deze stroom bevindt zich in twee delen:

1. Een autorisatie code aanvragen. Dit onderdeel delegeert het micro soft Identity platform een persoonlijk dialoog venster met de gebruiker. De gebruiker meldt zich aan en ontvangt toestemming voor het gebruik van web-Api's. Wanneer dit persoonlijke dialoog venster is voltooid, ontvangt de toepassing een autorisatie code op de omleidings-URI.
1. Een toegangs token voor de API aanvragen door de autorisatie code in te wisselen.

Het [scenario voor web-app-aanmeld gebruikers](scenario-web-app-sign-user-overview.md) werd alleen de eerste poot. Hier vindt u informatie over het wijzigen van de Web-API die gebruikers aanmeldt, zodat deze nu web-API'S aanroept.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotheken die web-app-scenario's ondersteunen

De bibliotheken die de autorisatie code stroom ondersteunen voor web-apps zijn:

| MSAL-bibliotheek | Beschrijving |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Ondersteunde platforms zijn .NET Framework en .NET Core-platformen (niet UWP, Xamarin. iOS en Xamarin. Android als deze platformen worden gebruikt voor het bouwen van open bare client toepassingen) |
| ![MSAL python](media/sample-v2-code/logo_python.png) <br/> MSAL python | Ontwikkeling in uitvoering-in open bare preview |
| ![MSAL java](media/sample-v2-code/logo_java.png) <br/> MSAL java | Ontwikkeling in uitvoering-in open bare preview |

Selecteer het tabblad dat overeenkomt met het platform waarin u bent geïnteresseerd:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

De gebruiker die zich aanmeldt, wordt overgedragen aan de middleware Open ID Connect (OIDC), die u wilt aansluiten op het OIDC-proces. De manier om dit te doen, is afhankelijk van het Framework dat u gebruikt.
In het geval van ASP.NET Core kunt u zich abonneren op middleware OIDC-gebeurtenissen. Het principe is dat:

- U laat ASP.NET core een autorisatie code aanvragen via de Open ID Connect middleware. Door deze ASP.NET/ASP.NET-kern te doen, kunnen gebruikers zich aanmelden en toestemming geven
- U abonneert u op de ontvangst van de autorisatie code door de web-app. Dit abonnement wordt uitgevoerd via een C# gemachtigde.
- Wanneer de verificatie code wordt ontvangen, gebruikt u MSAL-bibliotheken voor het inwisselen van de code en worden de resulterende toegangs tokens en vernieuwings tokens opgeslagen in de token cache. Van daaruit kan de cache worden gebruikt in andere onderdelen van de toepassing, bijvoorbeeld in controllers, om andere tokens op de achtergrond te verkrijgen.

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de [Stapsgewijze zelf studie voor de ASP.net core-web-app, hoofd stuk 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Raadpleeg deze zelf studie voor meer informatie over de volledige implementatie.

> [!NOTE]
> Voor een volledig begrip van de onderstaande code fragmenten moet u bekend zijn met [ASP.net core fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals), en met name [afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) en- [Opties](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

De gebruiker die zich aanmeldt, wordt overgedragen aan de middleware Open ID Connect (OIDC), die u wilt aansluiten op het OIDC-proces. De manier om dit te doen, is afhankelijk van het Framework dat u gebruikt.
In het geval van ASP.NET kunt u zich abonneren op middleware OIDC-gebeurtenissen. Het principe is dat:

- U laat ASP.NET core een autorisatie code aanvragen via de Open ID Connect middleware. Door deze ASP.NET/ASP.NET-kern te doen, kunnen gebruikers zich aanmelden en toestemming geven
- U abonneert u op de ontvangst van de autorisatie code door de web-app. Dit is een C# gemachtigde.
- Wanneer de verificatie code wordt ontvangen, gebruikt u MSAL-bibliotheken voor het inwisselen van de code. De resulterende toegangs tokens en vernieuwings tokens zijn, vervolgens opgeslagen in de token cache. Van daaruit kan de cache worden gebruikt in andere onderdelen van de toepassing, bijvoorbeeld in controllers, om andere tokens op de achtergrond te verkrijgen.

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

# <a name="javatabjava"></a>[Java](#tab/java)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de Java- [Webtoepassing die micro soft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) MSAL Java Web app-voor beeld aanroept.
Met het voor beeld kan MSAL Java de autorisatie code-URL produceren en de navigatie naar het micro soft Identity platform authoring-eind punt verwerken. Het is ook mogelijk om Sprint beveiliging te gebruiken om u aan te melden bij de gebruiker. U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

# <a name="pythontabpython"></a>[Python](#tab/python)

Code fragmenten in dit artikel en het volgende worden opgehaald uit de python- [Webtoepassing die micro soft Graph MSAL aanroept](https://github.com/Azure-Samples/ms-identity-python-webapp) . Voor beeld van Python-web-app.
Met het voor beeld kunt u momenteel MSAL. Python produceert de autorisatie code-URL en verwerkt de navigatie naar het micro soft Identity platform authoring-eind punt. U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code die de autorisatie code inwisselt

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

In ASP.NET Core is het principe dat in het `Startup.cs` bestand. U moet zich abonneren op de `OnAuthorizationCodeReceived` Open ID Connect-gebeurtenis en van deze gebeurtenis roept u MSAL aan. De methode van de NET-`AcquireTokenFromAuthorizationCode`, die het gevolg is van de opslag in de token cache, het toegangs token voor de aangevraagde `scopes`en een vernieuwings token dat wordt gebruikt voor het vernieuwen van het toegangs token wanneer het bijna is verlopen, of om een token namens dezelfde gebruiker te verkrijgen , maar voor een andere resource.

In de praktijk wordt de [ASP.net core-zelf studie voor web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) -apps geprobeerd u te voorzien van herbruikbare code voor uw web-apps.

Dit is de code [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) , met de aanroep naar de `AddMicrosoftIdentityPlatformAuthentication` methode waarmee verificatie wordt toegevoegd aan de web-app en `AddMsal` die de mogelijkheid biedt om Web-api's aan te roepen. De aanroep van `AddInMemoryTokenCaches` is het kiezen van de implementatie van een token cache tussen de volgende items:

```CSharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` is gedefinieerd in [constanten. cs # N5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

U hebt de inhoud van `AddMicrosoftIdentityPlatformAuthentication` in de [Web-app die de gebruikers code configuratie meldt](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code) , al bestudeerd

### <a name="the-addmsal-method"></a>De methode AddMsal

De code voor `AddMsal` bevindt zich in [micro soft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```CSharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
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

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
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

- de ASP.NET Core web-app vraagt zowel een IDToken voor de gebruiker als een verificatie code (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)
- het `offline_access` bereik wordt toegevoegd. Het is nodig om ervoor te zorgen dat de gebruiker een vernieuwings token krijgt om de toepassing te laten toesturen.
- de app wordt geabonneerd op de OIDC-`OnAuthorizationCodeReceived` gebeurtenis en wisselt de aanroep met behulp van MSAL.NET, die hier is ingekapseld in een herbruikbaar onderdeel dat `ITokenAcquisition`implementeert.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>De methode TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

De `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` methode bevindt zich in [micro soft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Dit zorgt ervoor dat:

- ASP.NET probeert de verificatie code niet parallel in te wisselen met MSAL.NET (`context.HandleCodeRedemption();`)
- De claims in de IDToken zijn beschikbaar voor MSAL om een token cache sleutel te berekenen voor het account van de gebruiker
- de MSAL.NET-toepassing wordt indien nodig geïnstantieerd
- de code wordt ingewisseld door de MSAL.NET-toepassing
- Het nieuwe ID-token wordt gedeeld met ASP.NET Core (tijdens het aanroepen van `context.HandleCodeRedemption(null, result.IdToken);`). Het toegangs token is niet gedeeld met ASP.NET Core. Het blijft in de MSAL.NET-token cache die aan de gebruiker is gekoppeld, waar deze klaar is om te worden gebruikt in ASP.NET Core-controllers.

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
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
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

In ASP.NET Core maakt het samen stellen van de vertrouwelijke client toepassing gebruik van gegevens die zich in de HTTP context bevinden. Toegang via de eigenschap `CurrentHttpContext`, de HTTP context, die aan de aanvraag is gekoppeld, kent de URL voor de web-app en de aangemelde gebruiker (in een `ClaimsPrincipal`). De `BuildConfidentialClientApplication` gebruikt ook de ASP.NET Core Configuratie, die een sectie ' AzureAD ' bevat, en die is gebonden aan:

- de `_applicationOptions` gegevens structuur van het type [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- het `azureAdOptions` exemplaar van het type [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) gedefinieerd in ASP.net core `Authentication.AzureAD.UI`. Ten slotte moet de toepassing token caches onderhouden. Meer informatie hierover vindt u in de volgende sectie.

De code voor de `GetOrBuildConfidentialClientApplication()` methode bevindt zich in [micro soft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). De functie maakt gebruik van leden die zijn geïnjecteerd door het injecteren van afhankelijkheden (door gegeven in de constructor van TokenAcquisition in [micro soft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```CSharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
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

Als u de som wilt berekenen, worden `AcquireTokenByAuthorizationCode` de autorisatie code die door ASP.NET is aangevraagd, in feite ingewisseld en worden de tokens opgehaald die zijn toegevoegd aan de cache van MSAL.NET-gebruikers tokens. Van daaruit worden ze vervolgens gebruikt in de ASP.NET Core-controllers.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

De manier waarop ASP.NET items afhandelt, is vergelijkbaar met ASP.NET Core, behalve dat de configuratie van OpenIdConnect en het abonnement op de `OnAuthorizationCodeReceived` gebeurtenis plaatsvindt in het [App_Start\Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -bestand. U vindt soort gelijke concepten als in ASP.NET Core, maar in ASP.NET moet u de RedirectUri in het [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)opgeven. Deze configuratie is een beetje minder krachtig dan wat er in ASP.NET Core gebeurt, omdat u deze moet wijzigen wanneer u de toepassing implementeert.

```CSharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

Zie in [Web-app die gebruikers code configuratie aanmeldt](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) om te begrijpen hoe de verificatie code wordt opgehaald met het Java-voor beeld. Zodra de app is ontvangen, worden de [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) overgedragen aan de methode `AuthHelper.processAuthenticationCodeRedirect` in [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97), en wordt vervolgens `getAuthResultByAuthCode`aangeroepen:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

De `getAuthResultByAuthCode` methode is gedefinieerd in [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Er wordt een MSAL-`ConfidentialClientApplication` gemaakt en `acquireToken()` aangeroepen met `AuthorizationCodeParameters` die zijn gemaakt op basis van de autorisatie code.

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

Zodra de autorisatie code stroom is aangevraagd in een [Web-app die de gebruikers code configuratie aanmeldt](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), wordt de autorisatie code ontvangen op de `authorized` functie die door de/GETATOKEN-URL wordt gerouteerd. Zie [app. py # l30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
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
Het gebruik van client verklaringen is een geavanceerd scenario dat wordt beschreven in [client verklaringen](msal-net-client-assertions.md)

## <a name="token-cache"></a>Token cache

> [!IMPORTANT]
> In web-apps (of Web-Api's als belang rijk) verschilt de implementatie van de token cache van de implementaties van de token cache van het Desktop-toepassing (dit is vaak [gebaseerd op een bestand](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Het is belang rijk om veiligheids-en prestatie redenen ervoor te zorgen dat voor web-apps en Web-Api's één token cache per gebruiker (per account) moet zijn. U moet de token cache voor elk account serialiseren.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

De zelf studie voor ASP.NET core maakt gebruik van afhankelijkheids injectie om u te laten bepalen welke implementatie van de token cache in het Startup.cs-bestand voor uw toepassing. Micro soft. Identity. web wordt geleverd met een aantal vooraf gemaakte serialisatiefunctie voor de token cache die wordt beschreven in de [serialisatie](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)van de token-cache. Een interessante mogelijkheid is om ASP.NET Core [gedistribueerde geheugen caches](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)te kiezen:

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Zie ook de ASP.NET Core zelf studies voor de web-app voor meer informatie over de token-cache providers [| De token caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fase van de zelf studie

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In web-apps (of Web-Api's als belang rijk) verschilt de implementatie van de token cache van de implementaties van de token cache van het Desktop-toepassing (dit is vaak [gebaseerd op bestanden](scenario-desktop-acquire-token.md#file-based-token-cache). Het kan de ASP.NET-sessie of het server geheugen gebruiken. Zie voor instantie hoe de cache-implementatie is gehookd na het maken van de MSAL.NET [-toepassing in MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java biedt methoden voor het serialiseren en deserialiseren van de token cache. Het Java-voor beeld verwerkt de serialisatie van de sessie, zoals wordt geïllustreerd in de methode `getAuthResultBySilentFlow` in [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

De details van de `SessionManagementHelper` klasse vindt u in[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

In het python-voor beeld wordt in het cache geheugen op account gegarandeerd dat er een vertrouwelijke client toepassing voor elke aanvraag opnieuw wordt gemaakt en dat deze wordt geserialiseerd in de kolf sessie cache:

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
> [Aanmelden bij de web-app](scenario-web-app-call-api-sign-in.md)
