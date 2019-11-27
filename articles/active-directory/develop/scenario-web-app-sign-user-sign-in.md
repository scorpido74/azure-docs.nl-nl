---
title: Web-app die gebruikers aanmeldt (aanmelden)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die gebruikers aanmeldt (aanmelden)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b41e46401d600ebaba1febb86aafbd55c8399a
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482557"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Web-app die gebruikers aanmeldt: aanmelden en afmelden

Meer informatie over het toevoegen van een aanmelding aan de code voor uw web-app die gebruikers aantekent. Lees vervolgens hoe ze zich kunnen afmelden.

## <a name="sign-in"></a>Aanmelden

Aanmelden bestaat uit twee delen:

- De knop aanmelden op de HTML-pagina
- De aanmeldings actie in de onderliggende code in de controller

### <a name="sign-in-button"></a>Knop Aanmelden

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core wordt de aanmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml`. Deze wordt alleen weer gegeven als er geen geverifieerd account is. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich nog niet heeft aangemeld of zich heeft afgemeld.

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC wordt de afmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml`. Deze wordt alleen weer gegeven wanneer er een geverifieerd account is. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich eerder heeft aangemeld.

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

# <a name="javatabjava"></a>[Java](#tab/java)

In de Java Quick Start bevindt de aanmeldings knop zich in het [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) -bestand.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

