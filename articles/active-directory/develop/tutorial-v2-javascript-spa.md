---
title: Azure AD v 2.0 java script-toepassing met één pagina (SPA) begeleide setup | Microsoft Docs
description: Hoe Java script-SPA-toepassingen een API kunnen aanroepen waarvoor toegangs tokens zijn vereist door Azure Active Directory v 2.0-eind punt
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61790954393923bbf330ad3a534d1d33d1a44bbc
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983482"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Gebruikers aanmelden en de Microsoft Graph-API aanroepen vanuit een Java script-toepassing met één pagina (SPA)

In deze hand leiding wordt gedemonstreerd hoe een Java script-toepassing met één pagina (SPA) kan:
- Persoonlijke accounts aanmelden, evenals werk-en school accounts 
- Een toegangstoken verkrijgen
- Roep de Microsoft Graph-API of andere Api's aan die toegangs tokens van het *micro soft Identity platform-eind punt* vereisen

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voor beeld-app die wordt gegenereerd door deze hand leiding werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

De voorbeeld toepassing die door deze hand leiding is gemaakt, maakt het mogelijk een Java script-beveiligd-wachtwoord verificatie op te vragen van de Microsoft Graph API of een web-API die tokens van het micro soft Identity platform-eind punt accepteert Wanneer een gebruiker zich aanmeldt, wordt in dit scenario een toegangs token aangevraagd en toegevoegd aan HTTP-aanvragen via de autorisatie-header. Het ophalen en vernieuwen van tokens worden verwerkt door de micro soft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotheken

Deze hand leiding maakt gebruik van de volgende bibliotheek:

|Bibliotheek|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Preview van micro soft-verificatie bibliotheek voor Java script|

