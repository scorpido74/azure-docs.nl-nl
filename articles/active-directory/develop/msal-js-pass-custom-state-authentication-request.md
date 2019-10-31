---
title: Aangepaste status door geven in verificatie aanvragen (micro soft-verificatie bibliotheek voor Java script)
titleSuffix: Microsoft identity platform
description: Meer informatie over het door geven van een aangepaste status parameter waarde in verificatie aanvraag met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527c1937da8634f7448c82ca8c0331fb5cfb85e3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150617"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Aangepaste status door geven in verificatie aanvragen met behulp van MSAL. js
De para meter *State* , zoals gedefinieerd door OAuth 2,0, wordt opgenomen in een verificatie aanvraag en wordt ook geretourneerd in het token antwoord om vervalsing van aanvragen op meerdere sites te voor komen. Micro soft-verificatie bibliotheek voor Java script (MSAL. js) geeft standaard een wille keurige, gegenereerde unieke *status* parameter waarde in de verificatie aanvragen door.

De para meter State kan ook worden gebruikt om informatie over de status van de app te coderen voordat deze wordt omgeleid. U kunt de status van de gebruiker in de app door geven, zoals de pagina of weer gave waarin deze zich bevonden, als invoer voor deze para meter. Met de MSAL. JS-bibliotheek kunt u uw aangepaste status door geven als para meter State in het `Request`-object:

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
};
```

Bijvoorbeeld:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

De door gegeven status wordt toegevoegd aan de unieke GUID die is ingesteld door MSAL. js wanneer de aanvraag wordt verzonden. Wanneer het antwoord wordt geretourneerd, controleert MSAL. js of de status overeenkomt en retourneert de aangepaste status door gegeven in het `Response`-object als `accountState`.

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

Lees voor meer informatie over [het bouwen van een single-page-toepassing (Spa)](scenario-spa-overview.md) met behulp van MSAL. js.