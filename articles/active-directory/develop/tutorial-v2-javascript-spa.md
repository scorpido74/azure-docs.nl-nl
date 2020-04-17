---
title: JavaScript-zelfstudie voor apps op één pagina - Microsoft-identiteitsplatform | Azure
description: Hoe JavaScript SPA-toepassingen een API kunnen aanroepen waarvoor toegangstokens vereist zijn voor Azure Active Directory v2.0-eindpunt
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
ms.openlocfilehash: 4a6694a072231f98383c13e6a42aedf68f62ac93
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533782"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een JavaScript-toepassing met één pagina (SPA)

Deze handleiding laat zien hoe een JavaScript single-page applicatie (SPA) kan:
- Persoonlijke accounts aanmelden, werk- en schoolaccounts
- Een toegangstoken verkrijgen
- Bel de Microsoft Graph API of andere API's waarvoor toegangstokens van het Eindpunt van het *Microsoft-identiteitsplatform* nodig zijn

>[!NOTE]
> Als u nieuw bent op het Microsoft-identiteitsplatform, raden we u aan om met de [gebruikers aan te melden en snel een toegangstoken te krijgen in een JavaScript SPA.](quickstart-v2-javascript.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voorbeeld-app die door deze handleiding wordt gegenereerd, werkt

![Laat zien hoe de voorbeeld-app die door deze zelfstudie wordt gegenereerd, werkt](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

Met de voorbeeldtoepassing die door deze handleiding is gemaakt, kan een JavaScript SPA de Microsoft Graph API of een web-API opvragen die tokens van het eindpunt van het Microsoft-identiteitsplatform accepteert. In dit scenario wordt, nadat een gebruiker zich heeft aanmeldt, een toegangstoken aangevraagd en toegevoegd aan HTTP-aanvragen via de autorisatiekop. Dit token wordt gebruikt om het profiel en de e-mails van de gebruiker te verkrijgen via **de MS Graph API.** Tokenacquisitie en -verlenging worden afgehandeld door de **Microsoft Authentication Library (MSAL) voor JavaScript.**

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotheken

In deze handleiding wordt de volgende bibliotheek gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft-verificatiebibliotheek voor JavaScript|

> [!NOTE]
> *Msal.js* richt zich op het eindpunt van het Microsoft-identiteitsplatform, waarmee persoonlijke accounts en school- en werkaccounts kunnen inloggen en tokens kunnen aanschaffen. Het eindpunt van het Microsoft-identiteitsplatform heeft [een aantal beperkingen.](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)
> Zie de [eindpuntvergelijkingshandleiding](../azuread-dev/azure-ad-endpoint-comparison.md)om de verschillen tussen de v1.0- en v2.0-eindpunten te begrijpen.

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Uw webserver of -project instellen

> Liever het project van dit voorbeeld downloaden? [Download de projectbestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> Als u het codevoorbeeld wilt configureren voordat u het uitvoert, gaat u naar de [configuratiestap](#register-your-application).

## <a name="prerequisites"></a>Vereisten

* Als u deze zelfstudie wilt uitvoeren, hebt u een lokale webserver nodig, zoals [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core)of IIS Express-integratie met [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Instructies in deze handleiding zijn gebaseerd op een webserver die is ingebouwd in Node.js. We raden u aan [Visual Studio Code](https://code.visualstudio.com/download) te gebruiken als uw geïntegreerde ontwikkelomgeving (IDE).

## <a name="create-your-project"></a>Uw project maken

Zorg ervoor dat [Node.js](https://nodejs.org/en/download/) is geïnstalleerd en maak vervolgens een map om uw toepassing te hosten. Daar implementeren we een eenvoudige [Express](https://expressjs.com/) webserver `index.html` om uw bestand te serveren.

1. Zoek eerst met de geïntegreerde terminal van Visual Studio Code uw projectmap en installeer Vervolgens Express met NPM.

1. Maak vervolgens een JS-bestand met de naam `server.js`. JS en voeg vervolgens de volgende code toe:

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

U hebt nu een eenvoudige server om uw SPA te bedienen. De beoogde mapstructuur aan het einde van deze zelfstudie ziet er als volgt uit:

![een tekstafbeelding van de beoogde SPA-mapstructuur](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Maak de SPA-gebruikersinterface

1. Maak `index.html` een bestand voor uw JavaScript SPA. Dit bestand implementeert een gebruikersinterface die is gebouwd met **Bootstrap 4 Framework** en importeert scriptbestanden voor configuratie, verificatie en API-aanroep.

   Voeg `index.html` in het bestand de volgende code toe:

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
   > U de versie van MSAL.js in het vorige script vervangen door de nieuwste versie onder [MSAL.js releases](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

2. Maak nu een .js-bestand met de naam `ui.js`, dat DOM-elementen opent en bijwerkt en voegt de volgende code toe:

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

Voordat u verder gaat met verificatie, registreert u uw toepassing in **Azure Active Directory.**

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u het account rechtsboven en stelt u uw portalsessie in op de Azure AD-tenant die u wilt gebruiken.
1. Ga naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
1. Selecteer in de sectie **URI omleiden** het **webplatform** in de vervolgkeuzelijst en stel de waarde in op de URL van de toepassing die is gebaseerd op uw webserver.
1. Selecteer **Registreren**.
1. Noteer op de pagina **Overzicht** van de app de waarde **van de id-toepassing (client)** voor later gebruik.
1. Voor deze quickstart moet de [Impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld. Selecteer **Verificatie**in het linkerdeelvenster van de geregistreerde toepassing .
1. Schakel in **Geavanceerde instellingen**onder **Impliciete toekenning**de **selectievakjes ID-tokens** en **Toegangstokens** in. ID-tokens en toegangstokens zijn vereist omdat deze app gebruikers moet aanmelden en een API moet aanroepen.
1. Selecteer **Opslaan**.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Een omleidings-URL instellen voor Node.js
>
> Voor Node.js u de webserverpoort instellen in het *server.js-bestand.* Deze zelfstudie maakt gebruik van poort 3000, maar u elke andere beschikbare poort gebruiken.
>
> Als u een omleidings-URL in de registratiegegevens van de toepassing wilt instellen, schakelt u terug naar het deelvenster **Toepassingsregistratie** en gaat u op een van de volgende handelingen:
>
> - Stel *`http://localhost:3000/`* in als de **URL omleiden**.
> - Als u een aangepaste TCP-poort *`http://localhost:<port>/`* gebruikt, gebruikt u (waarbij * \<poort>* het aangepaste TCP-poortnummer is).
>   1. Kopieer de **URL-waarde.**
>   1. Ga terug naar het deelvenster **Toepassingsregistratie** en plak de gekopieerde waarde als de **URL omleiden**.
>

### <a name="configure-your-javascript-spa"></a>Uw JavaScript SPA configureren

Maak een nieuw .js-bestand met de naam `authConfig.js`, dat uw configuratieparameters voor verificatie bevat en voeg de volgende code toe:

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
 - Enter_the_Application_Id_Here>is de **applicatie (client) ID** voor de applicatie die u hebt geregistreerd. * \<*
 - Enter_the_Cloud_Instance_Id_Here>is het voorbeeld van de Azure-cloud. * \<* Voer voor de hoofd- of *https://login.microsoftonline.com*globale Azure-cloud gewoon . Voor **nationale** wolken (bijvoorbeeld, China), zie [Nationale wolken](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - Enter_the_Tenant_info_here>is ingesteld op een van de volgende opties: * \<*
   - Als uw toepassing *accounts in deze organisatiemap*ondersteunt, vervangt u deze waarde door de **tenant-id** of **tenantnaam** (bijvoorbeeld *contoso.microsoft.com).*
   - Als uw toepassing *accounts in een organisatiemap*ondersteunt, vervangt u deze waarde door **organisaties**.
   - Als uw toepassing *accounts in een organisatiemap en persoonlijke Microsoft-accounts*ondersteunt, vervangt u deze waarde door **algemene**. Als u de ondersteuning alleen voor *persoonlijke Microsoft-accounts*wilt beperken, vervangt u deze waarde door **consumenten**.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>De Microsoft-verificatiebibliotheek (MSAL) gebruiken om zich bij de gebruiker aan te melden

Maak een nieuw .js-bestand met de naam `authPopup.js`, dat uw verificatie- en tokenacquisitielogica bevat en voeg de volgende code toe:

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

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

Nadat een gebruiker voor de eerste keer de `signIn` knop `loginPopup` **Aanmelden** heeft geselecteerd, roept de methode op om zich bij de gebruiker aan te melden. Met deze methode wordt een pop-upvenster geopend met het eindpunt van het *Microsoft-identiteitsplatform* om de referenties van de gebruiker te vragen en te valideren. Na een succesvolle aanmelding wordt de gebruiker omgeleid naar de oorspronkelijke *index.html-pagina.* Een token wordt ontvangen, verwerkt door `msal.js`, en de informatie in het token wordt in de cache opgeslagen. Dit token staat bekend als het *ID-token* en bevat basisinformatie over de gebruiker, zoals de gebruikersnaam. Als u van plan bent om gegevens die door dit token worden verstrekt voor alle doeleinden te gebruiken, moet u ervoor zorgen dat dit token wordt gevalideerd door uw backendserver om te garanderen dat het token is uitgegeven aan een geldige gebruiker voor uw toepassing.

De SPA die wordt `acquireTokenSilent` gegenereerd `acquireTokenPopup` door deze handleidingaanroepen en/of om een *toegangstoken* te verkrijgen dat wordt gebruikt om de Microsoft Graph-API op te vragen voor gebruikersprofielgegevens. Als u een voorbeeld nodig hebt dat het ID-token valideert, bekijkt u [deze](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2-voorbeeld") voorbeeldtoepassing in GitHub. Het voorbeeld maakt gebruik van een ASP.NET web-API voor tokenvalidatie.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Na de eerste aanmelding wilt u gebruikers niet elke keer dat ze een token nodig hebben om toegang te krijgen tot een bron opnieuw verifiëren. Dus *acquireTokenSilent* moet het grootste deel van de tijd worden gebruikt om tokens te verwerven. Er zijn echter situaties waarin u gebruikers moet dwingen om te communiceren met het eindpunt van het Microsoft-identiteitsplatform. Voorbeelden zijn:

- Gebruikers moeten hun referenties opnieuw invoeren omdat het wachtwoord is verlopen.
- Uw toepassing vraagt toegang tot een bron en u hebt de toestemming van de gebruiker nodig.
- Tweestapsverificatie is vereist.

Aanroepen *van acquireTokenPopup* opent een pop-upvenster (of *acquireTokenRedirect* leidt gebruikers door naar het eindpunt van het Microsoft-identiteitsplatform). In dat venster moeten gebruikers communiceren door hun referenties te bevestigen, toestemming te geven voor de vereiste bron of de tweestapsverificatie te voltooien.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `acquireTokenSilent` methode verwerkt tokenacquisitie en -vernieuwing zonder enige interactie van de gebruiker. Nadat `loginPopup` (of) `loginRedirect`wordt uitgevoerd voor `acquireTokenSilent` de eerste keer, is de methode vaak gebruikt om tokens gebruikt om toegang te krijgen tot beschermde bronnen voor latere oproepen te verkrijgen. (Oproepen om tokens aan te vragen of te vernieuwen worden in stilte gedaan.) `acquireTokenSilent` kan in sommige gevallen mislukken. Het wachtwoord van de gebruiker is bijvoorbeeld mogelijk verlopen. Uw toepassing kan deze uitzondering op twee manieren verwerken:

1. Direct een `acquireTokenPopup` oproep doen, waardoor een aanmeldingsprompt van de gebruiker wordt geactiveerd. Dit patroon wordt vaak gebruikt in online toepassingen waar er geen niet-geverifieerde inhoud in de toepassing beschikbaar is voor de gebruiker. Het voorbeeld dat door deze begeleide instelling wordt gegenereerd, maakt gebruik van dit patroon.

1. Toepassingen kunnen ook een visuele indicatie maken aan de gebruiker dat een interactieve aanmelding vereist is, zodat de `acquireTokenSilent` gebruiker het juiste moment kan selecteren om in te loggen of de toepassing op een later tijdstip opnieuw kan proberen. Dit wordt vaak gebruikt wanneer de gebruiker andere functionaliteit van de toepassing kan gebruiken zonder te worden verstoord. Er kan bijvoorbeeld niet-geverifieerde inhoud beschikbaar zijn in de toepassing. In deze situatie kan de gebruiker beslissen wanneer hij zich wil aanmelden om toegang te krijgen tot de beveiligde bron of om de verouderde informatie te vernieuwen.

> [!NOTE]
> Deze quickstart `loginPopup` maakt `acquireTokenPopup` standaard gebruik van de methoden en de methoden. Als u Internet Explorer als browser gebruikt, wordt `loginRedirect` `acquireTokenRedirect` het aanbevolen om te gebruiken en methoden te gebruiken, vanwege een [bekend probleem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) met betrekking tot de manier waarop Internet Explorer met pop-upvensters omgaat. Als u wilt zien hoe u hetzelfde resultaat te bereiken met behulp van, `Redirect methods` [zie](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Bel de Microsoft Graph API met behulp van het token dat u zojuist hebt aangeschaft

1. Maak eerst een .js-bestand met de naam `graphConfig.js`, waarmee je REST-eindpunten opslaat. Voeg de volgende code toe:

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   Waar:
   - Enter_the_Graph_Endpoint_Here>is het voorbeeld van MS Graph API. * \<* Voor het globale MS Graph API-eindpunt `https://graph.microsoft.com`vervangt u deze tekenreeks eenvoudig door . Voor nationale cloudimplementaties verwijzen wij u naar [Graph API Documentation](https://docs.microsoft.com/graph/deployments).

1. Maak vervolgens een .js-bestand met de naam `graph.js`, waarmee een REST-aanroep wordt gemaakt naar de Microsoft Graph API en de volgende code wordt toegevoegd:

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

<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep tegen een beveiligde API

In de voorbeeldtoepassing die door `callMSGraph()` deze handleiding is `GET` gemaakt, wordt de methode gebruikt om een HTTP-aanvraag te maken tegen een beveiligde bron waarvoor een token vereist is. De aanvraag retourneert de inhoud vervolgens naar de beller. Met deze methode wordt het verkregen token toegevoegd aan de *koptekst HTTP Autorisatie*. Voor de voorbeeldtoepassing die door deze handleiding is gemaakt, is de bron het eindpunt van microsoft graph-api *me,* waarmee de profielgegevens van de gebruiker worden weergegeven.

<!--end-collapse-->

## <a name="test-your-code"></a>Uw code testen

1. Configureer de server om te luisteren naar een TCP-poort die is gebaseerd op de locatie van uw *index.html-bestand.* Voor Node.js start u de webserver om naar de poort te luisteren door de volgende opdrachten uit te voeren op een opdrachtregelprompt vanuit de toepassingsmap:

   ```bash
   npm install
   npm start
   ```
1. Voer in uw **http://localhost:3000** **http://localhost:{port}** browser de *poort* in of waar naar uw webserver luistert. U ziet de inhoud van uw *index.html-bestand* en de knop **Aanmelden.**

## <a name="test-your-application"></a>Uw toepassing testen

Nadat de browser uw *index.html-bestand* heeft geladen, selecteert u **Aanmelden**. U wordt gevraagd zich aan te melden bij het eindpunt van het Microsoft-identiteitsplatform:

![Het aanmeldingsvenster voor JavaScript SPA-account](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Toestemming geven voor toegang tot toepassingen

De eerste keer dat u zich aanmeldt bij uw aanvraag, wordt u gevraagd deze toegang te verlenen tot uw profiel en u aan te melden:

![Het venster 'Gevraagde machtigingen'](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Toepassingsresultaten weergeven

Nadat u zich hebt aangemeld, worden uw gebruikersprofielgegevens geretourneerd in de Microsoft Graph API-reactie die wordt weergegeven:

![Resultaten van de Microsoft Graph API-aanroep](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over scopes en gedelegeerde machtigingen

De Microsoft Graph API vereist dat de *user.read-scope* het profiel van een gebruiker leest. Standaard wordt dit bereik automatisch toegevoegd in elke toepassing die is geregistreerd op het registratieportaal. Andere API's voor Microsoft Graph en aangepaste API's voor uw back-endserver vereisen mogelijk extra scopes. De Microsoft Graph API vereist bijvoorbeeld het *e-mailbereik.Leesbereik* om de e-mails van de gebruiker weer te geven.

> [!NOTE]
> De gebruiker kan worden gevraagd om extra toestemmingen als u het aantal scopes te verhogen.

Als een back-end-API geen scope vereist (niet aanbevolen), u *clientId* gebruiken als het bereik in de aanroepen om tokens te verkrijgen.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
