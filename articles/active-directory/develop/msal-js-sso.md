---
title: Eenmalige aanmelding (micro soft-verificatie bibliotheek voor Java script)
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van eenmalige aanmelding met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: da39b8435acdd11108a945c6bac5147dc8b6ad50
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150573"
---
# <a name="single-sign-on-with-msaljs"></a>Eenmalige aanmelding met MSAL.js

Met eenmalige aanmelding (SSO) kunnen gebruikers hun referenties eenmaal invoeren om zich aan te melden en een sessie tot stand te brengen die opnieuw kan worden gebruikt in meerdere toepassingen zonder dat ze opnieuw moeten worden geverifieerd. Dit biedt een naadloze ervaring voor de gebruiker en vermindert de herhaalde prompts voor referenties.

Azure AD biedt SSO-mogelijkheden voor toepassingen door een sessie cookie in te stellen wanneer de gebruiker de eerste keer verifieert. Met de MSAL. JS-bibliotheek kunnen toepassingen op een paar manieren gebruikmaken van deze.

## <a name="sso-between-browser-tabs"></a>SSO tussen browser tabbladen

Wanneer uw toepassing is geopend op meerdere tabbladen en u zich voor het eerst aanmeldt op een tabblad, wordt de gebruiker ook aangemeld op de andere tabbladen zonder dat daarom wordt gevraagd. MSAL. js slaat de ID-token voor de gebruiker op in de browser `localStorage` en wordt de gebruiker bij de toepassing ondertekend op de andere geopende tabbladen.

MSAL. js maakt standaard gebruik van `sessionStorage` waardoor de sessie niet tussen tabbladen kan worden gedeeld. Als u SSO tussen tabbladen wilt ophalen, moet u de `cacheLocation` in MSAL. js instellen op `localStorage` zoals hieronder wordt weer gegeven.

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

Wanneer een gebruiker zich verifieert, wordt een sessie cookie ingesteld op het Azure AD-domein in de browser. MSAL. js is afhankelijk van deze sessie cookie om eenmalige aanmelding te bieden voor de gebruiker tussen verschillende toepassingen. MSAL. js slaat ook de ID-tokens en toegangs tokens van de gebruiker op in de browser opslag per toepassings domein. Als gevolg hiervan varieert het SSO-gedrag voor verschillende gevallen:  

### <a name="applications-on-the-same-domain"></a>Toepassingen in hetzelfde domein

Wanneer toepassingen worden gehost op hetzelfde domein, kan de gebruiker zich eenmaal aanmelden bij een app en vervolgens worden geverifieerd naar de andere apps zonder dat hiervoor een prompt wordt gevraagd. MSAL. js maakt gebruik van de tokens die in de cache worden opgeslagen voor de gebruiker op het domein om eenmalige aanmelding te bieden.

### <a name="applications-on-different-domain"></a>Toepassingen op een ander domein

Wanneer toepassingen worden gehost in verschillende domeinen, is de tokens in het cache geheugen van domein A niet toegankelijk via MSAL. js in domein B.

Dit betekent dat wanneer gebruikers zich hebben aangemeld op domein A naar een toepassing in het domein B navigeert, ze worden omgeleid of wordt gevraagd met de pagina Azure AD. Omdat Azure AD de cookie van de gebruikers sessie nog steeds heeft, wordt de gebruiker aangemeld en hoeven de referenties niet opnieuw te worden opgegeven. Als de gebruiker meerdere gebruikers accounts heeft in een sessie met Azure AD, wordt de gebruiker gevraagd het betreffende account te kiezen om zich aan te melden.

### <a name="automatically-select-account-on-azure-ad"></a>Automatisch accounts selecteren in azure AD

In bepaalde gevallen heeft de toepassing toegang tot de verificatie context van de gebruiker en wil de Azure AD-account selectie prompt voor komen wanneer er meerdere accounts zijn aangemeld.  Dit kan op verschillende manieren worden gedaan:

**Sessie-ID (SID) gebruiken**

