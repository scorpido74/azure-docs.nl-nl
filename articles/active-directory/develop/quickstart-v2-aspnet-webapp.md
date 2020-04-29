---
title: Aanmelding voor micro soft Identity platform toevoegen aan een ASP.NET-Web-app | Azure
description: Meer informatie over hoe u micro soft-aanmelding kunt implementeren in een ASP.NET-Web-app met behulp van OpenID Connect Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 7f927d230efba580cff163d31c6f572a10d0b4aa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80991137"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Snelstartgids: aanmelden van micro soft Identity platform toevoegen aan een ASP.NET-Web-app
In deze Quick Start gebruikt u een code voorbeeld om te zien hoe een ASP.NET-Web-app zich aanmeldt bij persoonlijke accounts (hotmail.com, outlook.com, anderen) en werk-en school accounts van elk Azure Active Directory (Azure AD)-exemplaar.  (Zie [hoe het voor beeld werkt](#how-the-sample-works) voor een illustratie.)
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens de voorbeeldcode](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
> 1. Voer een naam in voor de toepassing en klik op **Registreren**.
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
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `ASPNET-Quickstart`.
>      - Voeg `http://localhost:44368/` de **omleidings-URI**toe en klik op **registreren**.
>      - Selecteer in het navigatie deel venster links onder de sectie beheren de optie **verificatie**
>          - Selecteer in de Subsectie **impliciete toekenning** **id-tokens**.
>          - En selecteer vervolgens **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
> Voor een juiste werking van het codevoorbeeld uit deze snelstart, moet u een antwoord-URL als `https://localhost:44368/` toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met dit kenmerk

#### <a name="step-2-download-your-project"></a>Stap 2: Uw project downloaden

> [!div renderon="docs"]
> [Down load de Visual Studio 2019-oplossing](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal"]
> Voer het project uit met behulp van Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het code voorbeeld downloaden](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app. 

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Stap 3: uw Visual Studio-project uitvoeren

1. Pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
1. Open de oplossing in Visual Studio (AppModelv2-Web-App-OpenIDConnect-DotNet.sln)
1. Afhankelijk van de versie van Visual Studio moet u mogelijk met de rechter muisknop op het project `AppModelv2-WebApp-OpenIDConnect-DotNet` klikken en **NuGet-pakketten herstellen**
1. Open de Package Manager-console (weer gave-> andere Windows-> Package Manager-console) en voer`Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`

> [!div renderon="docs"]
> 5. Bewerk **Web.config** en vervang de parameters `ClientId` en `Tenant` door:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    Waar:
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Tenant_Info_Here`: is een van de onderstaande opties:
>   - Als uw toepassing **alleen mijn organisatie**ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld contoso.onmicrosoft.com)
>   - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>   - Als uw toepassing **Alle Microsoft-accountgebruikers** ondersteunt, vervang deze waarde dan door `common`
>
> > [!TIP]
> > - Als u de waarden van *Toepassings-id*, *Map-id (tenant)* en *Ondersteunde accounttypen* wilt weten, gaat u naar de **Overzichtspagina**
> > - Zorg ervoor dat de `redirectUri` waarde voor in **Web. config** overeenkomt met de **omleidings-URI** die is gedefinieerd voor de app-registratie in azure AD (als dat niet het geval is, navigeert u naar het menu **verificatie** voor de app-registratie en werkt u de **omleidings-URI** bij naar overeenkomst)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Meer informatie

Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, hoofd argumenten en ook als u aanmelden wilt toevoegen aan een bestaande ASP.NET-toepassing.

### <a name="how-the-sample-works"></a>Hoe het voor beeld werkt
![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN Middleware NuGet-pakketten

U kunt de verificatiepijplijn instellen met verificatie op basis van cookies met behulp van OpenID Connect in ASP.NET met OWIN Middleware-pakketten. U kunt deze pakketten installeren door de volgende opdrachten in de **Package Manager Console** van Visual Studio uit te voeren:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb  
```

### <a name="owin-startup-class"></a>OWIN-opstartklasse

De OWIN-middleware gebruikt een *opstart klasse* die wordt uitgevoerd wanneer het hosting proces wordt geïnitialiseerd. In deze Quick Start vindt u het *Startup.cs* -bestand in de hoofdmap. De volgende code toont de parameter die wordt gebruikt door deze snelstart:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Waar  |  |
> |---------|---------|
> | `ClientId`     | Toepassings-id van de toepassing die is geregistreerd in Azure Portal |
> | `Authority`    | Het STS-eindpunt voor de gebruiker voor verificatie. Meestal <https://login.microsoftonline.com/{tenant}/v2.0> voor openbare cloud, waarbij {tenant} de naam is van uw tenant, uw tenant-id of *common* voor een verwijzing naar het algemene eindpunt (gebruikt voor toepassingen met meerdere tenants) |
> | `RedirectUri`  | URL waar gebruikers na verificatie worden verzonden naar het micro soft Identity platform-eind punt |
> | `PostLogoutRedirectUri`     | URL waar gebruikers naartoe worden gestuurd na afmelding |
> | `Scope`     | De lijst met bereiken die wordt aangevraagd, gescheiden door spaties |
> | `ResponseType`     | Aanvragen dat het antwoord van de verificatie een id-token bevat |
> | `TokenValidationParameters`     | Een lijst met parameters voor de validatie van tokens. In dit geval is `ValidateIssuer` ingesteld op `false` om aan te geven dat aanmeldingen vanaf persoonlijke, werk- of schoolaccounttypen kunnen worden geaccepteerd |
> | `Notifications`     | Een lijst met gemachtigden die kunnen worden uitgevoerd in verschillende *OpenIdConnect*-berichten |


> [!NOTE]
> De `ValidateIssuer = false` instelling is een vereenvoudiging voor deze Quick Start. In echte toepassingen moet u de uitgever valideren.
> Bekijk de voor beelden om te begrijpen hoe u dit doet.

### <a name="initiate-an-authentication-challenge"></a>Verificatievraag initiëren

U kunt afdwingen dat een gebruiker zich aanmeldt door een verificatievraag aan te vragen in uw controller:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Het aanvragen van een verificatievraag met behulp van de bovenstaande methode is optioneel en wordt normaal gesproken gebruikt als u wilt dat een weergave toegankelijk is voor zowel geverifieerde als niet-geverifieerde gebruikers. U kunt controllers ook beveiligen met behulp van de methode die wordt beschreven in de volgende sectie.

### <a name="protect-a-controller-or-a-controllers-method"></a>Een controller of de methode van een controller beveiligen

U kunt een controller of controlleracties beveiligen met behulp van het kenmerk `[Authorize]`. Dit kenmerk beperkt toegang tot de controller of acties door alleen geverifieerde gebruikers toegang toe te staan tot de acties in de controller, wat betekent dat de verificatievraag automatisch wordt uitgevoerd wanneer een *niet-geverifieerde* gebruiker toegang probeert te krijgen tot een van deze acties of controller die zijn voorzien van het kenmerk `[Authorize]`.

## <a name="next-steps"></a>Volgende stappen

Volg de ASP.NET-zelfstudie voor een volledige stapsgewijze handleiding voor het bouwen van toepassingen en nieuwe functies, met inbegrip van een volledige uitleg van deze snelstart.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>De stappen voor het maken van de toepassing die wordt gebruikt in deze snelstart

> [!div class="nextstepaction"]
> [Zelfstudie Aanmelding](./tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
