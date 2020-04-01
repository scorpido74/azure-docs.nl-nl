---
title: Een token aanschaffen om een web-API aan te roepen (apps met één pagina) - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een toepassing met één pagina (een token aanschaffen om een API aan te roepen)
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
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419668"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Toepassing met één pagina: een token aanschaffen om een API aan te roepen

Het patroon voor het verkrijgen van tokens voor API's met MSAL.js is om eerst een stille tokenaanvraag te proberen met behulp van de `acquireTokenSilent` methode. Wanneer deze methode wordt aangeroepen, controleert de bibliotheek eerst de cache in browseropslag om te zien of er een geldig token bestaat en retourneert deze. Wanneer er geen geldig token in de cache is, wordt een stil tokenverzoek naar Azure Active Directory (Azure AD) verstuurt vanuit een verborgen iframe. Met deze methode kan de bibliotheek ook tokens vernieuwen. Zie Tokenlevensduur voor meer informatie over de waarden van eenmalige [aanmeldingssessie](active-directory-configurable-token-lifetimes.md)en tokenlevensduur in Azure AD.

De stille tokenaanvragen voor Azure AD mislukken mogelijk om redenen zoals een verlopen Azure AD-sessie of een wachtwoordwijziging. In dat geval u een beroep doen op een van de interactieve methoden (die de gebruiker zal vragen) om tokens te verwerven:

* [Pop-upvenster](#acquire-a-token-with-a-pop-up-window), met behulp van`acquireTokenPopup`
* [Redirect](#acquire-a-token-with-a-redirect), door gebruik te maken van`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Kies tussen een pop-up- of omleidingservaring

 U niet zowel de pop-up- als omleidingsmethoden in uw toepassing gebruiken. De keuze tussen een pop-up- of omleidingservaring is afhankelijk van uw toepassingsstroom:

* Als u niet wilt dat gebruikers tijdens de verificatie van uw hoofdtoepassingspagina afstappen, raden we de pop-upmethode aan. Omdat de omleiding van de verificatie plaatsvindt in een pop-upvenster, blijft de status van de hoofdtoepassing behouden.

* Als gebruikers browserbeperkingen of -beleidsregels hebben waarbij pop-ups zijn uitgeschakeld, u de omleidingsmethode gebruiken. Gebruik de omleidingsmethode met de Internet Explorer-browser, omdat er bekende problemen zijn [met pop-upvensters in Internet Explorer.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

U instellen dat de API-scopes die het toegangstoken moet bevatten, worden opgenomen wanneer het de aanvraag voor toegangstoken wordt opgebouwd. Houd er rekening mee dat niet alle gevraagde scopes worden toegekend in het toegangstoken. Dat hangt af van de toestemming van de gebruiker.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Een token aanschaffen met een pop-upvenster

# <a name="javascript"></a>[Javascript](#tab/javascript)

De volgende code combineert het eerder beschreven patroon met de methoden voor een pop-upervaring:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

# <a name="angular"></a>[Angular](#tab/angular)

De MSAL Angular wrapper biedt de HTTP interceptor, die automatisch toegang tokens in stilte te verwerven en hechten aan de HTTP-verzoeken aan API's.

U de scopes voor `protectedResourceMap` API's opgeven in de configuratieoptie. `MsalInterceptor`zal deze scopes aanvragen bij het automatisch verwerven van tokens.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Voor het succes en het mislukken van de stille token-acquisitie biedt MSAL Angular callbacks waarop u zich abonneren. Het is ook belangrijk om te onthouden om je af te melden.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

U ook expliciet tokens aanschaffen met behulp van de acquire-token-methoden zoals beschreven in de kern-MSAL.js-bibliotheek.

---

## <a name="acquire-a-token-with-a-redirect"></a>Een token met een omleiding aanschaffen

# <a name="javascript"></a>[Javascript](#tab/javascript)

Het volgende patroon is zoals eerder beschreven, maar wordt weergegeven met een omleidingsmethode om tokens interactief te verkrijgen. U moet de omleidingscallback registreren zoals eerder vermeld.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Optionele claims aanvragen

U optionele claims gebruiken voor de volgende doeleinden:

- Voeg aanvullende claims toe aan tokens voor uw aanvraag.
- Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
- Aangepaste claims voor uw toepassing toevoegen en openen.

Als u optionele `IdToken`claims wilt aanvragen in , `claimsRequest` kunt u `AuthenticationParameters.ts` een tekenreeksclaimobject naar het veld van de klasse verzenden.

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

Zie [Optionele claims](active-directory-optional-claims.md)voor meer informatie.

# <a name="angular"></a>[Angular](#tab/angular)

Deze code is hetzelfde als eerder beschreven.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen](scenario-spa-call-api.md)
