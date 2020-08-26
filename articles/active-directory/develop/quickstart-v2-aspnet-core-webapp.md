---
title: Aanmelden met Microsoft toevoegen aan ASP.NET Core-web-apps - Microsoft-identiteitsplatform | Azure
description: Informatie over het implementeren van Microsoft-aanmelding in een ASP.NET Core-web-app met behulp van OpenID Connect
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: fdc1f0db956d0f64938b6a0433fda21dc4462ced
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691323"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Quickstart: aanmelding met Microsoft toevoegen aan een ASP.NET Core-web-app
In deze quickstart gebruikt u een codevoorbeeld om te leren hoe een ASP.NET Core-web-app persoonlijke accounts (hotmail.com, outlook.com, enz.) en werk- en schoolaccounts kan aanmelden vanuit een willekeurig exemplaar van Azure Active Directory (Azure AD). (Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de [Azure-portal - App-registraties](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Ga naar de pagina [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) in het Microsoft-identiteitsplatform voor ontwikkelaars.
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>    - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `AspNetCore-Quickstart`.
>    - Bij **Omleidings-URI** voegt u `https://localhost:44321/` toe en selecteert u **Registreren**.
> 1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
>    - Bij **Omleidings-URI’s** voegt u `https://localhost:44321/signin-oidc` toe en selecteert u **Opslaan**.
>    - Bij **Geavanceerde instellingen** stelt u de **afmeldings-URL** in op `https://localhost:44321/signout-oidc`.
>    - Bij **Impliciete toekenning** controleert u de **id-tokens**.
>    - Selecteer **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Als u het codevoorbeeld uit deze snelstart wilt gebruiken, moet u antwoord-URL's toevoegen als `https://localhost:44321/` en `https://localhost:44321/signin-oidc`, voegt u de afmeldings-URL toe als `https://localhost:44321/signout-oidc` en vraagt u de id-tokens aan die moeten worden uitgegeven door het autorisatie-eindpunt.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-aspnet-core-project"></a>Stap 2: uw ASP.NET Core-project downloaden

> [!div renderon="docs"]
> [De ASP.NET Core-oplossing downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Voer het project uit.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en is klaar om te worden uitgevoerd.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-run-your-aspnet-core-project"></a>Stap 3: uw ASP.NET Core-project uitvoeren
> 1. Pak het zip-bestand uit in een lokale map in de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
> 1. Open de oplossing in uw IDE
> 1. Bewerk het bestand **appsettings.json**. Zoek `ClientId` en werk de waarde van `ClientId` bij met de **Toepassings-id (client-id)** van de toepassing die u hebt geregistreerd.
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
> > Om de waarden van **Toepassings-id (client-id)** , **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

## <a name="more-information"></a>Meer informatie

Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn en ook als u aanmelding wilt toevoegen aan een bestaande ASP.NET Core-toepassing.

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

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

Met de methode `AddAuthentication` wordt de service geconfigureerd voor het toevoegen van verificatie op basis van cookies. Deze verificatie wordt gebruikt in browserscenario's en om vragen te sturen naar OpenID Connect.

Met de regel die `.AddAzureAd` bevat, wordt het Microsoft-identiteitsplatform toegevoegd aan uw toepassing. Vervolgens wordt deze geconfigureerd voor aanmelding met het Microsoft-identiteitsplatform-eindpunt.

> |Waar | Beschrijving |
> |---------|---------|
> | ClientId  | Toepassings-id (client-id) van de toepassing die is geregistreerd in de Azure-portal. |
> | Instantie | Het STS-eindpunt voor gebruikersverificatie. Meestal is dit `https://login.microsoftonline.com/{tenant}/v2.0` voor openbare cloud, waarbij {tenant} de naam is van uw tenant, uw tenant-id of *common* voor een verwijzing naar het algemene eindpunt (gebruikt voor toepassingen met meerdere tenants) |
> | TokenValidationParameters | Een lijst met parameters voor de validatie van tokens. In dit geval is `ValidateIssuer` ingesteld op `false` om aan te geven dat aanmeldingen vanaf persoonlijke, werk- of schoolaccounts kunnen worden geaccepteerd. |


> [!NOTE]
> De instelling `ValidateIssuer = false` is een vereenvoudiging voor deze quickstart. In echte toepassingen moet u de verlener valideren.
> Bekijk de voorbeelden om te begrijpen hoe u dat kunt doen.
>
> Let ook op de methode `Configure`, die twee belangrijke methoden bevat: `app.UseCookiePolicy()` en `app.UseAuthentication()`

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

U kunt een controller of controllermethoden beveiligen met behulp van het kenmerk `[Authorize]`. Met dit kenmerk wordt toegang tot de controller of methoden beperkt door alleen geverifieerde gebruikers toe te staan. Dit betekent dat de verificatiecontrole kan worden gestart om de controller te benaderen als de gebruiker niet is geverifieerd.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de GitHub-opslagplaats van deze ASP.NET Core-zelfstudie voor meer informatie, waaronder instructies over het toevoegen van verificatie aan een gloednieuwe ASP.NET Core-web-app, het aanroepen van Microsoft Graph en andere Microsoft-API’s, het aanroepen van uw eigen API’s, het toevoegen van verificatie, het aanmelden van gebruikers in nationale clouds of met sociale identiteiten, en meer:

> [!div class="nextstepaction"]
> [Zelfstudie ASP.NET Core-web-app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
