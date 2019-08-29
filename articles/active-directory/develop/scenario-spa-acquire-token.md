---
title: Toepassing met één pagina (een Token ophalen om een API aan te roepen)-micro soft Identity-platform
description: Meer informatie over het bouwen van een toepassing met één pagina (een Token ophalen om een API aan te roepen)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135713"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Toepassing met één pagina: een Token ophalen om een API aan te roepen

Het patroon voor het verkrijgen van tokens voor api's met MSAL. js is om eerst een Silent token aanvraag te proberen `acquireTokenSilent` met behulp van de methode. Wanneer deze methode wordt aangeroepen, controleert de bibliotheek eerst de cache in de browser opslag om te zien of een geldig token bestaat en retourneert. Wanneer er geen geldig token in de cache staat, verzendt het een Silent-token aanvraag naar Azure Active Directory (Azure AD) van een verborgen iframe. Met deze methode kan de bibliotheek ook tokens vernieuwen. Zie [levens duur](active-directory-configurable-token-lifetimes.md)van tokens voor meer informatie over de waarden van de sessie voor eenmalige aanmelding en levens duur van tokens in azure AD.

De aanvragen voor het Silent-token voor Azure AD kunnen om een of andere reden mislukken, zoals een verlopen Azure AD-sessie of een wachtwoord wijziging. In dat geval kunt u een van de interactieve methoden (waarmee de gebruiker wordt gevraagd) aanroepen om tokens te verkrijgen.

* [Token verkrijgen met een pop-upvenster](#acquire-token-with-a-pop-up-window) met`acquireTokenPopup`
* [Token met omleiding verkrijgen](#acquire-token-with-redirect) met behulp van`acquireTokenRedirect`

**Kiezen tussen een pop-up-of omleidings ervaring**

 U kunt geen combi natie van zowel de pop-up-als omleidings methoden in uw toepassing gebruiken. De keuze tussen een pop-up-of omleidings ervaring is afhankelijk van uw toepassings stroom.

* Als u niet wilt dat de gebruiker tijdens de verificatie naar de hoofd pagina van de toepassing navigeert, is het raadzaam om de pop-upmethode te gebruiken. Omdat de verificatie omleiding plaatsvindt in een pop-upvenster, blijft de status van de hoofd toepassing behouden.

* Er zijn bepaalde gevallen waarin u mogelijk de omleidings methoden moet gebruiken. Als gebruikers van uw toepassing browser beperkingen of-beleid hebben waarin pop-ups Windows zijn uitgeschakeld, kunt u de omleidings methoden gebruiken. Het is ook raadzaam om de omleidings methoden te gebruiken in de browser van Internet Explorer, omdat er bepaalde [bekende problemen met Internet Explorer optreden](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) bij het verwerken van pop-upvensters.

U kunt de API-bereiken instellen waarvan u wilt dat het toegangs token moet worden opgenomen bij het bouwen van de toegangs token aanvraag. Houd er rekening mee dat alle aangevraagde bereiken mogelijk niet in het toegangs token mogen worden verleend en is afhankelijk van de toestemming van de gebruiker.

## <a name="acquire-token-with-a-pop-up-window"></a>Token verkrijgen met een pop-upvenster

### <a name="javascript"></a>JavaScript

Het bovenstaande patroon met behulp van de methoden voor een pop-upervaring:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

De MSAL-hoek wrapper biedt het gemak voor het toevoegen van de HTTP-Interceptor, waarmee automatisch toegangs tokens op de achtergrond worden opgehaald en aan de HTTP-aanvragen voor Api's worden gekoppeld.

U kunt de scopes voor api's opgeven in de `protectedResourceMap` configuratie optie, die door de MsalInterceptor wordt aangevraagd bij het automatisch ophalen van tokens.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Voor het slagen en mislukken van de aanschaf van de Silent-tokens, MSAL hoek biedt retour aanroepen waarop u zich kunt abonneren. Het is ook belang rijk om te onthouden dat u zich wilt afmelden.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

U kunt ook expliciet tokens verkrijgen met behulp van de methoden voor het ophalen van tokens, zoals beschreven in de core MSAL. JS-bibliotheek.

## <a name="acquire-token-with-redirect"></a>Token ophalen met omleiden

### <a name="javascript"></a>JavaScript

Het patroon wordt hierboven beschreven, maar wordt weer gegeven met een omleidings methode om token interactief te verkrijgen. U moet de retour aanroep van de omleiding registreren zoals hierboven wordt vermeld.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>Aanvraag voor optionele claims
U kunt in uw app optionele claims aanvragen om op te geven welke extra claims moeten worden toegevoegd aan de tokens voor uw toepassing. Als u optionele claims wilt aanvragen in de id_token, kunt u een stringified-claim object verzenden naar het veld claimsRequest van de klasse AuthenticationParameters. TS.

U kunt optionele claims gebruiken voor de volgende doel einden:

- Voor het toevoegen van aanvullende claims in tokens voor uw toepassing.
- Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
- Aangepaste claims toevoegen en openen voor uw toepassing.


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
Voor meer informatie over optionele claims, voor het afhandelen van [optionele claims](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

Dit is hetzelfde als hierboven beschreven.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen](scenario-spa-call-api.md)
