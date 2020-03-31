---
title: Aanmelding sein voor één pagina & afmelding - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een toepassing op één pagina (aanmelden)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159961"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Toepassing met één pagina: aan- en afmelden

Meer informatie over het toevoegen van aanmelding aan de code voor uw toepassing op één pagina.

Voordat u tokens toegang krijgen tot API's in uw toepassing, hebt u een geverifieerde gebruikerscontext nodig. U gebruikers op twee manieren aanmelden bij uw toepassing in MSAL.js:

* [Pop-upvenster](#sign-in-with-a-pop-up-window), met `loginPopup` behulp van de methode
* [Omleiden,](#sign-in-with-redirect)met `loginRedirect` behulp van de methode

U ook optioneel de scopes van de API's doorgeven waarvoor u de gebruiker nodig hebt om toestemming te geven op het moment van aanmelden.

> [!NOTE]
> Als uw toepassing al toegang heeft tot een geverifieerde gebruikerscontext of ID-token, u de inlogstap overslaan en direct tokens aanschaffen. Zie [SSO zonder MSAL.js login](msal-js-sso.md#sso-without-msaljs-login)voor meer informatie.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Kiezen tussen een pop-up- of omleidingservaring

U niet zowel de pop-up- als omleidingsmethoden in uw toepassing gebruiken. De keuze tussen een pop-up- of omleidingservaring is afhankelijk van uw toepassingsstroom:

* Als u niet wilt dat gebruikers tijdens de verificatie van uw hoofdtoepassingspagina afstappen, raden we de pop-upmethode aan. Omdat de omleiding van de verificatie plaatsvindt in een pop-upvenster, blijft de status van de hoofdtoepassing behouden.

* Als gebruikers browserbeperkingen of -beleidsregels hebben waarbij pop-upvensters zijn uitgeschakeld, u de omleidingsmethode gebruiken. Gebruik de omleidingsmethode met de Internet Explorer-browser, omdat er bekende problemen zijn [met pop-upvensters in Internet Explorer.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

## <a name="sign-in-with-a-pop-up-window"></a>Aanmelden met een pop-upvenster

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

Met de MSAL Angular wrapper u specifieke `MsalGuard` routes in uw toepassing beveiligen door toe te voegen aan de routedefinitie. Deze bewaker zal zich beroepen op de methode om in te loggen wanneer die route wordt geopend.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Schakel de configuratieoptie `popUp` in voor een pop-upvensterervaring. U ook de scopes die toestemming vereisen als volgt doorgeven:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```
---

## <a name="sign-in-with-redirect"></a>Aanmelden met omleiding

# <a name="javascript"></a>[Javascript](#tab/javascript)

De omleidingsmethoden geven geen belofte terug vanwege de verhuizing van de hoofdapp. Als u de geretourneerde tokens wilt verwerken en openen, moet u succes- en foutcallbacks registreren voordat u de omleidingsmethoden aanroept.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

De code hier is hetzelfde als eerder beschreven in de sectie over aanmelden met een pop-upvenster. De standaardstroom is omleiden.

> [!NOTE]
> Het ID-token bevat niet de goedgekeurde scopes en vertegenwoordigt alleen de geverifieerde gebruiker. De goedgekeurde scopes worden geretourneerd in het toegangstoken, dat u in de volgende stap zult verkrijgen.

---

## <a name="sign-out"></a>Afmelden

De MSAL-bibliotheek `logout` biedt een methode waarmee de cache in browseropslag wordt gewist en een afmeldingsverzoek wordt verstuurt naar Azure Active Directory (Azure AD). Na het afmelden wordt de bibliotheek standaard omgeleid naar de startpagina van de toepassing.

U de URI configureren waarnaar deze moet `postLogoutRedirectUri`worden omgeleid na het afmelden door. Deze URI moet ook worden geregistreerd als de afmelding URI in uw aanvraag registratie.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de app](scenario-spa-acquire-token.md)
