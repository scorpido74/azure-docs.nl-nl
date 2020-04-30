---
title: Zelf studie voor app met één pagina-Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe hoek-SPA-toepassingen een API kunnen aanroepen die toegangs tokens van het micro soft Identity platform-eind punt vereist.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c645ab45711698e4a6f582678e2a850e15dea62a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181593"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Zelf studie: gebruikers aanmelden en de Microsoft Graph-API aanroepen vanuit een hoek toepassing met één pagina

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie kunnen worden gewijzigd vóór algemene Beschik baarheid (GA).

In deze zelf studie wordt gedemonstreerd hoe een hoek van een enkele toepassing met één pagina (SPA) kan:
- Persoonlijke accounts, werk accounts of school accounts aanmelden.
- Haal een toegangs token op.
- Roep de Microsoft Graph-API of andere Api's aan waarvoor toegangs tokens van het *micro soft Identity platform-eind punt*zijn vereist.

>[!NOTE]
>In deze zelf studie leert u hoe u een nieuw hoek-beveiligd-wachtwoord verificatie maakt met behulp van micro soft Authentication Library (MSAL). Als u een voor beeld-app wilt downloaden, raadpleegt u [Quick](quickstart-v2-angular.md)start.

## <a name="how-the-sample-app-works"></a>De werking van de voor beeld-app

