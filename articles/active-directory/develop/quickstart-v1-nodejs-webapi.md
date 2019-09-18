---
title: 'Quickstart: Een Web-API beveiligen met Azure Active Directory'
description: Meer informatie over het bouwen van een Node.js REST-web-API die is geïntegreerd met Azure Active Directory voor verificatie.
services: active-directory
documentationcenter: nodejs
author: rwike77
manager: CelesteDG
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.custom: seo-javascript-september2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7307afb2ce9e10326c42f96733d3f80979c4a4b3
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056543"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Quickstart: Een Web-API beveiligen met Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

In deze snelstartgids leert u hoe u een [Restify](http://restify.com/)-API-eindpunt met [Passport](http://passportjs.org/) kunt beveiligen met behulp van de [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad)-module, zodat communicatie met Azure Active Directory (Azure AD) kan worden afgehandeld.

Deze snelstartgids gaat in op de problemen met betrekking tot het beveiligen van API-eindpunten. De zorgen over aanmelden en het behouden van verificatietokens zijn hier niet geïmplementeerd en vallen onder verantwoordelijkheid van een clienttoepassing. Raadpleeg [Aan- en afmelden bij de Node.js-web-app met Azure Active Directory](quickstart-v1-openid-connect-code.md) voor meer informatie over een clientimplementatie.

Het volledige codevoorbeeld dat bij dit artikel hoort, is beschikbaar op [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Vereisten

Zorg dat u aan deze vereisten voldoet voordat u begint.

### <a name="create-the-sample-project"></a>Het voorbeeldproject maken

De servertoepassing vereist een paar pakketafhankelijkheden om Restify en Passport te ondersteunen en accountinformatie die aan Azure Active Directory wordt doorgegeven.

Voeg om te beginnen de volgende code toe aan een bestand met de naam `package.json`:

```Shell
{
  "name": "active-directory-webapi-nodejs",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "4.0.0",
    "restify": "7.7.0"
  }
}
```

Voer na het maken van `package.json` `npm install` uit in uw opdrachtprompt om de pakketafhankelijkheden te installeren.

#### <a name="configure-the-project-to-use-active-directory"></a>Het project configureren met Active Directory

Er zijn een paar specifieke waarden voor het account om te beginnen met het configureren van de toepassing; u kunt deze waarden verkrijgen in de Azure CLI. De eenvoudigste manier om met de CLI aan de slag te gaan, is door de Azure Cloud Shell te gebruiken.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Voer de volgende opdracht in de cloud shell in:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

De [argumenten](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) voor de `create`-opdracht bevatten:

| Argument  | Description |
|---------|---------|
|`display-name` | Beschrijvende naam van de registratie |
|`homepage` | URL waar gebruikers zich kunnen aanmelden en uw toepassing kunnen gebruiken |
|`identifier-uris` | Unieke URI’s, gescheiden door een spatie, die Azure Active Directory voor deze toepassing kan gebruiken |

U hebt de volgende informatie nodig voordat u verbinding met Azure Active Directory kunt maken:

| Name  | Description | Naam van variabele in configuratiebestand |
| ------------- | ------------- | ------------- |
| Tenantnaam  | [Tenantnaam](quickstart-create-new-tenant.md) die u wilt gebruiken voor verificatie | `tenantName`  |
| Client-id  | Client-id is de OAuth-term die wordt gebruikt voor de _toepassing-ID_ in AAD. |  `clientID`  |

Kopieer de `appId`-waarde uit de registratierespons in de Azure Cloud Shell en maak een nieuw bestand met de naam `config.js`. Voeg vervolgens de volgende code toe en vervang uw waarden door de tokens tussen haakjes:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```

Raadpleeg de documentatie voor de [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage)-module voor meer informatie over de afzonderlijke configuratie-instellingen.

### <a name="implement-the-server"></a>De server implementeren

De module [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) heeft twee verificatiestrategieën: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) en [Bearer](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). De server die in dit artikel wordt geïmplementeerd, gebruikt de Bearer-strategie om het API-eindpunt te beveiligen.

### <a name="step-1-import-dependencies"></a>Stap 1: Afhankelijkheden importeren

Maak een nieuw bestand met de naam `app.js` en plak de volgende tekst:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require ('restify').plugins
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

In deze sectie van de code:

- Wordt naar de modules `restify` en invoegtoepassingen verwezen om een Restify-server in te stellen.
- Zijn de modules `passport` en `passport-azure-ad` verantwoordelijk voor communicatie met Azure Active Directory.
- Wordt de variabele `config` geïnitialiseerd met waarden uit het bestand `config.js` dat in de vorige stap werd gemaakt.
- Wordt er een matrix voor `authenticatedUserTokens` gemaakt om gebruikerstokens op te slaan wanneer deze aan beveiligde eindpunten worden doorgegeven.
- De `serverPort` is gedefinieerd op basis van de poort van de procesomgeving of op basis van het configuratiebestand.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Stap 2: Een verificatiestrategie instantiëren

Omdat u een eindpunt beveiligt, moet u een strategie opgeven die verantwoordelijk is voor het vaststellen of de huidige aanvraag wel of niet afkomstig is van een geverifieerde gebruiker. De variabele `authenticatonStrategy` is hier een instantie van de klasse `passport-azure-ad` `BearerStrategy`. Voeg de volgende code toe achter de `require`-instructies.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```

Deze implementatie maakt gebruik van automatische registratie door verificatietokens aan de `authenticatedUserTokens`-matrix toe te voegen als deze niet al bestaan.

Nadat een nieuwe instantie van de strategie is gemaakt, moet u deze doorgeven in Passport via de `use`-methode. Voeg de volgende code toe aan `app.js` om de strategie in Passport te gebruiken.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Stap 3: Serverconfiguratie

Nu de verificatiestrategie is gedefinieerd kunt u de Restify-server instellen met een aantal basisinstellingen en instellen dat Passport moet worden gebruikt voor de beveiliging.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Deze server wordt geïnitialiseerd en geconfigureerd om autorisatie-headers te parseren en vervolgens ingesteld om Passport te gebruiken.

### <a name="step-4-define-routes"></a>Stap 4: Routes definiëren

U kunt nu routes definiëren en bepalen welke met Azure Active Directory moet worden beveiligd. Dit project bevat twee routes waarvan het hoogste niveau open is en de `/api`-route is ingesteld om verificatie te vereisen.

Voeg in `app.js` de volgende code toe voor de route van de hoofdmap:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

De route van de hoofdmap staat alle verzoeken via de route toe en retourneert een bericht dat een opdracht bevat om de `/api`-route te testen. De `/api`-route is daarentegen vergrendeld met behulp van [`passport.authenticate`](http://passportjs.org/docs/authenticate). Voeg de volgende code toe achter de route van de hoofdmap.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Deze configuratie staat alleen geverifieerde aanvragen toe die toegang met behulp van een bearer-token tot `/api` bevatten. De optie `session: false` wordt gebruikt om sessies uit te schakelen en te vereisen dat bij elke aanvraag aan de API een token wordt doorgegeven.

Ten slotte wordt de server zo ingesteld dat deze de `listen`-methode aanroept om naar de geconfigureerde poort te luisteren.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Nu de server is geïmplementeerd kunt u de server starten door een opdrachtprompt te openen en de volgende regel in te voeren:

```shell
npm start
```

Als de server wordt uitgevoerd, kunt u een aanvraag naar de server verzenden om de resultaten te testen. Open een bash-shell en voer de volgende code in om de respons van de route van de hoofdmap te demonstreren:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

De respons ziet er als volgt uit als u uw server goed hebt geconfigureerd:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

U kunt vervolgens de route die verificatie vereist testen door de volgende opdracht in uw bash-shell in te voeren:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Als u de server goed hebt geconfigureerd, moet de server reageren met de status `Unauthorized`.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Nu u een veilige API hebt gemaakt, kunt u een client implementeren die verificatietokens aan de API kan doorgeven.

## <a name="next-steps"></a>Volgende stappen

* U moet een equivalent van de client implementeren om verbinding te maken met de server die verantwoordelijk is voor het aanmelden, afmelden en beheren van tokens. Raadpleeg de clienttoepassingen in [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) en [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android) voor voorbeelden op basis van code.
* Raadpleeg [Aan- en afmelden bij de Node.js-web-app met Azure Active Directory](quickstart-v1-openid-connect-code.md) voor een stapsgewijze zelfstudie.
