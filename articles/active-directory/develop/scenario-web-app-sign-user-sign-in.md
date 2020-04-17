---
title: Schrijf een web-app die gebruikers aan-/uitmeldt - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een web-app die gebruikers aan-/uitlogt
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
ms.openlocfilehash: 2ab5697ceff612e65174fdb7f9ef6137e2c8b9a5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537063"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Web-app die zich aanmeldt bij gebruikers: aanmelden en afmelden

Meer informatie over het toevoegen van aanmelding aan de code voor uw web-app die gebruikers aanmeldt. Leer vervolgens hoe je ze laten afmelden.

## <a name="sign-in"></a>Aanmelden

Aanmelden bestaat uit twee delen:

- De aanmeldingsknop op de HTML-pagina
- De aanmeldingsactie in de code-behind in de controller

### <a name="sign-in-button"></a>Aanmeldingsknop

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core wordt de aanmeldingsknop weergegeven in `Views\Shared\_LoginPartial.cshtml`. Het wordt alleen weergegeven als er geen geverifieerd account is. Dat wil zeggen, het wordt weergegeven wanneer de gebruiker zich nog niet heeft aangemeld of zich heeft afgemeld.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC wordt de afmeldingsknop weergegeven in `Views\Shared\_LoginPartial.cshtml`. Het wordt alleen weergegeven als er een geverifieerd account is. Dat wil zeggen, het wordt weergegeven wanneer de gebruiker zich eerder heeft aangemeld.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

In onze Java quickstart bevindt de aanmeldingsknop zich in het [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) bestand.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

