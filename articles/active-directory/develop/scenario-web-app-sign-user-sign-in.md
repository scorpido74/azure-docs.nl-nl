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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: be7801515355452306cd5e7afa709a0681c7c314
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562196"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Web-app waarmee gebruikers zich aanmelden

Meer informatie over het toevoegen van een aanmelding aan de code voor uw web-app die gebruikers aanmeldt.

## <a name="sign-in"></a>Aanmelden

De code die u in de voor gaande artikel [code configuratie van de app](scenario-web-app-sign-user-app-configuration.md) hebt opgenomen, is alleen nodig voor het implementeren van afmelding. Zodra de gebruiker zich heeft aangemeld bij uw app, wilt u deze waarschijnlijk inschakelen om u af te melden. ASP.NET-kern afmeldingen voor u.

## <a name="what-sign-out-involves"></a>Welke afmelding moet worden uitgevoerd

Afmelden bij een web-app is meer dan het verwijderen van de informatie over het aangemelde account uit de status van de web-app.
De web-app moet ook de gebruiker omleiden naar het micro `logout` Soft Identity platform-eind punt om u af te melden. Als uw web-app de gebruiker omleidt naar het `logout` eind punt, wordt met dit eind punt de sessie van de gebruiker uit de browser gewist. Als uw app niet naar het `logout` eind punt heeft gereageerd, wordt de gebruiker opnieuw geverifieerd bij uw app zonder de referenties opnieuw in te voeren, omdat ze een geldige sessie voor eenmalige aanmelding met het micro soft Identity platform-eind punt hebben.

Zie de sectie [een aanvraag voor een afmelding verzenden](v2-protocols-oidc.md#send-a-sign-out-request) in het [micro soft Identity-platform en de conceptuele documentatie van het OpenID Connect Connect-protocol](v2-protocols-oidc.md) voor meer informatie.

## <a name="application-registration"></a>Toepassingsregistratie

Tijdens de registratie van de toepassing hebt u een post afmeldings- **URI**geregistreerd. In onze zelf studie hebt u `https://localhost:44321/signout-oidc` geregistreerd in het veld afmeldings- **URL** van de sectie **Geavanceerde instellingen** op de pagina **verificatie** . Zie [de webApp-app registreren](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) voor meer informatie.

## <a name="aspnet-core-code"></a>ASP.NET Core code

### <a name="signout-button"></a>Knop Afmelden

De knop Afmelden is beschikbaar in `Views\Shared\_LoginPartial.cshtml` en wordt alleen weer gegeven wanneer er een geverifieerd account is (dat wil zeggen dat de gebruiker zich eerder heeft aangemeld).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`actie van de`AccountController`

Als u op de knop Afmelden in de web- `SignOut` app drukt, `Account` wordt de actie op de controller geactiveerd. In eerdere versies van de ASP.net core-sjablonen is `Account` de controller Inge sloten met de web-app, maar dit is niet langer het geval omdat het nu deel uitmaakt van het ASP.net core-Framework zelf. 

De code voor de `AccountController` is beschikbaar vanuit de ASP.net-kern opslagplaats op van [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Het account beheer:

- Hiermee stelt u een omleidings-URI voor OpenID Connect in `/Account/SignedOut` zodat de controller weer wordt aangeroepen wanneer Azure AD de afmelding heeft uitgevoerd
- Aanroepen `Signout()`, waarmee de OpenIdConnect-middleware contact kan opnemen met `logout` het micro soft Identity platform-eind punt:

  - Hiermee wist u de sessie cookie uit de browser en
  - Roept uiteindelijk de afmeldings- **URL**op, die standaard wordt weer gegeven, wordt de pagina afgemelde weer gave [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) vermeld als onderdeel van ASP.net core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>De aanroep naar het `logout` eind punt wordt onderschept

Met de ASP.net core OpenIdConnect middleware kan uw app de aanroep naar het micro soft Identity platform `logout` -eind punt onderscheppen door een `OnRedirectToIdentityProviderForSignOut`OpenIdConnect-gebeurtenis op te geven met de naam. De web-app gebruikt deze om te voor komen dat het dialoog venster account selecteren wordt weer gegeven aan de gebruiker bij het afmelden. Deze Onderschep ping wordt uitgevoerd in `AddAzureAdV2Authentication` de van `Microsoft.Identity.Web` de herbruikbare bibliotheek. Zie [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET-code

In ASP.NET wordt afmelden geactiveerd vanuit de methode afmelden () op een controller (bijvoorbeeld AccountController). Deze methode maakt geen deel uit van het ASP.NET-Framework (in tegens telling tot wat er gebeurt in ASP.NET Core) en gebruikt geen async en dat is de reden:

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
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Als u ASP.NET Core of ASP.NET niet wilt gebruiken, kunt u de protocol documentatie raadplegen. deze is beschikbaar via [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-web-app-sign-user-production.md)