Sessie-ID is een [optionele claim](active-directory-optional-claims.md) die kan worden geconfigureerd in de id-tokens. Met deze claim kan de toepassing de Azure AD-sessie van de gebruiker identificeren onafhankelijk van de account naam of gebruikers naam van de gebruiker. U kunt de SID in de aanvraag parameters door geven aan de aanroep van `acquireTokenSilent`. Hierdoor kan Azure AD de account selectie overs Laan. SID is gebonden aan de sessie cookie en biedt geen verdere browser contexten.

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
> SID kan alleen worden gebruikt met Silent verificatie aanvragen die zijn gedaan door `acquireTokenSilent` aanroep in MSAL. js.
[Hier](active-directory-optional-claims.md)vindt u de stappen voor het configureren van optionele claims in het manifest van uw toepassing.

**Aanmeldings Hint gebruiken**

Als u geen SID-claim hebt geconfigureerd of de account selectie prompt wilt overs laan in interactieve verificatie aanroepen, kunt u dit doen door een `login_hint` op te geven in de aanvraag parameters en eventueel een `domain_hint` als `extraQueryParameters` in de interactieve methoden MSAL. js (@no __t_3_, `loginRedirect`, `acquireTokenPopup` en `acquireTokenRedirect`). Bijvoorbeeld:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

U kunt de waarden voor login_hint en domain_hint ophalen door de claims te lezen die zijn geretourneerd in het ID-token voor de gebruiker.

* **loginHint** moet worden ingesteld op de `preferred_username` claim in het id-token.

* **domain_hint** moet alleen worden door gegeven wanneer de/veelvoorkomende-instantie wordt gebruikt. De domein Hint wordt bepaald door de Tenant-ID (TID).  Als de `tid` claim in het ID-token is `9188040d-6c67-4c5b-b112-36a304b66dad` is deze consument. Anders is het organisaties.

Lees [hier](v2-oauth2-implicit-grant-flow.md) meer informatie over de waarden voor aanmeldings hint en domein hint.

> [!Note]
> U kunt SID en login_hint niet tegelijkertijd door geven. Dit resulteert in een fout melding.

## <a name="sso-without-msaljs-login"></a>SSO zonder MSAL. js-aanmelding

MSAL. js vereist standaard dat een aanmeldings methode wordt aangeroepen om een gebruikers context te maken voordat tokens voor Api's worden opgehaald. Aangezien de aanmeldings methoden interactief zijn, ziet de gebruiker een prompt.

Er zijn bepaalde gevallen waarin toepassingen toegang hebben tot de context van de geverifieerde gebruiker of ID-token via authenticatie die is gestart in een andere toepassing en SSO wilt gebruiken om tokens te verkrijgen zonder u eerst aan te melden via MSAL. js.

Een voor beeld hiervan is: een gebruiker is aangemeld bij een bovenliggende webtoepassing die als host fungeert voor een andere Java script-toepassing die wordt uitgevoerd als een invoeg toepassing of invoeg toepassing.

De SSO-ervaring in dit scenario kan als volgt worden bereikt:

Geef de `sid` indien beschikbaar (of `login_hint` en eventueel `domain_hint`) als aanvraag parameters aan de aanroep MSAL. js `acquireTokenSilent` als volgt:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO in ADAL. js naar MSAL. js update

MSAL. js brengt functie pariteit met ADAL. js voor Azure AD-verificatie scenario's. Als u de migratie van ADAL. js naar MSAL. js eenvoudig wilt maken en u wilt voor komen dat gebruikers zich opnieuw aanmelden, leest de bibliotheek het ID-token dat de gebruikers sessie vertegenwoordigt in ADAL. js-cache en worden de gebruikers naadloos ondertekend in MSAL. js.  

Als u gebruik wilt maken van het gedrag van eenmalige aanmelding (SSO) bij het bijwerken vanuit ADAL. js, moet u ervoor zorgen dat de bibliotheken `localStorage` gebruiken voor cache-tokens. Stel de `cacheLocation` in op `localStorage` in de configuratie MSAL. js en ADAL. js bij initialisatie als volgt:


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

Zodra deze is geconfigureerd, kan MSAL. js de status van de geverifieerde gebruiker in ADAL. js lezen en gebruiken om SSO in MSAL. js op te geven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de waarden voor [eenmalige aanmelding en levens duur van tokens](active-directory-configurable-token-lifetimes.md) in azure AD.
