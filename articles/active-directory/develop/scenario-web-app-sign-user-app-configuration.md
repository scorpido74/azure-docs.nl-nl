---
title: Een web-app configureren die gebruikers aantekent - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een web-app die zich aanmeldt bij gebruikers (codeconfiguratie)
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
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537108"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Web-app die zich aanmeldt bij gebruikers: codeconfiguratie

Meer informatie over het configureren van de code voor uw web-app die gebruikers aanmeldt.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotheken voor het beveiligen van web-apps

<!-- This section can be in an include for web app and web APIs -->
De bibliotheken die worden gebruikt om een web-app (en een web-API) te beschermen, zijn:

| Platform | Bibliotheek | Beschrijving |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensies voor identiteitsmodel voor .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Microsoft Identity Model Extensions voor .NET wordt rechtstreeks gebruikt door ASP.NET en ASP.NET Core en stelt een set DLL's voor die zowel op .NET Framework als .NET Core worden uitgevoerd. Vanuit een ASP.NET of ASP.NET Core-web-app u tokenvalidatie beheren met behulp van de klasse **TokenValidationParameters** (met name in sommige partnerscenario's). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Ondersteuning voor Java-webtoepassingen |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Ondersteuning voor Python-webtoepassingen |

Selecteer het tabblad dat overeenkomt met het platform waarin u geïnteresseerd bent:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Codefragmenten in dit artikel en de volgende worden geëxtraheerd uit de [ASP.NET Core web app incrementele tutorial, hoofdstuk 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

U verwijzen naar deze zelfstudie voor volledige implementatiedetails.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Codefragmenten in dit artikel en het volgende worden uit het voorbeeld van de [web-app ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)gehaald.

U naar dit voorbeeld verwijzen voor volledige implementatiedetails.

# <a name="java"></a>[Java](#tab/java)

Codefragmenten in dit artikel en de volgende worden geëxtraheerd uit de [Java-webtoepassing die Microsoft-grafiekvoorbeeld](https://github.com/Azure-Samples/ms-identity-java-webapp) in MSAL Java aanroept.

U naar dit voorbeeld verwijzen voor volledige implementatiedetails.

# <a name="python"></a>[Python](#tab/python)

Codefragmenten in dit artikel en de volgende worden geëxtraheerd uit de [Python-webtoepassing die Microsoft-grafiekvoorbeeld](https://github.com/Azure-Samples/ms-identity-python-webapp) in MSAL Python aanroept.

U naar dit voorbeeld verwijzen voor volledige implementatiedetails.

---

## <a name="configuration-files"></a>Configuratiebestanden

Webtoepassingen die gebruikers aanmelden met behulp van het Microsoft-identiteitsplatform, worden meestal geconfigureerd via configuratiebestanden. De instellingen die u moet invullen zijn:

- De cloudinstantie`Instance`( ) als u wilt dat uw app wordt uitgevoerd in nationale clouds, bijvoorbeeld
- Het publiek in de`TenantId`tenant-ID ( )
- De client-id (`ClientId`) voor uw toepassing, zoals gekopieerd van de Azure-portal

Soms kunnen toepassingen worden geparameterd door `Authority`, `Instance` dat `TenantId`is een samenvoeging van en .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core bevinden deze instellingen zich in het [appsettings.json-bestand](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) in de sectie 'AzureAd'.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

In ASP.NET Core bevat een ander bestand ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) de URL (`applicationUrl`) en de TLS/SSL-poort (`sslPort`) voor uw toepassing en verschillende profielen.

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

In de Azure-portal moeten de antwoord-URI's die u moet registreren op de **pagina Verificatie** voor uw toepassing overeenkomen met deze URL's. Voor de twee voorgaande configuratiebestanden `https://localhost:44321/signin-oidc`zouden ze . De reden `applicationUrl` is `http://localhost:3110`dat `sslPort` is , maar is opgegeven (44321). `CallbackPath`is, `/signin-oidc`zoals gedefinieerd `appsettings.json`in .

Op dezelfde manier zou de uitloging `https://localhost:44321/signout-callback-oidc`URI worden ingesteld op .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt de toepassing geconfigureerd via het [web.config-bestand,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) lijnen 12 tot en met 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
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

In de Azure-portal moeten de antwoord-URI's die u moet registreren op de **pagina Verificatie** voor uw toepassing overeenkomen met deze URL's. Dat wil zeggen, `https://localhost:44326/`ze zouden moeten zijn.

# <a name="java"></a>[Java](#tab/java)

In Java bevindt de configuratie zich in `src/main/resources`het bestand [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) onder .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

In de Azure-portal moeten de antwoord-URI's die u moet registreren `redirectUri` op de pagina **Verificatie** voor uw toepassing overeenkomen met de instanties die de toepassing definieert. Dat wil zeggen, `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`ze zouden moeten zijn en.

# <a name="python"></a>[Python](#tab/python)

Hier is het Python-configuratiebestand in [app_config.py:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Deze quickstart stelt voor om het clientgeheim op te slaan in het configuratiebestand voor eenvoud. In uw productie-app wilt u andere manieren gebruiken om uw geheim op te slaan, zoals een sleutelkluis of een omgevingsvariabele zoals beschreven in [de documentatie van Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initialisatiecode

De initialisatiecode is anders afhankelijk van het platform. Voor ASP.NET Core en ASP.NET wordt aanmelden aan gebruikers gedelegeerd aan de Middenware van OpenID Connect. De ASP.NET of ASP.NET Core-sjabloon genereert webtoepassingen voor het Azure Active Directory (Azure AD) v1.0-eindpunt. Sommige configuratie is vereist om ze aan te passen aan het Microsoft-identiteitsplatform (v2.0) eindpunt. In het geval van Java, het is behandeld door de lente met de medewerking van de aanvraag.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core-web-apps (en web-API's) `[Authorize]` wordt de toepassing beveiligd omdat u een kenmerk hebt op de controllers of de controllersacties. Met dit kenmerk wordt gecontroleerd of de gebruiker is geverifieerd. De code die de toepassing initialiseert, bevindt zich in het Startup.cs bestand.

Als u verificatie wilt toevoegen met het Microsoft-identiteitsplatform (voorheen Azure AD v2.0), moet u de volgende code toevoegen. De opmerkingen in de code moeten voor zichzelf spreken.

> [!NOTE]
> Als u uw project start met het standaardASP.NET Core-webproject binnen Visual studio of met behulp van, `dotnet new mvc`is de methode `AddAzureAD` standaard beschikbaar. Dat komt omdat de gerelateerde pakketten automatisch worden geladen.
>
> Als u een project helemaal opnieuw bouwt en de volgende code probeert te gebruiken, raden we u aan het NuGet-pakket **Microsoft.AspNetCore.Authentication.AzureAD.UI** aan uw project toe te voegen om de `AddAzureAD` methode beschikbaar te maken.

De volgende code is beschikbaar op [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
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

De `AddMicrosoftIdentityPlatformAuthentication` extensiemethode is gedefinieerd in [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Het:

- Hiermee voegt u de verificatieservice toe.
- Hiermee configureert u opties om het configuratiebestand te lezen.
- Hiermee configureert u de OpenID Connect-opties zodat de gebruikte autoriteit het eindpunt van het Microsoft-identiteitsplatform (voorheen Azure AD v2.0) is.
- Valideert de uitgever van het token.
- Hiermee zorgt u ervoor dat de `preferred_username` claims die overeenkomen met de naam worden toegewezen aan de claim in het ID-token.

Naast de configuratie u de naam van de `AddMicrosoftIdentityPlatformAuthentication`configuratiesectie opgeven wanneer u aanroept. Standaard is `AzureAd`het.

Het traceren van OpenId Connect middleware-gebeurtenissen kan u helpen problemen op te lossen als verificatie niet werkt. Instellen `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` om u te laten zien hoe informatie wordt uitgewerkt door de set van ASP.NET Core middleware als het vordert van de HTTP-reactie op de identiteit van de gebruiker in `HttpContext.User`.

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
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
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
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

De `AadIssuerValidator` klasse maakt het mogelijk de uitgever van het token in veel gevallen te valideren. Deze klasse werkt met een v1.0- of v2.0-token, een toepassing met één tenant of multitenant of een toepassing die gebruikers aanmeldt met hun persoonlijke Microsoft-accounts in de azure public cloud of nationale clouds. Het is beschikbaar bij [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

De code met betrekking tot verificatie in een ASP.NET web-app en web API's bevindt zich in het [bestand App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

De Java-sample maakt gebruik van het Spring framework. De toepassing is beveiligd omdat u een filter implementeert, dat elk HTTP-antwoord onderschept. In de quickstart voor Java-webapps bevindt dit filter zich `AuthFilter` in `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

Het filter verwerkt de OAuth 2.0-autorisatiecodestroom en`isAuthenticated()` controleert of de gebruiker is geverifieerd (methode). Als de gebruiker niet is geverifieerd, wordt de URL van de eindpunten voor Azure AD-autorisatie berekend en wordt de browser naar deze URI omgeleid.

Wanneer het antwoord binnenkomt, met de autorisatiecode, verwerft het token met behulp van MSAL Java. Wanneer het token uiteindelijk van het tokeneindpunt (op het omleidingspunt URI) wordt ontvangen, wordt de gebruiker aangemeld.

Zie voor meer `doFilter()` informatie de methode in [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> De code `doFilter()` van de is geschreven in een iets andere volgorde, maar de stroom is de beschreven.

Zie [Microsoft-identiteitsplatform en OAuth 2.0-autorisatiecodestroom](v2-oauth2-auth-code-flow.md)voor meer informatie over de autorisatiecodestroom die deze methode activeert.

# <a name="python"></a>[Python](#tab/python)

Het Python-monster maakt gebruik van Flask. De initialisatie van Flask en MSAL Python gebeurt in [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Volgende stappen

In het volgende artikel leert u hoe u aanmelding en afmelding activeren.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
