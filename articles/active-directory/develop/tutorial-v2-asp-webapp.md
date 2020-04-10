---
title: Aanmelding toevoegen aan het Microsoft-identiteitsplatform ASP.NET web-app
titleSuffix: Microsoft identity platform
description: Microsoft-aanmelding implementeren voor een ASP.NET-oplossing met behulp van een traditionele browsertoepassing en OpenID Connect-standaard
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 94d3993c6a0c62a68ea77a888d3351c8fea1d935
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990987"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Aanmelding toevoegen aan Microsoft aan een ASP.NET web-app

Deze handleiding laat zien hoe u aanmelding bij Microsoft implementeren via een ASP.NET MVC-oplossing met behulp van een traditionele toepassing op webbrowsers en OpenID Connect.

Wanneer u deze handleiding hebt voltooid, kan uw aanvraag aanmeldingen van persoonlijke accounts accepteren van outlook.com en live.com. Daarnaast kunnen werk- en schoolaccounts van elk bedrijf of organisatie die is geïntegreerd met het Microsoft-identiteitsplatform zich aanmelden bij uw app.

> Deze handleiding vereist Microsoft Visual Studio 2019.  Is dit niet het geval?  [Download Visual Studio 2019 gratis](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Als u nieuw bent op het Microsoft-identiteitsplatform, raden we u aan te beginnen met de aanmelding van het [Microsoft-identiteitsplatform aan een web-app voor ASP.NET.](quickstart-v2-aspnet-webapp.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voorbeeld-app die door deze handleiding wordt gegenereerd, werkt

![Laat zien hoe de voorbeeld-app die door deze zelfstudie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

De voorbeeldtoepassing die u maakt, is gebaseerd op een scenario waarin u de browser gebruikt om toegang te krijgen tot een ASP.NET website die een gebruiker vraagt zich te verifiëren via een aanmeldingsknop. In dit scenario vindt het grootste gedeelte van het werk om de webpagina weer te geven plaats aan de serverzijde.

## <a name="libraries"></a>Bibliotheken

In deze handleiding worden de volgende bibliotheken gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware die een toepassing in staat stelt om OpenIdConnect te gebruiken voor verificatie|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware waarmee een toepassing een gebruikerssessie kan onderhouden door cookies te gebruiken|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware waarmee OWIN-gebaseerde toepassingen kunnen worden uitgevoerd op Internet Information Services (IIS) met behulp van de ASP.NET aanvraagpijplijn|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie wordt beschreven hoe u de verificatiepijplijn installeren en configureren via OWIN middleware voor een ASP.NET project met OpenID Connect.

> Download je liever het Visual Studio-project van dit voorbeeld? [Download een project](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) en ga naar de [toepassing Registreren](#register-your-application) om het codevoorbeeld te configureren voordat u dit uitvoert.

### <a name="create-your-aspnet-project"></a>Uw ASP.NET-project maken

1. In Visual Studio: Ga naar **Bestand** > **Nieuw** > **Project**.
2. Selecteer onder **Visual C#\Web** de optie **ASP.NET-webtoepassing (.NET Framework)**.
3. Geef de toepassing een naam en selecteer **OK**.
4. Schakel **Leeg**in en schakel het selectievakje in om **MVC-verwijzingen** toe te voegen.

## <a name="add-authentication-components"></a>Onderdelen voor verificatie toevoegen

1. In Visual Studio: Ga naar **Tools** > **Nuget Package Manager** > **Package Manager Console**.
2. Voeg *NuGet-pakketten voor OWIN-middleware* toe door het volgende te typen in het Package Manager Console-venster:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Over deze bibliotheken
> Deze bibliotheken maken single sign-on (SSO) mogelijk door OpenID Connect te gebruiken via cookiegebaseerde verificatie. Nadat de verificatie is voltooid en het token dat de gebruiker vertegenwoordigt, is verzonden naar de toepassing, wordt met OWIN-middleware een sessiecookie gemaakt. De browser gebruikt deze cookie vervolgens bij volgende verzoeken, zodat de gebruiker het wachtwoord niet opnieuw hoeft te typen en er geen extra verificatie nodig is.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>De verificatiepijplijn configureren

De volgende stappen worden gebruikt om een OWIN middleware Startup-klasse te maken om OpenID Connect-verificatie te configureren. Deze klasse wordt automatisch uitgevoerd wanneer uw IIS-proces wordt gestart.

> [!TIP]
> Ga als volgt te werk als uw project geen `Startup.cs`-bestand bevat in de hoofdmap:
> 1. Klik met de rechtermuisknop op de hoofdmap van het project en selecteer vervolgens De klasse**Nieuw item** > **OWIN Opstarten** **toevoegen** > .<br/>
> 2. Noem het **Startup.cs.**
>
>> Zorg ervoor dat de geselecteerde klasse een OWIN Startup-klasse is en geen standaard Klasse C# . Bevestig dit door te controleren of u [assemblage: OwinStartup(type({NameSpace}. Opstarten))] boven de naamruimte.

1. *Owin-* en *Microsoft.IdentityModel-verwijzingen* toevoegen naar Startup.cs:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Vervang de opstartklasse door de volgende code:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
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
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> Instelling `ValidateIssuer = false` is een vereenvoudiging voor deze quickstart. In echte toepassingen moet u de uitgever valideren.
> Bekijk de voorbeelden om te leren hoe u dat doen.

<!--start-collapse-->
> ### <a name="more-information"></a>Meer informatie
> De parameters die u opgeeft in *OpenIDConnectAuthenticationOptions* dienen als coördinaten voor de toepassing om te communiceren met het identiteitsplatform van Microsoft. Omdat de OpenID Connect middleware cookies op de achtergrond gebruikt, moet u ook cookieverificatie instellen zoals de vorige code wordt weergegeven. De waarde *ValidateIssuer* geeft aan dat OpenIdConnect de toegang tot één specifieke organisatie niet moet beperken.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Een controller toevoegen om aanmeldings- en afmeldingsaanvragen te verwerken

Voer de volgende stappen uit om een nieuwe controller te maken om aanmeldings- en afmeldingsmethoden bloot te leggen:

1.  Klik met de rechtermuisknop op de map **Controllers** en selecteer**Controller** **toevoegen** > .
2.  Selecteer **MVC-controller (.NET-versie) – Leeg**.
3.  Selecteer **Toevoegen**.
4.  Noem deze **HomeController** en selecteer **Toevoegen**.
5.  Owin-verwijzingen toevoegen aan de klasse:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Voeg de volgende twee methoden toe om aanmelding en afmelding bij uw controller te verwerken door een verificatieuitdaging te starten:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>De startpagina van de app maken voor aanmelding door gebruikers

Maak in Visual Studio een nieuwe weergave om de aanmeldingsknop toe te voegen en gebruikersgegevens na verificatie weer te geven:

1.  Klik met de rechtermuisknop op de map **Weergaven\Startpagina** en selecteer **Weergave toevoegen**.
2.  Geef de nieuwe **weergave-index**een naam .
3.  Voeg de volgende HTML toe aan het bestand. Deze bevat de aanmeldknop:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Meer informatie
>  Met deze pagina wordt een aanmeldknop toegevoegd, in de SVG-indeling met een zwarte achtergrond:<br/>![Aanmelden bij Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Ga voor meer aanmeldingsknoppen naar de [richtlijnen voor branding.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Huisstijlrichtlijnen")
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Een controller toevoegen om de claims van de gebruiker weer te geven
Deze controller demonstreert het gebruik van het kenmerk `[Authorize]` om een controller te beveiligen. Dit kenmerk beperkt de toegang tot de controller door alleen geverifieerde gebruikers toe te staan. De volgende code maakt gebruik van het kenmerk om gebruikersclaims weer te geven die zijn opgehaald als onderdeel van aanmelding:

1.  Klik met de rechtermuisknop op de map **Controllers** en selecteer**Controller** **toevoegen** > .
2.  Selecteer **MVC-controller {versie} – Leeg**.
3.  Selecteer **Toevoegen**.
4.  Noem deze **ClaimsController**.
5.  Vervang de code van de klasse controller door de volgende code. Hiermee wordt `[Authorize]` het kenmerk aan de klasse toegevoegd:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Meer informatie
> Vanwege het gebruik `[Authorize]` van het kenmerk kunnen alle methoden van deze controller alleen worden uitgevoerd als de gebruiker is geverifieerd. Als de gebruiker niet is geverifieerd en toegang probeert te krijgen tot de controller, initieert OWIN een verificatieuitdaging en dwingt de gebruiker zich te verifiëren. De voorgaande code kijkt naar de lijst met claims voor specifieke gebruikerskenmerken die zijn opgenomen in het id-token van de gebruiker. Deze kenmerken omvatten de volledige naam en gebruikersnaam van de gebruiker, en het globale onderwerp voor de gebruikers-id. Ze omvatten ook de *Tenant-id*. Dit is de id voor de organisatie van de gebruiker. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Een weergave maken om de claims van de gebruiker weer te geven

Maak in Visual Studio een nieuwe weergave om de claims van de gebruiker weer te geven op een webpagina:

1.  Klik met de rechtermuisknop op **de map Weergaven\Claims** en selecteer **Weergave toevoegen**.
2.  Geef de nieuwe **weergave-index**een naam .
3.  Voeg de volgende HTML toe aan het bestand:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Uw toepassing registreren

Om uw aanvraag te registreren en uw registratiegegevens voor toepassingen toe te voegen aan uw oplossing, hebt u twee opties:

### <a name="option-1-express-mode"></a>Optie 1: Express-modus

Voer de volgende stappen uit om uw aanvraag snel te registreren:

1. Ga naar de nieuwe [Azure-portal - deelvenster App-registraties.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)
1. Voer een naam in voor de toepassing en selecteer **Registreren**.
1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren in één klik.

### <a name="option-2-advanced-mode"></a>Optie 2: Geavanceerde modus

Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:

1. Open Visual Studio en vervolgens:
   1. in Solution Explorer selecteert u het project en bekijkt u het venster Eigenschappen (als u geen venster Eigenschappen ziet, drukt u op F4).
   1. SSL-ingeschakeld wijzigen `True`in .
   1. Klik met de rechtermuisknop op het project in Visual Studio, selecteer **Eigenschappen**en selecteer het **tabblad Web.** Wijzig **in** de sectie Servers de instelling **Projecturl** in de **SSL-URL**.
   1. Kopieer de SSL-URL. U voegt deze URL toe aan de lijst met URL's omleiden in de lijst met omleidings-URL's van de registratieportal in de volgende stap.<br/><br/>![Projecteigenschappen](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure AD-tenant.
1. Ga naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app, zoals **ASPNET-Tutorial**.
   1. Voeg de SSL-URL die u vanuit Visual Studio `https://localhost:44368/`hebt gekopieerd toe in stap 1 (bijvoorbeeld ) in **de URL van Antwoord**en selecteer **Register**.
1. Selecteer het **menu Verificatie,** selecteer **ID-tokens** onder **Impliciete toekenning**en selecteer **Opslaan**.
1. Voeg het volgende toe in het bestand web.config, dat zich in de hoofdmap in de `configuration\appSettings` sectie bevindt:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Vervang `ClientId` door de applicatie-id die u zojuist hebt geregistreerd.
1. Vervang `redirectUri` door de SSL-URL van uw project.

## <a name="test-your-code"></a>Uw code testen

Als u uw toepassing wilt testen in Visual Studio, drukt u op F5 om uw project uit te voeren. De browser wordt<span></span>geopend voor de locatie http:// localhost:{port} en u ziet de knop **Aanmelden met Microsoft.** Selecteer de knop om het aanmeldingsproces te starten.

Wanneer u klaar bent om uw test uit te voeren, gebruikt u een Azure AD-account (werk- of schoolaccount) of een persoonlijk Microsoft-account<span>(live.</span> com of <span>outlook.</span> com) om in te loggen.

![Aanmelden bij Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Aanmelden bij uw Microsoft-account](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform
>  Toepassingen die integreren met het Microsoft-identiteitsplatform volgen een autorisatiemodel dat gebruikers en beheerders controle geeft over hoe gegevens kunnen worden geopend. Nadat een gebruiker zich heeft geverifieerd met het Microsoft-identiteitsplatform om toegang te krijgen tot deze toepassing, wordt hem of haar toestemming gevraagd om toestemming voor de door de toepassing gevraagde machtigingen ('Uw basisprofiel bekijken' en 'Toegang houden tot gegevens waartoe u toegang hebt gegeven'). Na het accepteren van deze machtigingen gaat de gebruiker verder met de resultaten van de toepassing. De gebruiker kan echter in plaats daarvan worden gevraagd met een pagina met toestemming voor **beheerders nodig** als een van de volgende gebeurtenissen optreedt:
>  > - De toepassingsontwikkelaar voegt eventuele aanvullende machtigingen toe waarvoor **beheerderstoestemming**vereist is.
>  > - Of de tenant is geconfigureerd (in **Enterprise Applications -> Gebruikersinstellingen)** waar gebruikers niet kunnen instemmen met apps die namens hen toegang krijgen tot bedrijfsgegevens.
>
> Raadpleeg machtigingen en [toestemming in het eindpunt van het Microsoft-identiteitsplatform voor](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)meer informatie.
<!--end-collapse-->

#### <a name="view-application-results"></a>Toepassingsresultaten weergeven

Nadat u zich hebt aangemeld, wordt de gebruiker doorgestuurd naar de startpagina van uw website. De startpagina is de HTTPS-URL die is opgegeven in de registratiegegevens van uw toepassing in de Microsoft Application Registration Portal. De startpagina bevat een *welkomstbericht 'Hallo \<gebruiker>',* een link om af te melden en een link om de claims van de gebruiker te bekijken. De koppeling voor de claims van de gebruiker maakt verbinding met de claimcontroller die u eerder hebt gemaakt.

### <a name="view-the-users-claims"></a>De claims van de gebruiker weergeven

Als u de claims van de gebruiker wilt bekijken, selecteert u de koppeling om naar de controllerweergave te bladeren die alleen beschikbaar is voor geverifieerde gebruikers.

#### <a name="view-the-claims-results"></a>Bekijk de schaderesultaten

Nadat u naar de controllerweergave hebt gebladerd, ziet u een tabel met de basiseigenschappen voor de gebruiker:

|Eigenschap |Waarde |Beschrijving |
|---|---|---|
|**Naam** |De volledige naam van de gebruiker | Voor- en achternaam van de gebruiker
|**Gebruikersnaam** |Gebruiker<span>@domain.com</span> | De gebruikersnaam die wordt gebruikt om de gebruiker te identificeren|
|**Onderwerp** |Onderwerp |Een tekenreeks die de gebruiker op een unieke wijze identificeert op het web|
|**Tenant-id** |GUID | Een **guid** die op unieke wijze de Azure AD-organisatie van de gebruiker vertegenwoordigt|

Daarnaast ziet u een tabel met alle claims die zich in de verificatieaanvraag bevinden. Zie de lijst [met claims die zich in een ID-token bevinden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)voor meer informatie.

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Toegang tot een methode testen met een kenmerk Autoriseren (optioneel)

Voer de volgende stappen uit om als anonieme `Authorize` gebruiker toegang te testen tot een controller die door het kenmerk is beveiligd:

1. Selecteer de koppeling om de gebruiker af te melden en voltooi het afmeldingsproces.
2. Typ in uw<span></span>browser http:// localhost:{port}/claims om toegang te `Authorize` krijgen tot uw controller die door het kenmerk wordt beschermd.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Verwachte resultaten na toegang tot een beveiligde controller

U wordt gevraagd zich te verifiëren om de weergave van de beveiligde controller te gebruiken.

## <a name="advanced-options"></a>Geavanceerde opties

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Bescherm uw hele website
Om uw hele website te beschermen, voegt u `AuthorizeAttribute` in `GlobalFilters` het bestand `Application_Start` **Global.asax** het kenmerk toe aan het filter in de methode:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Beperken wie zich kan aanmelden bij uw aanvraag

Wanneer u de toepassing bouwt die door deze handleiding is gemaakt, accepteert uw toepassing standaard aanmeldingen van persoonlijke accounts (waaronder outlook.com, live.com en anderen) en werk- en schoolaccounts van elk bedrijf of organisatie dat is geïntegreerd met het Microsoft-identiteitsplatform. Dit is een aanbevolen optie voor SaaS-toepassingen.

Om de aanmeldingstoegang van gebruikers voor uw toepassing te beperken, zijn er meerdere opties beschikbaar.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Optie 1: Gebruikers beperken vanaf het Active Directory-exemplaar van één organisatie om zich aan te melden bij uw toepassing (één tenant)

Deze optie wordt vaak gebruikt voor *LOB-toepassingen:* Als u wilt dat uw toepassing alleen aanmeldingen accepteert van accounts die behoren tot een specifiek Azure AD-exemplaar (inclusief *gastaccounts* van dat exemplaar), voert u de volgende stappen uit:

1. Wijzig in het bestand web.config `Tenant` de `Common` waarde voor de parameter van `contoso.onmicrosoft.com`naar de tenantnaam van de organisatie, zoals .
2. Stel in de [klasse OWIN Startup](#configure-the-authentication-pipeline)het `ValidateIssuer` argument in op `true`.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Optie 2: Toegang beperken tot gebruikers in een specifieke lijst met organisaties

U aanmeldingstoegang beperken tot alleen die gebruikersaccounts die zich in een Azure AD-organisatie bevinden die zich in de lijst met toegestane organisaties bevindt:
1. Stel in de [klasse OWIN Startup](#configure-the-authentication-pipeline)het `ValidateIssuer` argument in op `true`.
2. Stel de waarde `ValidIssuers` van de parameter in op de lijst met toegestane organisaties.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Optie 3: Een aangepaste methode gebruiken om emittenten te valideren

U een aangepaste methode implementeren om emittenten te valideren met behulp van de parameter **IssuerValidator.** Zie [TokenValidationParameters, klasse TokenValidationParameters,](/previous-versions/visualstudio/dn464192(v=vs.114))voor meer informatie over het gebruik van deze parameter.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe web-apps web-API's kunnen oproepen.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Meer informatie over het maken van de toepassing die wordt gebruikt in deze quickstart-handleiding

Meer informatie over web-apps die webAPI's aanroepen met het Microsoft-identiteitsplatform:

> [!div class="nextstepaction"]
> [Web-apps die web-API's aanroepen](scenario-web-app-sign-user-overview.md)

Meer informatie over het maken van webapps die Microsoft Graph aanroepen:

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET zelfstudie](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
