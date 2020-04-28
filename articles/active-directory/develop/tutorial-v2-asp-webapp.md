---
title: Aanmelden bij micro soft Identity platform ASP.NET Web-app toevoegen
titleSuffix: Microsoft identity platform
description: Implementatie van micro soft-aanmelding bij een ASP.NET-oplossing met behulp van een traditionele toepassing op basis van de webbrowser en OpenID Connect Connect Standard
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
ms.openlocfilehash: 4b9dac92f0cff213622f0087b281814251f06ffd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181610"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Aanmelden bij micro soft toevoegen aan een ASP.NET-Web-app

In deze hand leiding wordt beschreven hoe u aanmelden bij micro soft implementeert via een ASP.NET MVC-oplossing met behulp van een traditionele toepassing op basis van een webbrowser en OpenID connect verbinding maken.

Wanneer u deze hand leiding hebt voltooid, kan uw toepassing aanmeldingen van persoonlijke accounts accepteren vanaf het moment dat outlook.com en live.com. Daarnaast kunnen werk-en school accounts van elk bedrijf of organisatie die is geïntegreerd met het micro soft Identity-platform, zich aanmelden bij uw app.

> Voor deze hand leiding is micro soft Visual Studio 2019 vereist.  Is dit niet het geval?  [Down load Visual Studio 2019 gratis](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Als u niet bekend bent met het micro soft-identiteits platform, raden we u aan om te beginnen met het [micro soft Identity platform-aanmeldings onderdeel toevoegen aan een ASP.net-Web-app](quickstart-v2-aspnet-webapp.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voor beeld-app die wordt gegenereerd door deze hand leiding werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

De voorbeeld toepassing die u maakt, is gebaseerd op een scenario waarin u de browser gebruikt om toegang te krijgen tot een ASP.NET-website die een gebruiker vraagt om zich te verifiëren via een aanmeldings knop. In dit scenario vindt het grootste gedeelte van het werk om de webpagina weer te geven plaats aan de serverzijde.

## <a name="libraries"></a>Bibliotheken

In deze hand leiding worden de volgende bibliotheken gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware die een toepassing in staat stelt om OpenIdConnect te gebruiken voor verificatie|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware waarmee een toepassing een gebruikers sessie kan onderhouden met behulp van cookies|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware waarmee op OWIN gebaseerde toepassingen kunnen worden uitgevoerd op Internet Information Services (IIS) met behulp van de ASP.NET-aanvraag pijplijn|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie wordt beschreven hoe u de verificatie pijplijn kunt installeren en configureren via OWIN middleware in een ASP.NET-project met behulp van OpenID Connect Connect.

> Wilt u in plaats daarvan het Visual Studio-project van dit voor beeld downloaden? [Down load een project](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) en ga naar de [registratie van uw toepassing](#register-your-application) om het code voorbeeld te configureren voordat u het programma uitvoert.

### <a name="create-your-aspnet-project"></a>Uw ASP.NET-project maken

1. Ga in Visual Studio naar het **bestand** > **Nieuw** > **project**.
2. Selecteer onder **Visual C#\Web** de optie **ASP.NET-webtoepassing (.NET Framework)**.
3. Geef de toepassing een naam en selecteer **OK**.
4. Selecteer **leeg**en schakel vervolgens het selectie vakje in om **MVC** -verwijzingen toe te voegen.

## <a name="add-authentication-components"></a>Onderdelen voor verificatie toevoegen

1. Ga in Visual Studio naar de **hulpprogram ma's** > **Nuget package manager** > **Package Manager console**.
2. Voeg *NuGet-pakketten voor OWIN-middleware* toe door het volgende te typen in het Package Manager Console-venster:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>Over deze bibliotheken
Deze bibliotheken maken gebruik van eenmalige aanmelding (SSO) met behulp van OpenID connect-verbinding maken via op cookies gebaseerde verificatie. Nadat de verificatie is voltooid en het token dat de gebruiker vertegenwoordigt, is verzonden naar de toepassing, wordt met OWIN-middleware een sessiecookie gemaakt. De browser gebruikt deze cookie vervolgens op volgende aanvragen, zodat de gebruiker het wacht woord niet opnieuw hoeft te typen en er geen aanvullende verificatie nodig is.

## <a name="configure-the-authentication-pipeline"></a>De verificatiepijplijn configureren

De volgende stappen worden gebruikt voor het maken van een OWIN-middleware voor het configureren van OpenID Connect Connect-verificatie. Deze klasse wordt automatisch uitgevoerd wanneer het IIS-proces wordt gestart.

> [!TIP]
> Ga als volgt te werk als uw project geen `Startup.cs`-bestand bevat in de hoofdmap:
> 1. Klik met de rechter muisknop op de hoofdmap van het project en selecteer vervolgens**Nieuw item** >  **toevoegen** > **OWIN opstart klasse**.<br/>
> 2. Noem deze **Startup.cs**.
>
>> Zorg ervoor dat de geselecteerde klasse een OWIN-opstart klasse is en niet een standaard C#-klasse. Bevestig dit door te controleren of [assembly: OwinStartup (typeof ({NameSpace}) wordt weer geven. Opstarten)] boven de naam ruimte.

1. *OWIN* -en *micro soft. Identity model* -verwijzingen toevoegen aan startup.CS:

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
> De `ValidateIssuer = false` instelling is een vereenvoudiging voor deze Quick Start. In echte toepassingen moet u de uitgever valideren.
> Bekijk de voor beelden om te leren hoe u dit doet.

### <a name="more-information"></a>Meer informatie

De para meters die u in *OpenIDConnectAuthenticationOptions* opgeeft, fungeren als coördinaten voor de toepassing om te communiceren met het micro soft Identity-platform. Omdat de OpenID Connect Connect middleware cookies op de achtergrond gebruikt, moet u ook cookie-verificatie instellen als de voor gaande code. De *ValidateIssuer* -waarde vertelt OpenIdConnect niet om de toegang tot één specifieke organisatie te beperken.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Een controller toevoegen om aanmeldings- en afmeldingsaanvragen te verwerken

Voer de volgende stappen uit om een nieuwe controller te maken om aanmeldings-en afmeldings methoden weer te geven:

1.  Klik met de rechter muisknop op de map **controllers** en selecteer**controller** **toevoegen** > .
2.  Selecteer **MVC-controller (.NET-versie) – Leeg**.
3.  Selecteer **Toevoegen**.
4.  Noem deze **HomeController** en selecteer vervolgens **toevoegen**.
5.  OWIN-verwijzingen toevoegen aan de klasse:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Voeg de volgende twee methoden toe voor het afhandelen van de aanmelding en het afmelden bij uw controller door een verificatie Challenge te initiëren:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>De start pagina van de app voor het aanmelden van gebruikers maken

Maak in Visual Studio een nieuwe weer gave om de aanmeldings knop toe te voegen en om gebruikers gegevens na verificatie weer te geven:

1.  Klik met de rechtermuisknop op de map **Weergaven\Startpagina** en selecteer **Weergave toevoegen**.
2.  Geef een naam op voor de nieuwe weer gave- **index**.
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

### <a name="more-information"></a>Meer informatie
 Met deze pagina wordt een aanmeldknop toegevoegd, in de SVG-indeling met een zwarte achtergrond:<br/>![Aanmelden bij Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Voor meer aanmeldings knoppen gaat u naar de [richt lijnen voor huis stijl](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Huisstijlrichtlijnen").

## <a name="add-a-controller-to-display-users-claims"></a>Een controller toevoegen om claims van de gebruiker weer te geven
Deze controller demonstreert het gebruik van het kenmerk `[Authorize]` om een controller te beveiligen. Dit kenmerk beperkt de toegang tot de controller door alleen geverifieerde gebruikers toe te staan. De volgende code maakt gebruik van het kenmerk om gebruikers claims weer te geven die zijn opgehaald als onderdeel van de aanmelding:

1.  Klik met de rechter muisknop op de map **controllers** en selecteer vervolgens**controller** **toevoegen** > .
2.  Selecteer **MVC-controller {versie} – Leeg**.
3.  Selecteer **Toevoegen**.
4.  Noem deze **ClaimsController**.
5.  Vervang de code van uw klasse controller door de volgende code. Hiermee voegt u `[Authorize]` het kenmerk toe aan de klasse:

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

### <a name="more-information"></a>Meer informatie
Vanwege het gebruik van het `[Authorize]` kenmerk, kunnen alle methoden van deze controller alleen worden uitgevoerd als de gebruiker is geverifieerd. Als de gebruiker niet is geverifieerd en probeert toegang te krijgen tot de controller, initieert OWIN een verificatie test en dwingt de gebruiker zich te verifiëren. De voor gaande code bekijkt de lijst met claims voor specifieke gebruikers kenmerken die zijn opgenomen in het id-token van de gebruiker. Deze kenmerken omvatten de volledige naam en gebruikersnaam van de gebruiker, en het globale onderwerp voor de gebruikers-id. Ze omvatten ook de *Tenant-id*. Dit is de id voor de organisatie van de gebruiker.

## <a name="create-a-view-to-display-the-users-claims"></a>Een weergave maken om de claims van de gebruiker weer te geven

Maak in Visual Studio een nieuwe weergave om de claims van de gebruiker weer te geven op een webpagina:

1.  Klik met de rechter muisknop op de map **Views\Claims** en selecteer vervolgens **weer gave toevoegen**.
2.  Geef een naam op voor de nieuwe weer gave- **index**.
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

U hebt twee opties om uw toepassing te registreren en de registratie gegevens van uw toepassing aan uw oplossing toe te voegen:

### <a name="option-1-express-mode"></a>Optie 1: Express-modus

Voer de volgende stappen uit om uw toepassing snel te registreren:

1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) .
1. Voer een naam in voor de toepassing en selecteer **Registreren**.
1. Volg de instructies voor het downloaden en automatisch configureren van uw nieuwe toepassing met één klik.

### <a name="option-2-advanced-mode"></a>Optie 2: geavanceerde modus

Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:

1. Open Visual Studio en voer vervolgens de volgende handelingen uit:
   1. in Solution Explorer selecteert u het project en bekijkt u de venster Eigenschappen (als u geen venster Eigenschappen ziet, drukt u op F4).
   1. Wijzig SSL ingeschakeld in `True`.
   1. Klik met de rechter muisknop op het project in Visual Studio, selecteer **Eigenschappen**en selecteer vervolgens het tabblad **Web** . In de sectie **servers** wijzigt u de instelling van de **project-URL** in de SSL- **URL**.
   1. Kopieer de SSL-URL. In de volgende stap gaat u deze URL toevoegen aan de lijst met omleidings-Url's in de lijst met omleidings-Url's van de registratie Portal.<br/><br/>![Projecteigenschappen](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Meld u met een werk-of school account aan bij de [Azure Portal](https://portal.azure.com) of gebruik een persoonlijke Microsoft-account.
1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek en stelt u uw portal sessie in op de gewenste Azure AD-Tenant.
1. Ga naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecteer **nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app, zoals **ASPNET-zelf studie**.
   1. Voeg de SSL-URL die u hebt gekopieerd uit Visual Studio in stap 1 ( `https://localhost:44368/`bijvoorbeeld) toe aan de **antwoord-URL**en selecteer **registreren**.
1. Selecteer het menu **verificatie** , selecteer **id-tokens** onder **impliciete toekenning**en selecteer vervolgens **Opslaan**.
1. Voeg het volgende toe aan het bestand Web. config, dat zich in de hoofdmap van `configuration\appSettings` de sectie bevindt:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Vervang `ClientId` door de toepassings-id die u zojuist hebt geregistreerd.
1. Vervang `redirectUri` door de SSL-URL van uw project.

## <a name="test-your-code"></a>Uw code testen

Als u uw toepassing wilt testen in Visual Studio, drukt u op F5 om het project uit te voeren. De browser wordt geopend op de<span></span>locatie http://localhost: {Port} en u ziet de knop **Aanmelden met Microsoft** . Selecteer de knop om het aanmeldings proces te starten.

Wanneer u klaar bent om uw test uit te voeren, gebruikt u een Azure AD-account (werk-of school account) of een persoonlijk Microsoft-account (<span>Live.</span> com of <span>Outlook.</span> com) om u aan te melden.

![Aanmelden bij Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Meld u aan bij uw Microsoft-account](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Machtigingen en toestemming in het micro soft Identity platform-eind punt

Toepassingen die zijn geïntegreerd met het micro soft Identity-platform, volgen een autorisatie model waarmee gebruikers en beheerders controle kunnen krijgen over de manier waarop gegevens worden geopend. Nadat een gebruiker is geverifieerd met het micro soft-identiteits platform voor toegang tot deze toepassing, wordt u gevraagd om toestemming te geven aan de machtigingen die door de toepassing zijn aangevraagd (uw basis profiel bekijken en de toegang tot gegevens waartoe u toegang hebt verleend). Nadat deze machtigingen zijn geaccepteerd, blijft de gebruiker de resultaten van de toepassing. Het is echter mogelijk dat de gebruiker wordt gevraagd om een **beheerders toestemming** pagina als een van de volgende situaties zich voordoet:

- De ontwikkelaar van de toepassing voegt extra machtigingen toe waarvoor **beheerders toestemming**nodig heeft.
- Of de Tenant is geconfigureerd (in **Enter prise-toepassingen-> gebruikers instellingen**), waarbij gebruikers niet kunnen instemmen op apps die namens hen toegang hebben tot Bedrijfs gegevens.

Raadpleeg [machtigingen en toestemming in het micro soft Identity platform-eind punt](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)voor meer informatie.

### <a name="view-application-results"></a>Toepassings resultaten weer geven

Nadat u zich hebt aangemeld, wordt de gebruiker omgeleid naar de start pagina van uw website. De start pagina is de HTTPS-URL die is opgegeven in de registratie gegevens van uw toepassing in de micro soft Application Registration-Portal. De start pagina bevat het welkomst bericht *' Hello \<User> '* , een koppeling voor afmelden en een koppeling om de claims van de gebruiker weer te geven. De koppeling voor de claims van de gebruiker maakt verbinding met de claim controller die u eerder hebt gemaakt.

### <a name="view-the-users-claims"></a>De claims van de gebruiker weer geven

Als u de claims van de gebruiker wilt weer geven, selecteert u de koppeling om te bladeren naar de controller weergave die alleen beschikbaar is voor geverifieerde gebruikers.

#### <a name="view-the-claims-results"></a>De claim resultaten weer geven

Nadat u naar de weer gave controller hebt gebladerd, ziet u een tabel die de basis eigenschappen voor de gebruiker bevat:

|Eigenschap |Waarde |Beschrijving |
|---|---|---|
|**Naam** |Volledige naam van de gebruiker | Voor- en achternaam van de gebruiker
|**Gebruikers** |gebruiker<span>@domain.com</span> | De gebruikers naam die wordt gebruikt om de gebruiker te identificeren|
|**Onderwerp** |Onderwerp |Een teken reeks waarmee de gebruiker op het web op unieke wijze wordt geïdentificeerd|
|**Tenant-id** |GUID | Een unieke **GUID** die de Azure AD-organisatie van de gebruiker vertegenwoordigt|

Daarnaast ziet u een tabel met alle claims in de verificatie aanvraag. Zie de [lijst met claims die zich in een ID-token bevinden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)voor meer informatie.

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Toegang tot een methode met een kenmerk voor autoriseren testen (optioneel)

Voer de volgende stappen uit om de toegang te testen als een anonieme gebruiker naar `Authorize` een controller die wordt beveiligd door het kenmerk:

1. Selecteer de koppeling om u aan te melden bij de gebruiker en het afmeldings proces te volt ooien.
2. Typ http://<span></span>localhost: {Port}/claims in uw browser om toegang te krijgen tot uw controller die wordt beveiligd `Authorize` door het kenmerk.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Verwachte resultaten na toegang tot een beveiligde controller

U wordt gevraagd om u te verifiëren om de weer gave beveiligde controller te gebruiken.

## <a name="advanced-options"></a>Geavanceerde opties

### <a name="protect-your-entire-website"></a>Uw hele website beveiligen

Als u uw hele website wilt beveiligen, voegt u in het bestand **Global. asax** het `AuthorizeAttribute` kenmerk `GlobalFilters` toe aan het `Application_Start` filter in de methode:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>Beperken wie zich kan aanmelden bij uw toepassing

Wanneer u de toepassing bouwt die door deze hand leiding wordt gemaakt, accepteert uw toepassing standaard aanmeldingen van persoonlijke accounts (waaronder outlook.com, live.com en anderen), evenals werk-en school accounts van elk bedrijf of organisatie dat is geïntegreerd met het micro soft Identity platform. Dit is een aanbevolen optie voor SaaS-toepassingen.

Als u toegang door gebruikers wilt beperken voor uw toepassing, zijn er meerdere opties beschikbaar.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Optie 1: gebruikers beperken tot slechts één organisatie Active Directory exemplaar aanmelden bij uw toepassing (single-tenant)

Deze optie wordt vaak gebruikt voor *LOB-toepassingen*: als u wilt dat uw toepassing aanmeldingen alleen accepteert van accounts die deel uitmaken van een specifiek exemplaar van Azure AD (met inbegrip van *Gast accounts* van die instantie), voert u de volgende stappen uit:

1. Wijzig in het bestand Web. config de waarde voor de `Tenant` para meter van `Common` naar de Tenant naam van de organisatie, zoals. `contoso.onmicrosoft.com`
2. Stel in uw [OWIN-opstart klasse](#configure-the-authentication-pipeline)het `ValidateIssuer` argument in `true`op.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>Optie 2: de toegang tot gebruikers in een specifieke lijst met organisaties beperken

U kunt aanmeldings toegang beperken tot alleen de gebruikers accounts die zich in een Azure AD-organisatie in de lijst met toegestane organisaties bevinden:
1. Stel in uw [OWIN-opstart klasse](#configure-the-authentication-pipeline)het `ValidateIssuer` argument in `true`op.
2. Stel de waarde van de `ValidIssuers` para meter in op de lijst met toegestane organisaties.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Optie 3: een aangepaste methode gebruiken om verleners te valideren

U kunt een aangepaste methode implementeren om verleners te valideren met behulp van de para meter **IssuerValidator** . Zie [TokenValidationParameters-klasse](/previous-versions/visualstudio/dn464192(v=vs.114))voor meer informatie over het gebruik van deze para meter.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe web-apps web-Api's kunnen aanroepen.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Meer informatie over het maken van de toepassing die wordt gebruikt in deze Snelstartgids

Meer informatie over web apps die web-Api's aanroepen met het micro soft Identity-platform:

> [!div class="nextstepaction"]
> [Web-apps die web-Api's aanroepen](scenario-web-app-sign-user-overview.md)

Meer informatie over het bouwen van web-apps die Microsoft Graph aanroepen:

> [!div class="nextstepaction"]
> [Microsoft Graph zelf studie voor ASP.NET](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
