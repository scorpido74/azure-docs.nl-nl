---
title: Java script-zelf studie voor apps met één pagina 2,0-micro soft-identiteits platform | Azure
description: Hoe Java script-SPA-toepassingen de verificatie code stroom kunnen gebruiken voor het aanroepen van een API waarvoor toegangs tokens zijn vereist door Azure Active Directory v 2.0-eind punt
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890382"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Meld u aan bij gebruikers en roep de Microsoft Graph-API aan vanuit een Java script-toepassing met één pagina (SPA)-MSAL. js 2,0

> [!IMPORTANT]
> Deze functie is momenteel in preview. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functie kunnen worden gewijzigd vóór algemene Beschik baarheid (GA).

In deze zelf studie wordt gebruikgemaakt van een versie van MSAL. js die gebruikmaakt van de OAuth 2,0-autorisatie code stroom met PKCE. Raadpleeg de [documentatie](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)voor meer informatie over dit protocol, evenals de verschillen tussen de impliciete stroom en de autorisatie code stroom. Als u een zelf studie zoekt die gebruikmaakt van de impliciete stroom, raadpleegt u de [zelf studie MSAL. js v1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

Deze versie van MSAL. js verbetert de huidige MSAL-kern bibliotheek en gebruikt de autorisatie code stroom in de browser. De meeste functies die beschikbaar zijn in de oude bibliotheek, zijn beschikbaar in deze versie, maar er zijn nuances voor de verificatie stroom in beide. Deze versie biedt **geen** ondersteuning voor de impliciete stroom.

In deze hand leiding wordt gedemonstreerd hoe een Java script-toepassing met één pagina (SPA) kan:
- Persoonlijke accounts aanmelden, evenals werk-en school accounts
- Een toegangstoken verkrijgen
- Roep de Microsoft Graph-API of andere Api's aan die toegangs tokens van het *micro soft Identity platform-eind punt* vereisen

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voor beeld-app die wordt gegenereerd door deze hand leiding werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Meer informatie

De voorbeeld toepassing die door deze hand leiding is gemaakt, maakt het mogelijk een Java script-beveiligd-wachtwoord verificatie op te vragen van de Microsoft Graph API of een web-API die tokens van het micro soft Identity platform-eind punt accepteert Wanneer een gebruiker zich aanmeldt, wordt in dit scenario een toegangs token aangevraagd en toegevoegd aan HTTP-aanvragen via de autorisatie-header. Het ophalen en vernieuwen van tokens worden verwerkt door de micro soft Authentication Library (MSAL).

### <a name="libraries"></a>Bibliotheken

Deze hand leiding maakt gebruik van de volgende bibliotheek:

|Bibliotheek|Beschrijving|
|---|---|
|[msal. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Micro soft-verificatie bibliotheek voor Java script-browser pakket|

## <a name="set-up-your-web-server-or-project"></a>Uw webserver of project instellen

Wilt u in plaats daarvan het project van dit voor beeld downloaden? Als u het project wilt uitvoeren met behulp van een lokale webserver, zoals node. js, kloont u de project bestanden:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Als u het code voorbeeld wilt configureren voordat u het uitvoert, gaat u naar de [configuratie stap](#register-your-application).

## <a name="prerequisites"></a>Vereisten

* Als u deze zelf studie wilt uitvoeren, hebt u een lokale webserver nodig, zoals [node. js](https://nodejs.org/en/download/) of [.net core](https://www.microsoft.com/net/core).

* Als u node. js gebruikt om het project uit te voeren, installeert u een Integrated Development Environment (IDE) zoals [Visual Studio code](https://code.visualstudio.com/download) voor het bewerken van de project bestanden.

* De instructies in deze zelf studie zijn gebaseerd op node. js.

## <a name="create-your-project"></a>Uw project maken

Zorg ervoor dat [node. js](https://nodejs.org/en/download/) is geïnstalleerd en maak een map om uw toepassing te hosten. Implementeer vervolgens een kleine [Express](https://expressjs.com/) -webserver om uw `index.html` bestand te leveren.

1. Ga eerst naar de projectmap in uw Terminal en voer de volgende NPM-opdrachten uit.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Maak vervolgens een JS-bestand met de naam *server. js*en voeg de volgende code toe:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

U hebt nu een eenvoudige server voor uw beveiligd-wachtwoord verificatie. Aan het einde van deze zelf studie hebt u de volgende mapstructuur:

![een tekst voors telling van de gewenste indeling van de beveiligd-wachtwoord verificatie-map](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>De beveiligd-wachtwoord verificatie-gebruikers interface maken

1. Maak een *index. html-* bestand voor uw Java script-beveiligd-wachtwoord verificatie in de map *app* . Dit bestand implementeert een UI die is gebouwd met **Boots trap 4 Framework** en importeert script bestanden voor configuratie-, verificatie-en API-aanroepen.

   Voeg in het bestand *index. html* de volgende code toe:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
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

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > U kunt de versie van MSAL. js in het voor gaande script vervangen door de meest recente versie onder [MSAL. js-releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Maak nu een JS-bestand met de naam *UI. js* dat DOM-elementen opent en bijwerkt, en voeg de volgende code toe:

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

Volg de instructies voor het [registreren van een nieuwe toepassing met één pagina](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Een omleidings-URL instellen voor node. js

Voor node. js kunt u de webserver poort instellen in het bestand *server. js* . In deze zelf studie wordt poort 3000 gebruikt, maar u kunt elke andere beschik bare poort gebruiken.

Als u een omleidings-URL wilt instellen in de registratie gegevens van de toepassing, gaat u terug naar het deel venster **toepassings registratie** en registreert u een nieuw **beveiligd-wachtwoord** verificatie-op een van de volgende manieren:

- Instellen *`http://localhost:3000/`* als de **omleidings-URL**.
- Als u een aangepaste TCP-poort gebruikt, gebruikt *`http://localhost:<port>/`* u (waarbij * \<poort>* het aangepaste TCP-poort nummer is).

### <a name="configure-your-javascript-spa"></a>Uw Java script-SPA configureren

Maak een nieuw. js-bestand met de naam *authConfig. js* dat uw configuratie parameters voor verificatie bevat en voeg de volgende code toe:

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
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Waar:
 - Enter_the_Application_Id_Here>de **client-id** is van de toepassing die u hebt geregistreerd. * \<*
 - Enter_the_Cloud_Instance_Id_Here>is het exemplaar van de Azure-Cloud. * \<* Voor de hoofd-of wereld wijde Azure- *https://login.microsoftonline.com*Cloud voert u in. Zie [nationale Clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)voor **nationale** Clouds (bijvoorbeeld China).
 - Enter_the_Tenant_info_here>is ingesteld op een van de volgende opties: * \<*
   - Als uw toepassing *accounts in deze organisatie Directory*ondersteunt, vervangt u deze waarde door de **Tenant-id** of **Tenant naam** (bijvoorbeeld *contoso.Microsoft.com*).
   - Als uw toepassing *accounts in een organisatorische Directory*ondersteunt, vervangt u deze waarde door **organisaties**.
   - Als uw toepassing *accounts in een organisatorische map en persoonlijke micro soft-accounts*ondersteunt, vervangt u deze waarde door **common**. Als u de ondersteuning voor *persoonlijke micro soft-accounts*wilt beperken, moet u deze waarde vervangen door **consumenten**.
- *\ Enter_the_Redirect_Uri_Here>* de poort is die u in de portal hebt*`http://localhost:3000/`* geregistreerd ()


Maak een nieuw. js-bestand `graphConfig.js`met de naam, dat uw configuratie parameters bevat waarmee Microsoft Graph-API wordt aangeroepen en voeg de volgende code toe:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- Enter_the_Graph_Endpoint_Here>is het exemplaar van MS Graph API. * \<* Voor het globale MS Graph API-eind punt vervangt u deze teken `https://graph.microsoft.com`reeks door. Raadpleeg de [documentatie van Graph API](https://docs.microsoft.com/graph/deployments)voor de implementaties van nationale Clouds.

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Gebruik de micro soft Authentication Library (MSAL) om u aan te melden bij de gebruiker

### <a name="popup"></a>Achtergrond
Maak een nieuw. js-bestand `authPopup.js`met de naam, dat uw authenticatie-en Token verwervings logica voor de pop-up popUp bevat, en voeg de volgende code toe:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
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
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Omleiden
Maak een nieuw. js-bestand `authRedirect.js`met de naam, dat uw verificatie-en Token verwervings logica voor het omleiden van de aanmelding bevat, en voeg de volgende code toe:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Meer informatie

Nadat een gebruiker de **aanmeldings** knop voor de eerste keer selecteert, wordt de `signIn` methode aangeroepen `loginPopup` om zich aan te melden bij de gebruiker. Met deze methode wordt een pop-upvenster geopend met het *micro soft Identity platform-eind punt* om de referenties van de gebruiker te vragen en te valideren. Na een geslaagde aanmelding initieert *msal. js* de [autorisatie code stroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

Op dit moment wordt een met PKCE beveiligde autorisatie code verzonden naar het eind punt van het CORS-beveiligde token en wordt uitgewisseld voor tokens. Er worden een ID-token, toegangs token en vernieuwings token ontvangen, verwerkt door *msal. js*en de informatie in het token wordt opgeslagen in de cache.

Het ID-token bevat basis informatie over de gebruiker, zoals de weergave naam. Als u van plan bent om gegevens te gebruiken die door dit token worden verstrekt, moet de server worden gevalideerd door de back-endserver om te garanderen dat het token is uitgegeven aan een geldige gebruiker voor uw toepassing. Het vernieuwings token heeft een beperkte levens duur en verloopt na 24 uur. Het vernieuwings token kan worden gebruikt om nieuwe toegangs tokens op de achtergrond te verkrijgen.

Het beveiligd-wachtwoord verificatie dat door `acquireTokenSilent` deze hand leiding `acquireTokenPopup` wordt gegenereerd, roept en/of een *toegangs token* op dat wordt gebruikt om een query uit te voeren op de Microsoft Graph-API voor gebruikers profiel gegevens. Als u een voor beeld nodig hebt dat het ID-token valideert, raadpleegt u de voorbeeld toepassing [Active-Directory-java script-singlepageapp-DotNet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) op github. In het voor beeld wordt een ASP.NET-Web-API gebruikt voor het valideren van tokens.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Na de eerste aanmelding moeten uw gebruikers niet elke keer dat ze een token moeten aanvragen, opnieuw worden geverifieerd om toegang te krijgen tot een bron. Gebruik `acquireTokenSilent`om dergelijke herauthenticatie aanvragen te voor komen. Er zijn echter een aantal situaties waarin u mogelijk wilt afdwingen dat gebruikers met het micro soft Identity platform-eind punt communiceren. Bijvoorbeeld:

- Gebruikers moeten hun referenties opnieuw invoeren omdat het wacht woord is verlopen.
- Uw toepassing vraagt toegang tot een resource en u hebt toestemming van de gebruiker nodig.
- Verificatie met twee factoren is vereist.

Met `acquireTokenPopup` aanroepen wordt een pop-upvenster `acquireTokenRedirect` geopend (of worden gebruikers omgeleid naar het micro soft Identity platform-eind punt). In dat venster moeten gebruikers communiceren door hun referenties te bevestigen, toestemming te geven aan de vereiste resource of de twee ledige verificatie te volt ooien.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `acquireTokenSilent` methode verwerkt het verkrijgen van tokens en vernieuwingen zonder tussen komst van de gebruiker. Nadat `loginPopup` (of `loginRedirect`) voor de eerste keer wordt uitgevoerd, `acquireTokenSilent` is de methode die wordt gebruikt om tokens te verkrijgen die worden gebruikt voor toegang tot beveiligde resources voor volgende aanroepen. (Aanroepen naar aanvragen of vernieuwings tokens worden op de achtergrond uitgevoerd.) `acquireTokenSilent` kan in sommige gevallen mislukken. Het is bijvoorbeeld mogelijk dat het wacht woord van de gebruiker is verlopen. Uw toepassing kan deze uitzonde ring op twee manieren afhandelen:

1. Een aanroep maken om `acquireTokenPopup` onmiddellijk een aanmeldings prompt van een gebruiker te activeren. Dit patroon wordt vaak gebruikt in online toepassingen waarbij er geen niet-geverifieerde inhoud in de toepassing beschikbaar is voor de gebruiker. Het voor beeld dat door deze begeleide installatie wordt gegenereerd, maakt gebruik van dit patroon.

1. Geef visueel aan de gebruiker door dat een interactieve aanmelding is vereist, zodat de gebruiker het juiste tijdstip kan selecteren om zich aan te melden, of de toepassing `acquireTokenSilent` kan op een later tijdstip opnieuw worden geprobeerd. Deze techniek wordt vaak gebruikt wanneer de gebruiker andere functionaliteit van de toepassing kan gebruiken zonder te worden onderbroken. Het is bijvoorbeeld mogelijk dat er niet-geverifieerde inhoud beschikbaar is in de toepassing. In dit geval kan de gebruiker beslissen wanneer hij of zij zich wil aanmelden om toegang te krijgen tot de beveiligde resource, of om de verouderde informatie te vernieuwen.

> [!NOTE]
> Deze Quick Start maakt `loginPopup` standaard `acquireTokenPopup` gebruik van de methoden en. Als u Internet Explorer als uw browser gebruikt, is het raadzaam om gebruik `loginRedirect` te maken `acquireTokenRedirect` van en methoden, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot de manier waarop Internet Explorer pop-upvensters verwerkt. Als u wilt weten hoe u hetzelfde resultaat kunt krijgen met behulp van omleidings methoden, raadpleegt u [*authRedirect. js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>De Microsoft Graph-API aanroepen met behulp van het token dat u zojuist hebt aangeschaft


 Maak een JS-bestand met de naam *Graph. js* dat een rest-aanroep naar Microsoft Graph-API maakt en voeg de volgende code toe:

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
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
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In de voorbeeld toepassing die door deze hand leiding is `callMSGraph()` gemaakt, wordt de methode gebruikt om `GET` een HTTP-aanvraag te doen tegen een beveiligde bron waarvoor een token is vereist. De aanvraag retourneert vervolgens de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd aan de *http-autorisatie-header*. Voor de voorbeeld toepassing die door deze hand leiding is gemaakt, is de resource het Microsoft Graph API *me* -eind punt, waarin de profiel gegevens van de gebruiker worden weer gegeven.

## <a name="test-your-code"></a>Uw code testen

1. Voor node. js start u de webserver door de volgende opdrachten uit te voeren in de toepassingsmap:

   ```bash
   npm install
   npm start
   ```
1. Voer **http://localhost:3000** in uw browser in of **http://localhost:{port}**, waarbij *poort* de poort is waarop uw webserver luistert. U ziet nu de inhoud van het bestand *index. html* en de knop **Aanmelden** .

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

Voor de Microsoft Graph-API is het bereik *User. Read* vereist om het profiel van een gebruiker te lezen. Deze scope wordt standaard automatisch toegevoegd in elke toepassing die is geregistreerd in de Azure Portal. Andere Api's voor Microsoft Graph, evenals aangepaste Api's voor uw back-endserver, vereisen mogelijk extra scopes. De Microsoft Graph-API vereist bijvoorbeeld het bereik *mail. Read* om de e-mail berichten van de gebruiker weer te geven.

> [!NOTE]
> De gebruiker wordt mogelijk gevraagd om aanvullende toestemming wanneer u scopes toevoegt.

Als voor een back-end-API geen bereik is vereist (niet aanbevolen), kunt u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

De [MSAL. js github opslag plaats](https://github.com/AzureAD/microsoft-authentication-library-for-js) bevat aanvullende Bibliotheek Documentatie, een veelgestelde vragen en biedt ondersteuning voor het probleem.
