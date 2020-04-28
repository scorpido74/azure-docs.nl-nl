---
title: Aanmelden voor een app met één pagina & afmelden-micro soft Identity-platform | Azure
description: Meer informatie over het maken van een toepassing met één pagina (aanmelden)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 7e809def048c95b6688a13ac99783615eb045d11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885186"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Toepassing met één pagina: aanmelden en afmelden

Meer informatie over het toevoegen van een aanmelding aan de code voor uw toepassing met één pagina.

Voordat u tokens kunt ophalen voor toegang tot Api's in uw toepassing, hebt u een geverifieerde gebruikers context nodig. U kunt op twee manieren gebruikers aan uw toepassing aanmelden in MSAL. js:

* [Pop-upvenster](#sign-in-with-a-pop-up-window), met behulp `loginPopup` van de-methode
* [Omleiden](#sign-in-with-redirect), met behulp van de `loginRedirect` -methode

U kunt eventueel ook de scopes door geven van de Api's waarvoor u de gebruiker om toestemming moet vragen op het moment dat u zich aanmeldt.

> [!NOTE]
> Als uw toepassing al toegang heeft tot een geverifieerde gebruikers context of ID-token, kunt u de aanmeldings stap overs Laan en de tokens rechtstreeks ophalen. Zie [SSO zonder MSAL. js login](msal-js-sso.md#sso-without-msaljs-login)(Engelstalig) voor meer informatie.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Kiezen tussen een pop-up-of omleidings ervaring

U kunt niet zowel de pop-up-als omleidings methoden in uw toepassing gebruiken. De keuze tussen een pop-up-of omleidings ervaring is afhankelijk van uw toepassings stroom:

* Als u niet wilt dat gebruikers tijdens de verificatie naar de hoofd pagina van de toepassing gaan, wordt de pop-upmethode aangeraden. Omdat de verificatie omleiding plaatsvindt in een pop-upvenster, blijft de status van de hoofd toepassing behouden.

* Als gebruikers browser beperkingen of-beleid hebben waarin pop-upvensters zijn uitgeschakeld, kunt u de omleidings methode gebruiken. Gebruik de omleidings methode met de Internet Explorer-browser, omdat er [bekende problemen zijn met pop-upvensters in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

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

Met de MSAL-hoek wrapper kunt u specifieke routes in uw toepassing beveiligen `MsalGuard` door deze toe te voegen aan de definitie van de route. Deze Guard roept de-methode aan om u aan te melden wanneer de route wordt geopend.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Schakel de `popUp` configuratie optie in voor een pop-upvenster. U kunt ook de volgende bereiken door geven die toestemming nodig heeft:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Aanmelden met omleiding

# <a name="javascript"></a>[Javascript](#tab/javascript)

De omleidings methoden retour neren geen belofte als gevolg van de verplaatsing van de hoofd toepassing. Als u de geretourneerde tokens wilt verwerken en openen, moet u geslaagde en fout-Call backs registreren voordat u de omleidings methoden aanroept.

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

De code is hetzelfde als hierboven beschreven in het gedeelte over aanmelden met een pop-upvenster. De standaard stroom wordt omgeleid.

> [!NOTE]
> Het ID-token bevat niet de toegestane bereiken en vertegenwoordigt alleen de geverifieerde gebruiker. De toegestane bereiken worden geretourneerd in het toegangs token, dat u in de volgende stap aanschaft.

---

## <a name="sign-out"></a>Afmelden

De MSAL-bibliotheek biedt `logout` een methode waarmee de cache in browser opslag wordt gewist en waarmee een afmeldings aanvraag wordt verzonden naar Azure Active Directory (Azure AD). Nadat u zich hebt afgemeld, wordt de tape wisselaar standaard weer doorgestuurd naar de start pagina van de toepassing.

U kunt de URI configureren waarnaar de omleiding moet worden omgeleid na het instellen `postLogoutRedirectUri`van de registratie. Deze URI moet ook worden geregistreerd als de afmeldings-URI in de registratie van uw toepassing.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
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
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de app](scenario-spa-acquire-token.md)
