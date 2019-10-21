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
ms.openlocfilehash: f558ecf583c96f36b8bbee19c7c9cbb2ee57aa31
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596734"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Web-app die gebruikers code configuratie aanmeldt

Meer informatie over het configureren van de code voor uw web-app die gebruikers aanmeldt.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotheken die worden gebruikt voor het beveiligen van Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
De bibliotheken die worden gebruikt voor het beveiligen van een web-app (en een web-API) zijn:

| Platform | Bibliotheek | Beschrijving |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identiteits model-uitbrei dingen voor .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Micro soft Identity Extensions voor .NET maakt rechtstreeks gebruik van ASP.NET en ASP.NET Core en stelt een set Dll's voor die beide worden uitgevoerd op .NET Framework en .NET core. Vanuit een ASP.NET/ASP.NET core-web-app kunt u token validatie beheren met de **TokenValidationParameters** -klasse (met name bij sommige ISV-scenario's) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL voor Java-momenteel in open bare preview |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL voor python-momenteel in open bare preview |

Selecteer het tabblad dat overeenkomt met het platform waarin u bent geïnteresseerd:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de [Stapsgewijze zelf studie voor de ASP.net core-web-app, hoofd stuk 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Raadpleeg deze zelf studie voor meer informatie over de volledige implementatie.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit het voor beeld van de [ASP.net-Web-app](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

# <a name="javatabjava"></a>[Java](#tab/java)

Code fragmenten in dit artikel en het volgende worden geëxtraheerd uit de Java- [Webtoepassing die micro soft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j web app-voor beeld aanroept

U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

# <a name="pythontabpython"></a>[Python](#tab/python)

Code fragmenten in dit artikel en het volgende worden opgehaald uit de python- [Webtoepassing die micro soft Graph MSAL aanroept](https://github.com/Azure-Samples/ms-identity-python-webapp) . Voor beeld van Python-web-app

U kunt voor meer informatie over de volledige implementatie verwijzen naar dit voor beeld.

---

## <a name="configuration-files"></a>Configuratie bestanden

Webtoepassingen die aanmelden bij gebruikers met het micro soft-identiteits platform worden meestal geconfigureerd via configuratie bestanden. De instellingen die u moet invullen zijn:

- de Cloud `Instance` als u uw app wilt uitvoeren (bijvoorbeeld in nationale Clouds)
- de doel groep in `tenantId`
- de `clientId` voor uw toepassing, zoals gekopieerd van de Azure Portal.

Soms kunnen toepassingen worden parametrized door de `authority`. Dit zijn de samen voeging van de `instance` en de `tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core bevinden deze instellingen zich in het bestand [appSettings. json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) , in de sectie ' AzureAD '.

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

In ASP.NET Core is er een ander bestand [properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) dat de URL (`applicationUrl`) en de SSL-poort (`sslPort`) voor uw toepassing en verschillende profielen bevat.

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

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met deze url's. dat wil zeggen dat voor de twee hierboven genoemde configuratie bestanden `https://localhost:44321/signin-oidc` zijn, omdat de applicationUrl is `http://localhost:3110` maar de `sslPort` is opgegeven (44321) en de `CallbackPath` `/signin-oidc` is zoals gedefinieerd in de `appsettings.json`.
  
Op dezelfde manier wordt de afmeldings-URI ingesteld op `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt de toepassing geconfigureerd via de [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) -bestands regels 12-15

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

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met deze url's. dat is `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

In Java bevindt de configuratie zich in het bestand [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) onder `src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

In de Azure Portal moeten de antwoord-Uri's die u moet registreren op de **verificatie** pagina voor uw toepassing, overeenkomen met de redirectUris die zijn gedefinieerd door de toepassing, die `http://localhost:8080/msal4jsample/secure/aad` en `http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Dit is het python-configuratie bestand in [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> In deze Quick Start wordt voorgesteld het client geheim op te slaan in het configuratie bestand voor eenvoud. In uw productie-app wilt u andere manieren gebruiken om uw geheim op te slaan, zoals een sleutel kluis of een omgevings variabele zoals beschreven in de documentatie van de kolf: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Initialisatie code

De initialisatie code verschilt, afhankelijk van het platform. Voor ASP.NET Core en ASP.NET wordt het aanmelden van gebruikers overgedragen aan de middleware OpenIDConnect. Nu maken we met de ASP.NET/ASP.NET Core-sjabloon webtoepassingen voor het Azure AD v 1.0-eind punt. Daarom is er een beetje configuratie vereist om deze aan te passen aan het micro soft Identity platform (v 2.0)-eind punt. In het geval van Java wordt het afgehandeld met de samen werking van de toepassing.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core Web Apps (en Web-Api's) is de toepassing beveiligd omdat u een `[Authorize]` kenmerk hebt voor de controllers of de controller acties. Met dit kenmerk wordt gecontroleerd of de gebruiker is geverifieerd. De code die de initialisatie van de toepassing uitvoert, bevindt zich in het `Startup.cs`-bestand en voor het toevoegen van verificatie met het micro soft-identiteits platform (voorheen Azure AD v 2.0) moet u de volgende code toevoegen. De opmerkingen in de code moeten zichzelf verklaren.

  > [!NOTE]
  > Als u het project in Visual Studio start met het standaard ASP.NET core-project, of als u `dotnet new mvc` gebruikt, is de methode `AddAzureAD` standaard beschikbaar omdat de gerelateerde pakketten automatisch worden geladen.
  > Als u echter een volledig nieuw project bouwt en probeert de onderstaande code te gebruiken, raden we u aan het NuGet-pakket **' micro soft. AspNetCore. Authentication. AzureAD. UI '** toe te voegen aan uw project om de `AddAzureAD` methode beschikbaar te maken.
  
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

Naast de configuratie kunt u bij het aanroepen van `AddMicrosoftIdentityPlatformAuthentication` het volgende opgeven:

- de naam van de configuratie sectie (standaard AzureAD)
- Als u de OpenIdConnect-middleware-gebeurtenissen wilt traceren, die u kunnen helpen bij het oplossen van problemen met uw webtoepassing als verificatie niet werkt: als u `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` instelt op `true` wordt uitgelegd hoe informatie wordt uitgesteld door de set ASP.NET Core middleware voortgang van het HTTP-antwoord op de identiteit van de gebruiker in de `HttpContext.User`.

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

De `AadIssuerValidator`-klasse maakt het mogelijk dat de uitgever van het token in veel gevallen (v 1.0 of v 2.0-token, een single-Tenant of een multi tenant toepassing of toepassing die zich aanmeldt bij gebruikers met hun persoonlijke micro soft-accounts, in de open bare Azure-Cloud of de National Clouds). Het is beschikbaar via [micro soft. Identity. web/resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

De code die betrekking heeft op verificatie in ASP.NET Web app/Web-Api's bevindt zich in het bestand [App_Start/startup. auth. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

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

Het Java-voor beeld maakt gebruik van het lente-Framework. De toepassing is beveiligd omdat u een `Filter` implementeert, waarmee elke HTTP-reactie wordt onderschept. In de Quick start voor de Java-Web-app wordt dit filter `AuthFilter` in `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. Het filter verwerkt de OAuth 2,0-autorisatie code stroom en daarom:

- Hiermee wordt gecontroleerd of de gebruiker is geverifieerd (`isAuthenticated()` methode)
- Als de gebruiker niet is geverifieerd, wordt de URL van de Azure AD-autorisatie-eind punten berekend en wordt de browser omgeleid naar deze URI
- Wanneer het antwoord arriveert, met daarin de verificatie code stroom, kan de msal4j het token ophalen.
- Wanneer het het token uiteindelijk van het token eindpunt ontvangt (op de omleidings-URI), is de gebruiker aangemeld.

Zie de `doFilter()`-methode in [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) voor meer informatie.

> [!NOTE]
> De code van de `doFilter()` wordt in een iets andere volg orde geschreven, maar de stroom is die wordt beschreven.

Zie het [micro soft Identity platform en de OAuth 2,0-autorisatie code stroom](v2-oauth2-auth-code-flow.md) voor meer informatie over de autorisatie code stroom die door deze methode wordt geactiveerd

# <a name="pythontabpython"></a>[Python](#tab/python)

Het python-voor beeld maakt gebruik van een fles. De initialisatie van de kolf en de MSAL. Python wordt uitgevoerd in de [app. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

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

In het volgende artikel leert u hoe u het aanmelden en afmelden kunt activeren.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Meld u aan en af](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Meld u aan en af](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Meld u aan en af](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Meld u aan en af](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
