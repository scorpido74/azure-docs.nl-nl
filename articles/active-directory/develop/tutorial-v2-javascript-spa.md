---
title: Java script-zelf studie voor een app met één pagina-micro soft Identity-platform | Azure
description: Hoe Java script-SPA-toepassingen een API kunnen aanroepen waarvoor toegangs tokens zijn vereist door Azure Active Directory v 2.0-eind punt
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6f0253490d39e69d491dd5fd3ab0d0d0a32d47bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181559"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Gebruikers aanmelden en de Microsoft Graph-API aanroepen vanuit een Java script-toepassing met één pagina (SPA)

In deze hand leiding wordt gedemonstreerd hoe een Java script-toepassing met één pagina (SPA) kan:
- Persoonlijke accounts aanmelden, evenals werk-en school accounts
- Een toegangstoken verkrijgen
- Roep de Microsoft Graph-API of andere Api's aan die toegangs tokens van het *micro soft Identity platform-eind punt* vereisen

>[!NOTE]
> Als u niet bekend bent met het micro soft-identiteits platform, raden we u aan om te beginnen met de [gebruikers voor aanmelden en een toegangs token in een Java script-Spa-Snelstartgids te verkrijgen](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voor beeld-app die wordt gegenereerd door deze hand leiding werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Meer informatie

De voorbeeld toepassing die door deze hand leiding is gemaakt, maakt het mogelijk een Java script-beveiligd-wachtwoord verificatie op te vragen van de Microsoft Graph API of een web-API die tokens van het micro soft Identity platform-eind punt accepteert Wanneer een gebruiker zich aanmeldt, wordt in dit scenario een toegangs token aangevraagd en toegevoegd aan HTTP-aanvragen via de autorisatie-header. Dit token wordt gebruikt om het profiel en de e-mail berichten van de gebruiker via **MS Graph API**te verkrijgen. Het verkrijgen en vernieuwen van tokens wordt verwerkt door de **micro soft Authentication Library (MSAL) voor Java script**.

### <a name="libraries"></a>Bibliotheken

Deze hand leiding maakt gebruik van de volgende bibliotheek:

|Bibliotheek|Beschrijving|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Micro soft-verificatie bibliotheek voor Java script|

## <a name="set-up-your-web-server-or-project"></a>Uw webserver of project instellen

> Wilt u in plaats daarvan het project van dit voor beeld downloaden? [Down load de project bestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Als u het code voorbeeld wilt configureren voordat u het uitvoert, gaat u naar de [configuratie stap](#register-your-application).

## <a name="prerequisites"></a>Vereisten

* Als u deze zelf studie wilt uitvoeren, hebt u een lokale webserver nodig, zoals [node. js](https://nodejs.org/en/download/), [.net core](https://www.microsoft.com/net/core)of IIS Express integratie met [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* De instructies in deze hand leiding zijn gebaseerd op een webserver die is gebouwd in node. js. U wordt aangeraden [Visual Studio code](https://code.visualstudio.com/download) als uw Integrated Development Environment (IDE) te gebruiken.

## <a name="create-your-project"></a>Uw project maken

Zorg ervoor dat [node. js](https://nodejs.org/en/download/) is geïnstalleerd en maak een map om uw toepassing te hosten. Daar gaan we een eenvoudige [Express](https://expressjs.com/) -webserver implementeren voor uw `index.html` bestand.

1. Eerst kunt u met Visual Studio code geïntegreerde Terminal de projectmap vinden en vervolgens Express installeren met behulp van NPM.

1. Maak vervolgens een JS-bestand met de `server.js`naam en voeg de volgende code toe:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

U hebt nu een eenvoudige server voor uw beveiligd-wachtwoord verificatie. Aan het einde van deze zelf studie hebt u de volgende mapstructuur:

![een tekst voors telling van de gewenste indeling van de beveiligd-wachtwoord verificatie-map](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>De beveiligd-wachtwoord verificatie-gebruikers interface maken

1. Maak een `index.html` bestand voor uw Java script-beveiligd-wachtwoord verificatie. Dit bestand implementeert een UI die is gebouwd met **Boots trap 4 Framework** en importeert script bestanden voor configuratie, authenticatie en API-aanroepen.

   Voeg in `index.html` het bestand de volgende code toe:

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
         </div>
       </div>
       </div>
       <br>
       <br>
         <div class="col-md-4">
           <div class="list-group" id="list-tab" role="tablist">
           </div>
         </div>
         <div class="col-md-5">
           <div class="tab-content" id="nav-tabContent">
           </div>
         </div>
       </div>
       <br>
       <br>

       <!-- importing bootstrap.js and supporting js libraries -->
       <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
       <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
       <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

       <!-- importing app scripts (load order is important) -->
       <script type="text/javascript" src="./authConfig.js"></script>
       <script type="text/javascript" src="./graphConfig.js"></script>
       <script type="text/javascript" src="./ui.js"></script>

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > U kunt de versie van MSAL. js in het voor gaande script vervangen door de meest recente versie onder [MSAL. js-releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Maak nu een JS-bestand met de `ui.js`naam, waarmee DOM-elementen worden geopend en bijgewerkt, en voeg de volgende code toe:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Uw toepassing registreren

Voordat u verder gaat met verificatie, registreert u uw toepassing op **Azure Active Directory**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u het account in de rechter bovenhoek en stelt u vervolgens uw portal-sessie in op de Azure AD-Tenant die u wilt gebruiken.
1. Ga naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Selecteer in de sectie **URI omleiden** het **webplatform in** de vervolg keuzelijst en stel vervolgens de waarde in op de toepassings-URL die is gebaseerd op uw webserver.
1. Selecteer **Registreren**.
1. Noteer de waarde van de **toepassing (client)** op de pagina app- **overzicht** voor later gebruik.
1. Voor deze quickstart moet de [Impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld. Selecteer in het linkerdeel venster van de geregistreerde toepassing **verificatie**.
1. Selecteer in **Geavanceerde instellingen**onder **impliciete toekenning**de selectie vakjes **id-tokens** en **toegangs tokens** . ID-tokens en toegangs tokens zijn vereist omdat deze app gebruikers moet aanmelden en een API aanroept.
1. Selecteer **Opslaan**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Een omleidings-URL instellen voor node. js
>
> Voor node. js kunt u de webserver poort instellen in het bestand *server. js* . In deze zelf studie wordt poort 3000 gebruikt, maar u kunt elke andere beschik bare poort gebruiken.
>
> Als u een omleidings-URL wilt instellen in de registratie gegevens van de toepassing, gaat u terug naar het deel venster **toepassings registratie** en voert u een van de volgende handelingen uit:
>
> - Instellen *`http://localhost:3000/`* als de **omleidings-URL**.
> - Als u een aangepaste TCP-poort gebruikt, gebruikt *`http://localhost:<port>/`* u (waarbij * \<poort>* het aangepaste TCP-poort nummer is).
>   1. Kopieer de **URL** -waarde.
>   1. Ga terug naar het deel venster **toepassings registratie** en plak de gekopieerde waarde als de **omleidings-URL**.
>

### <a name="configure-your-javascript-spa"></a>Uw Java script-SPA configureren

Maak een nieuw. js-bestand `authConfig.js`met de naam, dat uw configuratie parameters voor verificatie bevat, en voeg de volgende code toe:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 Waar:
 - Enter_the_Application_Id_Here>de **client-id** is van de toepassing die u hebt geregistreerd. * \<*
 - Enter_the_Cloud_Instance_Id_Here>is het exemplaar van de Azure-Cloud. * \<* Voor de hoofd-of wereld wijde Azure- *https://login.microsoftonline.com*Cloud voert u in. Zie [nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)voor **nationale** Clouds (bijvoorbeeld China).
 - Enter_the_Tenant_info_here>is ingesteld op een van de volgende opties: * \<*
   - Als uw toepassing *accounts in deze organisatie Directory*ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld *contoso.Microsoft.com*).
   - Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**.
   - Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Gebruik de micro soft Authentication Library (MSAL) om u aan te melden bij de gebruiker

Maak een nieuw. js-bestand `authPopup.js`met de naam, dat uw verificatie-en Token verwervings logica bevat, en voeg de volgende code toe:

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>Meer informatie

Nadat een gebruiker de **aanmeldings** knop voor de eerste keer selecteert, wordt de `signIn` methode aangeroepen `loginPopup` om zich aan te melden bij de gebruiker. Met deze methode wordt een pop-upvenster geopend met het *micro soft Identity platform-eind punt* om de referenties van de gebruiker te vragen en te valideren. Nadat het aanmelden is geslaagd, wordt de gebruiker teruggeleid naar de oorspronkelijke *index. html-* pagina. Er wordt een token ontvangen, verwerkt `msal.js`door en de informatie in het token wordt opgeslagen in de cache. Dit token staat bekend als *id-token* en bevat basis informatie over de gebruiker, zoals de weergave naam van de gebruiker. Als u van plan bent om gegevens te gebruiken die door dit token worden verstrekt, moet u ervoor zorgen dat dit token wordt gevalideerd door de back-endserver om te garanderen dat het token is uitgegeven aan een geldige gebruiker voor uw toepassing.

Het beveiligd-wachtwoord verificatie dat door `acquireTokenSilent` deze hand leiding `acquireTokenPopup` wordt gegenereerd, roept en/of een *toegangs token* op dat wordt gebruikt om een query uit te voeren op de Microsoft Graph-API voor gebruikers profiel gegevens. Als u een voor beeld nodig hebt dat het ID-token valideert, bekijkt u [deze](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-java script-singlepageapp-DotNet-webapi-v2-voor beeld") voorbeeld toepassing in github. In het voor beeld wordt een ASP.NET-Web-API gebruikt voor het valideren van tokens.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Na de eerste aanmelding wilt u gebruikers niet vragen om opnieuw te verifiëren wanneer ze een token nodig hebben om toegang te krijgen tot een resource. Daarom moet *acquireTokenSilent* de meeste tijd worden gebruikt voor het verkrijgen van tokens. Er zijn echter situaties waarin u gebruikers moet dwingen met het micro soft Identity platform-eind punt te communiceren. Voorbeelden zijn:

- Gebruikers moeten hun referenties opnieuw invoeren omdat het wacht woord is verlopen.
- Uw toepassing vraagt toegang tot een resource en u hebt toestemming van de gebruiker nodig.
- Verificatie met twee factoren is vereist.

Als *acquireTokenPopup* wordt aangeroepen, wordt een pop-upvenster geopend (of *acquireTokenRedirect* worden gebruikers omgeleid naar het micro soft Identity platform-eind punt). In dat venster moeten gebruikers communiceren door hun referenties te bevestigen, toestemming te geven aan de vereiste resource of de twee ledige verificatie te volt ooien.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `acquireTokenSilent` methode verwerkt het verkrijgen van tokens en vernieuwingen zonder tussen komst van de gebruiker. Nadat `loginPopup` (of `loginRedirect`) voor de eerste keer wordt uitgevoerd, `acquireTokenSilent` is de methode die wordt gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources voor volgende aanroepen. (Aanroepen naar aanvragen of vernieuwings tokens worden op de achtergrond uitgevoerd.) `acquireTokenSilent` kan in sommige gevallen mislukken. Het is bijvoorbeeld mogelijk dat het wacht woord van de gebruiker is verlopen. Uw toepassing kan deze uitzonde ring op twee manieren afhandelen:

1. Een aanroep naar `acquireTokenPopup` onmiddellijk uitvoeren, waarmee een aanmeldings prompt van de gebruiker wordt geactiveerd. Dit patroon wordt vaak gebruikt in online toepassingen waarbij er geen niet-geverifieerde inhoud in de toepassing beschikbaar is voor de gebruiker. Het voor beeld dat door deze begeleide installatie wordt gegenereerd, maakt gebruik van dit patroon.

1. Toepassingen kunnen ook een visuele indicatie geven aan de gebruiker dat een interactieve aanmelding is vereist, zodat de gebruiker de juiste tijd kan selecteren om zich aan te melden, of de toepassing kan op `acquireTokenSilent` een later tijdstip opnieuw worden geprobeerd. Dit wordt meestal gebruikt wanneer de gebruiker andere functionaliteit van de toepassing kan gebruiken zonder te worden onderbroken. Het is bijvoorbeeld mogelijk dat er niet-geverifieerde inhoud beschikbaar is in de toepassing. In dit geval kan de gebruiker beslissen wanneer hij of zij zich wil aanmelden om toegang te krijgen tot de beveiligde resource, of om de verouderde informatie te vernieuwen.

> [!NOTE]
> Deze Quick Start maakt `loginPopup` standaard `acquireTokenPopup` gebruik van de methoden en. Als u Internet Explorer als uw browser gebruikt, is het raadzaam om gebruik `loginRedirect` te maken `acquireTokenRedirect` van en methoden, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot de manier waarop Internet Explorer pop-upvensters verwerkt. `Redirect methods` [Zie](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)als u wilt weten hoe u hetzelfde resultaat kunt krijgen met.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>De Microsoft Graph-API aanroepen met behulp van het token dat u zojuist hebt aangeschaft

1. Maak eerst een JS-bestand met de `graphConfig.js`naam, waarin uw rest-eind punten worden opgeslagen. Voeg de volgende code toe:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Waar:
   - Enter_the_Graph_Endpoint_Here>is het exemplaar van MS Graph API. * \<* Voor het globale MS Graph API-eind punt vervangt u deze teken `https://graph.microsoft.com`reeks door. Raadpleeg de [documentatie van Graph API](https://docs.microsoft.com/graph/deployments)voor de implementaties van nationale Clouds.

1. Maak vervolgens een. js-bestand met `graph.js`de naam, waardoor een rest-aanroep naar Microsoft Graph-API wordt gemaakt en voeg de volgende code toe:

   ```javascript
   function callMSGraph(endpoint, token, callback) {
     const headers = new Headers();
     const bearer = `Bearer ${token}`;

     headers.append("Authorization", bearer);

     const options = {
         method: "GET",
         headers: headers
     };

     console.log('request made to Graph API at: ' + new Date().toString());

     fetch(endpoint, options)
       .then(response => response.json())
       .then(response => callback(response, endpoint))
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In de voorbeeld toepassing die door deze hand leiding is `callMSGraph()` gemaakt, wordt de methode gebruikt om `GET` een HTTP-aanvraag te doen tegen een beveiligde bron waarvoor een token is vereist. De aanvraag retourneert vervolgens de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd aan de *http-autorisatie-header*. Voor de voorbeeld toepassing die door deze hand leiding is gemaakt, is de resource het Microsoft Graph API *me* -eind punt, waarin de profiel gegevens van de gebruiker worden weer gegeven.

## <a name="test-your-code"></a>Uw code testen

1. Configureer de server om naar een TCP-poort te Luis teren die is gebaseerd op de locatie van het bestand *index. html* . Voor node. js start u de webserver om naar de poort te Luis teren door de volgende opdrachten uit te voeren vanaf een opdracht regel prompt vanuit de toepassingsmap:

   ```bash
   npm install
   npm start
   ```
1. Voer **http://localhost:3000** in uw browser in of **http://localhost:{port}**, waarbij *poort* de poort is waarnaar uw webserver luistert. U ziet nu de inhoud van het bestand *index. html* en de knop **Aanmelden** .

## <a name="test-your-application"></a>Uw toepassing testen

Nadat de browser het bestand *index. html* heeft geladen, selecteert u **Aanmelden**. U wordt gevraagd om u aan te melden met het micro soft Identity platform-eind punt:

![Het aanmeld venster voor het Java script-account](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt u gevraagd de toegang tot uw profiel toe te kennen en u aan te melden:

![Het venster machtigingen aangevraagd](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Toepassings resultaten weer geven

Nadat u zich hebt aangemeld, worden de gegevens van uw gebruikers profiel geretourneerd in de Microsoft Graph API-reactie die wordt weer gegeven:

![Resultaten van de API-aanroep van Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

Voor de Microsoft Graph-API is het bereik *User. Read* vereist om het profiel van een gebruiker te lezen. Deze scope wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd op de registratie Portal. Andere Api's voor Microsoft Graph, evenals aangepaste Api's voor uw back-endserver, vereisen mogelijk extra scopes. De Microsoft Graph-API vereist bijvoorbeeld het bereik *mail. Read* om de e-mail berichten van de gebruiker weer te geven.

> [!NOTE]
> De gebruiker wordt mogelijk gevraagd om aanvullende toestemmingen wanneer u het aantal bereiken verhoogt.

Als voor een back-end-API geen bereik is vereist (niet aanbevolen), kunt u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
