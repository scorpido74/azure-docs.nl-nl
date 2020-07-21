---
title: Een web-app configureren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het configureren van de code van een web-app die web-Api's aanroept
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 662520b9e31b4fe9a0925683fd0e661ce179e5b2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518144"
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
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Ondersteuning voor .NET Framework-en .NET core-platforms. Niet ondersteund zijn Universeel Windows-platform (UWP), Xamarin. iOS en Xamarin. Android, omdat deze platformen worden gebruikt voor het bouwen van open bare client toepassingen. Voor ASP.NET Core web apps en Web-Api's wordt MSAL.NET ingekapseld in een bibliotheek op een hoger niveau met de naam [micro soft. Identity. Web](https://aka.ms/ms-identity-web)|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL voor Python | Ondersteuning voor python-webtoepassingen. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL voor Java | Ondersteuning voor Java-webtoepassingen. |

Selecteer het tabblad voor het platform waarin u bent geïnteresseerd:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Als u wilt dat uw web-app beveiligde Api's aanroept wanneer u micro soft. Identity. web gebruikt, hoeft u alleen `AddWebAppCallsProtectedWebApi` een indeling voor de serialisatie van de token cache aan te roepen en op te geven (bijvoorbeeld in-Memory token cache):

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddMicrosoftWebAppAuthentication(Configuration, "AzureAd")
            .AddMicrosoftWebAppCallsdWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

Zie [token cache serialisatie Options](#token-cache) (Engelstalig) als u meer wilt weten over de token cache.

> [!NOTE]
> Als u de code voorbeelden hier volledig wilt begrijpen, moet u bekend zijn met [ASP.net core fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals), met name bij het [invoegen van afhankelijkheden](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) en [Opties](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Omdat het aanmelden van de gebruiker is gedelegeerd naar de middleware Open ID Connect (OIDC), moet u communiceren met het OIDC-proces. Hoe u communiceert, is afhankelijk van het Framework dat u gebruikt.

Voor ASP.NET kunt u zich abonneren op middleware OIDC-gebeurtenissen:

- U kunt ASP.NET Core een autorisatie code aanvragen met behulp van de Open ID Connect middleware. Met ASP.NET of ASP.NET Core kunnen gebruikers zich aanmelden en toestemming geven.
- U bent geabonneerd op de web-app om de autorisatie code te ontvangen. Dit abonnement wordt uitgevoerd met behulp van een C#-gemachtigde.
- Wanneer de autorisatie code wordt ontvangen, gebruikt u MSAL-bibliotheken om deze te inwisselen. De resulterende toegangs tokens en vernieuwings tokens worden opgeslagen in de token cache. De cache kan worden gebruikt in andere onderdelen van de toepassing, zoals controllers, om andere tokens op de achtergrond te verkrijgen.

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Mogelijk wilt u dit voor beeld raadplegen voor volledige implementatie details.

# <a name="java"></a>[Java](#tab/java)

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de Java- [Webtoepassing die Microsoft Graph aanroept](https://github.com/Azure-Samples/ms-identity-java-webapp), een voor beeld van een web-app dat gebruikmaakt van MSAL voor Java.
Met het voor beeld wordt momenteel MSAL voor Java de autorisatie code-URL geproduceerd en wordt de navigatie naar het verificatie-eind punt voor het micro soft Identity-platform verwerkt. Het is ook mogelijk Sprint beveiliging te gebruiken om de gebruiker te ondertekenen in. Mogelijk wilt u het voor beeld voor volledige implementatie gegevens raadplegen.

# <a name="python"></a>[Python](#tab/python)

Code voorbeelden in dit artikel en het volgende worden geëxtraheerd uit de python- [webtoepassing Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), een voor beeld van een web-app dat gebruikmaakt van MSAL. Python.
Met het voor beeld kunt u momenteel MSAL. Python produceert de autorisatie code-URL en verwerkt de navigatie naar het autorisatie-eind punt voor het micro soft Identity-platform. Mogelijk wilt u het voor beeld voor volledige implementatie gegevens raadplegen.

---

## <a name="code-that-redeems-the-authorization-code"></a>Code die de autorisatie code inwisselt

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Micro soft. Identity. Web vereenvoudigt uw code door de juiste OpenID Connect Connect-instellingen in te stellen, u te abonneren op de gebeurtenis ontvangen code en de code te inwisselen. Er is geen aanvullende code vereist om de autorisatie code te inwisselen. Zie [micro soft. Identity. web source code](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) voor meer informatie over hoe dit werkt.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET verwerkt dingen op soort gelijke wijze als ASP.NET Core, behalve dat de configuratie van OpenID Connect Connect en het abonnement op de `OnAuthorizationCodeReceived` gebeurtenis plaatsvindt in het bestand [App_Start \Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . De concepten zijn ook vergelijkbaar met die in ASP.NET Core, behalve dat u in ASP.NET de `RedirectUri` in [Web.config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)moet opgeven. Deze configuratie is iets minder krachtig dan het in ASP.NET Core, omdat u het moet wijzigen wanneer u de toepassing implementeert.

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

# <a name="java"></a>[Java](#tab/java)

Bekijk de [Web-app die zich in gebruikers aanmeldt: code configuratie](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) om te begrijpen hoe het Java-voor beeld de autorisatie code ontvangt. Nadat de app de code heeft ontvangen, wordt de [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegeren aan de- `AuthHelper.processAuthenticationCodeRedirect` methode in [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Aanroepen `getAuthResultByAuthCode` .

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

De `getAuthResultByAuthCode` methode is gedefinieerd in [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Er wordt een MSAL gemaakt `ConfidentialClientApplication` en vervolgens aangeroepen `acquireToken()` `AuthorizationCodeParameters` vanuit de autorisatie code.

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

De autorisatie code stroom is aangevraagd zoals weer gegeven in [de web-app die zich aanmeldt bij gebruikers: code configuratie](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). De code wordt vervolgens ontvangen op de `authorized` functie, die door de route wordt verzonden vanuit de `/getAToken` URL. Zie [app. py # l30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) voor de volledige context van deze code:

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

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

De zelf studie voor ASP.NET core maakt gebruik van afhankelijkheids injectie om u te laten bepalen welke implementatie van de token cache in het Startup.cs-bestand voor uw toepassing. Micro soft. Identity. web wordt geleverd met vooraf ontwikkelde token-cache-serialisatiefunctie beschreven in de [serialisatie van de token cache](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Een interessante mogelijkheid is om ASP.NET Core [gedistribueerde geheugen caches](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)te kiezen:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftWebAppAuthentication(Configuration, "AzureAd")
            .AddMicrosoftWebAppCallsWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

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

Zie ook het artikel [cache serialisatie](https://aka.ms/ms-id-web/token-cache-serialization) van micro soft. Identity. Web en zelf studies voor de ASP.net core-web-apps voor meer informatie over de token-cache providers. [ Token caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fase van de zelf studie voor web-apps.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="java"></a>[Java](#tab/java)

MSAL Java biedt methoden voor het serialiseren en deserialiseren van de token cache. In het Java-voor beeld wordt de serialisatie vanuit de sessie verwerkt, zoals wordt weer gegeven in de `getAuthResultBySilentFlow` methode in [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

De details van de- `SessionManagementHelper` klasse zijn opgenomen in het [MSAL-voor beeld voor Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

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
