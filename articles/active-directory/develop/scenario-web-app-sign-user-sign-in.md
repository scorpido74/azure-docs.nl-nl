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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086464"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Web-app waarmee gebruikers zich aanmelden

Meer informatie over het toevoegen van een aanmelding aan de code voor uw web-app die gebruikers aanmeldt.

## <a name="sign-in"></a>Aanmelden

De code die we in de vorige artikel [code configuratie van de app](scenario-web-app-sign-user-app-configuration.md) hebben bekeken, is alleen nodig voor het implementeren van aanmelding.
Zodra de gebruiker zich heeft aangemeld bij uw app, wilt u deze waarschijnlijk inschakelen om u af te melden. ASP.NET-kern afmeldingen voor u.

## <a name="what-sign-out-involves"></a>Welke afmelding moet worden uitgevoerd

Afmelden bij een web-app is meer dan het verwijderen van de informatie over het aangemelde account uit de status van de web-app.
De web-app moet ook de gebruiker omleiden naar het micro `logout` Soft Identity platform-eind punt om u af te melden. Als uw web-app de gebruiker omleidt naar het `logout` eind punt, wordt met dit eind punt de sessie van de gebruiker uit de browser gewist. Als uw app niet naar het `logout` eind punt heeft gereageerd, wordt de gebruiker opnieuw geverifieerd bij uw app zonder de referenties opnieuw in te voeren, omdat ze een geldige sessie voor eenmalige aanmelding met het micro soft Identity platform-eind punt hebben.

Zie de sectie [een aanvraag voor een afmelding verzenden](v2-protocols-oidc.md#send-a-sign-out-request) in het [micro soft Identity-platform en de conceptuele documentatie van het OpenID Connect Connect-protocol](v2-protocols-oidc.md) voor meer informatie.

### <a name="application-registration"></a>Toepassingsregistratie

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Tijdens de registratie van de toepassing hebt u een **post afmeldings-URI**geregistreerd. In onze zelf studie hebt u `https://localhost:44321/signout-oidc` geregistreerd in het veld **afmeldings-URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** . Zie [de webApp-app registreren](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) voor meer informatie.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Tijdens de registratie van de toepassing hebt u een **post afmeldings-URI**geregistreerd. In onze zelf studie hebt u `https://localhost:44308/Account/EndSession` geregistreerd in het veld **afmeldings-URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** . Zie [de webApp-app registreren](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet) voor meer informatie.

# <a name="javatabjava"></a>[Java](#tab/java)

Tijdens de registratie van de toepassing registreert u een URI voor een **post afmelding**. In onze zelf studie hebt u `http://localhost:8080/msal4jsample/` geregistreerd in het veld **afmeldings-URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** .

# <a name="pythontabpython"></a>[Python](#tab/python)

Tijdens de registratie van de toepassing hoeft u geen afmeldings-URL te registreren. Het voor beeld implementeert geen globale afmelding

---

### <a name="sign-out-button"></a>Knop voor afmelden

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

In ASP.net core wordt de afmeldings knop weer gegeven in `Views\Shared\_LoginPartial.cshtml` en wordt deze alleen getoond wanneer er een geverifieerd account is (dat wil zeggen dat de gebruiker zich eerder heeft aangemeld).

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

In ASP.NET MVC is de knop Afmelden beschikbaar in `Views\Shared\_LoginPartial.cshtml` en wordt alleen weer gegeven wanneer er een geverifieerd account is (dat wil zeggen dat de gebruiker zich eerder heeft aangemeld).

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

In de Java-Snelstartgids bevindt de afmeldings knop zich in het main/resources/templates/auth_page.html-bestand

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

De afmeldings knop bevindt zich in de python-Snelstartgids in het [Templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20) -bestand

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`actie van de controller

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Als u in ASP.net op de knop voor **Afmelden** op de web-app `SignOut` drukt, wordt `AccountController` de actie op de controller geactiveerd. In eerdere versies van de ASP.net core-sjablonen is `Account` de controller Inge sloten met de web-app, maar het is niet langer het geval omdat het nu deel uitmaakt van het ASP.net core-Framework zelf.

De code voor de `AccountController` is beschikbaar vanuit de ASP.net-kern opslagplaats op van [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Het account beheer:

- Hiermee stelt u een omleidings-URI voor OpenID Connect in `/Account/SignedOut` zodat de controller weer wordt aangeroepen wanneer Azure AD de afmelding heeft uitgevoerd
- Aanroepen `Signout()`, waarmee de OpenIdConnect-middleware contact kan opnemen met `logout` het micro soft Identity platform-eind punt:

  - Hiermee wist u de sessie cookie uit de browser en
  - de **afmeldings-URL**wordt weer gegeven, die standaard de pagina afgemelde weer gave [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) weergeeft, ook als onderdeel van ASP.net core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Bij ASP.net wordt afmelden geactiveerd vanuit de `SignOut()` methode op een controller (bijvoorbeeld AccountController). Deze methode maakt geen deel uit van het ASP.NET-Framework (in tegens telling tot wat er gebeurt in ASP.NET Core). Dit wordt niet `async`gebruikt en dat is de reden:

- Hiermee wordt een OpenID Connect-afmeldings uitdaging verzonden
- Hiermee wordt de cache gewist
- Hiermee wordt omgeleid naar de pagina die het wil

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

In Java wordt de afmelding afgehandeld door het afmelden van het micro soft Identity platform-eind punt rechtstreeks aan te roepen en de post_logout_redirect_uri op te geven.

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

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>De aanroep naar het `logout` eind punt wordt onderschept

Met de afmeldings-URI van de post kunnen toepassingen deel nemen aan de globale afmelding.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Met de ASP.net core OpenIdConnect middleware kan uw app de aanroep naar het micro soft Identity platform `logout` -eind punt onderscheppen door een `OnRedirectToIdentityProviderForSignOut`OpenIdConnect-gebeurtenis op te geven met de naam. Zie [micro soft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) voor een voor beeld van hoe u zich abonneert op deze gebeurtenis (om de token cache te wissen)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

In ASP.NET die u aan de middleware delegeert om de afmelding uit te voeren, wordt de sessie cookie gewist:

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

De omleidings-URI voor afmelden in de Java-Snelstartgids bevat alleen de index. html-pagina 

# <a name="pythontabpython"></a>[Python](#tab/python)

In de Quick Start van python, de omleidings-URI na afmelden, wordt de pagina index. html weer gegeven.

---

## <a name="protocol"></a>Protocol

Als u meer wilt weten over afmelden, leest u de protocol documentatie, die beschikbaar is via [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-web-app-sign-user-production.md)
