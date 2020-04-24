---
title: Aanmelden met micro soft toevoegen aan ASP.NET Core web apps-micro soft Identity platform | Azure
description: Meer informatie over het implementeren van micro soft-aanmelding op een ASP.NET Core web-app met behulp van OpenID Connect Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: db488e4a9ec9aa0f4f12c8de45f123dba1a93cdf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112708"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Snelstartgids: aanmelden toevoegen met micro soft aan een ASP.NET Core web-app
In deze Quick Start gebruikt u een code voorbeeld om te zien hoe een ASP.NET Core web-app zich kan aanmelden bij persoonlijke accounts (hotmail.com, outlook.com, anderen) en werk-en school accounts van elk Azure Active Directory (Azure AD)-exemplaar. (Zie [hoe het voor beeld werkt](#how-the-sample-works) voor een illustratie.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens de voorbeeldcode](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de [Azure Portal-app-registraties](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>    - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `AspNetCore-Quickstart`.
>    - In **omleidings**- `https://localhost:44321/`URI, toevoegen en **registreren**selecteren.
> 1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
>    - In **omleidings**- `https://localhost:44321/signin-oidc`uri's toevoegen en selecteert u **Opslaan**.
>    - Bij **Geavanceerde instellingen** stelt u de **afmeldings-URL** in op `https://localhost:44321/signout-oidc`.
>    - Bij **Impliciete toekenning** controleert u de **id-tokens**.
>    - Selecteer **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal
> Als u het codevoorbeeld uit deze snelstart wilt gebruiken, moet u antwoord-URL's toevoegen als `https://localhost:44321/` en `https://localhost:44321/signin-oidc`, voegt u de afmeldings-URL toe als `https://localhost:44321/signout-oidc` en vraagt u de id-tokens aan die moeten worden uitgegeven door het autorisatie-eindpunt.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-aspnet-core-project"></a>Stap 2: uw ASP.NET Core-project downloaden

> [!div renderon="docs"]
> [Down load de Visual Studio 2019-oplossing](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Voer het project uit met behulp van Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het code voorbeeld downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en is klaar om te worden uitgevoerd.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Stap 3: uw Visual Studio-project uitvoeren
> 1. Pak het zip-bestand uit in een lokale map in de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
> 1. Open de oplossing in Visual Studio
> 1. Bewerk het bestand **appsettings.json**. Zoek `ClientId` en werk de waarde bij `ClientId` met de waarde van de **toepassing (client) ID** van de toepassing die u hebt geregistreerd.
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here"
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```



> [!div renderon="docs"]
> Waar:
> - `Enter_the_Application_Id_here`: is de **toepassings-id** (client-id) voor de toepassing die is geregistreerd in de Azure-portal. U vindt de **toepassings-id (client-id)** op de pagina **Overzicht** van de app.
> - `Enter_the_Tenant_Info_Here` - is een van de volgende opties:
>   - Als uw toepassing **Alleen accounts in deze organisatiemap** ondersteunt, vervangt u deze waarde door de **tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>   - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>   - Als uw toepassing **Alle Microsoft-accountgebruikers** ondersteunt, vervang deze waarde dan door `common`
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)**, **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

## <a name="more-information"></a>Meer informatie

In deze sectie vindt u een overzicht van de code die is vereist om gebruikers aan te melden. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, hoofd argumenten en ook als u aanmelden wilt toevoegen aan een bestaande ASP.NET Core-toepassing.

### <a name="how-the-sample-works"></a>Hoe het voor beeld werkt
![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Opstartklasse

De *Microsoft.AspNetCore.Authentication*-middleware maakt gebruik van een opstartklasse die wordt uitgevoerd wanneer in het hostingproces het volgende wordt geïnitialiseerd:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

De- `AddAuthentication` methode configureert de service om verificatie op basis van cookies toe te voegen, die wordt gebruikt in browser scenario's en om de uitdaging voor OpenID connect-verbinding in te stellen.

De regel met `.AddAzureAd` de micro soft Identity platform-verificatie toevoegen aan uw toepassing. Deze wordt vervolgens geconfigureerd om u aan te melden met het micro soft Identity platform-eind punt.

> |Waar  |  |
> |---------|---------|
> | ClientId  | Toepassings-id (client-id) van de toepassing die is geregistreerd in de Azure-portal. |
> | Instantie | Het STS-eindpunt voor gebruikersverificatie. Meestal is dit <https://login.microsoftonline.com/{tenant}/v2.0> voor openbare cloud, waarbij {tenant} de naam is van uw tenant, uw tenant-id of *common* voor een verwijzing naar het algemene eindpunt (gebruikt voor toepassingen met meerdere tenants) |
> | TokenValidationParameters | Een lijst met parameters voor de validatie van tokens. In dit geval is `ValidateIssuer` ingesteld op `false` om aan te geven dat aanmeldingen vanaf persoonlijke, werk- of schoolaccounts kunnen worden geaccepteerd. |


> [!NOTE]
> De `ValidateIssuer = false` instelling is een vereenvoudiging voor deze Quick Start. In echte toepassingen moet u de uitgever valideren.
> Bekijk de voor beelden om te begrijpen hoe u dit doet.
>
> Let ook op `Configure` de methode die twee belang rijke methoden `app.UserCookiePolicy()` bevat: en`app.UseAuthentication()`

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more core
    app.UseCookiePolicy();
    app.UseAuthentication();
    // more core
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Een controller of de methode van een controller beveiligen

U kunt een controller of controllermethoden beveiligen met behulp van het kenmerk `[Authorize]`. Met dit kenmerk wordt de toegang tot de controller of methoden beperkt door alleen geverifieerde gebruikers toe te staan, wat betekent dat de verificatie vraag kan worden gestart om toegang te krijgen tot de controller als de gebruiker niet is geverifieerd.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk de GitHub-opslag plaats voor deze ASP.NET Core-zelf studie voor meer informatie, waaronder instructies over het toevoegen van verificatie aan een gloed nieuwe ASP.NET Core-webtoepassing, het aanroepen van Microsoft Graph en andere micro soft-Api's, het aanroepen van uw eigen Api's, het toevoegen van autorisatie, het aanmelden van gebruikers in nationale Clouds of met sociale identiteiten en meer :

> [!div class="nextstepaction"]
> [Zelf studie voor ASP.NET Core web-app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
