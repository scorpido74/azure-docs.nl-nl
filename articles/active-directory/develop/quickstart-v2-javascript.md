---
title: Gebruikers aanmelden in JavaScript-apps met één pagina | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een JavaScript-app een API kan aanroepen waarvoor toegangstokens nodig zijn via het Microsoft-identiteitsplatform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 526922d73f2176825b8bba8982316f3c9c129963
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473581"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Snelstart: Gebruikers aanmelden en een toegangstoken krijgen in een JavaScript SPA

In deze quickstart gebruikt u een codevoorbeeld om te leren hoe een Toepassing met één pagina voor JavaScript gebruikers van persoonlijke accounts, werkaccounts en schoolaccounts kan aanmelden. Een JavaScript SPA kan ook een toegangstoken krijgen om de Microsoft Graph API of een web-API aan te roepen. (Zie [hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (om projectbestanden te bewerken)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>De quickstart-toepassing registreren en downloaden
> Als u de snellestarttoepassing wilt starten, gebruikt u een van de volgende opties.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1 (Express): Uw app registreren en automatisch configureren en vervolgens het voorbeeld van uw code downloaden
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u het account rechtsboven en stelt u uw portalsessie in op de Azure Active Directory-tenant (Azure AD) die u wilt gebruiken.
> 1. Ga naar de nieuwe [Azure-portal - deelvenster App-registraties.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Voer een naam in voor de toepassing. 
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2 (handleiding): Uw toepassing en codevoorbeeld handmatig registreren en configureren
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
>
> 1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u uw account rechtsboven en stelt u uw portalsessie in op de Azure AD-tenant die u wilt gebruiken.
> 1. Ga naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**. Noteer op de pagina **Overzicht** van de app de waarde **van de id-toepassing (client)** voor later gebruik.
> 1. Voor deze quickstart moet de [Impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld. Selecteer **Verificatie**in het linkerdeelvenster van de geregistreerde toepassing .
> 1. Selecteer **onder Platformconfiguraties**de optie **Een platform toevoegen**. Er wordt een paneel aan de linkerkant geopend. Selecteer daar het gebied **Webtoepassingen.**
> 1. Nog steeds aan de linkerkant, `http://localhost:3000/`stelt u de **WAARDE URI-omleiding** in op . Selecteer vervolgens **Access Token** en **ID-token**.
> 1. Selecteer **Configureren**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal
> Als u het codevoorbeeld in dit quickstartwerk `redirectUri` `http://localhost:3000/` wilt maken, moet u een as-as toevoegen en **Impliciete subsidie**inschakelen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-javascript/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden

> [!div renderon="docs"]
> Als u het project met een webserver wilt uitvoeren met Behulp van Node.js, [downloadt u de kernprojectbestanden.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="portal"]
> Het project uitvoeren met een webserver met Behulp van Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Stap 3: Uw JavaScript-app configureren
>
> Bewerk in de map *JavaScriptSPA* *authConfig.js* `authority` en `redirectUri` stel `msalConfig`de `clientID`waarden en waarden in onder .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };  
>
>```

> [!div renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Waar:
> - Enter_the_Application_Id_Here>is de **applicatie (client) ID** voor de applicatie die u hebt geregistreerd. * \<*
> - Enter_the_Cloud_Instance_Id_Here>is het voorbeeld van de Azure-cloud. * \<* Voer voor de hoofd- of *https://login.microsoftonline.com*globale Azure-cloud gewoon . Voor **nationale** wolken (bijvoorbeeld, China), zie [Nationale wolken](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>is ingesteld op een van de volgende opties: * \<*
>    - Als uw toepassing *accounts in deze organisatiemap*ondersteunt, vervangt u deze waarde door de **tenant-id** of **tenantnaam** (bijvoorbeeld *contoso.microsoft.com).*
>    - Als uw toepassing *accounts in een organisatiemap*ondersteunt, vervangt u deze waarde door **organisaties**.
>    - Als uw toepassing *accounts in een organisatiemap en persoonlijke Microsoft-accounts*ondersteunt, vervangt u deze waarde door **algemene**. Als u de ondersteuning alleen voor *persoonlijke Microsoft-accounts*wilt beperken, vervangt u deze waarde door **consumenten**.
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)**, **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om uit te voeren
> We hebben uw project geconfigureerd met waarden van de eigenschappen van uw app. 

> [!div renderon="docs"]
> 
> Bewerk vervolgens het bestand *graphConfig.js* om het `graphMeEndpoint` object `graphMeEndpoint` in `apiConfig` te stellen en voor het object.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Waar:
> - Enter_the_Graph_Endpoint_Here>is het eindpunt waar tegen API-aanroepen worden uitgevoerd. * \<* Voer voor de belangrijkste of globale `https://graph.microsoft.com`Microsoft Graph API-service gewoon in . Zie [Nationale cloudimplementatie](https://docs.microsoft.com/graph/deployments) voor meer informatie
>
> #### <a name="step-4-run-the-project"></a>Stap 4: Het project uitvoeren

Voer het project uit met een webserver met [Node.js:](https://nodejs.org/en/download/)

1. Als u de server wilt starten, voert u de volgende opdracht uit in de projectmap:
    ```batch
    npm install
    npm start
    ```
1. Open een webbrowser en `http://localhost:3000/`ga naar .

1. Selecteer **Aanmelden** om de aanmelding te starten en roep vervolgens Microsoft Graph API aan.

Nadat de browser de toepassing heeft geladen, selecteert u **Aanmelden**. De eerste keer dat u zich aanmeldt, wordt u gevraagd uw toestemming te geven om de toepassing toegang te geven tot uw profiel en u aan te melden. Nadat u bent aangemeld, moeten uw gebruikersprofielgegevens op de pagina worden weergegeven.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Hoe de voorbeeld-app in deze quickstart werkt](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

De MSAL-bibliotheek meldt zich bij gebruikers en vraagt de tokens aan die worden gebruikt om toegang te krijgen tot een API die wordt beschermd door het identiteitsplatform van Microsoft. Het bestand quickstart *index.html* bevat een verwijzing naar de bibliotheek:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> U de vorige versie vervangen door de nieuwste versie onder [MSAL.js releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Als u Node.js hebt geïnstalleerd, u de nieuwste versie downloaden via Node.js Package Manager (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren

De quickstartcode laat ook zien hoe u de MSAL-bibliotheek initialiseren:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };  

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Waar  |  |
> |---------|---------|
> |`clientId`     | De toepassings-id van de toepassing die is geregistreerd in de Azure-portal.|
> |`authority`    | (Optioneel) De url van de autoriteit die accounttypen ondersteunt, zoals eerder beschreven in de configuratiesectie. De standaardautoriteit `https://login.microsoftonline.com/common`is . |
> |`redirectUri`     | De applicatieregistratie is geconfigureerd reply/redirectUri. In dit `http://localhost:3000/`geval. |
> |`cacheLocation`  | (Optioneel) Hiermee stelt u de browseropslag in voor de auth-status. De standaardinstelling is sessionStorage.   |
> |`storeAuthStateInCookie`  | (Optioneel) De bibliotheek die de verificatieaanvraag opslaat, staat die vereist is voor validatie van de verificatiestromen in de browsercookies. Deze cookie is ingesteld voor IE- en Edge-browsers om bepaalde bekende problemen te [beperken.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) |

Zie [Clienttoepassingen initialiseren](msal-js-initializing-client-applications.md)voor meer informatie over beschikbare configureerbare opties.

### <a name="sign-in-users"></a>Gebruikers aanmelden

In het volgende codefragment wordt weergegeven hoe u gebruikers kunt aanmelden:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Waar  |  |
> |---------|---------|
> | `scopes`   | (Optioneel) Bevat scopes die worden aangevraagd voor toestemming van de gebruiker bij aanmeldingstijd. Bijvoorbeeld, `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API’s (`api://<Application ID>/access_as_user`). |

> [!TIP]
> U ook de methode `loginRedirect` gebruiken om de huidige pagina om te leiden naar de aanmeldingspagina in plaats van een pop-upvenster.

### <a name="request-tokens"></a>Tokens aanvragen

MSAL gebruikt drie methoden om `acquireTokenRedirect` `acquireTokenPopup`tokens te verwerven: ,`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `acquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat de methode `loginRedirect` of `loginPopup` de eerste keer is uitgevoerd, wordt voor volgende aanroepen meestal de methode `acquireTokenSilent` gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources. Aanroepen voor het aanvragen of vernieuwen van tokens worden op de achtergrond uitgevoerd.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Waar  |  |
> |---------|---------|
> | `scopes`   | Bevat bereiken die worden aangevraagd om te worden geretourneerd in het toegangstoken voor de API. Bijvoorbeeld, `[ "mail.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API’s (`api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Er zijn situaties waarin u gebruikers moet dwingen om te communiceren met het eindpunt van het Microsoft-identiteitsplatform. Bijvoorbeeld:
* Gebruikers moeten mogelijk hun referenties opnieuw invoeren omdat hun wachtwoord is verlopen.
* Uw toepassing vraagt om toegang tot aanvullende resourcescopes waar toe de gebruiker toestemming moet geven.
* Tweestapsverificatie is vereist.

Het gebruikelijke aanbevolen patroon voor de `acquireTokenSilent` meeste toepassingen is om eerst `acquireTokenPopup` te `acquireTokenRedirect`bellen, vervolgens de uitzondering te vangen en vervolgens een interactieve aanvraag te starten (of) te bellen (of) te starten.

De `acquireTokenPopup` resultaten aanroepen in een pop-upvenster om u aan te melden. (Of `acquireTokenRedirect` resulteert in het doorleiden van gebruikers naar het eindpunt van het Microsoft-identiteitsplatform.) In dat venster moeten gebruikers communiceren door hun referenties te bevestigen, de toestemming te geven voor de vereiste bron of de tweestapsverificatie te voltooien.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Deze quickstart `loginRedirect` maakt `acquireTokenRedirect` gebruik van de methoden en methoden met Microsoft Internet Explorer, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot de verwerking van pop-upvensters door Internet Explorer.

## <a name="next-steps"></a>Volgende stappen

Zie voor een meer gedetailleerde stapsgewijze handleiding voor het bouwen van de applicatie voor deze quickstart:

> [!div class="nextstepaction"]
> [Zelfstudie om in te loggen en MS Graph te bellen](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Zie het belangrijkste voor documentatie, veelgestelde vragen, problemen en meer voor het zoeken naar de MSAL-repo voor documentatie, veelgestelde vragen en meer:

> [!div class="nextstepaction"]
> [MSAL.js GitHub repo](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
