---
title: Aangepaste status in verificatie aanvragen (MSAL.js) door geven | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het door geven van een aangepaste status parameter waarde in verificatie aanvraag met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 840c371e63aacf8ef410cbf84cc9f68137dd77df
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85477580"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Aangepaste status in verificatie aanvragen door geven met behulp van MSAL.js

De para meter *State* , zoals gedefinieerd door OAuth 2,0, wordt opgenomen in een verificatie aanvraag en wordt ook geretourneerd in het token antwoord om vervalsing van aanvragen op meerdere sites te voor komen. Micro soft Authentication Library voor Java script (MSAL.js) geeft standaard een wille keurige, door de para meter gegenereerde unieke *status* waarde in de verificatie aanvragen door.

De para meter State kan ook worden gebruikt om informatie over de status van de app te coderen voordat deze wordt omgeleid. U kunt de status van de gebruiker in de app door geven, zoals de pagina of weer gave waarin deze zich bevonden, als invoer voor deze para meter. Met de MSAL.js-bibliotheek kunt u uw aangepaste status door geven als para meter State in het `Request` object:

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
> Als u een token in de cache wilt overs Laan en naar de server wilt gaan, geeft u de Booleaanse waarde door aan `forceRefresh` het AuthenticationParameters-object dat wordt gebruikt om een aanmeldings-of Token aanvraag te maken.
> `forceRefresh`mag niet standaard worden gebruikt vanwege de invloed op de prestaties van uw toepassing.
> Afhankelijk van de cache krijgt uw gebruikers een betere ervaring.
> Het overs laan van de cache mag alleen worden gebruikt in scenario's waarin u weet dat de gegevens die momenteel in de cache zijn opgeslagen, geen actuele informatie bevatten.
> Zoals een beheer hulpprogramma waarmee rollen worden toegevoegd aan een gebruiker die een nieuw token moet verkrijgen met bijgewerkte rollen.

Bijvoorbeeld:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

De door gegeven status wordt toegevoegd aan de unieke GUID die is ingesteld door MSAL.js bij het verzenden van de aanvraag. Wanneer het antwoord wordt geretourneerd, wordt door MSAL.js gecontroleerd of de status overeenkomt en wordt de aangepaste status in het `Response` object geretourneerd als `accountState` .

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

Lees voor meer informatie over [het bouwen van een toepassing met één pagina](scenario-spa-overview.md) met behulp van MSAL.js.