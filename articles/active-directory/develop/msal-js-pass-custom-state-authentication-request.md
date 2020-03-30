---
title: Aangepaste status doorgeven in verificatieaanvragen (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het doorgeven van een aangepaste parameterwaarde voor statusinverificatie in verificatieaanvraag met behulp van de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084937"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Aangepaste status doorgeven in verificatieaanvragen met MSAL.js

De *parameter status,* zoals gedefinieerd door OAuth 2.0, is opgenomen in een verificatieaanvraag en wordt ook geretourneerd in de tokenrespons om vervalsingsaanvallen op meerdere plaatsen te voorkomen. Microsoft Authentication Library for JavaScript (MSAL.js) passeert standaard een willekeurig gegenereerde unieke *parameterwaarde* in de verificatieaanvragen.

De parameter status kan ook worden gebruikt om informatie over de status van de app te coderen voordat u deze omleidt. U de status van de gebruiker in de app, zoals de pagina of weergave waarop ze zich bevonden, doorgeven als invoer voor deze parameter. Met de MSAL.js-bibliotheek u uw aangepaste `Request` status doorgeven als parameter voor de status in het object:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Als u een token in de cache wilt overslaan en naar `forceRefresh` de server wilt gaan, gaat u de booleaan doorgeven aan het object AuthenticationParameters dat wordt gebruikt om een aanmeldings-/tokenaanvraag in te dienen.
> `forceRefresh`mag niet standaard worden gebruikt, vanwege de impact op de prestaties van uw toepassing.
> Vertrouwen op de cache geeft uw gebruikers een betere ervaring.
> Het overslaan van de cache mag alleen worden gebruikt in scenario's waarvan u weet dat de momenteel in de cache opgeslagen gegevens geen up-to-date informatie hebben.
> Zoals een beheertool die rollen toevoegt aan een gebruiker die een nieuw token met bijgewerkte rollen moet krijgen.

Bijvoorbeeld:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

De doorgegeven status wordt toegevoegd aan de unieke GUID-set die MSAL.js bij het verzenden van de aanvraag heeft ingesteld. Wanneer het antwoord wordt geretourneerd, controleert MSAL.js op een statusovereenkomst `Response` en `accountState`retourneert vervolgens de aangepaste doorgegeven status in het object als .

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Lees voor meer informatie over [het bouwen van een applicatie (SPA)](scenario-spa-overview.md) met MSAL.js.