---
title: Toegang tot gebruikers gegevens in statische Web Apps van Azure
description: Meer informatie over het lezen van autorisatie provider-geretourneerde gebruikers gegevens.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83597749"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Toegang tot gebruikers gegevens in azure static Web Apps Preview

Azure static Web Apps biedt gebruikers informatie met betrekking tot authenticatie via een [eind punt voor directe toegang](#direct-access-endpoint) en met [API-functies](#api-functions).

Veel gebruikers interfaces zijn sterk afhankelijk van gebruikers verificatie gegevens. Het eind punt voor directe toegang is een API voor het hulp programma waarmee gebruikers gegevens worden weer gegeven zonder dat er een aangepaste functie hoeft te worden geïmplementeerd. Het eind punt voor directe toegang valt buiten het gemak niet onder koude start vertragingen die zijn gekoppeld aan serverloze architectuur.

## <a name="client-principal-data"></a>Client-Principal-gegevens

Met client Principal Data-object worden door de gebruiker geïdentificeerde informatie beschikbaar gesteld aan uw app. De volgende eigenschappen worden aanbevolen in het Principal-object van de client:

| Eigenschap  | Beschrijving |
|-----------|---------|
| `identityProvider` | De naam van de [ID-provider](authentication-authorization.md). |
| `userId` | Een statische Web Apps-specifieke Azure-id voor de gebruiker. <ul><li>De waarde is uniek per app. Bijvoorbeeld, dezelfde gebruiker retourneert een andere `userId` waarde op een andere statische web apps resource.<li>De waarde blijft behouden voor de levens duur van een gebruiker. Als u dezelfde gebruiker weer verwijdert en aan de app toevoegt, wordt er een nieuwe `userId` gegenereerd.</ul>|
| `userDetails` | De gebruikers naam of het e-mail adres van de gebruiker. Sommige providers retour neren het [e-mail adres van de gebruiker](authentication-authorization.md), terwijl anderen de [gebruikers ingang](authentication-authorization.md)verzenden. |
| `userRoles`     | Een matrix met de [toegewezen rollen](authentication-authorization.md)van de gebruiker. |

Het volgende voor beeld is een principal-voorbeeld object van de client:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Eind punt voor directe toegang

U kunt een `GET` aanvraag verzenden naar de `/.auth/me` route en direct toegang tot de client-Principal-gegevens ontvangen. Wanneer de status van uw weer gave afhankelijk is van de autorisatie gegevens, gebruikt u deze methode voor de beste prestaties.

Voor aangemelde gebruikers bevat het antwoord een JSON-object van de client. Aanvragen van niet-geverifieerde gebruikers retour neren `null` .

Met de API voor [ophalen](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> krijgt u toegang tot de client-principalgegevens met de volgende syntaxis.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API-functies

Client-principalgegevens worden door gegeven aan API-functies in de `x-ms-client-principal` aanvraag header. De client-principalgegevens worden verzonden als een [Base64](https://www.wikipedia.org/wiki/Base64)-gecodeerde teken reeks met een geserialiseerd JSON-object.

In de volgende voorbeeld functie ziet u hoe u gebruikers gegevens kunt lezen en retour neren.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Ervan uitgaande dat de bovenstaande functie een naam heeft `user` , kunt u de browser-API [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> gebruiken om toegang te krijgen tot de reactie van de API met behulp van de volgende syntaxis.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> de [ophaal](https://caniuse.com/#feat=fetch) -API en de [wachtende](https://caniuse.com/#feat=mdn-javascript_operators_await) operator worden niet ondersteund in Internet Explorer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-instellingen configureren](application-settings.md)