In de Snelle Start van Python is er geen aanmeldingsknop. De code-behind vraagt de gebruiker automatisch om zich aan te melden wanneer deze de basis van de web-app bereikt. Zie [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`actie van de controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Als ASP.NET, activeert het selecteren van de `SignIn` **aanmeldingsknop** in de web-app de actie op de `AccountController` controller. In eerdere versies van de `Account` ASP.NET kernsjablonen is de controller ingebed in de web-app. Dat is niet langer het geval, omdat de controller nu deel uitmaakt van het ASP.NET Core framework.

De code `AccountController` voor is beschikbaar in de ASP.NET Core repository in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Het accountbeheer daagt de gebruiker uit door door te verwijzen naar het eindpunt van het Microsoft-identiteitsplatform. Zie voor meer informatie de [SignIn-methode](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) die wordt geleverd als onderdeel van ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt afmelden `SignOut()` geactiveerd vanuit de methode op een controller (bijvoorbeeld [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Deze methode maakt geen deel uit van het ASP.NET framework (in tegenstelling tot wat er in ASP.NET Core gebeurt). Het stuurt een OpenID sign-in challenge na het voorstellen van een omleiding URI.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

In Java wordt afmelden afgehandeld door het `logout` eindpunt van het `post_logout_redirect_uri` Microsoft-identiteitsplatform rechtstreeks te bellen en de waarde te verstrekken. Zie [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)voor meer informatie.

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

In tegenstelling tot andere platforms zorgt MSAL Python ervoor dat de gebruiker zich laat inloggen vanaf de aanmeldingspagina. Zie [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

De `_build_msal_app()` methode wordt als volgt gedefinieerd in [app.py#L81-L88:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88)

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Nadat de gebruiker zich bij uw app heeft aangemeld, wilt u hem of haar laten afmelden.

## <a name="sign-out"></a>Afmelden

Als u zich afmeldt bij een web-app, wordt de informatie over het aangemelde account verwijderd uit de status van de web-app.
De web-app moet de gebruiker ook `logout` doorverwijzen naar het eindpunt van het Microsoft-identiteitsplatform om zich af te melden.

Wanneer uw web-app de `logout` gebruiker doorverwijst naar het eindpunt, wordt de sessie van de gebruiker met dit eindpunt gewist uit de browser. Als uw app niet naar `logout` het eindpunt is gegaan, wordt de gebruiker opnieuw geverifieerd naar uw app zonder zijn of haar referenties opnieuw in te voeren. De reden hiervoor is dat ze een geldige aanmeldingssessie hebben met het eindpunt van het Microsoft-identiteitsplatform.

Zie de sectie [Een afmeldingsverzoek verzenden](v2-protocols-oidc.md#send-a-sign-out-request) in het Microsoft-identiteitsplatform en de documentatie van [het OpenID Connect-protocol](v2-protocols-oidc.md) voor meer informatie.

### <a name="application-registration"></a>Een toepassing registreren

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Tijdens de registratie van de aanvraag registreert u een na-uitloging URI. In onze zelfstudie `https://localhost:44321/signout-oidc` hebt u zich geregistreerd in het **veld URL-aanmelding** van de sectie **Geavanceerde instellingen** op de **pagina Verificatie.** Zie De [webApp-app registreren](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)voor meer informatie .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Tijdens de registratie van de aanvraag registreert u een na-uitloging URI. In onze zelfstudie `https://localhost:44308/Account/EndSession` hebt u zich geregistreerd in het **veld URL-aanmelding** van de sectie **Geavanceerde instellingen** op de **pagina Verificatie.** Zie De [webApp-app registreren](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)voor meer informatie .

# <a name="java"></a>[Java](#tab/java)

Tijdens de registratie van de aanvraag registreert u een na-uitloging URI. In onze zelfstudie `http://localhost:8080/msal4jsample/sign_out` hebt u zich geregistreerd in het **veld URL-aanmelding** van de sectie **Geavanceerde instellingen** op de **pagina Verificatie.**

# <a name="python"></a>[Python](#tab/python)

Tijdens de registratie van de aanvraag hoeft u geen extra afmeld-URL te registreren. De app wordt teruggeroepen op de belangrijkste URL.

---

### <a name="sign-out-button"></a>Afmelden, knop

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core wordt de afmeldingsknop weergegeven in `Views\Shared\_LoginPartial.cshtml`. Het wordt alleen weergegeven als er een geverifieerd account is. Dat wil zeggen, het wordt weergegeven wanneer de gebruiker zich eerder heeft aangemeld.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC wordt de afmeldingsknop weergegeven in `Views\Shared\_LoginPartial.cshtml`. Het wordt alleen weergegeven als er een geverifieerd account is. Dat wil zeggen, het wordt weergegeven wanneer de gebruiker zich eerder heeft aangemeld.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

In onze Java quickstart bevindt de afmeldknop zich in het main/resources/templates/auth_page.html bestand.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

In de Snelstart van Python bevindt de afmeldingsknop zich in het bestand [templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`actie van de controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET activeert het selecteren van de `SignOut` **knop Afmelden** in de web-app de actie op de `AccountController` controller. In eerdere versies van de `Account` ASP.NET Core-sjablonen is de controller ingebed in de web-app. Dat is niet langer het geval, omdat de controller nu deel uitmaakt van het ASP.NET Core framework.

De code `AccountController` voor de is beschikbaar vanuit de ASP.NET core repository in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Het accountbeheer:

- Hiermee stelt u een `/Account/SignedOut` OpenID-omleiding in naar de controller, zodat de controller wordt teruggeroepen wanneer Azure AD de afmelding heeft voltooid.
- Oproepen `Signout()`, waarmee de OpenID Connect middleware `logout` contact kan opnemen met het eindpunt van het Microsoft-identiteitsplatform. Het eindpunt dan:

  - De sessiecookie wordt gewist bij de browser.
  - Roept de uitlog-URL terug. Standaard wordt op de uitlog-URL de afmeldweergavepagina [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)weergegeven. Deze pagina is ook beschikbaar als onderdeel van ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt afmelden `SignOut()` geactiveerd vanuit de methode op een controller (bijvoorbeeld [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Deze methode maakt geen deel uit van het ASP.NET kader, in tegenstelling tot wat er gebeurt in ASP.NET Core. Het:

- Hiermee verzendt u een OpenID-uitmeldingsuitdaging.
- Ruimt de cache.
- Verwijst door naar de pagina die het wil.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

In Java wordt afmelden afgehandeld door het `logout` eindpunt van het `post_logout_redirect_uri` Microsoft-identiteitsplatform rechtstreeks te bellen en de waarde te verstrekken. Zie [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)voor meer informatie.

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

De code die de gebruiker afmeldt staat in [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Het gesprek naar `logout` het eindpunt onderscheppen

De post-logout URI stelt applicaties in staat om deel te nemen aan de wereldwijde afmelding.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Met de ASP.NET Core OpenID Connect middleware kan uw `logout` app het gesprek naar het `OnRedirectToIdentityProviderForSignOut`eindpunt van het Microsoft-identiteitsplatform onderscheppen door een OpenID Connect-gebeurtenis met de naam . Zie [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)voor een voorbeeld van hoe u zich abonneren op deze gebeurtenis (om de tokencache te wissen).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET delegeren aan de middleware om de afmelding uit te voeren, waarbij u de sessiecookie vrijmaakt:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

In de Java quickstart geeft de post-logout redirect URI gewoon de index.html-pagina weer.

# <a name="python"></a>[Python](#tab/python)

In de Python quickstart geeft de post-logout redirect URI alleen de index.html-pagina weer.

---

## <a name="protocol"></a>Protocol

Als u meer wilt weten over afmelden, leest u de protocoldocumentatie die beschikbaar is via [Open ID Connect.](./v2-protocols-oidc.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verplaatsen naar productie](scenario-web-app-sign-user-production.md)
