---
title: Zelfstudie voor Angular-apps met één pagina - Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe Angular SPA-toepassingen een API kunnen aanroepen die toegangstokens vereisen van het Microsoft-identiteitsplatformeindpunt.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-javascript
ms.openlocfilehash: 67ce5f898f2f9b6be088a0d01aec908c93ce7418
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446896"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Zelfstudie: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een Angular-app met één pagina

In deze zelfstudie leert u hoe u een Angular-app met één pagina (SPA):
- Persoonlijke accounts, werkaccounts of schoolaccounts aanmeldt.
- Een toegangstoken verkrijgt.
- De Microsoft Graph API of andere API's aanroept die toegangstokens nodig hebben van het *Microsoft-identiteitsplatformeindpunt*.

>[!NOTE]
>In deze zelfstudie wordt uitgelegd hoe u een nieuwe Angular SPA maakt met behulp van Microsoft Authentication Library (MSAL). Als u een voorbeeld-app wilt downloaden, raadpleegt u de [quickstart](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Hoe de voorbeeld-app werkt

![Diagram dat laat zien hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

### <a name="more-information"></a>Meer informatie

Met de voorbeeldtoepassing die in deze zelfstudie wordt gemaakt, kan een Angular SPA een query uitvoeren bij de Microsoft Graph API of een web-API die tokens accepteert van het Microsoft-identiteitsplatformeindpunt. De MSAL voor de Angular-bibliotheek is een wrapper van de core MSAL.js-bibliotheek. Daarmee kunnen Angular-toepassingen (6+) enterprise-gebruikers verifiëren met behulp van Microsoft Azure Active Directory, Microsoft-accountgebruikers en gebruikers van een sociale identiteit (zoals Facebook, Google en LinkedIn). De bibliotheek zorgt er ook voor dat toepassingen toegang krijgen tot Microsoft-cloudservices of Microsoft Graph.

Wanneer in dit scenario een gebruiker zich aanmeldt, wordt er een toegangstoken gevraagd en toegevoegd aan HTTP-aanvragen via de autorisatie-header. Tokens worden opgehaald en verlengd door MSAL.

### <a name="libraries"></a>Bibliotheken

Deze zelfstudie maakt gebruik van de volgende bibliotheek:

|Bibliotheek|Beschrijving|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Micro Authentication Library voor JavaScript Angular Wrapper|

U vindt de broncode voor de MSAL.js-bibliotheek in de opslagplaats [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) op GitHub.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te volgen:

* Een lokale webserver, zoals [Node.js](https://nodejs.org/en/download/). De instructies in deze zelfstudie zijn gebaseerd op Node.js.
* Een Integrated Development Environment (IDE), zoals [Visual Studio Code](https://code.visualstudio.com/download), om de projectbestanden te bewerken.

## <a name="create-your-project"></a>Uw project maken

Genereer een nieuw Angular-toepassing aan de hand van de volgende npm-opdrachten:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Uw toepassing registreren

Volg de [instructies om een toepassing met één pagina te registreren](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) in de Azure-portal.

Noteer op de pagina **Overzicht** van uw registratie de waarde **Toepassings-id (client)** voor later gebruik.

Registreer de waarde van uw **Omleidings-URI** als **http://localhost:4200/** en schakel instellingen voor impliciete toekenning in.

## <a name="configure-the-application"></a>De toepassing configureren

1. Bewerk in de map *src/app* *app.module.TS* en voeg `MSALModule` toe aan de constant `imports` en `isIE`:

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
    |Voer_hier_de_toepassings-id_in|Op de pagina **Overzicht** van de registratie van uw toepassing, is dit uw waarde voor **Toepassings-id (client)** . |
    |Voer_hier_het_cloud-exemplaar_in|Dit is het exemplaar van de Azure-cloud. Voer **https://login.microsoftonline.com** in voor de primaire of algemene Azure-cloud. Raadpleeg [Nationale clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) voor nationale clouds (bijvoorbeeld China).|
    |Voer_hier_tenantgegevens_in| Stel een van de volgende opties in: Als uw toepassing *accounts in deze organisatiemap ondersteunt*, vervangt u deze waarde door de map-id (tenant) of tenantnaam (bijvoorbeeld **contoso.microsoft.com**). Als uw toepassing *accounts in elke organisatiemap* ondersteunt, vervang deze waarde dan door **organisaties**. Als uw toepassing *accounts in elke organisatiemap en persoonlijke Microsoft-accounts* ondersteunt, vervang deze waarde dan door **algemeen**. Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervang deze waarde dan door **consumenten**. |
    |Voer_hier_de_omleidings-URI_in|Vervang door **http://localhost:4200** .|

    Raadpleeg [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md) voor meer informatie over beschikbare opties die u kunt configureren.

2. Voeg boven in hetzelfde bestand de volgende importinstructie toe:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Voeg de volgende importinstructies toe boven in `src/app/app.component.ts`:

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Een gebruiker aanmelden

Voeg de volgende code toe aan `AppComponent` om een gebruiker aan te melden:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

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
> We raden `loginRedirect` te gebruiken voor Internet Explorer-gebruikers.

## <a name="acquire-a-token"></a>Een token verkrijgen

### <a name="angular-interceptor"></a>Angular-interceptor

MSAL Angular biedt de klasse `Interceptor` die automatisch tokens ophaalt voor uitgaande aanvragen naar bekende beveiligde bronnen en die gebruikmaken van de Angular `http`-client.

Neem eerste de klasse `Interceptor` op als provider voor je toepassing:

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

Geef vervolgens een map beschermde resources op voor `MsalModule.forRoot()` als `protectedResourceMap` en neem de bereiken op in `consentScopes`:

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

Haal als laatst een gebruikersprofiel op met een HTTP-aanvraag:

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
MSAL gebruikt drie methoden om tokens op te halen: `acquireTokenRedirect`, `acquireTokenPopup` en `acquireTokenSilent`. Het wordt echter aanbevolen om de klasse `MsalInterceptor` te gebruiken voor Angular-apps, zoals in het vorige gedeelte is laten zien.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `acquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat de methode `loginRedirect` of `loginPopup` de eerste keer is uitgevoerd, wordt voor volgende aanroepen meestal `acquireTokenSilent` gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources. Aanroepen voor het aanvragen of vernieuwen van tokens worden op de achtergrond uitgevoerd.

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

In die code bevat `scopes` bereiken die worden gevraagd om te worden geretourneerd in de toegangstoken voor de API.

Bijvoorbeeld:

* `["user.read"]` voor Microsoft Graph
* `["<Application ID URL>/scope"]` voor aangepaste web-API's (`api://<Application ID>/access_as_user`)

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Soms moet de gebruiker het Microsoft-identiteitsplatformeindpunt gebruiken. Bijvoorbeeld:

* Gebruikers moeten mogelijk hun referenties opnieuw opgeven omdat het wachtwoord is verlopen.
* De toepassing vraagt toegang tot aanvullende resourcebereiken waarvoor de gebruiker toestemming moet geven.
* Wanneer verificatie in twee stappen is vereist.

Het patroon dat wordt aanbevolen voor de meeste toepassingen: eerst `acquireTokenSilent` aanroepen, vervolgens de uitzondering detecteren, en daarna `acquireTokenPopup` (of `acquireTokenRedirect`) aanroepen om een interactieve aanvraag te starten.

Als u `acquireTokenPopup` aanroept, wordt er een aanmeldvenster geopend. Het is ook mogelijk dat `acquireTokenRedirect` gebruikers omleidt naar het Microsoft-identiteitsplatformeindpunt. In dat venster moeten gebruikers hun referenties bevestigen, toestemming geven aan de vereiste resource of tweeledige verificatie uitvoeren.

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
> In deze quickstart wordt gebruikgemaakt van de methoden `loginRedirect` en `acquireTokenRedirect` met Internet Explorer, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) dat te maken heeft met het verwerken van pop-upvensters in Internet Explorer.

## <a name="log-out"></a>Afmelden

Voeg de volgende code toe om een gebruiker af te melden:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Gebruikersinterface toevoegen
Voor een voorbeeld over hoe u een gebruikersinterface toevoegt aan de Angular Material-onderdeelbibliotheek, raadpleegt u de [voorbeeldtoepassing](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Uw code testen

1.  Start de webserver om te luisteren naar de poort door de volgende opdrachten in een opdrachtregelprompt in de toepassingsmap uit te voeren:

    ```bash
    npm install
    npm start
    ```
1. Voer in uw browser **http://localhost:4200** of **http://localhost:{port}** in, waarbij *poort* de poort is waarop uw webserver luistert.


### <a name="provide-consent-for-application-access"></a>Toestemming voor toepassingstoegang geven

De eerste keer dat u zich aanmeldt bij uw toepassing, wordt u gevraagd om deze toegang te geven tot uw profiel en om u aan te melden:

![Het venster 'Machtiging gegeven'](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Bereiken en gedelegeerde toestemmingen toevoegen

De Microsoft Graph-API vereist het bereik *user.read* om het profiel van een gebruiker te lezen. Dit bereik wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in de registratieportal. Overige API's voor Microsoft Graph, evenals aangepaste API's voor uw back-endserver, vereisen mogelijk aanvullende bereiken. De Microsoft Graph API vereist bijvoorbeeld het bereik *Calendars.Read* om te luisteren naar de agenda van de gebruiker.

Als u toegang wilt krijgen tot de agenda van de gebruiker in de context van een toepassing, voegt u de gedelegeerde toestemming *Calendars.Read* toe aan de toepassingsregistratiegegevens. Voeg vervolgens het bereik *Calendars.Read* toe aan de oproep `acquireTokenSilent`.

>[!NOTE]
>De gebruiker wordt mogelijk gevraagd om aanvullende machtigingen te geven naarmate u het aantal bereiken verhoogt.

Als voor een back-end-API geen bereik is vereist (niet aanbevolen), kunt u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Als u geen ervaring hebt met identiteits- en toegangsbeheer, zijn er verschillende artikelen die u helpen moderne verificatieconcepten te leren, beginnend met [verificatie vs. autorisatie](authentication-vs-authorization.md).

Als u meer wilt weten over het ontwikkelen van toepassingen met één pagina op het Microsoft-identiteitsplatform, is de reeks artikelen [Scenario: Een toepassing met één pagina](scenario-spa-overview.md) nuttig om mee aan de slag te gaan.