![Diagram dat laat zien hoe de voor beeld-app die in deze zelf studie is gegenereerd, werkt](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Meer informatie

Met de voorbeeld toepassing die in deze zelf studie wordt gemaakt, kan een hoek beveiligd-wachtwoord verificatie query's uitvoeren op de Microsoft Graph-API of een web-API die tokens accepteert van het micro soft Identity platform-eind punt. De MSAL voor hoek bibliotheek is een wrapper van de core MSAL. JS-bibliotheek. Met deze functie kunnen gebruikers van een hoek (6 +) zakelijke gebruikers verifiëren door gebruik te maken van Microsoft Azure Active Directory, Microsoft-account gebruikers en sociale identiteiten, zoals Facebook, Google en LinkedIn. Met de bibliotheek kunnen toepassingen ook toegang krijgen tot micro soft-Cloud Services of-Microsoft Graph.

Wanneer een gebruiker zich aanmeldt, wordt in dit scenario een toegangs token aangevraagd en toegevoegd aan HTTP-aanvragen via de autorisatie-header. Het ophalen en vernieuwen van tokens worden verwerkt door MSAL.

### <a name="libraries"></a>Bibliotheken

In deze zelf studie wordt de volgende bibliotheek gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Micro soft-verificatie bibliotheek voor hoek-wrapper voor Java script|

U vindt de bron code voor de bibliotheek MSAL. js in de [AzureAD/micro soft-Authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) -opslag plaats op github.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt uitvoeren, hebt u het volgende nodig:

* Een lokale webserver, zoals [node. js](https://nodejs.org/en/download/). De instructies in deze zelf studie zijn gebaseerd op node. js.
* Een Integrated Development Environment (IDE), zoals [Visual Studio code](https://code.visualstudio.com/download), voor het bewerken van de project bestanden.

## <a name="create-your-project"></a>Uw project maken

Genereer een nieuwe hoek toepassing met behulp van de volgende NPM-opdrachten:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Uw toepassing registreren

Volg de [instructies voor het registreren van een toepassing met één pagina](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) in de Azure Portal.

Noteer de waarde van de **toepassings-id** voor later gebruik op de pagina **overzicht** van apps van uw registratie.

Registreer uw **omleidings** - **http://localhost:4200/** URI-waarde als en schakel instellingen voor impliciete toekenning in.

## <a name="configure-the-application"></a>De toepassing configureren

1. Bewerk *app. module. TS* `MSALModule` in de map *src/app* en voeg deze toe `imports` aan de `isIE` constante:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Vervang deze waarden:

    |Waardenaam|Info|
    |---------|---------|
    |Enter_the_Application_Id_Here|Op de pagina **overzicht** van de registratie van uw toepassing is dit de waarde van uw **toepassing (client)** . |
    |Enter_the_Cloud_Instance_Id_Here|Dit is het exemplaar van de Azure-Cloud. Voer **https://login.microsoftonline.com**in voor de hoofd-of wereld wijde Azure-Cloud. Zie [nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)voor nationale Clouds (bijvoorbeeld China).|
    |Enter_the_Tenant_Info_Here| Stel in op een van de volgende opties: als uw toepassing *accounts in deze organisatie-Directory*ondersteunt, vervangt u deze waarde door de Directory-id (Tenant) of Tenant naam (bijvoorbeeld **contoso.Microsoft.com**). Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**. Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**. |
    |Enter_the_Redirect_Uri_Here|Vervang door **http://localhost:4200**.|

    Zie [client toepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschik bare opties die kunnen worden geconfigureerd.

2. Voeg boven aan hetzelfde bestand de volgende import instructie toe:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Voeg de volgende import instructies toe aan het begin `src/app/app.component.ts`van:

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Een gebruiker aanmelden

Voeg de volgende code toe `AppComponent` om u aan te melden bij een gebruiker:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> U wordt aangeraden voor gebruikers van Internet Explorer te gebruiken `loginRedirect` .

## <a name="acquire-a-token"></a>Een token verkrijgen

### <a name="angular-interceptor"></a>Hoek-Interceptor

MSAL hoek biedt een `Interceptor` klasse die automatisch tokens ophaalt voor uitgaande aanvragen die gebruikmaken van `http` de hoek-client naar bekende beveiligde bronnen.

Voeg eerst de `Interceptor` klasse toe als een provider voor uw toepassing:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Geef vervolgens een kaart van beveiligde resources op `MsalModule.forRoot()` als `protectedResourceMap` en deze scopes in `consentScopes`gebruiken:

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Haal tot slot het profiel van een gebruiker op met een HTTP-aanvraag:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
MSAL maakt gebruik van drie methoden om tokens `acquireTokenRedirect`te `acquireTokenPopup`verkrijgen: `acquireTokenSilent`, en. We raden u echter aan de `MsalInterceptor` klasse te gebruiken voor hoek-apps, zoals wordt weer gegeven in de vorige sectie.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `acquireTokenSilent` -methode verwerkt het ophalen van tokens en verlengingen zonder tussen komst van de gebruiker. Nadat de `loginRedirect` or `loginPopup` -methode voor de eerste keer is uitgevoerd `acquireTokenSilent` , wordt doorgaans gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources in latere aanroepen. Aanroepen voor het aanvragen of vernieuwen van tokens worden op de achtergrond uitgevoerd.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

In die code `scopes` bevat de scopes die worden aangevraagd om te worden geretourneerd in het toegangs token voor de API.

Bijvoorbeeld:

* `["user.read"]`voor Microsoft Graph
* `["<Application ID URL>/scope"]`voor aangepaste web-Api's (dat wil `api://<Application ID>/access_as_user`zeggen)

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Soms hebt u de gebruiker nodig om te kunnen communiceren met het micro soft Identity platform-eind punt. Bijvoorbeeld:

* Gebruikers moeten mogelijk hun referenties opnieuw invoeren, omdat het wacht woord is verlopen.
* Uw toepassing vraagt toegang tot extra resource scopes aan die de gebruiker nodig heeft om toestemming te geven.
* Verificatie met twee factoren is vereist.

Het aanbevolen patroon voor de meeste toepassingen is om `acquireTokenSilent` het eerst aan te roepen, vervolgens de uitzonde `acquireTokenPopup` ring op `acquireTokenRedirect`te vangen en vervolgens (of) aan te roepen om een interactieve aanvraag te starten.

Resultaten `acquireTokenPopup` worden aangeroepen in een pop-upvenster. `acquireTokenRedirect` U kunt ook gebruikers omleiden naar het micro soft Identity platform-eind punt. In dat venster moeten gebruikers hun referenties bevestigen, toestemming geven voor de vereiste resource of twee ledige verificatie volt ooien.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Deze Snelstartgids maakt gebruik `loginRedirect` van `acquireTokenRedirect` de methoden en van micro soft Internet Explorer vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot het verwerken van pop-upvensters door Internet Explorer.

## <a name="log-out"></a>Afmelden

Voeg de volgende code toe om een gebruiker af te melden:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Gebruikersinterface toevoegen
Zie de [voorbeeld toepassing](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)voor een voor beeld van het toevoegen van de gebruikers interface met behulp van de component bibliotheek voor hoek materiaal.

## <a name="test-your-code"></a>Uw code testen

1.  Start de webserver om naar de poort te Luis teren door de volgende opdrachten uit te voeren op de opdracht regel prompt vanuit de toepassingsmap:

    ```bash
    npm install
    npm start
    ```
1. Voer **http://localhost:4200** in uw browser in of **http://localhost:{port}**, waarbij *poort* de poort is waarop uw webserver luistert.


### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt u gevraagd de toegang tot uw profiel te verlenen en u toe te staan om u aan te melden:

![Het venster machtigingen aangevraagd](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Bereiken en gedelegeerde machtigingen toevoegen

Voor de Microsoft Graph-API is het bereik *User. Read* vereist om het profiel van een gebruiker te lezen. Deze scope wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd op de registratie Portal. Andere Api's voor Microsoft Graph, evenals aangepaste Api's voor uw back-endserver, vereisen mogelijk extra scopes. De Microsoft Graph-API vereist bijvoorbeeld het bereik *Calendars. Read* om de agenda's van de gebruiker weer te geven.

Als u toegang wilt krijgen tot de agenda's van de gebruiker in de context van een toepassing, voegt u de *agenda's toe.* gedelegeerde machtigingen lezen voor de registratie gegevens van de toepassing. Voeg vervolgens de *agenda's* toe aan de `acquireTokenSilent` aanroep.

>[!NOTE]
>De gebruiker wordt mogelijk gevraagd om aanvullende toestemmingen wanneer u het aantal bereiken verhoogt.

Als voor een back-end-API geen bereik is vereist (niet aanbevolen), kunt u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Lees vervolgens hoe u zich aanmeldt bij een gebruiker en tokens aanschaft in de hoek zelf studie:

> [!div class="nextstepaction"]
> [Zelf studie voor hoek](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
