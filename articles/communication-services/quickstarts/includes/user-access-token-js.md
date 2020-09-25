---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944591"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

### <a name="install-the-package"></a>Het pakket installeren

Gebruik de opdracht `npm install` voor het installeren van de clientbibliotheek voor Azure Communication Services-beheer voor JavaScript.

```console

npm install @azure/communication-administration --save

```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in het **package.json**-bestand.

## <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open een nieuw tekstbestand in uw code-editor
1. Voeg een `require` oproep toe om de `CommunicationIdentityClient` te laden
1. Maak de structuur voor het programma, waaronder eenvoudige afhandeling van uitzonderingen

Gebruik de volgende code om te beginnen:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Sla het nieuwe bestand op als **issue-token.js** in de map *user-tokens-quickstart*.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `CommunicationIdentityClient` tot stand met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `main` methode toe:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Een gebruiker maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `createUser` om een nieuwe vermelding in de map te maken met een unieke `Id`. U moet een toewijzing onderhouden tussen de gebruikers van uw toepassing en de door de Communication Services gegenereerde identiteiten (bijv. door ze op te slaan in de database van uw toepassingsserver).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Tokens voor gebruikerstoegang uitgeven

Gebruik de methode `issueToken` om een toegangstoken voor een Communication Services-gebruiker uit te geven. Als u de optionele `user` parameter niet opgeeft, wordt er een nieuwe gebruiker gemaakt en geretourneerd met het token.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Tokens voor gebruikerstoegang zijn kortdurige referenties die opnieuw moeten worden uitgegeven om te voor komen dat uw gebruikers problemen ondervinden met de service. De antwoordeigenschap `expiresOn` geeft de levensduur van het token aan.

## <a name="revoke-user-access-tokens"></a>Tokens voor gebruikerstoegang intrekken

In sommige gevallen moet u de tokens voor gebruikerstoegang wellicht intrekken, bijvoorbeeld wanneer een gebruiker het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. U kunt de methode `revokeTokens` gebruiken om alle toegangstokens van een gebruiker ongeldig te maken.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>Een gebruiker verwijderen

Als u een gebruiker verwijdert, worden alle actieve tokens ingetrokken en wordt voorkomen dat nieuwe tokens voor de identiteiten worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de gebruiker is gekoppeld.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer vanuit een consoleprompt naar de map die het bestand *issue-token.js* bevat, en voer vervolgens de volgende `node`-opdracht uit om de app uit te voeren.

```console
node ./issue-token.js
```
