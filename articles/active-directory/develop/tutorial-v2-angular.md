---
title: Hoekige zelfstudie van apps met één pagina - Azure
titleSuffix: Microsoft identity platform
description: Ontdek hoe Angular SPA-toepassingen een API kunnen aanroepen waarvoor toegangstokens van het eindpunt van het Microsoft-identiteitsplatform vereist zijn.
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
ms.openlocfilehash: 84d7be64e7157c35639fec19cb28fe787fe29b19
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533884"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Zelfstudie: Gebruikers aanmelden en de Microsoft Graph-API aanroepen vanuit een toepassing met één pagina

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie kunnen veranderen voordat algemene beschikbaarheid (GA) is.

Deze zelfstudie laat zien hoe een Angular single-page applicatie (SPA) kan:
- Meld u aan voor persoonlijke accounts, werkaccounts of schoolaccounts.
- Een toegangstoken aanschaffen.
- Bel de Microsoft Graph API of andere API's waarvoor toegangstokens nodig zijn vanaf het eindpunt van het *Microsoft-identiteitsplatform.*

>[!NOTE]
>In deze zelfstudie u een nieuwe Hoekige SPA maken met behulp van Microsoft Authentication Library (MSAL). Als u een voorbeeld-app wilt downloaden, raadpleegt u de [quickstart](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Hoe de voorbeeld-app werkt

![Diagram dat laat zien hoe de voorbeeld-app die in deze zelfstudie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

Met de voorbeeldtoepassing die in deze zelfstudie is gemaakt, kan een Hoekige SPA de Microsoft Graph-API of een web-API opvragen die tokens van het eindpunt van het Microsoft-identiteitsplatform accepteert. De MSAL for Angular bibliotheek is een wrapper van de kern MSAL.js bibliotheek. Hiermee kunnen Hoekhoekige (6+) toepassingen bedrijfsgebruikers verifiëren met Behulp van Microsoft Azure Active Directory, Gebruikers van Microsoft-accounts en gebruikers van sociale identiteiten (zoals Facebook, Google en LinkedIn). De bibliotheek stelt de toepassingen ook in staat om toegang te krijgen tot Microsoft-cloudservices of Microsoft Graph.

In dit scenario wordt, nadat een gebruiker zich heeft aanmeldt, een toegangstoken aangevraagd en toegevoegd aan HTTP-aanvragen via de autorisatiekop. Token acquisitie en vernieuwing worden afgehandeld door MSAL.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotheken

In deze zelfstudie wordt de volgende bibliotheek gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft-verificatiebibliotheek voor JavaScript-hoekige wrapper|

> [!NOTE]
> *Msal.js* richt zich op het eindpunt van het Microsoft-identiteitsplatform, waarmee persoonlijke accounts, werkaccounts en schoolaccounts kunnen inloggen en tokens kunnen aanschaffen. Het eindpunt van het Microsoft-identiteitsplatform heeft [een aantal beperkingen.](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)
> Zie de [eindpuntvergelijkingshandleiding](../azuread-dev/azure-ad-endpoint-comparison.md)om de verschillen tussen de v1.0- en v2.0-eindpunten te begrijpen.

U vindt de broncode voor de MSAL.js-bibliotheek in de [AzureAD/microsoft-authentication-library-for-js-repository](https://github.com/AzureAD/microsoft-authentication-library-for-js) op GitHub.

<!--end-collapse-->


## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt uitvoeren, moet u het sein

* Een lokale webserver, zoals [Node.js](https://nodejs.org/en/download/). Instructies in deze zelfstudie zijn gebaseerd op Node.js.
* Een geïntegreerde ontwikkelomgeving (IDE), zoals [Visual Studio Code,](https://code.visualstudio.com/download)om de projectbestanden te bewerken.

## <a name="create-your-project"></a>Uw project maken

Genereer een nieuwe Angular-toepassing met behulp van de volgende npm-opdrachten:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Uw toepassing registreren

Volg de instructies om een toepassing van één pagina te registreren in de [Azure-portal.](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)

Noteer op de pagina app **Overzicht** van uw inschrijving de **waarde van de id-toepassing (client)** voor later gebruik.

Registreer de WAARDE **http://localhost:4200/** **URI-omleiding** als en schakel impliciete subsidie-instellingen in.

## <a name="configure-the-application"></a>De toepassing configureren

1. Bewerk *app.module.ts* in `MSALModule` de `imports` `isIE` map *src/app* en voeg toe aan de constante:

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
    |Enter_the_Application_Id_Here|Op de **overzichtspagina** van uw aanvraagregistratie is dit de **id-waarde van uw toepassing (client).** |
    |Enter_the_Cloud_Instance_Id_Here|Dit is de instantie van de Azure-cloud. Voer voor de hoofd- **https://login.microsoftonline.com**of globale Azure-cloud . Voor nationale wolken (bijvoorbeeld, China), zie [Nationale wolken](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
    |Enter_the_Tenant_Info_Here| Stel in op een van de volgende opties: Als uw toepassing *accounts in deze organisatiemap*ondersteunt, vervangt u deze waarde door de id of tenantnaam (bijvoorbeeld **contoso.microsoft.com).** Als uw toepassing *accounts in een organisatiemap*ondersteunt, vervangt u deze waarde door **organisaties**. Als uw toepassing *accounts in een organisatiemap en persoonlijke Microsoft-accounts*ondersteunt, vervangt u deze waarde door **algemene**. Als u de ondersteuning alleen voor *persoonlijke Microsoft-accounts*wilt beperken, vervangt u deze waarde door **consumenten**. |
    |Enter_the_Redirect_Uri_Here|Vervangen **http://localhost:4200**door .|

    Zie [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschikbare configureerbare opties.

2. Voeg boven aan hetzelfde bestand de volgende importinstructie toe:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Voeg de volgende importoverzichten `src/app/app.component.ts`toe aan de bovenkant van :

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Aanmelden voor een gebruiker

Voeg de volgende `AppComponent` code toe om een gebruiker aan te melden:

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
> We raden `loginRedirect` u aan om internet explorer-gebruikers te gebruiken.

## <a name="acquire-a-token"></a>Een token verkrijgen

### <a name="angular-interceptor"></a>Hoekige interceptor

MSAL Angular `Interceptor` biedt een klasse die automatisch tokens verwerft voor uitgaande aanvragen die de Hoekige `http` client gebruiken voor bekende beschermde bronnen.

Ten eerste, `Interceptor` neem de klasse als een provider aan uw aanvraag:

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

Geef vervolgens een kaart met `MsalModule.forRoot()` `protectedResourceMap` beschermde bronnen op `consentScopes`om deze scopes op te nemen in:

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

Haal ten slotte het profiel van een gebruiker op met een HTTP-verzoek:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>overgenomenTokenSilent, overname TokenPopup, overnameTokenRedirect
MSAL gebruikt drie methoden om `acquireTokenRedirect` `acquireTokenPopup`tokens `acquireTokenSilent`te verwerven: , en . We raden echter `MsalInterceptor` aan om de klasse in plaats daarvan te gebruiken voor Hoekige apps, zoals in de vorige sectie wordt weergegeven.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `acquireTokenSilent` methode verwerkt tokenacquisities en -vernieuwingen zonder interactie van de gebruiker. Nadat `loginRedirect` de `loginPopup` of methode voor de `acquireTokenSilent` eerste keer is uitgevoerd, wordt vaak gebruikt om tokens te verkrijgen die worden gebruikt om toegang te krijgen tot beveiligde bronnen in latere oproepen. Aanroepen voor het aanvragen of vernieuwen van tokens worden op de achtergrond uitgevoerd.

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

In die `scopes` code worden scopes vermeld die worden gevraagd om te worden geretourneerd in het toegangstoken voor de API.

Bijvoorbeeld:

* `["user.read"]`voor Microsoft Graph
* `["<Application ID URL>/scope"]`voor aangepaste web-API's `api://<Application ID>/access_as_user`(dat wil zeggen)

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Soms moet de gebruiker communiceren met het eindpunt van het Microsoft-identiteitsplatform. Bijvoorbeeld:

* Gebruikers moeten mogelijk hun referenties opnieuw invoeren omdat hun wachtwoord is verlopen.
* Uw toepassing vraagt om toegang tot aanvullende resourcescopes waar toe de gebruiker toestemming moet geven.
* Tweestapsverificatie is vereist.

Het aanbevolen patroon voor de `acquireTokenSilent` meeste toepassingen is om eerst `acquireTokenPopup` te `acquireTokenRedirect`bellen, vervolgens de uitzondering te vangen en vervolgens een interactieve aanvraag te starten (of) te bellen (of) te starten.

Bellen `acquireTokenPopup` resulteert in een pop-upaanmeldingsvenster. U kunt `acquireTokenRedirect` gebruikers ook doorverwijzen naar het eindpunt van het Microsoft-identiteitsplatform. In dat venster moeten gebruikers hun referenties bevestigen, toestemming geven voor de vereiste bron of tweestapsverificatie voltooien.

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
> Deze quickstart `loginRedirect` maakt `acquireTokenRedirect` gebruik van de en methoden met Microsoft Internet Explorer vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot de verwerking van pop-upvensters door Internet Explorer.

## <a name="log-out"></a>Afmelden

Voeg de volgende code toe om een gebruiker uit te loggen:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Gebruikersinterface toevoegen
Zie de [voorbeeldtoepassing](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)voor een voorbeeld van het toevoegen van gebruikersinterface met behulp van de componentbibliotheek Hoekmateriaal.

## <a name="test-your-code"></a>Uw code testen

1.  Start de webserver om naar de poort te luisteren door de volgende opdrachten uit te voeren op een opdrachtregelprompt vanuit de toepassingsmap:

    ```bash
    npm install
    npm start
    ```
1. Voer in uw **http://localhost:4200** **http://localhost:{port}** browser de *poort* in of waar de poort is waarop uw webserver luistert.


### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich aanmeldt bij uw aanvraag, wordt u gevraagd deze toegang te verlenen tot uw profiel en u toe te staan u aan te melden:

![Het venster 'Gevraagde machtigingen'](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="add-scopes-and-delegated-permissions"></a>Scopes en gedelegeerde machtigingen toevoegen

De Microsoft Graph API vereist dat de *user.read-scope* het profiel van een gebruiker leest. Standaard wordt dit bereik automatisch toegevoegd in elke toepassing die is geregistreerd op het registratieportaal. Andere API's voor Microsoft Graph en aangepaste API's voor uw back-endserver vereisen mogelijk extra scopes. De Microsoft Graph API vereist bijvoorbeeld de *agenda's.Lees* bereik om de agenda's van de gebruiker weer te geven.

Als u de agenda's van de gebruiker wilt openen in de context van een toepassing, voegt u de gedelegeerde machtiging *Agenda's* toe aan de registratiegegevens van de toepassing. Voeg vervolgens het *bereik Agenda's toe.Lees* het bereik aan het `acquireTokenSilent` gesprek.

>[!NOTE]
>De gebruiker kan worden gevraagd om extra toestemmingen als u het aantal scopes te verhogen.

Als een back-end-API geen scope vereist (niet aanbevolen), u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Leer vervolgens hoe u zich aanmeldt bij een gebruiker en tokens aanschaffen in de instructie Lestekst Angular:

> [!div class="nextstepaction"]
> [Hoekige zelfstudie](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
