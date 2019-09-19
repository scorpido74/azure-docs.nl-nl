---
title: Web-app die gebruikers aanmeldt (code configuratie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die wordt aangemeld bij gebruikers (code configuratie)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a746b0f6d85e3f012cdd2e78fff8cd10a586950
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086760"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Web-app die gebruikers code configuratie aanmeldt

Meer informatie over het configureren van de code voor uw web-app die gebruikers aanmeldt.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotheken die worden gebruikt voor het beveiligen van Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotheken die worden gebruikt voor het beveiligen van een web-app (en een web-API) zijn:

| Platform | Bibliotheek | Description |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identiteits model-uitbrei dingen voor .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Micro soft Identity Extensions voor .NET maakt rechtstreeks gebruik van ASP.NET en ASP.NET Core en stelt een set Dll's voor die beide worden uitgevoerd op .NET Framework en .NET core. Vanuit een ASP.NET/ASP.NET core-web-app kunt u token validatie beheren met de **TokenValidationParameters** -klasse (met name bij sommige ISV-scenario's) |
| ![Java](media/sample-v2-code/logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL voor Java-momenteel in open bare preview |
| ![Python](media/sample-v2-code/logo_python.png) | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL voor python-momenteel in open bare preview |

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit:

- de [Stapsgewijze zelf studie voor het ASP.net Core van de web-app, hoofd stuk 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).
- het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)
- de [Java-webtoepassing die het micro soft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j web-app-voor beeld aanroept
- de [python-webtoepassing die micro soft Graph MSAL aanroept](https://github.com/Azure-Samples/ms-identity-python-webapp) . Voor beeld van Python-web-app

U kunt voor meer informatie over de volledige implementatie verwijzen naar deze zelf studies en voor beelden.

## <a name="configuration-files"></a>Configuratie bestanden

Webtoepassingen die aanmelden bij gebruikers met het micro soft-identiteits platform worden meestal geconfigureerd via configuratie bestanden. De instellingen die u moet invullen zijn:

- de Cloud `Instance` als u wilt dat uw app wordt uitgevoerd (bijvoorbeeld in nationale Clouds)
- de doel groep in`tenantId`
- de `clientId` voor uw toepassing, zoals gekopieerd van de Azure Portal.

Soms kunnen toepassingen worden parametrized door de `authority`, het samen voegen van de `instance` en de`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.net core bevinden deze instellingen zich in het `appsettings.json` bestand, in de sectie ' AzureAD '.

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.net Core is er nog een bestand (`properties\launchSettings.json`) dat de URL (`applicationUrl`) en de SSL-poort (`sslPort`) voor uw toepassing en verschillende profielen bevat.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met deze url's. dat wil zeggen dat voor `https://localhost:44321/signin-oidc` de twee hierboven genoemde configuratie bestanden de applicationUrl is `http://localhost:3110` , maar de `sslPort` is opgegeven (44321) en de `CallbackPath` is `/signin-oidc` gedefinieerd in de `appsettings.json`.
  
Op dezelfde manier wordt de afmeldings-URI ingesteld op `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.net wordt de toepassing via het `Web.Config` bestand geconfigureerd

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met deze url's. dat wil `https://localhost:44326/`zeggen.

# <a name="javatabjava"></a>[Java](#tab/java)

In Java bevindt de configuratie zich in `application.properties` het bestand onder`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

In de Azure Portal moeten de antwoord-uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met de redirectUris die zijn gedefinieerd door de toepassing `http://localhost:8080/msal4jsample/secure/aad` , dat wil zeggen en`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Dit is het python-configuratie bestand in [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/web_app_sample/app_config.py)

```Python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

---

## <a name="initialization-code"></a>Initialisatie code

De initialisatie code verschilt, afhankelijk van het platform. Voor ASP.NET Core en ASP.NET wordt het aanmelden van gebruikers overgedragen aan de middleware OpenIDConnect. Nu maken we met de ASP.NET/ASP.NET Core-sjabloon webtoepassingen voor het Azure AD v 1.0-eind punt. Daarom is er een beetje configuratie vereist om deze aan te passen aan het micro soft Identity platform (v 2.0)-eind punt. In het geval van Java wordt het afgehandeld met de samen werking van de toepassing.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.net core web apps (en Web-api's), is de toepassing beveiligd omdat u een `[Authorize]` kenmerk op de controllers of de controller acties hebt. Met dit kenmerk wordt gecontroleerd of de gebruiker is geverifieerd. De code die de initialisatie van de toepassing uitvoert, bevindt zich in het `Startup.cs` bestand en, om verificatie toe te voegen met het micro soft Identity platform (voorheen Azure AD v 2.0), moet u de volgende code toevoegen. De opmerkingen in de code moeten zichzelf verklaren.

  > [!NOTE]
  > Als u het project in Visual Studio start met het standaard ASP.net-hoofd project of `dotnet new mvc` als u `AddAzureAD` de methode gebruikt, is standaard beschikbaar omdat de gerelateerde pakketten automatisch worden geladen.
  > Als u echter een volledig nieuw project bouwt en probeert de onderstaande code te gebruiken, raden we u aan het NuGet-pakket **' micro soft. AspNetCore. Authentication. AzureAD. UI '** toe te voegen aan `AddAzureAD` uw project om de methode beschikbaar te maken.
  
De volgende code is beschikbaar via [opstarten. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

De `AddMicrosoftIdentityPlatformAuthentication` is een extensie methode die is gedefinieerd in [micro soft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). ,

- voegt de verificatie service toe
- opties configureren om het configuratie bestand te lezen
- Hiermee configureert u de OpenID Connect-verbindings opties zodat de gebruikte instantie het micro soft Identity platform (voorheen Azure AD v 2.0)-eind punt is
- de uitgever van het token is gevalideerd
- de claims die overeenkomen met de naam zijn toegewezen uit de claim ' preferred_username ' in het ID-token 

Naast de configuratie kunt u bij het aanroepen `AddMicrosoftIdentityPlatformAuthentication`de volgende opties opgeven:

- de naam van de configuratie sectie (standaard AzureAD)
- Als u de OpenIdConnect-middleware-gebeurtenissen wilt traceren, die u kunnen helpen bij het oplossen van problemen met uw webtoepassing `true` als verificatie niet werkt: `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` in dit geval wordt aangegeven hoe informatie wordt uitgesteld door de set ASP.net core middleware tijdens de uitvoering van het HTTP-antwoord op de identiteit van de gebruiker in de `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

De `AadIssuerValidator` -klasse maakt het mogelijk dat de verlener van het token in veel gevallen (v 1.0 of v 2.0-token, een single-Tenant of een multi tenant toepassing of toepassing die zich aanmeldt bij gebruikers met hun persoonlijke micro soft-accounts, in de open bare Azure-Cloud of de National Clouds). Het is beschikbaar via [micro soft. Identity. web/resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

De code die betrekking heeft op verificatie in ASP.net Web app/Web-api's bevindt zich in het `App_Start/Startup.Auth.cs` bestand.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Het Java-voor beeld maakt gebruik van het lente-Framework. De toepassing is beveiligd omdat u een `Filter`implementeert, waarmee elke HTTP-reactie wordt opgehaald. In de Quick start voor de Java-web `AuthFilter` - `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`app is dit in. Het filter verwerkt de OAuth 2,0-autorisatie code stroom en daarom:

- verifieert of de gebruiker is geverifieerd (`isAuthenticated()` methode)
- Als de gebruiker niet is geverifieerd, wordt de URL van de Azure AD-autorisatie-eind punten berekend en wordt de browser omgeleid naar deze URI
- Wanneer het antwoord arriveert, met daarin de verificatie code stroom, kan de msal4j het token ophalen.
- Wanneer het het token uiteindelijk van het token eindpunt ontvangt (op de omleidings-URI), is de gebruiker aangemeld.

Zie de `doFilter()` methode in [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) voor meer informatie.

> [!NOTE]
> De code van de `doFilter()` is in een iets andere volg orde geschreven, maar de stroom is die wordt beschreven.

Zie het [micro soft Identity platform en de OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md) voor meer informatie over de door deze methode geactiveerde autorisatie code stroom

# <a name="pythontabpython"></a>[Python](#tab/python)

Het python-voor beeld maakt gebruik van een fles. De initialisatie van de kolf en de MSAL. Python wordt uitgevoerd in de [app. py # L1-L17](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L1-L17)

```Python
import uuid
import flask
import requests
from flask import Flask, render_template, session, request
from flask_session import Session
import msal
import app_config

sess = Session()
app = Flask(__name__)
app.config.from_object('config.Config')
sess.init_app(app)
cache = msal.SerializableTokenCache()
application = msal.ConfidentialClientApplication(
    app_config.CLIENT_ID, authority=app_config.AUTHORITY,
    client_credential=app_config.CLIENT_SECRET,
    token_cache=cache)
```

Dit is MSAL. Python die ervoor zorgt dat de gebruiker zich aanmeldt. Zie [app. py # L74-84](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L74-84)

```Python
@app.route('/authenticate')
def authenticate():
    # Call to the authorize endpoint
    auth_state = str(uuid.uuid4())
    session[(request.cookies.get("session")+'state')] = auth_state
    authorization_url = application.get_authorization_request_url(app_config.SCOPE, state=auth_state,
                                                                  redirect_uri=app_config.REDIRECT_URI)
    resp = flask.Response(status=307)
    resp.headers['location'] = authorization_url
    return resp
```

---

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel leert u hoe u het aanmelden en afmelden kunt activeren.

> [!div class="nextstepaction"]
> [Meld u aan en af](scenario-web-app-sign-user-sign-in.md)