> [!NOTE]
> *Msal. js* is gericht op het micro soft Identity platform-eind punt, dat persoonlijke accounts en school-en werk accounts mogelijk maakt om zich aan te melden en tokens te verkrijgen. Het micro soft Identity platform-eind punt heeft [enkele beperkingen](azure-ad-endpoint-comparison.md#limitations).
> Zie de [hand leiding voor het vergelijken van eind punten](azure-ad-endpoint-comparison.md)om de verschillen tussen de eind punten v 1.0 en v 2.0 te begrijpen.

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Uw webserver of project instellen

> Wilt u in plaats daarvan het project van dit voor beeld downloaden? Voer een van de volgende bewerkingen uit:
> 
> - Als u het project wilt uitvoeren met behulp van een lokale webserver, zoals node. js, [downloadt u de project bestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Beschrijving Als u het project wilt uitvoeren met behulp van de micro soft Internet Information Services (IIS)-server, [downloadt u het Visual Studio-project](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Als u het code voorbeeld wilt configureren voordat u het uitvoert, gaat u naar de [configuratie stap](#register-your-application).

## <a name="prerequisites"></a>Vereisten

* Als u deze zelf studie wilt uitvoeren, hebt u een lokale webserver nodig, zoals [node. js](https://nodejs.org/en/download/), [.net core](https://www.microsoft.com/net/core)of IIS Express integratie met [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Als u node. js gebruikt om het project uit te voeren, installeert u een Integrated Development Environment (IDE), zoals [Visual Studio code](https://code.visualstudio.com/download), om de project bestanden te bewerken.

* De instructies in deze hand leiding zijn gebaseerd op node. js en Visual Studio 2017, maar u kunt elke andere ontwikkel omgeving of webserver gebruiken.

## <a name="create-your-project"></a>Uw project maken

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Optie 1: Node. js of andere webservers
> Zorg ervoor dat [node. js](https://nodejs.org/en/download/) is geïnstalleerd en maak een map om uw toepassing te hosten.
>
> ### <a name="option-2-visual-studio"></a>Optie 2: Visual Studio
> Als u Visual Studio gebruikt en een nieuw project maakt, voert u de volgende stappen uit:
> 1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**.
> 1. Selecteer onder **Visual C#\Web** de optie **ASP.NET-webtoepassing (.NET Framework)** .
> 1. Voer een naam in voor uw toepassing en selecteer **OK**.
> 1. Selecteer onder **nieuwe ASP.NET-webtoepassing**de optie **leeg**.

## <a name="create-the-spa-ui"></a>De beveiligd-wachtwoord verificatie-gebruikers interface maken
1. Maak een *index. html-* bestand voor uw Java script-beveiligd-wachtwoord verificatie. Als u Visual Studio gebruikt, selecteert u het project (hoofdmap van het project). Klik met de rechter muisknop en selecteer**HTML-pagina** **Nieuw item** >  **toevoegen** > en noem het bestand *index. html*.

1. Voeg in het bestand *index. html* de volgende code toe:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > U kunt de versie van MSAL. js in het voor gaande script vervangen door de meest recente versie onder [MSAL. js-releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Gebruik de micro soft Authentication Library (MSAL) om u aan te melden bij de gebruiker

Voeg de volgende code toe aan `index.html` het bestand binnen `<script></script>` de Tags:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

Nadat een gebruiker de **aanmeldings** knop voor de eerste keer selecteert, wordt de `signIn` methode aangeroepen `loginPopup` om zich aan te melden bij de gebruiker. Met deze methode wordt een pop-upvenster geopend met het *micro soft Identity platform-eind punt* om de referenties van de gebruiker te vragen en te valideren. Nadat het aanmelden is geslaagd, wordt de gebruiker teruggeleid naar de oorspronkelijke *index. html-* pagina. Er wordt een token ontvangen, verwerkt `msal.js`door en de informatie in het token wordt opgeslagen in de cache. Dit token staat bekend als *id-token* en bevat basis informatie over de gebruiker, zoals de weergave naam van de gebruiker. Als u van plan bent om gegevens te gebruiken die door dit token worden verstrekt, moet u ervoor zorgen dat dit token wordt gevalideerd door de back-endserver om te garanderen dat het token is uitgegeven aan een geldige gebruiker voor uw toepassing.

Het beveiligd-wachtwoord verificatie dat door `acquireTokenSilent` deze hand leiding `acquireTokenPopup` wordt gegenereerd, roept en/of een *toegangs token* op dat wordt gebruikt om een query uit te voeren op de Microsoft Graph-API voor gebruikers profiel gegevens. Als u een voor beeld nodig hebt dat het ID-token valideert, bekijkt u [deze](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "github Active-Directory-java script-singlepageapp-DotNet-webapi-v2 voorbeeld") toepassing in github. In het voor beeld wordt een ASP.NET-Web-API gebruikt voor het valideren van tokens.

#### <a name="getting-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Na de eerste aanmelding wilt u gebruikers niet vragen om opnieuw te verifiëren wanneer ze een token nodig hebben om toegang te krijgen tot een resource. Daarom moet *acquireTokenSilent* de meeste tijd worden gebruikt voor het verkrijgen van tokens. Er zijn echter situaties waarin u gebruikers moet dwingen met het micro soft Identity platform-eind punt te communiceren. Voorbeelden zijn:

- Gebruikers moeten hun referenties opnieuw invoeren omdat het wacht woord is verlopen.
- Uw toepassing vraagt toegang tot een resource en u hebt toestemming van de gebruiker nodig.
- Verificatie met twee factoren is vereist.

Als *acquireTokenPopup* wordt aangeroepen, wordt een pop-upvenster geopend (of *acquireTokenRedirect* worden gebruikers omgeleid naar het micro soft Identity platform-eind punt). In dat venster moeten gebruikers communiceren door hun referenties te bevestigen, toestemming te geven aan de vereiste resource of de twee ledige verificatie te volt ooien.

#### <a name="getting-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `acquireTokenSilent` methode verwerkt het verkrijgen van tokens en vernieuwingen zonder tussen komst van de gebruiker. Nadat `loginPopup` (of `loginRedirect`) voor de eerste keer wordt uitgevoerd, `acquireTokenSilent` is de methode die wordt gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources voor volgende aanroepen. (Aanroepen naar aanvragen of vernieuwings tokens worden op de achtergrond uitgevoerd.) `acquireTokenSilent` kan in sommige gevallen mislukken. Het is bijvoorbeeld mogelijk dat het wacht woord van de gebruiker is verlopen. Uw toepassing kan deze uitzonde ring op twee manieren afhandelen:

1. Een aanroep naar `acquireTokenPopup` onmiddellijk uitvoeren, waarmee een aanmeldings prompt van de gebruiker wordt geactiveerd. Dit patroon wordt vaak gebruikt in online toepassingen waarbij er geen niet-geverifieerde inhoud in de toepassing beschikbaar is voor de gebruiker. Het voor beeld dat door deze begeleide installatie wordt gegenereerd, maakt gebruik van dit patroon.

2. Toepassingen kunnen ook een visuele indicatie geven aan de gebruiker dat een interactieve aanmelding is vereist, zodat de gebruiker de juiste tijd kan selecteren om zich aan te melden, of de toepassing kan op `acquireTokenSilent` een later tijdstip opnieuw worden geprobeerd. Dit wordt meestal gebruikt wanneer de gebruiker andere functionaliteit van de toepassing kan gebruiken zonder te worden onderbroken. Het is bijvoorbeeld mogelijk dat er niet-geverifieerde inhoud beschikbaar is in de toepassing. In dit geval kan de gebruiker beslissen wanneer hij of zij zich wil aanmelden om toegang te krijgen tot de beveiligde resource, of om de verouderde informatie te vernieuwen.

> [!NOTE]
> In deze Quick Start `loginRedirect` worden `acquireTokenRedirect` de methoden en gebruikt wanneer Internet Explorer wordt gebruikt in de browser. We volgen deze praktijk vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot de manier waarop Internet Explorer pop-upvensters verwerkt.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>De Microsoft Graph-API aanroepen met behulp van het token dat u zojuist hebt aangeschaft

Voeg de volgende code toe aan `index.html` het bestand binnen `<script></script>` de Tags:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In de voorbeeld toepassing die door deze hand leiding is `callMSGraph()` gemaakt, wordt de methode gebruikt om `GET` een HTTP-aanvraag te doen tegen een beveiligde bron waarvoor een token is vereist. De aanvraag retourneert vervolgens de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd aan de *http-autorisatie-header*. Voor de voorbeeld toepassing die door deze hand leiding is gemaakt, is de resource het Microsoft Graph API *me* -eind punt, waarin de profiel gegevens van de gebruiker worden weer gegeven.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Een methode voor het afmelden van de gebruiker toevoegen

Voeg de volgende code toe aan `index.html` het bestand binnen `<script></script>` de Tags:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Uw toepassing registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u het account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure AD-Tenant die u wilt gebruiken.
1. Ga naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Selecteer in de sectie **URI omleiden** het **webplatform in** de vervolg keuzelijst en stel vervolgens de waarde in op de toepassings-URL die is gebaseerd op uw webserver.

   Voor informatie over het instellen en verkrijgen van de omleidings-URL voor node. js en Visual Studio, raadpleegt u de volgende sectie ' een omleidings-URL voor node. js instellen ' en [een omleidings-URL instellen voor Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Selecteer **Registreren**.
1. Noteer de waarde van de **toepassing (client)** op de pagina app- **overzicht** voor later gebruik.
1. Voor deze quickstart moet de [Impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld. Selecteer in het linkerdeel venster van de geregistreerde toepassing **verificatie**.
1. Selecteer in **Geavanceerde instellingen**onder **impliciete toekenning**de selectie vakjes **id-tokens** en **toegangs tokens** . ID-tokens en toegangs tokens zijn vereist omdat deze app gebruikers moet aanmelden en een API aanroept.
1. Selecteer **Opslaan**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Een omleidings-URL instellen voor node. js
> Voor node. js kunt u de webserver poort instellen in het bestand *server. js* . In deze zelf studie wordt poort 30662 gebruikt, maar u kunt elke andere beschik bare poort gebruiken.
>
> Als u een omleidings-URL wilt instellen in de registratie gegevens van de toepassing, gaat u terug naar het deel venster **toepassings registratie** en voert u een van de volgende handelingen uit:
>
> - Instellen *`http://localhost:30662/`* als de **omleidings-URL**.
> - Als u een aangepaste TCP-poort gebruikt, gebruikt *`http://localhost:<port>/`* u (waarbij  *\<poort >* het aangepaste TCP-poort nummer is).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Een omleidings-URL instellen voor Visual Studio
> Voer de volgende stappen uit om de omleidings-URL voor Visual Studio te verkrijgen:
> 1. Selecteer in Solution Explorer het project.
>
>    Het venster **Eigenschappen** wordt geopend. Als dat niet het geval is, drukt u op F4.
>
>    ![Het JavaScriptSPA-Project venster Eigenschappen](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Kopieer de **URL** -waarde.
> 1. Ga terug naar het deel venster **toepassings registratie** en plak de gekopieerde waarde als de **omleidings-URL**.

#### <a name="configure-your-javascript-spa"></a>Uw Java script-SPA configureren

1. Voeg in het bestand *index. html* dat u tijdens de project installatie hebt gemaakt de registratie gegevens voor de toepassing toe. Voeg boven aan het bestand, binnen de `<script></script>` Tags, de volgende code toe:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Waar:
    - Enter_the_Application_Id_here > is de **client-id** van de toepassing die u hebt geregistreerd.  *\<*
    - Enter_the_Tenant_info_here > is ingesteld op een van de volgende opties:  *\<*
       - Als uw toepassing *accounts in deze organisatie Directory*ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld *contoso.Microsoft.com*).
       - Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**.
       - Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**.

## <a name="test-your-code"></a>Uw code testen

Test uw code met behulp van een van de volgende omgevingen.

### <a name="test-with-nodejs"></a>Testen met node. js

Als u geen gebruik maakt van Visual Studio, controleert u of uw webserver is gestart.

1. Configureer de server om naar een TCP-poort te Luis teren die is gebaseerd op de locatie van het bestand *index. html* . Voor node. js start u de webserver om naar de poort te Luis teren door de volgende opdrachten uit te voeren vanaf een opdracht regel prompt vanuit de toepassingsmap:

    ```bash
    npm install
    node server.js
    ```
1. In uw browser voert u **http://\<span >\</span > localhost: 30662** of **http://\<span >\</span > localhost: {Port}** , waarbij *poort* de poort is die uw webserver is Luis teren naar. U ziet nu de inhoud van het bestand *index. html* en de knop **Aanmelden** .

### <a name="test-with-visual-studio"></a>Testen met Visual Studio

Als u Visual Studio gebruikt, selecteert u de project-oplossing en drukt u op F5 om het project uit te voeren. De browser wordt geopend op de<span></span>locatie van de http://localhost: {Port} en de **aanmeldings** knop moet zichtbaar zijn.

## <a name="test-your-application"></a>Uw toepassing testen

Nadat de browser het bestand *index. html* heeft geladen, selecteert u **Aanmelden**. U wordt gevraagd om u aan te melden met het micro soft Identity platform-eind punt:

![Het aanmeld venster voor het Java script-account](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt u gevraagd de toegang tot uw profiel toe te kennen en u aan te melden:

![Het venster machtigingen aangevraagd](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Toepassings resultaten weer geven

Nadat u zich hebt aangemeld, worden de gegevens van uw gebruikers profiel geretourneerd in de Microsoft Graph API-reactie die wordt weer gegeven:

![Resultaten van de API-aanroep van Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

Voor de Microsoft Graph-API is het bereik *User. Read* vereist om het profiel van een gebruiker te lezen. Deze scope wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd op de registratie Portal. Andere Api's voor Microsoft Graph, evenals aangepaste Api's voor uw back-endserver, vereisen mogelijk extra scopes. De Microsoft Graph-API vereist bijvoorbeeld het bereik *Calendars. Read* om de agenda's van de gebruiker weer te geven.

Als u toegang wilt krijgen tot de agenda's van de gebruiker in de context van een toepassing, voegt u de *agenda's toe.* gedelegeerde machtigingen lezen voor de registratie gegevens van de toepassing. Voeg vervolgens de *agenda's* toe aan de `acquireTokenSilent` aanroep.

>[!NOTE]
>De gebruiker wordt mogelijk gevraagd om aanvullende toestemmingen wanneer u het aantal bereiken verhoogt.

Als voor een back-end-API geen bereik is vereist (niet aanbevolen), kunt u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