In de Quick Start van python is er geen knop aanmelden. De code-behind vraagt de gebruiker automatisch om zich aan te melden wanneer de hoofdmap van de web-app wordt bereikt. Zie [app. py # l14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` actie van de controller

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Als u in ASP.NET de **aanmeldings** knop in de web-app selecteert, wordt de actie `SignIn` op de `AccountController` controller geactiveerd. In eerdere versies van de ASP.NET-kern sjablonen werd de `Account`-controller Inge sloten met de web-app. Dat is niet langer het geval omdat de controller nu deel uitmaakt van het ASP.NET Core Framework.

De code voor `AccountController` is beschikbaar vanuit de ASP.NET Core opslagplaats in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). De account besturing vraagt de gebruiker door omleiden naar het micro soft Identity platform-eind punt. Zie voor meer informatie de [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) -methode die deel uitmaakt van ASP.net core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt afmelden geactiveerd vanuit de methode `SignOut()` op een controller (bijvoorbeeld [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Deze methode maakt geen deel uit van het ASP.NET-Framework (in tegens telling tot wat er gebeurt in ASP.NET Core). Er wordt een OpenID Connect-aanmeldings vraag verzonden nadat een omleidings-URI is voorgesteld.

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

In Java wordt afmelden verwerkt door het micro soft Identity platform `logout`-eind punt rechtstreeks aan te roepen en de `post_logout_redirect_uri` waarde op te geven. Zie [AuthPageController. java # l30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)voor meer informatie.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

In tegens telling tot andere platforms zorgt MSAL python ervoor dat de gebruiker zich aanmeldt vanaf de aanmeldings pagina. Zie [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

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

De `_build_msal_app()` methode wordt als volgt gedefinieerd in [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) :

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

Nadat de gebruiker zich heeft aangemeld bij uw app, moet u deze inschakelen om u af te melden.

## <a name="sign-out"></a>Afmelden

Afmelden bij een web-app omvat meer dan het verwijderen van de informatie over het aangemelde account uit de status van de web-app.
De web-app moet ook de gebruiker omleiden naar het micro soft Identity platform `logout`-eind punt om u af te melden. 

Als uw web-app de gebruiker omleidt naar het `logout`-eind punt, wordt met dit eind punt de sessie van de gebruiker uit de browser gewist. Als uw app niet naar het `logout`-eind punt gaat, wordt de gebruiker opnieuw geverifieerd bij uw app zonder de referenties opnieuw in te voeren. De reden hiervoor is dat er een geldige sessie voor eenmalige aanmelding met het micro soft Identity platform-eind punt is.

Zie de sectie [een aanvraag voor een afmelding verzenden](v2-protocols-oidc.md#send-a-sign-out-request) in het [micro soft Identity-platform en de OpenID Connect Connect protocol](v2-protocols-oidc.md) -documentatie voor meer informatie.

### <a name="application-registration"></a>Een toepassing registreren

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Tijdens de registratie van de toepassing registreert u een URI na afmelding. In onze zelf studie hebt u `https://localhost:44321/signout-oidc` geregistreerd in het veld **Afmeldings-URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** . Zie [de webApp-app registreren](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)voor meer informatie.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Tijdens de registratie van de toepassing registreert u een URI na afmelding. In onze zelf studie hebt u `https://localhost:44308/Account/EndSession` geregistreerd in het veld **Afmeldings-URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** . Zie [de webApp-app registreren](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)voor meer informatie.

# <a name="javatabjava"></a>[Java](#tab/java)

Tijdens de registratie van de toepassing registreert u een URI na afmelding. In onze zelf studie hebt u `http://localhost:8080/msal4jsample/sign_out` geregistreerd in het veld **Afmeldings-URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Tijdens de registratie van de toepassing hoeft u geen extra afmeldings-URL te registreren. De app wordt teruggebeld op de hoofd-URL.

---

### <a name="sign-out-button"></a>Knop voor afmelden

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.NET Core wordt de afmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml`. Deze wordt alleen weer gegeven wanneer er een geverifieerd account is. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich eerder heeft aangemeld.

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET MVC wordt de afmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml`. Deze wordt alleen weer gegeven wanneer er een geverifieerd account is. Dat wil zeggen dat het wordt weer gegeven wanneer de gebruiker zich eerder heeft aangemeld.

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

# <a name="javatabjava"></a>[Java](#tab/java)

In de Java-Snelstartgids bevindt de afmeldings knop zich in het main/resources/templates/auth_page.html-bestand.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

In de Quick Start van python bevindt de afmeldings knop zich in het bestand [Templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` actie van de controller

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Als u in ASP.NET de knop voor **Afmelden** in de web-app selecteert, wordt de actie `SignOut` op de `AccountController` controller geactiveerd. In vorige versies van de ASP.NET Core sjablonen is de `Account`-controller Inge sloten met de web-app. Dat is niet langer het geval omdat de controller nu deel uitmaakt van het ASP.NET Core Framework.

De code voor de `AccountController` is beschikbaar vanuit de ASP.NET core-opslag plaats in [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Het account beheer:

- Hiermee wordt een omleidings-URI voor OpenID Connect ingesteld op `/Account/SignedOut`, zodat de controller weer wordt aangeroepen wanneer Azure AD de afmelding heeft voltooid.
- Aanroepen `Signout()`, waarmee de OpenID Connect Connect middleware contact kan opnemen met het micro soft Identity platform `logout`-eind punt. Het eind punt vervolgens:

  - Hiermee wordt de sessie cookie uit de browser gewist.
  - Roept de afmeldings-URL terug. De afmeldings-URL geeft standaard de afgemelde weergave pagina [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Deze pagina wordt ook meegeleverd als onderdeel van ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET wordt afmelden geactiveerd vanuit de methode `SignOut()` op een controller (bijvoorbeeld [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Deze methode maakt geen deel uit van het ASP.NET-Framework, in tegens telling tot wat er gebeurt in ASP.NET Core. ,

- Hiermee wordt een OpenID Connect-afmeldings Challenge verzonden.
- Hiermee wordt de cache gewist.
- Hiermee wordt omgeleid naar de pagina die het wil.

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

In Java wordt afmelden verwerkt door het micro soft Identity platform `logout`-eind punt rechtstreeks aan te roepen en de `post_logout_redirect_uri` waarde op te geven. Zie [AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)voor meer informatie.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

De code waarmee de gebruiker zich afmeldt, bevindt zich in [app. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>De aanroep naar het `logout`-eind punt wordt onderschept

Met de URI na afmelding kunnen toepassingen deel nemen aan de globale afmelding.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Met de ASP.NET Core OpenID Connect Connect middleware kan uw app de aanroep naar het micro soft Identity platform `logout`-eind punt onderscheppen door een OpenID Connect Connect-gebeurtenis op te geven met de naam `OnRedirectToIdentityProviderForSignOut`. Zie [micro soft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)voor een voor beeld van hoe u zich abonneert op deze gebeurtenis (om de token cache te wissen).

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET delegeren aan de middleware voor het uitvoeren van de afmelding, het verwijderen van de sessie cookie:

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

De omleidings-URI van de post-afmelding wordt in de Java Quick Start alleen de pagina index. html weer gegeven.

# <a name="pythontabpython"></a>[Python](#tab/python)

In de Quick Start van python wordt met de omleidings-URI na afmelden alleen de pagina index. html weer gegeven.

---

## <a name="protocol"></a>Protocol

Als u meer wilt weten over afmelden, leest u de protocol documentatie die beschikbaar is via [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-web-app-sign-user-production.md)
