---
title: Paginaherladen vermijden (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het voorkomen van herladen van pagina's bij het in stilte aanschaffen en vernieuwen van tokens met behulp van de Microsoft Authentication Library voor JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084952"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Vermijd paginaherladen bij het verkrijgen en vernieuwen van tokens in stilte met msal.js
Microsoft Authentication Library for JavaScript (MSAL.js) gebruikt verborgen `iframe` elementen om tokens op de achtergrond in stilte te verkrijgen en te vernieuwen. Azure AD retourneert het token terug naar de geregistreerde redirect_uri die zijn opgegeven in de tokenaanvraag (dit is standaard de hoofdpagina van de app). Aangezien het antwoord een 302 is, resulteert `redirect_uri` dit in de `iframe`HTML die overeenkomt met het krijgen geladen in de . Meestal `redirect_uri` is de app is de hoofdpagina en dit zorgt ervoor dat het te herladen.

In andere gevallen kan het mogelijk zijn dat het navigeren naar de `iframe` hoofdpagina van de app verificatie vereist, leiden tot geneste elementen of `X-Frame-Options: deny` fouten.

Aangezien MSAL.js de 302 die is uitgegeven door Azure AD niet kan `redirect_uri` verwijderen en het `iframe`geretourneerde token moet verwerken, kan het niet voorkomen dat het wordt geladen in de .

Volg deze oplossingen om te voorkomen dat de hele app opnieuw wordt geladen of andere fouten hierdoor worden veroorzaakt.

## <a name="specify-different-html-for-the-iframe"></a>Verschillende HTML opgeven voor het iframe

Stel `redirect_uri` de eigenschap op config in op een eenvoudige pagina, waarvoor geen verificatie vereist is. U moet ervoor zorgen dat `redirect_uri` deze overeenkomt met de geregistreerde Azure-portal. Dit heeft geen invloed op de inlogervaring van de gebruiker, aangezien MSAL de startpagina opslaat wanneer de gebruiker het aanmeldingsproces begint en terugverwijst naar de exacte locatie nadat de aanmelding is voltooid.

## <a name="initialization-in-your-main-app-file"></a>Initialisatie in uw hoofdappbestand

Als uw app zodanig is gestructureerd dat er één centraal Javascript-bestand is dat de initialisatie, routering en andere dingen van `iframe` de app definieert, u uw app-modules voorwaardelijk laden op basis van de vraag of de app in een of niet wordt geladen. Bijvoorbeeld:

In AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

In Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het bouwen van een applicatie (SPA) met](scenario-spa-overview.md) MSAL.js.