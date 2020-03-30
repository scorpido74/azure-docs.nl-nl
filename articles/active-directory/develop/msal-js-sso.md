---
title: Eenmalige aanmelding (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van enkele aanmeldingservaringen met de Microsoft-verificatiebibliotheek voor JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262852"
---
# <a name="single-sign-on-with-msaljs"></a>Eenmalige aanmelding met MSAL.js

Met Single Sign-On (SSO) kunnen gebruikers hun referenties eenmaal invoeren om zich aan te melden en een sessie in te stellen die kan worden hergebruikt voor meerdere toepassingen zonder dat ze zich opnieuw hoeven te verifiëren. Dit biedt een naadloze ervaring voor de gebruiker en vermindert de herhaalde vragen om referenties.

Azure AD biedt SSO-mogelijkheden voor toepassingen door een sessiecookie in te stellen wanneer de gebruiker de eerste keer verifieert. De MSAL.js bibliotheek stelt applicaties in staat om dit op een aantal manieren te benutten.

## <a name="sso-between-browser-tabs"></a>SSO tussen browsertabbladen

Wanneer uw toepassing in meerdere tabbladen is geopend en u zich eerst aanmeldt bij de gebruiker op één tabblad, wordt de gebruiker ook op de andere tabbladen aangemeld zonder te worden gevraagd. MSAL.js caches het ID-token voor `localStorage` de gebruiker in de browser en zal de gebruiker aanmelden bij de toepassing op de andere geopende tabbladen.

MsAL.js gebruikt `sessionStorage` standaard, waardoor de sessie niet kan worden gedeeld tussen tabbladen. Als u SSO tussen tabbladen wilt `cacheLocation` krijgen, moet `localStorage` u de in MSAL.js instellen op zoals hieronder wordt weergegeven.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO tussen apps

Wanneer een gebruiker zich verifieert, wordt een sessiecookie ingesteld op het Azure AD-domein in de browser. MSAL.js vertrouwt op deze sessiecookie om SSO voor de gebruiker tussen verschillende applicaties te bieden. MSAL.js caches ook de ID-tokens en toegangstokens van de gebruiker in de browseropslag per toepassingsdomein. Als gevolg hiervan varieert het SSO-gedrag voor verschillende gevallen:  

### <a name="applications-on-the-same-domain"></a>Toepassingen op hetzelfde domein

Wanneer toepassingen op hetzelfde domein worden gehost, kan de gebruiker zich één keer aanmelden bij een app en vervolgens zonder een prompt worden geverifieerd bij de andere apps. MSAL.js maakt gebruik van de tokens die in de cache zijn opgeslagen voor de gebruiker op het domein om SSO te leveren.

### <a name="applications-on-different-domain"></a>Toepassingen op verschillende domeinen

Wanneer toepassingen op verschillende domeinen worden gehost, kunnen de tokens die op domein A zijn opgeslagen, niet worden geopend door MSAL.js in domein B.

Dit betekent dat wanneer gebruikers zich hebben aangemeld bij domein A naar een toepassing op domein B navigeren, ze worden omgeleid of gevraagd met de Azure AD-pagina. Aangezien Azure AD nog steeds de gebruikerssessiecookie heeft, wordt de gebruiker aangemeld en hoeven deze de referenties niet opnieuw in te voeren. Als de gebruiker meerdere gebruikersaccounts heeft in sessie met Azure AD, wordt de gebruiker gevraagd het relevante account te kiezen waarmee u zich moet aanmelden.

### <a name="automatically-select-account-on-azure-ad"></a>Automatisch account selecteren op Azure AD

In bepaalde gevallen heeft de toepassing toegang tot de verificatiecontext van de gebruiker en wil de prompt voor de selectie van Azure AD-accounts voorkomen wanneer meerdere accounts zijn aangemeld.  Dit kan op een aantal verschillende manieren:

**Sessie-ID (SID) gebruiken**

Sessie-ID is een [optionele claim](active-directory-optional-claims.md) die kan worden geconfigureerd in de ID-tokens. Met deze claim kan de toepassing de Azure AD-sessie van de gebruiker identificeren, onafhankelijk van de accountnaam of gebruikersnaam van de gebruiker. U de SID in de `acquireTokenSilent` aanvraagparameters doorgeven aan het gesprek. Hierdoor kan Azure AD de accountselectie omzeilen. SID is gebonden aan de sessiecookie en zal de browsercontexten niet overschrijden.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID kan alleen worden gebruikt met `acquireTokenSilent` stille authenticatie verzoeken die door call in MSAL.js.
U vindt de stappen om optionele claims te configureren in uw toepassingsmanifest [hier.](active-directory-optional-claims.md)

**Inloghint gebruiken**

Als u de SID-claim niet hebt geconfigureerd of de vraag naar accountselectie in `login_hint` interactieve verificatiegesprekken moet omzeilen, u dit doen door een in de aanvraagparameters en optioneel een `domain_hint` `extraQueryParameters` as`loginPopup`in de interactieve MSAL.JS-methoden ( , `loginRedirect` `acquireTokenPopup` en `acquireTokenRedirect`) op te geven. Bijvoorbeeld:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

U de waarden voor login_hint en domain_hint krijgen door de claims te lezen die zijn geretourneerd in het ID-token voor de gebruiker.

* **loginHint** moet worden `preferred_username` ingesteld op de claim in het ID-token.

* **domain_hint** hoeft alleen te worden doorgegeven bij het gebruik van de /common authority. De domeinhint wordt bepaald door tenant-ID(tid).  Als `tid` de claim in `9188040d-6c67-4c5b-b112-36a304b66dad` het ID-token is, is het consumenten. Anders zijn het organisaties.

Lees [hier](v2-oauth2-implicit-grant-flow.md) voor meer informatie over de waarden voor login hint en domein hint.

> [!Note]
> Je SID en login_hint niet tegelijkertijd passeren. Dit resulteert in een foutreactie.

## <a name="sso-without-msaljs-login"></a>SSO zonder MSAL.js login

Door het ontwerp vereist MSAL.js dat een inlogmethode wordt aangeroepen om een gebruikerscontext vast te stellen voordat tokens voor API's worden verkregen. Aangezien inlogmethoden interactief zijn, ziet de gebruiker een prompt.

Er zijn bepaalde gevallen waarin toepassingen toegang hebben tot de context of ID-token van de geverifieerde gebruiker via verificatie die in een andere toepassing is gestart en SSO willen gebruiken om tokens te verwerven zonder eerst in te melden via MSAL.js.

Een voorbeeld hiervan is: een gebruiker is aangemeld bij een bovenliggende webtoepassing die een andere JavaScript-toepassing host die wordt uitgevoerd als een invoegtoepassing of plug-in.

De SSO-ervaring in dit scenario kan als volgt worden bereikt:

Geef `sid` de indien `login_hint` beschikbaar (of `domain_hint`eventueel) als aanvraagparameters `acquireTokenSilent` als volgt door aan de MSAL.js-aanroep:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO in ADAL.js naar MSAL.js-update

MSAL.js brengt functiepariteit met ADAL.js voor Azure AD-verificatiescenario's. Om de migratie van ADAL.js naar MSAL.js eenvoudig te maken en te voorkomen dat uw gebruikers opnieuw worden aangemeld, leest de bibliotheek het ID-token dat de gebruikerssessie vertegenwoordigt in de ADAL.js-cache en meldt u zich naadloos aan bij de gebruiker in MSAL.js.  

Om te profiteren van het single sign-on (SSO) gedrag bij het bijwerken van ADAL.js, moet u ervoor zorgen dat de bibliotheken worden gebruikt `localStorage` voor caching tokens. Stel `cacheLocation` de `localStorage` configuratie in op zowel de MSAL.js als de ADAL.js-configuratie bij initialisatie als volgt:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Zodra dit is geconfigureerd, kan MSAL.js de cachestatus van de geverifieerde gebruiker in ADAL.js lezen en deze gebruiken om SSO in MSAL.js te leveren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [enkele aanmeldingssessie- en tokenlevensduurwaarden](active-directory-configurable-token-lifetimes.md) in Azure AD.
